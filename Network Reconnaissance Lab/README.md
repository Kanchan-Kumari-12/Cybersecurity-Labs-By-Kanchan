#  Network Reconnaissance Lab — Nmap + Telnet

> A hands-on blue team / SOC analyst project demonstrating network scanning, service enumeration, and manual service verification using industry-standard tools in a controlled home lab environment.

---

##  Project Overview

This project simulates a real-world network reconnaissance workflow performed by SOC analysts and penetration testers. Using **Nmap** for port scanning and service detection, and **Telnet** for manual service verification, I identified and documented all exposed services on a vulnerable target machine deployed in a local Docker lab.

---

##  Tools & Environment

| Tool | Purpose |
|---|---|
| **Nmap 7.94** | Port scanning & service version detection |
| **Telnet** | Manual service verification & access testing |
| **Docker** | Lab environment — Metasploitable2 target |
| **WSL2 (Ubuntu 24.04)** | Attack machine / analyst workstation |
| **Metasploitable2** | Intentionally vulnerable target machine |

---

##  Lab Setup

```
┌─────────────────────────────────────┐
│         WSL2 — Ubuntu 24.04         │
│                                     │
│  ┌─────────┐      ┌──────────────┐  │
│  │  Nmap   │─────▶│ Metasploitable2│ │
│  │ Telnet  │      │ 172.17.0.2   │  │
│  └─────────┘      └──────────────┘  │
│    Analyst            Target        │
└─────────────────────────────────────┘
```

**Target IP:** `172.17.0.2`  
**Network:** Docker bridge (`172.17.0.0/16`)

---

##  Methodology

### Step 1 — Basic Port Scan
```bash
nmap 172.17.0.2
```
Quick scan to identify all open TCP ports (top 1000).

### Step 2 — Advanced Service Detection
```bash
nmap -sV -sC -A 172.17.0.2 -oN scan_results.txt
```

| Flag | Description |
|---|---|
| `-sV` | Service version detection |
| `-sC` | Default NSE scripts |
| `-A` | OS detection + traceroute |
| `-oN` | Save output to file |

### Step 3 — Manual Telnet Verification
```bash
telnet 172.17.0.2
# login: msfadmin
# password: msfadmin
```
Manually verified Telnet access and confirmed unauthenticated shell access risk.

---

##  Findings — Open Ports & Services

| Port | Service | Version | Risk Level |
|---|---|---|---|
| 21 | FTP | vsftpd 2.3.4 | 🔴 Critical |
| 22 | SSH | OpenSSH 4.7p1 | 🟡 Medium |
| 23 | Telnet | Linux telnetd | 🔴 Critical |
| 25 | SMTP | Postfix smtpd | 🟡 Medium |
| 80 | HTTP | Apache 2.2.8 | 🟡 Medium |
| 111 | RPCbind | v2 | 🟠 High |
| 139 | NetBIOS | Samba 3.0.20 | 🔴 Critical |
| 445 | SMB | Samba 3.0.20 | 🔴 Critical |
| 512 | rexecd | netkit-rsh | 🔴 Critical |
| 513 | rlogin | — | 🔴 Critical |
| 1524 | Backdoor shell | ingreslock | 🔴 Critical |
| 2121 | FTP | ProFTPD 1.3.1 | 🟠 High |
| 3306 | MySQL | 5.0.51a | 🔴 Critical |
| 5432 | PostgreSQL | 8.3.0 | 🔴 Critical |
| 5900 | VNC | Protocol 3.3 | 🔴 Critical |
| 6000 | X11 | — | 🟠 High |
| 6667 | IRC | UnrealIRCd | 🔴 Critical |
| 8009 | AJP | Apache Jserv | 🟠 High |
| 8180 | HTTP | Apache Tomcat 5.5 | 🟠 High |

**Total Open Ports Found: 21**

---

##  Key Vulnerabilities Identified

### 🔴 Critical — Telnet Service (Port 23)
- Telnet transmits data in **plaintext** — credentials visible in packet capture
- Successfully logged in using default credentials (`msfadmin:msfadmin`)
- **Recommendation:** Disable Telnet immediately; replace with SSH

### 🔴 Critical — Anonymous FTP (Port 21)
- vsftpd 2.3.4 — known backdoor vulnerability (CVE-2011-2523)
- Anonymous FTP login **allowed** without credentials
- **Recommendation:** Disable anonymous access; upgrade vsftpd

### 🔴 Critical — Databases Exposed (Ports 3306, 5432)
- MySQL and PostgreSQL accessible directly on network
- No firewall restriction detected
- **Recommendation:** Bind databases to localhost only; implement firewall rules

### 🔴 Critical — SMB Signing Disabled (Ports 139, 445)
- Samba 3.0.20 — vulnerable to MS08-067 style attacks
- Message signing disabled — susceptible to MITM attacks
- **Recommendation:** Enable SMB signing; upgrade Samba

### 🔴 Critical — Backdoor Shell (Port 1524)
- Port 1524 responding with root shell directly
- Zero authentication required
- **Recommendation:** Immediate isolation of system

---

##  Telnet Access — Evidence

```
Trying 172.17.0.2...
Connected to 172.17.0.2.
Escape character is '^]'.

06aae073d425 login: msfadmin
Password: 

Linux 06aae073d425 6.6.87.2-microsoft-standard-WSL2
Last login: Sun Jul 16 21:04:01 EDT 2017

msfadmin@06aae073d425:~$ whoami
msfadmin

msfadmin@06aae073d425:~$ hostname
06aae073d425

msfadmin@06aae073d425:~$ ifconfig
eth0  inet addr:172.17.0.2
```

---

##  Key Learnings

- How to perform **systematic network reconnaissance** using Nmap
- Difference between basic scans and **service version detection** (`-sV`)
- How **NSE scripts** (`-sC`) extract additional service information automatically
- Why **Telnet is dangerous** — plaintext protocol, no encryption
- How to **document findings** in a structured analyst report
- Importance of **default credential testing** in security assessments

---

##  Disclaimer

> This project was conducted entirely in a **controlled home lab environment** using intentionally vulnerable software (Metasploitable2) designed for security education. No real systems were scanned or accessed. All testing was performed legally and ethically.

---
