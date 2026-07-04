# Network Enumeration and Service Exploitation Assessment

## Overview

This project demonstrates a comprehensive network security assessment using a constrained toolset consisting of Nmap, Telnet, and Hydra. The objective was to identify exposed services, perform service enumeration, conduct banner analysis, execute credential attacks against exposed services, and retrieve sensitive information from accessible network resources.

The assessment highlights the importance of secure service configuration, credential management, and minimizing information disclosure through service banners.

---

## Objective

The goals of this assessment were to:

* Perform full TCP port enumeration
* Identify services running on non-standard ports
* Conduct banner grabbing and service fingerprinting
* Enumerate service versions
* Perform credential attacks against exposed services
* Access authenticated network resources
* Retrieve protected information from target services

---

## Tools Used

| Tool       | Purpose                                  |
| ---------- | ---------------------------------------- |
| Nmap       | Port scanning and service enumeration    |
| Telnet     | Banner grabbing and protocol interaction |
| Hydra      | Credential attacks                       |
| FTP Client | Service interaction                      |
| Curl       | HTTP header enumeration                  |

---

## Attack Path

```text
Target Discovery
        ↓
Full TCP Port Enumeration
        ↓
Service Identification
        ↓
Banner Grabbing
        ↓
Service Version Enumeration
        ↓
Credential Enumeration
        ↓
Password Attack
        ↓
Authenticated Access
        ↓
Information Disclosure
        ↓
Objective Completion
```

---

## MITRE ATT&CK Mapping

| Tactic            | Technique                                  |
| ----------------- | ------------------------------------------ |
| Reconnaissance    | T1595 – Active Scanning                    |
| Discovery         | T1046 – Network Service Discovery          |
| Credential Access | T1110 – Brute Force                        |
| Collection        | T1213 – Data from Information Repositories |
| Discovery         | T1592 – Gather Victim Host Information     |

---

# Phase 1: Full Port Enumeration

A full TCP port scan was performed to identify all exposed services.

### Command

```bash
sudo nmap -sS -sV -p- -T4 --open TARGET_IP
```

### Findings

| Port  | Service             |
| ----- | ------------------- |
| 22    | SSH                 |
| 80    | HTTP                |
| 139   | NetBIOS             |
| 445   | SMB                 |
| XXXX  | Node.js Web Service |
| XXXXX | FTP                 |

The assessment identified both standard and non-standard network services.

---

# Phase 2: HTTP Enumeration

HTTP response headers were inspected to identify server information and hidden data.

### Command

```bash
curl -I http://TARGET_IP
```

### Findings

* Web server identified as Lighttpd
* Information disclosure discovered within HTTP response headers

---

# Phase 3: SSH Banner Enumeration

The SSH service banner was manually enumerated.

### Command

```bash
telnet TARGET_IP 22
```

### Findings

* OpenSSH version identified
* Additional information disclosure discovered in the SSH banner

---

# Phase 4: FTP Service Enumeration

A non-standard FTP service was identified and investigated.

### Command

```bash
ftp TARGET_IP PORT
```

### Findings

* FTP server identified as vsFTPd 3.0.5
* Authentication required for access

---

# Phase 5: Credential Attack

Previously identified usernames were used during a password attack against the FTP service.

### Command

```bash
hydra -L USERS.txt -P rockyou.txt ftp://TARGET_IP:PORT
```

### Results

Multiple valid credentials were successfully identified.

---

# Phase 6: Authenticated Access

Using the discovered credentials, authenticated access was obtained to the FTP server.

### Activities

* Logged into the FTP service
* Enumerated available files
* Retrieved protected resources
* Extracted challenge data

---

# Phase 7: Web Challenge Enumeration

Additional web application functionality was discovered on a non-standard HTTP port.

### Activities

* Identified exposed web service
* Performed network analysis
* Solved the challenge through packet analysis and enumeration techniques

---

# Security Findings

The assessment identified several security weaknesses:

* Excessive service exposure
* Information disclosure through service banners
* Use of weak passwords
* Credential reuse
* Exposure of non-standard services
* Sensitive information disclosure

---

# Detection Opportunities

Defenders could detect this activity through:

* Port scan detection
* Service enumeration monitoring
* Banner grabbing alerts
* Brute-force detection mechanisms
* Authentication failure monitoring
* FTP access logging
* Web access anomaly detection

---

# Defensive Mitigations

Recommended security controls include:

* Disable unnecessary services
* Restrict service banner information
* Enforce strong password policies
* Implement account lockout mechanisms
* Deploy intrusion detection systems
* Monitor authentication attempts
* Restrict access to administrative services
* Conduct regular network audits

---

# Skills Demonstrated

* Network Enumeration
* TCP Port Scanning
* Service Fingerprinting
* Banner Grabbing
* Protocol Analysis
* Credential Attacks
* FTP Enumeration
* Web Enumeration
* Security Assessment Methodology
* MITRE ATT&CK Mapping

---

# Key Lessons Learned

* Full port scans often reveal overlooked attack surfaces.
* Service banners can leak sensitive information.
* Non-standard ports should never be assumed secure.
* Weak credentials remain a common attack vector.
* Thorough enumeration is essential during network assessments.
* Layered defenses are necessary to prevent service exploitation.

---

## Disclaimer

This assessment was conducted in an authorized laboratory environment for educational and security research purposes only.
