# OWASP Top 10 2025: IAAA Failures Analysis

> A hands-on security analysis of Identity, Authentication, Authorization, and Accountability (IAAA) failures by examining three critical OWASP Top 10 (2025) vulnerability categories: Broken Access Control (A01), Authentication Failures (A07), and Logging & Monitoring Failures (A09).

---

## Project Overview

In this project, I analyzed how weaknesses in the **Identity, Authentication, Authorization, and Accountability (IAAA)** security model contribute to three of the most critical vulnerabilities in the **OWASP Top 10 (2025)**. Working within a controlled lab environment, I explored how poor implementation of authorization, authentication, and logging mechanisms can expose web applications to unauthorized access, privilege escalation, account compromise, and undetected malicious activity.

Through practical demonstrations and vulnerability analysis, I gained a deeper understanding of how these security controls should be implemented and identified industry best practices for mitigating each type of weakness.

---

## Objectives

- Understand the four components of the IAAA security model.
- Analyze how Broken Access Control affects authorization.
- Examine common authentication weaknesses and exploitation techniques.
- Understand the role of logging and accountability in application security.
- Identify secure implementation practices for mitigating OWASP Top 10 risks.

---

# Lab Environment

| Component | Details |
|-----------|---------|
| Platform | TryHackMe |
| Operating System | Kali Linux |
| Browser | Firefox / Chrome |
| Target | Vulnerable Web Applications |
| Category | Web Application Security |

---

# Understanding the IAAA Model

The IAAA model provides a structured approach for securing modern web applications by ensuring that users are properly identified, authenticated, authorized, and monitored.

| Component | Description |
|-----------|-------------|
| **Identity** | Represents a unique user or service account. |
| **Authentication** | Verifies that the user is who they claim to be. |
| **Authorization** | Determines what actions the authenticated user is permitted to perform. |
| **Accountability** | Records user activities for auditing, monitoring, and incident response. |

During this project, I observed how failures in **Authorization**, **Authentication**, and **Accountability** directly correspond to major OWASP Top 10 vulnerabilities.

---

# Methodology

## A01 – Broken Access Control

### Scenario

I examined a vulnerable web application that retrieved user information based on a numeric identifier supplied in the URL.

Initial request:

```text
?id=7
```

Modified request:

```text
?id=6
```

After changing only the object identifier, the application displayed another user's information without verifying whether I was authorized to access that resource.

---

### Findings

The application trusted client-supplied identifiers without enforcing server-side authorization checks.

This demonstrated an **Insecure Direct Object Reference (IDOR)** vulnerability, where changing predictable identifiers allowed unauthorized access to sensitive resources.

---

### Security Impact

Broken Access Control may lead to:

- Unauthorized disclosure of sensitive information
- Modification of another user's data
- Privilege escalation
- Full compromise of application resources

---

### Mitigation

Recommended security controls include:

- Enforce server-side authorization on every request.
- Verify resource ownership before returning data.
- Replace predictable identifiers with indirect references or secure tokens.
- Apply the Principle of Least Privilege.
- Log and monitor unauthorized access attempts.

---

# A07 – Authentication Failures

### Scenario

I analyzed authentication weaknesses involving improper username validation and account verification logic.

One demonstrated issue involved registering a username using different capitalization.

Example:

```text
admin
```

versus

```text
aDmiN
```

Improper username normalization could allow attackers to bypass account restrictions or impersonate privileged users.

Additional authentication weaknesses examined included:

- Username enumeration
- Weak passwords
- Missing rate limiting
- Session management flaws
- Authentication logic vulnerabilities

---

### Findings

The authentication implementation failed to consistently normalize and validate user identities.

Poor authentication controls increase the likelihood of unauthorized account access through brute force attacks, credential stuffing, session hijacking, or application logic flaws.

---

### Security Impact

Authentication failures may allow attackers to:

- Compromise user accounts
- Escalate privileges
- Hijack active sessions
- Abuse weak password policies
- Gain unauthorized administrative access

---

### Mitigation

Recommended improvements include:

- Normalize usernames before comparison.
- Enforce strong password policies.
- Implement account lockout and rate limiting.
- Enable Multi-Factor Authentication (MFA).
- Secure session cookies.
- Regenerate session identifiers after successful authentication.

---

# A09 – Logging & Monitoring Failures

### Scenario

I reviewed how inadequate logging and monitoring reduce an organization's ability to detect, investigate, and respond to attacks.

Common issues identified included:

- Missing authentication logs
- Lack of alerts for brute-force attacks
- Missing privilege escalation logs
- Insufficient log retention
- Logs stored in locations that attackers could modify

---

### Findings

Without effective logging, malicious activities can remain undetected, making incident response and forensic investigations significantly more difficult.

Organizations may be unable to determine:

- Who performed an action
- When it occurred
- Which resources were affected
- Whether additional systems were compromised

---

### Security Impact

Logging failures reduce an organization's ability to:

- Detect attacks
- Investigate incidents
- Perform forensic analysis
- Meet compliance requirements
- Maintain accountability

---

### Mitigation

Recommended controls include:

- Log authentication events.
- Record authorization failures.
- Monitor privilege changes.
- Generate alerts for suspicious activity.
- Store logs centrally.
- Protect log integrity.
- Maintain appropriate log retention periods.

---

# Key Findings

Throughout this project, I observed that:

- Authorization must always be enforced on the server.
- Authentication controls are only effective when user identities are consistently validated.
- Accountability depends on comprehensive logging and monitoring.
- Weaknesses in any component of the IAAA model significantly increase application risk.
- Defense-in-depth requires Identity, Authentication, Authorization, and Accountability to work together.

---

# Skills Demonstrated

- Web Application Security
- OWASP Top 10 Analysis
- Access Control Assessment
- Authentication Security Review
- IDOR Identification
- Secure Authentication Practices
- Security Logging Analysis
- Risk Assessment
- Vulnerability Analysis
- Security Best Practices

---

# Tools Used

- Kali Linux
- Firefox / Google Chrome
- TryHackMe
- Web Application Testing Environment

---

# Evidence

The following activities were successfully completed during the lab:

- Demonstrated an IDOR vulnerability by modifying object identifiers.
- Evaluated authentication weaknesses caused by improper username validation.
- Reviewed the impact of insufficient security logging and monitoring.
- Analyzed how failures in Authorization, Authentication, and Accountability affect overall application security.

---

# Security Recommendations

- Perform authorization checks for every server request.
- Validate resource ownership before granting access.
- Use indirect object references or secure identifiers.
- Normalize usernames during authentication.
- Implement strong password policies and Multi-Factor Authentication.
- Apply account lockout and rate limiting.
- Secure session management using HttpOnly and Secure cookies.
- Enable centralized, tamper-resistant logging.
- Monitor authentication anomalies and privilege changes.
- Regularly perform access control testing and security reviews.

---

# Lessons Learned

This project reinforced how closely the IAAA model aligns with modern web application security. I observed that strong authentication alone is insufficient if authorization controls are weak, and that even well-secured applications become difficult to defend without proper logging and monitoring.

Understanding how these vulnerabilities interact provided valuable insight into secure application design and emphasized the importance of implementing layered security controls throughout the software development lifecycle.

---

# Conclusion

By analyzing **Broken Access Control (A01)**, **Authentication Failures (A07)**, and **Logging & Monitoring Failures (A09)**, I gained practical experience identifying common implementation flaws that affect modern web applications.

The project demonstrated that effective application security requires more than simply authenticating users—it also depends on enforcing authorization decisions, maintaining comprehensive audit logs, and ensuring accountability throughout the entire user lifecycle. Applying these principles significantly strengthens an application's resilience against common attack techniques.

---

# Disclaimer

> **This project was completed within an authorized TryHackMe training environment for educational purposes only. All activities were performed against intentionally vulnerable systems in a controlled lab environment. No testing was conducted against production systems or unauthorized targets.**


