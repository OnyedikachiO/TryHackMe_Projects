# Web Application Authentication and Access Control Testing with Burp Suite Intruder

## Overview

This project demonstrates practical web application security testing using Burp Suite Intruder against multiple authentication and authorization scenarios. The assessment covered credential stuffing attacks, insecure direct object reference (IDOR) discovery, and bypassing CSRF-protected authentication mechanisms through Burp Suite macros and session handling rules.

The exercise highlights common web application security weaknesses, including password reuse, insecure object references, and improper session management.

---

## Objectives

The goals of this assessment were to:

* Perform credential stuffing attacks
* Identify weak authentication controls
* Enumerate accessible resources
* Discover IDOR vulnerabilities
* Automate CSRF token handling
* Automate session management
* Bypass anti-automation protections
* Validate authentication weaknesses

---

## Attack Path

```text
Application Enumeration
        ↓
Login Form Analysis
        ↓
Credential Stuffing Attack
        ↓
Authenticated Access
        ↓
Endpoint Enumeration
        ↓
IDOR Discovery
        ↓
CSRF Token Analysis
        ↓
Burp Macro Creation
        ↓
Session Handling Automation
        ↓
Authenticated Credential Attack
```

---

## MITRE ATT&CK Mapping

| Tactic            | Technique                                  |
| ----------------- | ------------------------------------------ |
| Reconnaissance    | T1595 – Active Scanning                    |
| Credential Access | T1110.004 – Credential Stuffing            |
| Discovery         | T1087 – Account Discovery                  |
| Collection        | T1213 – Data from Information Repositories |
| Discovery         | T1083 – File and Directory Discovery       |
| Initial Access    | T1190 – Exploit Public-Facing Application  |

---

# Challenge 1: Credential Stuffing Attack

## Reconnaissance

The support portal login page was analyzed.

### Endpoint

```text
/support/login
```

The login form contained:

```html
<input name="username">
<input name="password">
```

No anti-automation protections were present:

* No CAPTCHA
* No account lockout
* No rate limiting
* No MFA
* No CSRF protection

---

## Attack Strategy

Rather than performing a brute-force attack, a credential stuffing attack was selected due to the availability of leaked employee credentials.

### Leaked Data

```text
usernames.txt
passwords.txt
emails.txt
combined.txt
```

---

## Burp Intruder Configuration

### Attack Type

```text
Pitchfork
```

### Positions

```text
username
password
```

### Payload Sets

| Payload | Wordlist      |
| ------- | ------------- |
| Set 1   | usernames.txt |
| Set 2   | passwords.txt |

---

## Attack Execution

Burp Intruder submitted credential pairs sequentially.

Since all responses returned:

```text
HTTP 302
```

response codes were not useful.

Instead, successful authentication attempts were identified through:

```text
Response Length Analysis
```

A significantly shorter response indicated successful authentication.

---

## Findings

The credential stuffing attack successfully identified valid credentials.

### Vulnerability

```text
Password Reuse
```

### Security Impact

* Unauthorized account access
* Account compromise
* Privilege abuse
* Data exposure

---

# Challenge 2: Insecure Direct Object Reference (IDOR)

## Initial Analysis

After authentication, ticket URLs followed the pattern:

```text
/support/ticket/NUMBER
```

Example:

```text
/support/ticket/12
```

The predictable numeric identifiers suggested possible IDOR exposure.

---

## Attack Methodology

A list of ticket identifiers was generated.

### Script

```python
for i in range(101):
    print(i)
```

### Execution

```bash
python3 numbers.py > numbers.txt
```

---

## Burp Intruder Configuration

### Attack Type

```text
Sniper
```

### Payload

```text
numbers.txt
```

### Target Position

```text
/support/ticket/§NUMBER§
```

---

## Results

Responses returning:

```text
HTTP 200
```

were analyzed manually.

Multiple tickets belonging to other users were accessible.

---

## Vulnerability

```text
Insecure Direct Object Reference (IDOR)
```

### Impact

* Unauthorized ticket access
* Sensitive information disclosure
* Broken access control
* Horizontal privilege escalation

---

# Extra Mile Challenge: CSRF-Protected Authentication

## Initial Analysis

The administrative login portal implemented additional controls.

### Response Example

```http
Set-Cookie: session=...
<input type="hidden"
name="loginToken"
value="...">
```

Observations:

* Session cookies changed on every request
* CSRF tokens changed on every request

Traditional credential stuffing would fail.

---

# Burp Macro Automation

## Objective

Automate retrieval of:

* Session cookies
* CSRF tokens

before every authentication attempt.

---

## Macro Configuration

The macro performed:

```http
GET /admin/login/
```

before each Intruder request.

The macro extracted:

```text
session
loginToken
```

---

## Session Handling Rule

A Burp Session Handling Rule was configured.

### Tool Scope

```text
Intruder
```

### Actions

```text
Run Macro
```

### Update Parameters

```text
loginToken
```

### Update Cookies

```text
session
```

---

## Burp Intruder Configuration

### Attack Type

```text
Pitchfork
```

### Payload Positions

```text
username
password
```

The macro automatically populated:

```text
session cookie
CSRF token
```

for every request.

---

## Successful Authentication Detection

Successful responses were identified through:

* HTTP 302 redirects
* Response length analysis

The attack successfully recovered valid administrator credentials.

---

# Vulnerabilities Identified

| Vulnerability                | Severity |
| ---------------------------- | -------- |
| Credential Stuffing          | High     |
| Password Reuse               | High     |
| Missing Rate Limiting        | High     |
| Missing MFA                  | Medium   |
| IDOR                         | Critical |
| Weak Session Management      | High     |
| CSRF Token Automation Bypass | Medium   |

---

# Detection Opportunities

Security teams could detect this activity through:

* Authentication anomaly monitoring
* Credential stuffing detection
* Failed login thresholds
* Session creation analysis
* Sequential resource access detection
* User behavior analytics
* Burp Intruder signature detection

---

# Defensive Mitigations

Recommended mitigations include:

* Implement MFA
* Enforce password rotation
* Deploy account lockout controls
* Introduce rate limiting
* Use CAPTCHA
* Validate object ownership server-side
* Replace sequential identifiers with UUIDs
* Monitor authentication anomalies
* Implement credential breach monitoring

---

# Tools Used

* Burp Suite Community
* Burp Intruder
* Burp Macros
* Session Handling Rules
* Python 3
* Firefox
* HTTP Proxy Analysis

---

# Skills Demonstrated

* Authentication Testing
* Credential Stuffing
* Burp Suite Intruder
* Session Management Testing
* CSRF Analysis
* Burp Macros
* Session Handling Rules
* Access Control Testing
* IDOR Discovery
* Forced Browsing
* HTTP Analysis
* Web Application Penetration Testing

---

# Key Lessons Learned

* Password reuse remains a major attack vector.
* Response length analysis can reveal successful authentication.
* Sequential identifiers frequently introduce IDOR vulnerabilities.
* CSRF protections alone do not prevent credential attacks.
* Burp Macros enable sophisticated authenticated attack automation.
* Broken access control remains one of the most critical web application vulnerabilities.

---

## Disclaimer

This assessment was conducted in an authorized laboratory environment for educational and security research purposes only.
