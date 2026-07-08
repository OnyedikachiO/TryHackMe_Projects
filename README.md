# Penetration Testing Portfolio

> A collection of hands-on penetration testing, offensive security, web application security, and vulnerability assessment projects completed in authorized lab and training environments.

---

## About

This repository documents practical penetration testing exercises performed across cybersecurity labs, Capture The Flag (CTF) environments, and intentionally vulnerable applications.

The projects demonstrate a structured offensive security methodology that spans the complete penetration testing lifecycle—from reconnaissance and attack surface mapping to exploitation, security analysis, and remediation.

The repository serves as a practical portfolio showcasing hands-on experience in identifying, validating, documenting, and mitigating common web application and infrastructure vulnerabilities while following ethical hacking principles and responsible security practices.

---

## Skills Demonstrated

* Penetration Testing
* Web Application Security
* API Security
* Authentication Testing
* Authorization Testing
* Access Control Testing
* Reconnaissance
* Content Discovery
* Directory Enumeration
* Subdomain Enumeration
* OSINT
* Web Fingerprinting
* Vulnerability Assessment
* Vulnerability Validation
* Exploitation
* Insecure Direct Object Reference (IDOR)
* Authentication Bypass
* File Inclusion Testing (LFI/RFI)
* Directory Traversal
* JWT Security Analysis
* Cryptographic Security Analysis
* Linux Enumeration
* Linux Privilege Escalation
* HTTP Protocol Analysis
* Cookie Security Testing
* Burp Suite Testing
* Command Line Tooling
* Security Documentation
* OWASP Top 10 Analysis

---

# Projects

## Secure API Development

Hands-on API security assessments focusing on secure authentication, authorization, and token validation.

### Topics Covered

* JWT Authentication
* Signature Verification
* Algorithm Confusion
* None Algorithm Attacks
* JWT Header Manipulation
* Claims Validation
* Token Expiration
* Secret Discovery
* Cross-Service Relay Attacks
* API Authorization Testing

**Skills**

* API Security Testing
* JWT Analysis
* Burp Suite
* Linux CLI
* Authentication Testing

---

## JWT Security

Practical projects exploring common weaknesses in JSON Web Tokens.

### Topics

* JWT Structure
* Header Manipulation
* Weak Secrets
* Signature Bypass
* Token Forgery
* Key Confusion
* Authorization Abuse

---

## Linux Privilege Escalation

Collection of Linux privilege escalation techniques performed within authorized lab environments.

### Topics

* Enumeration
* SUID Binaries
* Cron Jobs
* PATH Hijacking
* Linux Capabilities
* Writable Files
* Scheduled Tasks
* Misconfigurations
* Root Escalation

**Tools**

* LinPEAS
* GTFOBins
* Bash
* Find
* Linux CLI

---

## Net Sec Challenge (TryHackMe)

Network security exercises covering host enumeration, service identification, and exploitation.

### Skills

* Service Enumeration
* Port Scanning
* FTP
* SMB
* HTTP
* SSH
* File Analysis
* Password Discovery

---

## Content Discovery

Hands-on reconnaissance project focused on discovering hidden web application content through manual analysis, OSINT, and automated enumeration.

### Topics

* Robots.txt Analysis
* Sitemap Enumeration
* HTTP Header Enumeration
* Favicon Fingerprinting
* Framework Discovery
* Google Dorking
* Wayback Machine
* Public Repository Enumeration
* Cloud Storage Discovery
* Directory Enumeration

**Tools**

* Gobuster
* FFUF
* DIRB
* cURL
* SecLists
* Wappalyzer
* GitHub
* Google Dorking
* Wayback Machine

---

## Subdomain Enumeration

Practical reconnaissance exercises focused on expanding attack surface through subdomain discovery.

### Topics

* Certificate Transparency Logs
* DNS Enumeration
* DNS Bruteforcing
* Search Engine Enumeration
* Virtual Host Discovery
* Passive OSINT
* Active Enumeration

**Tools**

* DNSRecon
* Sublist3r
* FFUF
* crt.sh
* Google Search
* SecLists

---

## Authentication Bypass

Projects demonstrating common authentication weaknesses and techniques used to identify insecure authentication implementations.

### Topics

* Username Enumeration
* Password Brute Force
* Authentication Logic Flaws
* Password Reset Abuse
* Cookie Tampering
* Session Manipulation
* Base64 Encoding
* MD5 Hash Analysis

**Tools**

* FFUF
* cURL
* Burp Suite
* CrackStation
* Browser Developer Tools

---

## IDOR (Insecure Direct Object Reference)

Practical access control assessments demonstrating how insecure object references expose unauthorized resources.

### Topics

* Parameter Manipulation
* Encoded IDs
* Hashed IDs
* JSON API Enumeration
* API Endpoint Testing
* Horizontal Privilege Escalation
* Access Control Validation

**Tools**

* Burp Suite
* Browser Developer Tools
* cURL
* Base64 Utilities

---

## File Inclusion

Practical web application security projects covering Local File Inclusion (LFI), Remote File Inclusion (RFI), and Directory Traversal vulnerabilities.

### Topics

* Local File Inclusion (LFI)
* Remote File Inclusion (RFI)
* Directory Traversal
* Path Traversal
* PHP Include Functions
* Null Byte Injection
* Filter Bypass
* Remote Code Execution
* File Disclosure

**Tools**

* Burp Suite
* cURL
* Browser Developer Tools
* PHP
* HTTP Requests

---

# OWASP Top 10 (2025) Coverage

This repository contains practical projects aligned with multiple categories from the OWASP Top 10 (2025).

---

## A01 — Broken Access Control

Projects include:

* IDOR
* Forced Browsing
* Authorization Bypass
* Horizontal Privilege Escalation
* Vertical Privilege Escalation
* Missing Authorization Checks
* Access Control Validation

---

## A02 — Cryptographic Failures

Projects include:

* Weak Password Hashes
* MD5 Analysis
* Base64 Encoding
* Secret Exposure
* Weak Cryptographic Implementations
* Missing Encryption
* Key Management Issues

---

## A04 — Insecure Design

Projects include:

* Authentication Logic Flaws
* Password Reset Abuse
* Business Logic Weaknesses
* Security Design Analysis

---

## A05 — Security Misconfiguration

Projects include:

* Information Disclosure
* Default Administrative Interfaces
* Framework Fingerprinting
* HTTP Header Enumeration
* Exposed Configuration Files

---

## A07 — Identification & Authentication Failures

Projects include:

* Username Enumeration
* Password Brute Force
* Authentication Bypass
* Cookie Tampering
* Session Management Weaknesses
* Credential Attacks

---

## A09 — Security Logging & Monitoring Failures

Projects include:

* Security Analysis
* Detection Considerations
* Monitoring Recommendations
* Incident Response Improvements

---

# Penetration Testing Methodology

Most projects follow a structured assessment workflow:

1. Reconnaissance
2. Information Gathering
3. Attack Surface Mapping
4. Enumeration
5. Vulnerability Identification
6. Exploitation
7. Validation
8. Security Impact Assessment
9. Mitigation Recommendations
10. Documentation

---

# Tools & Technologies

* Burp Suite
* OWASP ZAP
* Nmap
* Gobuster
* FFUF
* DIRB
* DNSRecon
* Sublist3r
* cURL
* SecLists
* Wappalyzer
* CyberChef
* JWT Toolkit
* OpenSSL
* LinPEAS
* GTFOBins
* Wireshark
* Browser Developer Tools
* CrackStation
* Wayback Machine
* crt.sh
* GitHub
* Google Dorking

---

# Documentation Standards

Each project is documented using a consistent professional structure and typically includes:

* Project Overview
* Disclaimer
* Objectives
* Skills Learned
* Tools & Technologies
* Methodology
* Commands Used
* Findings
* Security Impact
* Recommendations
* Lessons Learned
* Outcome

---

# Ethical Notice

All demonstrations documented within this repository were performed exclusively inside authorized environments, including:

* TryHackMe Labs
* Capture The Flag (CTF) Platforms
* Local Virtual Machines
* Personal Practice Labs
* Deliberately Vulnerable Applications
* Educational Security Platforms

No testing was performed against systems without authorization.

---

# Learning Goals

This repository documents my continuous growth in:

* Offensive Security
* Ethical Hacking
* Penetration Testing
* Web Application Security
* API Security
* Authentication & Authorization Testing
* Vulnerability Research
* Secure Development Practices
* Security Engineering
* Professional Security Documentation

---

## Connect

This repository continues to grow as I complete additional penetration testing labs, web application assessments, security research, and offensive security projects.

Each project reflects practical, hands-on experience while emphasizing responsible disclosure, ethical hacking, and industry best practices.

⭐ If you find these projects helpful, feel free to explore the repository and follow my cybersecurity journey.
