# Content Discovery

## Project Overview

In this project, I explored various techniques used to discover hidden or unlinked content within a web application. Rather than relying solely on visible website functionality, I performed manual reconnaissance, leveraged open-source intelligence (OSINT), and used automated content discovery tools to identify sensitive resources that could increase the attack surface of a target application.

Throughout the assessment, I examined configuration files, HTTP headers, framework artifacts, archived content, public repositories, and automated directory enumeration to locate hidden files, administrative interfaces, and development resources.

---

## Objectives

- Understand the importance of content discovery during web application assessments.
- Perform manual discovery of hidden resources.
- Identify information leakage through common web files.
- Leverage OSINT techniques to gather intelligence about a target.
- Perform automated directory and file enumeration.
- Understand how exposed content can lead to further compromise.

---

## Skills Learned

- Web content enumeration
- Directory discovery
- Information gathering
- Robots.txt analysis
- Sitemap analysis
- HTTP header enumeration
- Framework fingerprinting
- OSINT techniques
- Favicon fingerprinting
- Automated web enumeration
- Wordlist-based discovery
- Web reconnaissance

---

## Tools & Technologies

- Gobuster
- FFUF
- DIRB
- cURL
- SecLists
- Browser Developer Tools
- MD5 Hashing
- Wappalyzer
- Wayback Machine
- GitHub
- Google Dorking

---

# Methodology

## 1. Robots.txt Analysis

I began the assessment by reviewing the website's **robots.txt** file.

Although intended to guide search engine crawlers, this file frequently exposes directories that administrators prefer not to have indexed.

### Command Used

```bash
curl http://<target>/robots.txt
```

During the assessment, I identified restricted paths that revealed internal website functionality and highlighted areas worthy of further investigation.

This reinforced the importance of reviewing publicly accessible configuration files during reconnaissance.

---

## 2. Favicon Fingerprinting

Next, I analyzed the website's favicon.

After downloading the favicon, I generated its MD5 hash and compared it against known favicon fingerprint databases.

### Command Used

```bash
curl http://<target>/images/favicon.ico | md5sum
```

The generated MD5 hash was then compared against known favicon fingerprint databases to identify the technology stack behind the application.

This process successfully identified the web framework being used by the application.

Framework fingerprinting is valuable because it allows attackers and security professionals to identify technologies that may have publicly documented vulnerabilities.

---

## 3. Sitemap Analysis

I reviewed the website's **sitemap.xml** file to identify pages intended for search engine indexing.

### Command Used

```bash
curl http://<target>/sitemap.xml
```

The sitemap contained additional application paths that were not immediately visible through normal website navigation.

This demonstrated how sitemap files can expose hidden application functionality, legacy pages, and administrative resources.

---

## 4. HTTP Header Enumeration

Using `curl` in verbose mode, I inspected the HTTP response headers returned by the web server.

### Command Used

```bash
curl -v http://<target>
```

The response disclosed valuable information including:

- Web server software
- Software versions
- Custom HTTP headers
- Response behavior

Custom headers also revealed information that was not visible within the website itself, highlighting another common source of information disclosure.

---

## 5. Framework Enumeration

After identifying the underlying web framework through favicon fingerprinting and page source analysis, I reviewed the framework's publicly available documentation.

The documentation revealed default administrative portal locations commonly deployed with the framework.

Testing these default paths against the target application successfully identified an exposed administrative interface.

This demonstrated how technology fingerprinting combined with publicly available documentation can significantly improve reconnaissance efforts.

---

# Open-Source Intelligence (OSINT)

## Google Dorking

I explored how advanced search operators can be used to discover publicly indexed content.

Useful search operators included:

- `site:`
- `inurl:`
- `intitle:`
- `filetype:`

These operators allow security professionals to discover exposed documents, administrative interfaces, backup files, and sensitive content indexed by search engines.

---

## Technology Fingerprinting

I reviewed the use of technology fingerprinting platforms capable of identifying:

- Web frameworks
- Content Management Systems (CMS)
- Programming languages
- JavaScript libraries
- Payment processors
- Analytics platforms

Understanding the underlying technology stack provides valuable intelligence during vulnerability assessments.

---

## Historical Website Analysis

I examined historical website archives to understand how previous versions of a website may expose retired pages or forgotten resources.

Archived snapshots can reveal:

- Deprecated endpoints
- Old administrative interfaces
- Legacy application functionality
- Historical documents

Historical reconnaissance often uncovers resources that remain active even after being removed from the current website.

---

## Public Code Repositories

I also explored the value of reviewing public source code repositories.

Public repositories may unintentionally expose:

- Source code
- Configuration files
- API keys
- Passwords
- Development documentation
- Internal project information

Reviewing publicly available repositories is an important OSINT technique during web application assessments.

---

## Cloud Storage Enumeration

I learned how publicly accessible cloud storage services can expose sensitive organizational data.

Improperly configured cloud storage buckets may allow unauthorized users to access:

- Website assets
- Backups
- Configuration files
- Source code
- Private documents

Cloud resource enumeration is now a common component of modern penetration testing engagements.

---


# Automated Content Discovery

## Wordlists

To automate enumeration, I used curated wordlists containing commonly used directory and file names.

Wordlists allow automated tools to efficiently test thousands of possible resources that may exist on a target web server.

### Wordlist Used

```text
/usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt
```

The SecLists project provides a comprehensive collection of security-focused wordlists commonly used for penetration testing, including lists for directory enumeration, file discovery, usernames, passwords, and fuzzing.

---

## FFUF

I used **FFUF (Fuzz Faster U Fool)** to perform high-speed directory and file brute-forcing against the target web application.

FFUF sent HTTP requests for every entry contained within the supplied wordlist and identified valid resources based on the application's response codes and content length.

### Command Used

```bash
ffuf -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt \
-u http://<target>/FUZZ
```

This scan successfully identified hidden directories that were not linked from the application's user interface.

---

## DIRB

I also performed directory enumeration using **DIRB** to validate previously discovered resources and identify additional hidden content.

DIRB recursively tested common directory and file names against the target application, helping uncover resources that were not publicly accessible through normal website navigation.

### Command Used

```bash
dirb http://<target>/ \
/usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt
```

This enumeration process revealed additional files and directories, including development resources exposed on the web server.

---

## Gobuster

Finally, I used **Gobuster** to perform another round of directory enumeration.

Gobuster provided an additional validation of the discovered content while identifying further hidden directories using the same curated wordlist.

### Command Used

```bash
gobuster dir \
-u http://<target>/ \
-w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt
```

Using multiple enumeration tools increased confidence in the assessment results by confirming the existence of exposed resources across different scanning methods.

---

# Key Findings

Throughout the assessment, I successfully identified multiple sources of hidden information, including:

- Restricted directories referenced in `robots.txt`
- Hidden pages listed in `sitemap.xml`
- Framework identification through favicon fingerprinting
- Information disclosure through HTTP response headers
- Default framework administrative interfaces
- Valuable intelligence gathered through OSINT techniques
- Public cloud storage naming conventions
- Hidden directories discovered through automated enumeration
- Development log files exposed on the web server

These findings demonstrated how seemingly minor information leaks can significantly expand the attack surface of a web application.

---

# Security Recommendations

Based on the assessment, I recommend the following improvements:

- Avoid exposing sensitive directories through `robots.txt`.
- Remove outdated or unnecessary entries from `sitemap.xml`.
- Disable directory listing where it is not required.
- Restrict access to development, backup, and log files.
- Limit information disclosed through HTTP response headers.
- Remove or secure default administrative portals.
- Keep frameworks and third-party software updated.
- Regularly audit publicly accessible cloud storage for excessive permissions.
- Monitor public repositories for accidental disclosure of sensitive information.
- Perform periodic content discovery assessments to identify exposed resources before attackers do.

---

# Lessons Learned

This project demonstrated that effective penetration testing begins with comprehensive reconnaissance.

Even without exploiting a vulnerability, content discovery can expose valuable intelligence that significantly increases an attacker's understanding of the target environment.

By combining manual inspection, OSINT techniques, and automated enumeration tools, I developed a structured methodology for uncovering hidden resources, administrative interfaces, development files, and information disclosures that are commonly overlooked during routine web browsing.

The project also reinforced the importance of validating findings using multiple techniques rather than relying on a single discovery method.

---
