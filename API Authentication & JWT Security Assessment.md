# API Authentication & JWT Security Assessment

## Overview

This project focused on assessing the security of token-based session management mechanisms used by modern APIs. The objective was to analyze how JSON Web Tokens (JWTs) are implemented for authentication and authorization, identify common weaknesses in token handling, and evaluate potential privilege escalation scenarios within a controlled lab environment.

The assessment explored API authentication workflows, token structures, signing algorithms, and authorization controls to understand how improper JWT implementations can lead to unauthorized access and privilege escalation.

---

## Objectives

* Analyze token-based session management in API environments.
* Understand the structure and security properties of JSON Web Tokens (JWTs).
* Assess authentication and authorization mechanisms within a REST API.
* Identify weaknesses in JWT implementation and validation.
* Evaluate privilege escalation opportunities through token manipulation.
* Document security risks and recommend remediation strategies.

---

## Environment

| Component             | Details                                     |
| --------------------- | ------------------------------------------- |
| Platform              | Controlled Training Laboratory              |
| API Framework         | Python Flask                                |
| Authentication Method | JWT-Based Authentication                    |
| Testing Tools         | cURL, Browser Developer Tools, JWT Decoders |
| Session Management    | Token-Based                                 |
| API Style             | REST API                                    |

---

## Methodology

The assessment followed a structured API security testing methodology:

### Phase 1: API Enumeration

The exposed API endpoints were identified and analyzed to understand the authentication flow and available functionality.

Key observations included:

* Authentication was performed using HTTP POST requests.
* Session management relied on JWTs rather than traditional cookies.
* User information retrieval occurred through authenticated GET requests.
* Authorization decisions appeared to depend on claims embedded within JWT payloads.

Example authentication workflow:

```bash
POST /api/v1.0/exampleX
{
    "username":"user",
    "password":"passwordX"
}
```

Successful authentication resulted in the issuance of a JWT token for subsequent requests.

---

### Phase 2: JWT Analysis

The received JWT tokens were decoded and analyzed to identify their internal structure.

JWTs were observed to contain three primary components:

| Component | Purpose                                  |
| --------- | ---------------------------------------- |
| Header    | Defines token type and signing algorithm |
| Payload   | Stores claims and authorization data     |
| Signature | Validates token integrity                |

Example JWT structure:

```
Header.Payload.Signature
```

The payload contained authorization-related claims that controlled user privileges within the application.

---

### Phase 3: Session Management Assessment

The token lifecycle was analyzed to understand how authentication state was maintained.

Observations included:

* Authentication state was managed entirely through bearer tokens.
* Tokens were transmitted through Authorization headers.
* Session information was stored client-side.
* Server-side validation depended on JWT signature verification.

Example request format:

```http
Authorization: Bearer eyJhbGciOi...
```

This architecture reduced dependence on traditional cookie-based session management while introducing JWT-specific attack surfaces.

---

### Phase 4: JWT Security Evaluation

The assessment focused on evaluating the security of common JWT signing mechanisms.

#### None Algorithm

The "none" algorithm removes signature verification entirely, allowing attackers to modify claims without requiring cryptographic validation.

Potential risks:

* Privilege escalation
* Authentication bypass
* Arbitrary claim modification

---

#### Symmetric Algorithms (HS256)

Symmetric algorithms rely on a shared secret key for signing and verification.

Assessment considerations included:

* Secret key strength
* Key exposure risks
* Signature validation implementation
* Key reuse across services

---

#### Asymmetric Algorithms (RS256)

Asymmetric algorithms utilize separate private and public keys.

Security evaluation focused on:

* Public/private key separation
* Signature validation logic
* Key management practices
* Algorithm enforcement

---

## Findings

### Finding 1: Client-Controlled Session Management

**Severity:** Medium

The application relied entirely on client-side bearer tokens for session management. Improper validation of token claims could potentially lead to unauthorized access.

**Impact:**

* Increased attack surface
* Risk of privilege escalation
* Reduced server-side session visibility

---

### Finding 2: Authorization Based on JWT Claims

**Severity:** High

Authorization decisions depended heavily on claims embedded within JWT payloads. Failure to validate token signatures correctly could allow attackers to modify privilege levels.

**Impact:**

* Unauthorized administrative access
* Privilege escalation
* Exposure of sensitive information

---

### Finding 3: JWT Algorithm Misconfiguration Risk

**Severity:** Critical

Improper handling of JWT signing algorithms, particularly acceptance of weak or unsigned tokens, could permit complete authentication bypass.

**Impact:**

* Authentication bypass
* Token forgery
* Full application compromise

---

## Security Impact

Improper implementation of JWT authentication can introduce significant risks, including:

* Authentication bypass
* Privilege escalation
* Unauthorized access to administrative functionality
* Exposure of sensitive user information
* Session hijacking opportunities
* Trust boundary violations between services

---

## Recommendations

The following security controls are recommended when implementing JWT-based authentication:

* Enforce strong signing algorithms such as RS256 or ES256.
* Explicitly reject unsigned ("none") JWTs.
* Validate JWT signatures on every request.
* Implement short token expiration periods.
* Rotate signing keys regularly.
* Store sensitive authorization logic server-side when possible.
* Validate all claims before authorizing access.
* Monitor for token abuse and anomalous authentication behavior.
* Implement token revocation mechanisms.
* Follow established JWT security best practices.

---

## Lessons Learned

This assessment demonstrated that while JWTs provide a flexible and scalable authentication mechanism for APIs, their security depends entirely on proper implementation.

Key takeaways included:

* JWT payload data should never be trusted without signature verification.
* Authorization logic embedded solely in client-controlled tokens increases risk.
* Token signing algorithms must be carefully selected and enforced.
* API security assessments require evaluating both authentication and authorization workflows.
* Session management vulnerabilities extend beyond traditional cookie-based applications.

---

## Disclaimer

This project was performed in an authorized training and laboratory environment for educational and defensive security purposes only. All testing activities were conducted against intentionally vulnerable systems or systems where explicit permission had been granted.
