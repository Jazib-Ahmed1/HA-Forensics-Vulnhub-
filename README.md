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
