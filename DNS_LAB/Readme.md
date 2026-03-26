# DNS Enumeration & Security Analysis Lab

##  Overview

This project demonstrates practical skills in **DNS enumeration, DNS security assessment, and detection of suspicious DNS activity** such as DNS tunneling and DGA-based malware.

The lab was performed in a controlled environment for educational purposes.

---

##  Tools & Technologies Used

* `dig` – DNS querying
* `tcpdump` – Packet capture
* `Wireshark` – Traffic analysis
* `iodine` – DNS tunneling simulation
* Linux (WSL / Ubuntu)

---

##  Part 1: DNS Enumeration

###  Information Gathering

Performed DNS reconnaissance to extract key records:

```bash
dig example.com ANY
dig example.com MX +short
dig example.com NS +short
dig example.com TXT +short | grep spf
dig _dmarc.example.com TXT +short
dig example.com CAA +short
```

###  Security Checks

* Verified **SPF, DMARC, and CAA records**
* Identified email security configurations
* Checked for misconfigurations

---

###  Zone Transfer Test (AXFR)

```bash
dig @ns1.example.com example.com AXFR
```

* Tested whether DNS zone transfer is enabled
* A properly secured server **should deny this request**
* Helps detect potential data leakage

---

###  Subdomain Enumeration

```bash
for sub in www mail ftp vpn dev staging; do
    dig $sub.example.com +short
done
```

* Discovered common subdomains
* Checked for exposed services

---

###  Wildcard DNS Detection

```bash
dig randomnonexistent.example.com +short
```

* Used to identify wildcard DNS configurations

---

##  Part 2: DNS Traffic Analysis (Without Tunneling Tools)

###  Traffic Capture

DNS traffic capture kiya using `tcpdump`:

```bash
sudo tcpdump -i eth0 port 53 -w dns_capture.pcap
```

* Network interface se DNS packets capture kiye
* Output `.pcap` file me store kiya for further analysis

---

###  Analysis using Wireshark

Captured file ko Wireshark me open karke DNS traffic analyze kiya.

**Filter used:**

```
dns
```

---

###  Observations

Analysis ke dauran kuch suspicious patterns observe hue:

* Random / unreadable subdomains (e.g., `xjs8df7g.domain.com`)
* High number of DNS queries to unusual domains
* Non-human readable domain names (possible algorithm-generated)
* Repeated queries within short time interval

---

###  Possible Security Issues

#### 🟠 DGA (Domain Generation Algorithm) Activity

* Random-looking domains indicate malware-generated domains
* Used by malware to contact command-and-control (C2) servers

####  Suspicious DNS Behavior

* High frequency queries can indicate:

  * Malware activity
  * Data exfiltration attempts
  * Misconfigured applications

---

##  Key Takeaways

* DNS traffic analysis se hidden threats detect kiye ja sakte hain
* Random domains often indicate **malicious activity**
* Packet capture tools jaise `tcpdump` aur Wireshark bahut powerful hote hain

---

##  Part 3: DNS Log Analysis

###  Sample Logs

```
2024-01-15 10:23:15 192.168.1.105 TXT SGVsbG8gV29ybGQ.data.suspicious.net
2024-01-15 10:23:16 192.168.1.105 TXT dGhpcyBpcyBhIHRlc3Q.data.suspicious.net
2024-01-15 10:23:17 192.168.1.105 TXT ZXhmaWx0cmF0aW9u.data.suspicious.net
```

---

###  Findings

####  DNS Tunneling Detected

* **Host:** `192.168.1.105`
* Reason:

  * Base64-encoded subdomains
  * Repeated TXT queries
* Decoded Data:

  * "Hello World"
  * "this is a test"
  * "exfiltration"

 Indicates **data exfiltration using DNS tunneling**

---

####  DGA Malware Activity

* **Host:** `192.168.1.110`
* Example domains:

  * `xjs8df7g.malware.com`
  * `9d8fjk23.malware.com`

Random domain patterns indicate **Domain Generation Algorithm (DGA)** usage

---

##  Key Learnings

* DNS can be abused for **data exfiltration**
* Monitoring TXT queries is critical
* DGA domains are detectable via randomness
* Proper DNS configuration prevents **zone transfer attacks**

---

##  Disclaimer

This project is for **educational purposes only**. All tests were conducted in a controlled lab environment or on permitted systems.

---

##  Future Improvements

* Automate suspicious domain detection using Python
* Integrate WHOIS-based domain age analysis
* Build real-time DNS monitoring dashboard

---
