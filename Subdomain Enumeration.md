# Subdomain Enumeration

## Project Overview

In this project, I explored multiple techniques for discovering subdomains associated with a target domain to expand the attack surface during a penetration test. By combining passive reconnaissance, Open-Source Intelligence (OSINT), DNS brute-forcing, and virtual host enumeration, I identified hidden subdomains that could potentially expose additional services, applications, or administrative interfaces.

The assessment demonstrated how subdomain enumeration plays a critical role in the reconnaissance phase of penetration testing by revealing assets that are not immediately visible through standard web browsing.

---

## Objectives

- Understand the importance of subdomain enumeration.
- Perform passive subdomain discovery using OSINT.
- Enumerate subdomains using Certificate Transparency logs.
- Discover subdomains through search engines.
- Perform DNS brute-force enumeration.
- Automate subdomain discovery using Sublist3r.
- Discover hidden virtual hosts using FFUF.

---

## Skills Learned

- Subdomain enumeration
- Passive reconnaissance
- DNS enumeration
- Certificate Transparency analysis
- Search engine reconnaissance
- Virtual host discovery
- Host header manipulation
- Wordlist-based enumeration
- Attack surface expansion
- Web reconnaissance

---

## Tools & Technologies

- crt.sh
- Google Search
- DNSRecon
- Sublist3r
- FFUF
- SecLists
- DNS
- HTTP Host Header

---

# Methodology

## 1. Certificate Transparency (CT) Logs

I began the assessment by querying Certificate Transparency logs to identify SSL/TLS certificates that had previously been issued for the target domain.

Certificate Transparency databases maintain publicly accessible records of issued certificates, making them an excellent source for passive subdomain discovery.

### Resource Used

```text
https://crt.sh
```

By reviewing historical certificate entries, I discovered additional subdomains that were not immediately visible through normal website navigation.

This technique provides valuable reconnaissance without generating traffic toward the target.

---

## 2. Search Engine Enumeration

Next, I leveraged search engine indexing to identify publicly exposed subdomains.

Using advanced Google search operators helped narrow results to only those belonging to the target domain while excluding common entries such as the primary website.

### Google Dork Used

```text
site:*.target-domain.com -site:www.target-domain.com
```

Search engines often index forgotten or rarely visited subdomains, making them an effective passive reconnaissance resource.

---

## 3. DNS Brute-Force Enumeration

After completing passive reconnaissance, I performed active DNS brute-force enumeration.

This technique systematically attempts thousands of common subdomain names using a predefined wordlist to identify valid DNS records associated with the target.

### Command Used

```bash
dnsrecon -t brt -d <target-domain>
```

The enumeration successfully identified additional subdomains that expanded the target's attack surface.

---

## 4. Automated OSINT Enumeration with Sublist3r

To automate passive reconnaissance, I used **Sublist3r**, which aggregates results from multiple public intelligence sources including:

- Search engines
- Certificate Transparency logs
- Passive DNS databases
- Threat intelligence platforms

### Command Used

```bash
sublist3r -d <target-domain>
```

Using multiple intelligence sources improved the overall coverage of discovered subdomains while reducing manual effort.

---

## 5. Virtual Host Enumeration

Not every subdomain has a publicly accessible DNS record.

Some applications exist only as virtual hosts configured on the web server and are accessible only when the correct **Host** header is supplied.

To identify these hidden virtual hosts, I performed Host Header fuzzing using FFUF.

### Initial Enumeration

```bash
ffuf \
-w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt \
-H "Host: FUZZ.<target-domain>" \
-u http://<target-ip>
```

After reviewing the results, I filtered the common response size to remove false positives.

### Filtered Enumeration

```bash
ffuf \
-w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt \
-H "Host: FUZZ.<target-domain>" \
-u http://<target-ip> \
-fs <response-size>
```

This technique successfully identified additional virtual hosts that were not publicly discoverable through DNS enumeration alone.

---

# Key Findings

Throughout the assessment, I successfully identified multiple subdomains using different reconnaissance techniques, including:

- Subdomains exposed through Certificate Transparency logs
- Search engine indexed subdomains
- DNS records identified through brute-force enumeration
- Additional subdomains discovered through Sublist3r
- Hidden virtual hosts identified using Host Header fuzzing

These findings demonstrated how combining passive and active reconnaissance techniques significantly expands the visible attack surface of a target organization.

---

# Security Recommendations

Based on the assessment, I recommend the following improvements:

- Regularly audit publicly exposed subdomains.
- Remove obsolete DNS records.
- Limit exposure of development and staging environments.
- Restrict access to administrative interfaces.
- Monitor Certificate Transparency logs for unauthorized certificates.
- Review search engine indexing policies.
- Disable unnecessary virtual hosts.
- Continuously monitor the organization's external attack surface.

---

# Lessons Learned

This project reinforced the importance of comprehensive subdomain enumeration during the reconnaissance phase of a penetration test.

No single technique is sufficient to discover every subdomain. By combining passive OSINT techniques with active enumeration methods, I was able to identify significantly more assets than any individual approach could reveal.

The assessment also demonstrated that hidden virtual hosts can remain completely undiscoverable through DNS enumeration alone, highlighting the importance of Host Header fuzzing during web application reconnaissance.
