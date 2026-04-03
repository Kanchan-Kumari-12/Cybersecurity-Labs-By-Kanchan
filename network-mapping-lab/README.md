#  Network Mapping & Security Analysis Lab

##  Objective

The goal of this lab is to:

* Map the network structure
* Identify segmentation
* Discover active hosts and services
* Analyze potential security risks

---

##  Lab Environment

* OS: Linux (Ubuntu / WSL)
* Tool: Nmap
* Network Type: Private Lab Network

---

##  Network Configuration

| Parameter       | Value          |
| --------------- | -------------- |
| IP Address      | 172.20.22.61   |
| Subnet          | 172.20.16.0/20 |
| Default Gateway | 172.20.16.1    |

---

##  Part 1: Local Network Discovery

### Commands Used

```bash
ip addr show
ip route show
sudo nmap -sn 172.20.16.0/20 -oN local_hosts.txt
```

### Findings

* Total Hosts Scanned: 4096
* Active Hosts Found: 2

  * 172.20.X.X (Gateway)
  * 172.20.X.X (Local Machine)

---

##  Part 2: Service Discovery

### Commands Used

```bash
sudo nmap -F 172.20.16.0/20 -oN quick_scan.txt
grep "open" quick_scan.txt
```

### Open Services

| Port     | Service | Status     |
| -------- | ------- | ---------- |
| 53/UDP   | DNS     | Expected   |
| 3306/TCP | MySQL   |  Exposed |

---

##  Part 3: Network Diagram

```
                Internet
                    |
            -----------------
            |               |
     [ Gateway / Router ]
        172.20.16.1
        (Hyper-V NAT)
            |
    ---------------------
    |                   |
[ Your Host ]     [ Other Hosts ]
172.20.22.61      (None active)

Open Services:
- 53/UDP → DNS
- 3306/TCP → MySQL 
```

---

##  Part 4: Security Assessment

### Key Observations

* Single flat network (no segmentation)
* Gateway filters most ports
* No access to management interfaces (SSH/HTTP blocked)
*  MySQL exposed on port 3306
*  Potential lateral movement risk

---

##  Risks Identified

* Exposed database service (MySQL)
* Lack of VLAN/network segmentation
* Internal network trust (flat architecture)

---

##  Recommendations

* Restrict MySQL to localhost:

  ```bash
  bind-address = 127.0.0.1
  ```
* Block unnecessary ports:

  ```bash
  sudo ufw deny 3306
  ```
* Implement VLAN segmentation
* Enable monitoring & logging

---

## Learning Outcomes

* Practical use of Nmap
* Network enumeration techniques
* Identifying real-world misconfigurations
* Understanding lateral movement risks

---

## Conclusion

The network has basic firewall protection but lacks internal segmentation, making it vulnerable to lateral movement and service exploitation.
