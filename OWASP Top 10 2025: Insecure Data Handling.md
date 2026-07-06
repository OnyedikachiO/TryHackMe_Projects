# OWASP Top 10 2025: Insecure Data Handling

## Project Overview

This project explored multiple **OWASP Top 10 2025** vulnerabilities related to insecure handling of sensitive data and unsafe processing of user-controlled input. Working within an authorized TryHackMe laboratory environment, I analyzed weaknesses in cryptographic implementation, injection vulnerabilities, and software/data integrity controls.

The assessment covered the following OWASP categories:

- **A04 – Cryptographic Failures**
- **A05 – Injection**
- **A08 – Software or Data Integrity Failures**

Throughout the project, I identified vulnerable application behavior, safely demonstrated exploitation techniques within the lab environment, and documented security recommendations aligned with industry best practices.

---

# Objectives

- Analyze weak cryptographic implementations.
- Assess server-side template injection vulnerabilities.
- Understand insecure deserialization attacks.
- Demonstrate secure handling of user-controlled input.
- Evaluate software integrity weaknesses.
- Recommend defensive controls following OWASP guidance.

---

# Lab Environment

| Component | Details |
|-----------|----------|
| Platform | TryHackMe |
| Environment | Authorized Virtual Lab |
| Category | Web Application Security |
| Focus | OWASP Top 10 2025 |
| Skills Practiced | Cryptographic Analysis, SSTI Testing, Secure Coding Review, Deserialization Analysis |

---

# A04 – Cryptographic Failures

## Objective

Assess how weak cryptographic implementations expose confidential information.

---

## Vulnerability Overview

Cryptographic failures occur when applications fail to properly protect sensitive information through secure encryption, hashing, or secret management.

Examples include:

- Weak encryption algorithms
- Predictable encryption keys
- Hardcoded secrets
- Missing encryption
- Poor key management
- Weak password hashing

Rather than breaking encryption itself, attackers frequently exploit implementation mistakes that make encrypted information recoverable.

---

## Practical Assessment

The target application stored user notes using a shared encryption key.

During analysis, I determined that the first three characters of the encryption key were already known:

```text
KEY
```

The remaining character could be any:

- Uppercase letter
- Lowercase letter
- Digit

This resulted in a search space of:

```text
62 possible keys
```

To efficiently identify the correct key, I first tested numeric values.

The application successfully decrypted the stored notes using:

```text
KEY1
```

---

## Evidence

After identifying the correct key, I successfully decrypted multiple encrypted notes.

Recovered information included:

- Internal meeting schedule
- Password reset URL
- Confidential note containing the challenge flag

The assessment demonstrated that predictable key generation rendered the encryption ineffective.

---

## Security Impact

Weak cryptography may allow attackers to:

- Recover encrypted information
- Access confidential documents
- Steal credentials
- Compromise sensitive communications
- Escalate attacks using exposed secrets

---

## Mitigation Strategies

Recommended controls include:

- Use modern encryption algorithms such as AES-GCM or ChaCha20-Poly1305.
- Store encryption keys within dedicated key management systems.
- Rotate cryptographic keys regularly.
- Never hardcode secrets in source code.
- Enforce TLS for all network communication.
- Perform cryptographic reviews during secure development.

---

# A05 – Injection

## Objective

Identify and exploit a Server-Side Template Injection (SSTI) vulnerability to demonstrate the risks of unsafe template rendering.

---

## Vulnerability Overview

Injection vulnerabilities occur when applications process untrusted user input without adequate validation or separation from executable logic.

Common examples include:

- SQL Injection
- Command Injection
- Server-Side Template Injection (SSTI)
- Prompt Injection

This assessment focused on SSTI within a vulnerable Flask application.

---

## Practical Assessment

The application rendered user input directly within a server-side template.

To confirm template evaluation, I submitted the following expression:

```jinja
{{ 7 * 7 }}
```

The server evaluated the expression and returned:

```text
49
```

This confirmed that arbitrary template expressions were being executed.

Next, I enumerated accessible template objects to better understand the execution context.

Example payloads included:

```jinja
{{ config.items() }}
```

```jinja
{{ request.__dict__ }}
```

After identifying accessible Flask globals, I leveraged the exposed application context to read a local file from the server.

Example payload:

```jinja
{{ request.application.__globals__.__builtins__.open('flag.txt').read() }}
```

The server returned the contents of the target file, demonstrating successful exploitation of the SSTI vulnerability.

---

## Security Impact

Server-Side Template Injection can allow attackers to:

- Execute arbitrary server-side expressions
- Read sensitive files
- Access application secrets
- Execute operating system commands
- Achieve remote code execution in severe cases

---

## Mitigation Strategies

Recommended controls include:

- Never render untrusted user input directly.
- Use template sandboxing where available.
- Validate and sanitize all user input.
- Separate executable template logic from user-controlled data.
- Restrict access to dangerous built-in objects.
- Apply least privilege throughout the application.

---

# A08 – Software or Data Integrity Failures

## Objective

Demonstrate how insecure deserialization can compromise application integrity through malicious serialized objects.

---

## Vulnerability Overview

Software or Data Integrity Failures occur when applications trust serialized objects, software updates, configuration files, or external components without verifying their integrity.

Insecure deserialization allows attackers to supply crafted objects that execute unintended code during deserialization.

---

## Practical Assessment

The target application accepted serialized Python pickle objects submitted by users.

To demonstrate the vulnerability, I generated a malicious pickle payload that executed code during deserialization.

The payload leveraged Python's `__reduce__()` method to invoke a file read operation on the server.

After generating the serialized object, I encoded it using Base64 before submitting it through the vulnerable web interface.

Once processed by the server, the application deserialized the object and executed the embedded operation, returning the contents of the protected file.

---

## Evidence

Attack workflow:

1. Created malicious Python pickle object.
2. Encoded the serialized payload using Base64.
3. Submitted the payload through the application.
4. Server deserialized the object.
5. Embedded code executed automatically.
6. Retrieved the contents of `flag.txt`.

---

## Security Impact

Successful exploitation may allow attackers to:

- Execute arbitrary code
- Read sensitive files
- Compromise application integrity
- Bypass authentication mechanisms
- Achieve full server compromise

---

## Mitigation Strategies

Recommended controls include:

- Never deserialize untrusted data.
- Replace unsafe serialization formats with secure alternatives.
- Verify the integrity of serialized objects.
- Digitally sign trusted data.
- Restrict modification of application artifacts.
- Secure CI/CD pipelines.
- Continuously monitor for integrity violations.

---

# Skills Demonstrated

Throughout this project, I demonstrated practical experience with:

- Cryptographic Security Assessment
- Encryption Key Analysis
- Secure Secret Management
- Server-Side Template Injection (SSTI)
- Flask Application Security Testing
- Template Context Enumeration
- Local File Disclosure
- Python Deserialization Analysis
- Malicious Pickle Payload Development
- Base64 Payload Encoding
- Secure Software Integrity Review
- OWASP Top 10 Security Assessment

---

# Key Takeaways

This project reinforced that protecting sensitive information requires far more than simply enabling encryption. Weak key management can completely undermine cryptographic controls, while unsafe handling of user input can allow attackers to execute server-side code or access confidential files. Likewise, blindly trusting serialized objects or external data introduces significant integrity risks that may lead to arbitrary code execution.

By understanding these vulnerabilities through hands-on assessment, I strengthened my ability to identify insecure implementations and recommend defensive controls aligned with modern secure software development practices.

---

# Disclaimer

This project was completed within an **authorized TryHackMe training environment** for educational and defensive cybersecurity purposes. All testing, payload generation, and exploitation techniques were performed exclusively against intentionally vulnerable laboratory systems. No unauthorized systems or third-party infrastructure were targeted.
