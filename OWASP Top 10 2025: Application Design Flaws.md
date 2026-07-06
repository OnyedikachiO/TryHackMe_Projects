# OWASP Top 10 2025: Application Design Flaws

## Project Overview

This project focused on identifying and analyzing several categories from the **OWASP Top 10 2025** that emphasize weaknesses in application design, deployment, dependency management, and cryptographic implementation. Working within an authorized TryHackMe lab environment, I investigated vulnerabilities that stem from insecure configurations, outdated software components, cryptographic mistakes, and flawed application architecture.

The assessment covered the following categories:

- **AS02 – Security Misconfigurations**
- **AS03 – Software Supply Chain Failures**
- **AS04 – Cryptographic Failures**
- **AS06 – Insecure Design**

Throughout the lab, I analyzed vulnerable applications, identified security weaknesses, demonstrated exploitation techniques in a controlled environment, and documented appropriate mitigation strategies.

---

# Objectives

- Understand common application security design flaws.
- Identify security misconfigurations in web applications.
- Analyze risks associated with outdated software dependencies.
- Investigate cryptographic implementation weaknesses.
- Assess insecure application design and business logic flaws.
- Recommend defensive security best practices aligned with OWASP guidance.

---

# Lab Environment

| Component | Details |
|-----------|----------|
| Platform | TryHackMe |
| Environment | Authorized Virtual Lab |
| Category | Web Application Security |
| Focus | OWASP Top 10 2025 |
| Skills Practiced | API Testing, Security Analysis, Enumeration, Cryptographic Assessment, Dependency Analysis |

---

# AS02 – Security Misconfigurations

## Objective

Assess how improper system configurations expose sensitive application information and increase the attack surface.

---

## Vulnerability Overview

Security misconfigurations occur when applications, servers, APIs, or cloud services are deployed using unsafe default settings, unnecessary functionality, excessive permissions, or verbose error handling.

Unlike software bugs, these vulnerabilities originate from insecure deployment or operational practices.

---

## Practical Assessment

During the lab, I investigated the exposed User Management API.

By supplying invalid input to the API endpoint, verbose debugging information was returned to the client.

Example request:

```bash
curl "http://<TARGET_IP>:5002/api/user/123'"
```

The server responded with detailed debugging information including:

- Internal traceback
- Application source file paths
- Exception details
- Sensitive debug information
- Challenge flag

This demonstrated that production debugging features were improperly enabled.

---

## Evidence

### Endpoint Tested

```http
GET /api/user/123'
```

Observed information leakage included:

- Python traceback
- Internal application paths
- Debug objects
- Sensitive application data

---

## Security Impact

A successful attacker could leverage exposed debug information to:

- Understand application internals
- Identify vulnerable source code
- Discover hidden endpoints
- Gather credentials or secrets
- Build targeted attacks

---

## Mitigation Strategies

Recommended controls include:

- Disable debugging in production.
- Remove verbose error messages.
- Harden default application configurations.
- Restrict administrative interfaces.
- Apply least privilege permissions.
- Regularly audit cloud resources and exposed services.
- Automate configuration reviews within CI/CD pipelines.

---

# AS03 – Software Supply Chain Failures

## Objective

Examine risks introduced through outdated or untrusted third-party software components.

---

## Vulnerability Overview

Modern applications rely heavily on external libraries, frameworks, APIs, containers, and AI models.

When these dependencies become outdated or compromised, attackers may exploit them without attacking the application's own source code.

---

## Practical Assessment

The lab application imported an outdated utility library that exposed an insecure debugging function.

The vulnerable endpoint accepted specially crafted JSON input that triggered the legacy debug routine.

Example request:

```http
POST /api/process
Content-Type: application/json

{
    "data":"debug",
    "debug":"true"
}
```

The application returned internal secrets including:

- Administrative token
- Internal secret key
- Software version
- Challenge flag

---

## Evidence

Observed sensitive information included:

```text
admin_token
internal_secret
version
flag
```

The endpoint should never expose internal debugging functionality in production.

---

## Security Impact

Supply chain weaknesses may allow attackers to:

- Execute unintended functionality
- Access internal secrets
- Abuse outdated libraries
- Compromise application integrity
- Escalate privileges

---

## Mitigation Strategies

Recommended controls include:

- Verify third-party libraries before deployment.
- Continuously monitor dependencies.
- Patch outdated packages promptly.
- Secure build pipelines.
- Verify software provenance.
- Audit AI models and external components.
- Implement Software Bill of Materials (SBOM) tracking.

---

# AS04 – Cryptographic Failures

## Objective

Identify weaknesses caused by poor cryptographic implementation and insecure key management.

---

## Vulnerability Overview

Cryptographic failures occur when applications misuse encryption or expose sensitive cryptographic material.

Examples include:

- Hardcoded encryption keys
- Weak encryption algorithms
- Poor secret management
- Missing encryption
- Insecure certificates

---

## Practical Assessment

The application attempted to protect sensitive files using client-side encryption.

During assessment, I reviewed the application's source code.

The JavaScript responsible for decryption was publicly accessible.

```
/static/js/decrypt.js
```

Within the script, the application exposed the decryption key required to unlock the protected file.

Because the key was delivered directly to every client, the encryption mechanism provided no real security.

---

## Security Impact

Exposed cryptographic material can allow attackers to:

- Decrypt protected data
- Recover sensitive information
- Steal credentials
- Compromise confidential files
- Bypass intended security controls

---

## Mitigation Strategies

Recommended controls include:

- Never hardcode encryption keys.
- Store secrets within dedicated key management systems.
- Use modern encryption algorithms.
- Rotate encryption keys regularly.
- Protect sensitive secrets using secure vaults.
- Enforce TLS across all communications.
- Separate client-side functionality from secret management.

---

# AS06 – Insecure Design

## Objective

Analyze security weaknesses introduced by flawed application architecture and incorrect design assumptions.

---

## Vulnerability Overview

Unlike implementation bugs, insecure design originates during planning and system architecture.

The lab demonstrated an application designed under the assumption that only mobile devices would access its backend APIs.

Because authentication controls relied on this assumption, undocumented endpoints remained accessible directly through the browser.

---

## Practical Assessment

I performed endpoint enumeration using **ffuf** to identify hidden API routes.

Example enumeration:

```bash
ffuf -u http://<TARGET_IP>:5005/FUZZ -w <wordlist>
```

Enumeration identified undocumented API endpoints.

After reviewing page source and exposed API responses, multiple user message endpoints were discovered, including administrative resources.

Examples included:

```text
/api/messages/user1
```

```text
/api/messages/user2
```

```text
/api/messages/admin
```

These endpoints could be accessed directly without the intended client application, exposing sensitive information due to flawed trust assumptions.

---

## Security Impact

Insecure application design may result in:

- Unauthorized API access
- Information disclosure
- Broken trust boundaries
- Business logic abuse
- Privilege escalation
- Increased attack surface

---

## Mitigation Strategies

Recommended controls include:

- Perform threat modeling during design.
- Never assume trusted clients.
- Enforce authentication on every endpoint.
- Validate authorization for every request.
- Apply least privilege principles.
- Separate user and administrative functionality.
- Conduct abuse-case testing.
- Treat AI components and automation as untrusted until verified.

---

# Skills Demonstrated

Throughout this project, I demonstrated practical experience with:

- API Security Assessment
- Security Misconfiguration Identification
- Error Message Analysis
- Information Disclosure Testing
- Dependency Risk Assessment
- Software Supply Chain Security
- Cryptographic Security Review
- Client-side Source Code Analysis
- Endpoint Enumeration
- Web Application Reconnaissance
- Business Logic Analysis
- OWASP Top 10 Security Principles

---

# Key Takeaways

This project reinforced that many critical application vulnerabilities originate long before attackers exploit them.

Security misconfigurations can unintentionally expose sensitive application internals, outdated software dependencies may introduce inherited vulnerabilities, poor cryptographic implementations can completely undermine data protection, and insecure architectural assumptions can expose hidden functionality without exploiting traditional software bugs.

Understanding these design-level weaknesses improves the ability to identify systemic security risks and recommend effective defensive controls throughout the software development lifecycle.

---

# Disclaimer

This project was completed within an **authorized TryHackMe training environment** for educational and defensive cybersecurity purposes. All testing, enumeration, and analysis were performed exclusively against intentionally vulnerable laboratory systems. No unauthorized systems or third-party infrastructure were targeted.
