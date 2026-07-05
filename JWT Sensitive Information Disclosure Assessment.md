# JWT Sensitive Information Disclosure Assessment

## Overview

This project focused on assessing a common security weakness in token-based authentication systems: the exposure of sensitive information within JSON Web Tokens (JWTs). The objective was to analyze how improper claim handling can result in credential disclosure and information leakage, allowing attackers to recover sensitive data without compromising the underlying cryptographic protections of the token.

The assessment demonstrated how sensitive application data embedded within JWT payloads can be recovered through simple token decoding, highlighting the importance of understanding that JWT payloads are encoded, not encrypted.

---

## Objectives

* Analyze JWT-based authentication mechanisms.
* Examine the structure and contents of issued JWTs.
* Identify sensitive information disclosure vulnerabilities within JWT claims.
* Evaluate the security impact of exposing confidential information in tokens.
* Document secure implementation practices for JWT-based authentication.

---

## Environment

| Component             | Details                          |
| --------------------- | -------------------------------- |
| Platform              | Controlled Training Laboratory   |
| Application Type      | REST API                         |
| Framework             | Python Flask                     |
| Authentication Method | JWT Authentication               |
| Signing Algorithm     | HS256                            |
| Testing Tools         | cURL, JWT Decoder, Browser Tools |
| Operating Environment | Linux Attack Host                |

---

## Methodology

The assessment followed a structured API security testing methodology focusing on authentication token analysis.

### Phase 1: Authentication Request

An authentication request was submitted to the API endpoint using valid user credentials.

```bash
curl -H 'Content-Type: application/json' \
-X POST \
-d '{ "username":"user", "password":"password1" }' \
http://target/api/v1.0/example1
```

The application returned a signed JSON Web Token used for subsequent authentication.

---

### Phase 2: JWT Enumeration

The returned token was separated into its three components:

* Header
* Payload
* Signature

The payload portion was Base64Url-decoded to inspect the claims stored within the token.

Example JWT structure:

```text
Header.Payload.Signature
```

---

### Phase 3: Sensitive Data Analysis

Upon decoding the JWT payload, several security concerns were identified.

The token contained:

* Username information
* Authentication-related data
* Authorization claims
* Sensitive application data

The assessment confirmed that confidential information had been embedded directly within the JWT payload and exposed to the client.

---

## Technical Analysis

The vulnerable implementation stored sensitive values directly inside the token payload before signing.

Example vulnerable implementation:

```python
payload = {
    "username": username,
    "password": password,
    "admin": 0,
    "sensitive_data": "[redacted]"
}

access_token = jwt.encode(payload, secret, algorithm="HS256")
```

Although the token was cryptographically signed, the payload remained fully readable by any authenticated user because JWT encoding does not provide confidentiality.

---

## Findings

### Finding 1: Sensitive Information Disclosure Through JWT Claims

**Severity:** High

The application exposed sensitive data by embedding confidential information directly within JWT payload claims.

#### Evidence

* JWT payloads were fully readable after Base64Url decoding.
* Sensitive application data was recoverable without bypassing authentication.
* No additional decryption or exploitation techniques were required.

#### Impact

An attacker with access to a valid JWT could:

* Recover confidential application information.
* Enumerate sensitive user attributes.
* Gather intelligence for privilege escalation attempts.
* Expose internal application logic and implementation details.

---

### Finding 2: Misunderstanding of JWT Security Properties

**Severity:** Medium

The application implementation incorrectly assumed that signed JWTs provide confidentiality.

#### Impact

This misunderstanding can lead developers to store:

* Credentials
* Password hashes
* Internal network information
* API secrets
* Authorization metadata
* Sensitive business data

inside client-accessible tokens.

---

## Security Impact

The exposure of sensitive information within JWT payloads can result in:

* Information disclosure
* Credential exposure
* User enumeration
* Privilege escalation opportunities
* Increased attack surface
* Intelligence gathering for further attacks

Even when cryptographic signatures remain secure, exposed claims can significantly weaken the application's overall security posture.

---

## Recommendations

The following controls should be implemented:

* Never store passwords or password hashes within JWT claims.
* Avoid placing sensitive business data inside tokens.
* Store confidential information server-side.
* Use JWTs only for identity and authorization claims.
* Retrieve sensitive data through server-side lookups after successful token validation.
* Implement strict token expiration policies.
* Perform regular security reviews of authentication implementations.

Example secure implementation:

```python
payload = jwt.decode(token, secret, algorithms=["HS256"])

username = payload["username"]
user_data = database_lookup(username)
```

---

## Lessons Learned

This assessment reinforced several important principles regarding token security:

* JWTs are encoded, not encrypted.
* Cryptographic signatures provide integrity, not confidentiality.
* Sensitive information should remain server-side whenever possible.
* Authentication tokens should contain only the minimum information necessary.
* Misunderstanding JWT security properties can lead to severe information disclosure vulnerabilities.

---

## Disclaimer

This project was conducted in an authorized laboratory and training environment for educational and defensive security purposes only. All testing activities were performed against intentionally vulnerable systems or systems where explicit authorization had been granted.
