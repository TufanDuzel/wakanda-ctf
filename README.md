# 🚩 Wakanda-1 CTF Analysis & Compromise

Welcome to my write-up for the **Wakanda-1** machine. This project represents a significant milestone in my journey toward becoming a professional **Red Team / Offensive Security** specialist. 

## 🎯 Purpose of this Repository
The main goal of this project was to apply theoretical knowledge in a controlled, isolated laboratory environment. It demonstrates a complete "Attack Lifecycle," starting from zero-knowledge reconnaissance to full administrative (root) compromise.

## 🧠 What I Learned & Developed
This CTF was more than just "getting a flag"; it challenged my problem-solving skills and technical depth in the following areas:

* [cite_start]**Advanced Reconnaissance:** Going beyond basic port scanning to perform service-specific enumeration and OS fingerprinting[cite: 4, 10].
* [cite_start]**Web Vulnerability Research:** Identifying and exploiting **Local File Inclusion (LFI)** by utilizing **PHP Filter Wrappers** to bypass execution and extract source code[cite: 13, 15, 16].
* [cite_start]**Linux Internals & Automation:** Understanding how system-level automation (Cronjobs) can be hijacked for lateral movement[cite: 26, 27].
* [cite_start]**Privilege Escalation Logic:** Learning to identify "misconfigurations" rather than just software bugs—specifically exploiting `sudo` permissions on system binaries like `pip`[cite: 34, 37, 38].
* [cite_start]**Tool Manipulation:** Using and modifying specialized exploits like **FakePip** to establish reverse shells from within a root-level process[cite: 35, 39].

## 🛠️ Tech Stack & Tools Used
* [cite_start]**OS:** Kali Linux (Attacker), Debian (Target)[cite: 10].
* [cite_start]**Networking:** Netdiscover (ARP Scanning), Nmap (Service Enumeration)[cite: 2, 4].
* [cite_start]**Exploitation:** PHP Wrappers, Netcat, Python PTY Spawning[cite: 15, 21, 29].
* [cite_start]**Post-Exploitation:** Linux Sudoers analysis, Cronjob hijacking[cite: 28, 37].

## 📈 Career Growth
As a final-year Computer Engineering student, this project has strengthened my understanding of:
1.  **The Attacker's Mindset:** Looking for the "weakest link" in a chain of configurations.
2.  **Documentation:** Learning how to record technical findings in a professional, "B2-level English" format for future reporting.
3.  [cite_start]**Persistence:** Debugging shell connections and understanding terminal stabilization (TTY)[cite: 20, 21].

---
*“In cybersecurity, the goal isn't just to find the door, but to understand how the lock was designed so you can find the flaw.”*