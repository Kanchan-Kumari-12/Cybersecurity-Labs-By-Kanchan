# Security Assessment Report

## 1. Network Segments

* Only ONE segment detected: 172.20.16.0/20
* No VLANs or subnet separation

 Flat network architecture

---

## 2. Controls Between Segments

* Gateway (172.20.X.X) filters traffic
* Most ports are blocked

Indicates firewall presence

---

## 3. Management Interface Access

Tested:

* SSH (22)
* Telnet (23)
* HTTP (80)
* HTTPS (443)

All filtered

Secure configuration

---

## 4. Unnecessary Services

### MySQL (Port 3306)

* Open and accessible
* Potential risks:

  * Unauthorized access
  * Brute-force attacks
  * Data exposure

### DNS (Port 53)

* Expected service

---

## 5. Lateral Movement Paths

### Observations:

* Flat network
* No segmentation
* Internal hosts reachable

### Attack Scenario:

1. Attacker gains access to one machine
2. Scans entire subnet
3. Exploits exposed services

Risk Level: HIGH

---

## 6. Recommendations

### Immediate:

* Restrict MySQL to localhost
* Apply firewall rules

### Long-Term:

* Implement VLANs
* Apply Zero Trust model
* Enable logging & monitoring

---

## 7. Final Verdict

| Category          | Status   |
| ----------------- | -------- |
| External Security | Good     |
| Internal Security | Weak     |
| Service Exposure  |  Risky   |

---

## Conclusion

The network lacks internal segmentation and exposes unnecessary services, increasing the risk of lateral movement and exploitation.
