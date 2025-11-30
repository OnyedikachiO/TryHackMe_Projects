# Application Design Flaws

Examine how A02, A03, A06, and A10 relate to **design flaws** in the application, and understand how these weaknesses can lead to security vulnerabilities if not properly addressed.

## AS02: Security Misconfigurations

**What It Is**

Security misconfigurations occur when systems, servers, or applications are deployed with unsafe defaults, incomplete settings, or exposed services. These are not code bugs but mistakes in how the environment, software, or network is set up, creating easy entry points for attackers.

**Why It Matters**

Even minor misconfigurations can expose sensitive data, enable privilege escalation, or give attackers a foothold. Modern applications use complex stacks, cloud services, and third-party APIs. A single exposed admin panel, open storage bucket, or misconfigured permission can compromise the entire system.

**Example**

In 2017, Uber exposed a backup AWS S3 bucket containing sensitive user data, including driver and rider information, because the bucket was publicly accessible. Attackers could download data directly without credentials, showing how a deployment mistake can lead to a major breach.

**Common Patterns**

- Default credentials or weak passwords left unchanged
- Unnecessary services or endpoints exposed to the internet
- Misconfigured cloud storage or permissions (S3, Azure Blob, GCP buckets)
- Unrestricted API access or missing authentication/authorization
- Verbose error messages exposing stack traces or system details
- Outdated software, frameworks, or containers with known vulnerabilities
- Exposed AI/ML endpoints without proper access controls

**How to Prevent It**

- Harden default configurations and remove unused features/services
- Enforce strong authentication and least privilege across all systems
- Limit network exposure and segment sensitive resources
- Keep software, frameworks, and containers up to date with patches
- Hide stack traces and system information from error messages
- Audit cloud configurations and permissions regularly
- Secure AI endpoints and automation services with proper access controls and monitoring
- Integrate configuration reviews and automated security checks into your deployment pipeline

### Challenge

Navigate to **10.80.171.216:5002**. It looks like the developers left unnecessary traces within their User Management APIs.

![owasp1.png](owasp1.png)

**What’s the flag?**

```bash
curl "http://10.80.171.216:5002/api/user/123'"
{
  "debug_info": {
    "flag": "THM{V3RB............}"
  },
  "error": "Invalid user ID format: 123'. Flag: THM{V3RB........}",
  "traceback": "Traceback (most recent call last):\n  File \"/app/app.py\", line 21, in get_user\n    raise ValueError(f\"Invalid user ID format: {user_id}. Flag: {FLAG}\")\nValueError: Invalid user ID format: 123'. Flag: THM{V3RB0S3_3RR0R_L34K}\n"
}
```

OR

```bash
GET http://10.80.171.216:5002/api/user/flag.txt     
{
  "debug_info": {
    "flag": "THM{V3RB......."
  },
  "error": "Invalid user ID format: flag.txt. Flag: THM{V3RB...........}",
  "traceback": "Traceback (most recent call last):\n  File \"/app/app.py\", line 21, in get_user\n    raise ValueError(f\"Invalid user ID format: {user_id}. Flag: {FLAG}\")\nValueError: Invalid user ID format: flag.txt. Flag: THM{V3RB0S3_3RR0R_L34K}\n"
}
```

## AS03:**Software Supply Chain Failures**

**What It Is**

Software supply chain failures occur when applications rely on components, libraries, services, or models that are outdated, compromised, or improperly verified. These issues don’t originate from your code but from the external software and tools your application depends on. Attackers exploit these weak points to inject malicious code, bypass security controls, or extract sensitive data.

**Why It Matters**

Modern applications rely heavily on third‑party packages, APIs, and AI models. A single compromised dependency can undermine the entire system, giving attackers access without ever touching your own code. Supply chain attacks are often automated, distributed, and hard to detect; making them extremely damaging.

**Example**

The 2021 SolarWinds Orion breach is a prime example. Attackers inserted malicious code into a trusted software update, affecting thousands of organisations that installed it automatically. The flaw wasn’t in SolarWinds’ application logic; it came from weaknesses in the build, verification, and distribution pipeline.

The same risk applies to AI systems: using unverified third‑party models, datasets, or fine‑tuned weights can introduce hidden behaviours, backdoors, or data leakage without the developer noticing.

**Common Patterns**

- Using unverified or unmaintained dependencies
- Automatically installing updates without verification
- Over‑reliance on third‑party AI models without auditing
- Insecure CI/CD or build pipelines that allow tampering
- Poor provenance or licensing tracking
- No ongoing vulnerability monitoring after deployment

**How to Protect the Supply Chain**

- Verify all third‑party components, libraries, and AI models before integrating them
- Continuously monitor and patch dependencies
- Sign, verify, and audit all updates and packages
- Secure CI/CD pipelines and build processes against tampering
- Track provenance and licensing for every dependency
- Apply runtime monitoring for unusual behaviour in libraries or AI components
- Include supply chain threat modelling throughout the SDLC; from testing to deployment and updates

---

### **Challenge**

Navigate to **10.80.171.216:5003**. The application is running outdated code and imports an old `lib/vulnerable_utils.py` component.

![owasp04.png](owasp04.png)

**Debug the application.**

The `vulnerable_utils.py` library includes a debug function that leaks internal secrets whenever it’s triggered. Because the Flask app blindly exposes this outdated function through the `/api/process` endpoint, an attacker can simply submit `"data": "debug"` and extract sensitive information; admin tokens, internal keys, and even the challenge flag.

![owasp_debug.png](owasp_debug.png)

![owasp02.png](owasp02.png)

**What’s the flag?**

![owasp03.png](4a39a0fc-e51b-4b32-afe6-574476850bea.png)

OR

```bash
POST /api/process HTTP/1.1
Host: 10.80.171.216:5003
Content-Type: application/json
Content-Lenth: 35
Content-Length: 37

{
"data":"debug",
"debug":"true"
}
```

```bash
HTTP/1.1 200 OK
Server: Werkzeug/3.1.3 Python/3.11.14
Date: Fri, 28 Nov 2025 01:25:03 GMT
Content-Type: application/json
Content-Length: 157
Connection: close

{
  "admin_token": "admin_token_12345",
  "flag": "THM{SUPPLY_...............}",
  "internal_secret": "internal_secret_key_2024",
  "version": "1.2.3"
}
```

## AS04: Cryptographic Failures

### **Cryptographic Failures**

**What It Is**

Cryptographic failures occur when encryption is implemented incorrectly; or not used when it should be. This includes weak algorithms, hard-coded keys, poor key management, or failing to encrypt sensitive data. These issues allow attackers to access information that should remain private.

**Why It Matters**

Modern web applications depend on cryptography for secure communication, data protection, identity verification, and secret management. When cryptographic controls break down, sensitive data such as passwords, tokens, and personal information becomes exposed. This can lead to account takeovers, data leaks, or full-scale system compromise.

Attackers exploit these weaknesses through man-in-the-middle attacks, brute-forcing weak keys, or simply retrieving secrets that were never properly protected.

**Common Patterns**

- Using deprecated or weak algorithms (MD5, SHA-1, ECB mode)
- Hard-coded secrets in source code or configs
- Weak or inconsistent key rotation practices
- Missing encryption for sensitive data in transit or at rest
- Self-signed or invalid TLS certificates
- AI/ML systems mishandling sensitive inputs or exposing model secrets

**How to Prevent It**

- Use strong, modern algorithms like AES-GCM or ChaCha20-Poly1305, and enforce TLS 1.3 with valid certificates
- Use secure key management systems (AWS KMS, Azure Key Vault, HashiCorp Vault)
- Rotate secrets and keys regularly with defined crypto-lifetime policies
- Document and enforce key lifecycle management procedures
- Maintain a full inventory of all keys, certificates, and owners
- Ensure AI models and automation agents never expose unencrypted secrets or sensitive data
- Explore the cryptographic weakness inside the web application for this room to reinforce these concepts

---

### **Challenge**

Navigate to **10.80.171.216:5004**. A cryptographic flaw exists in the application.

![OWASP01.png](OWASP01.png)

Find the key used to decrypt the file

Open the page source and locate **`/static/js/decrypt.js`**. Click that file to reveal the decryption key and any other embedded information needed to decrypt the protected document.

![OWASP02.png](OWASP02.png)

![OWASP03.png](OWASP03.png)

**What’s the flag?**

![OWASP05.png](921ad5b9-73bd-4109-8ed4-1329c3f54d21.png)

## AS06: Insecure Design

### **Insecure Design**

**What It Is**

Insecure design occurs when flawed logic, architecture, or assumptions are baked into a system from the beginning. These issues usually come from skipped threat modelling, missing design requirements, weak reviews, or simple oversight. Once this flawed logic makes it into production, everything built on top of it inherits the weakness.

With AI now embedded into modern systems, insecure design has expanded. Developers often assume AI outputs are safe, correct, or predictable. They treat AI-generated code, decisions, and classifications as if they come from a trustworthy component. When an AI system can write queries, produce code, or make access decisions without boundaries, the risk becomes part of the design itself.

**Example**

A well-known case is **Clubhouse**. The early backend assumed users would only access the service through the mobile app. Because of that assumption, the API lacked proper authentication. Anyone could directly query user data, room details, and even "private" conversations. Once researchers inspected the API, the whole idea of “private conversations” collapsed instantly.

**Why It Matters**

You can’t patch an insecure design. It’s not a bug—it’s the system’s foundation. Fixing it requires rethinking workflows, trust boundaries, and how both humans and AI components make decisions.

---

### **Common Insecure Designs in 2025**

- Weak business logic (e.g., broken recovery flows, flawed approval systems)
- Wrong assumptions about user behaviour or model behaviour
- AI components with unrestricted access or authority
- LLMs or automation agents operating without guardrails
- Debug or test bypasses accidentally left in production
- No abuse-case review or AI-focused threat modelling

---

### **Insecure Design in the AI Era**

AI introduces new design-level risks:

- **Prompt injection** occurs when user input mixes with system prompts, letting attackers manipulate context or extract sensitive data.
- **Blind trust in AI output** leads to fragile systems that act on unverified predictions or decisions.
- **Poisoned or unverified models** can hide malicious behaviours, backdoors, or harmful patterns—compromising systems silently.
- **Automation agents** can perform actions far beyond what the developer intended if not properly restricted.

Because of this, AI components must be treated as *untrusted inputs* until validated, monitored, and proven safe.

---

### **How to Design Securely**

- Treat every AI model and dependency as untrusted until verified.
- Validate, filter, and constrain all model inputs and outputs.
- Separate system prompts from user-provided content.
- Avoid sending sensitive data to models unless absolutely required, and protect it with strict controls.
- Require human review for high-risk or irreversible AI actions.
- Track model provenance and monitor behaviour over time.
- Use differential privacy for sensitive data handled by AI.
- Perform AI-specific threat modelling (prompt attacks, inference risks, agent misuse, supply chain threats).
- Define clear security requirements before implementing each feature.
- Apply least privilege to every user, API, model, and service.
- Enforce proper authentication, authorisation, and session management everywhere.
- Keep dependencies and third-party components verified and updated.
- Continuously test for logic flaws, abuse paths, and emerging risks—especially when new AI features are added.

---

### **Challenge**

Navigate to **10.80.130.28:5005**.

The developers appear to have assumed that **only mobile devices** can access the application.

Investigate the design flaw

![OWASP_A00.png](OWASP_A00.png)

**What’s the flag?**

I ran **ffuf** against the target to see if the application exposed any hidden or undocumented API endpoints. 

![OWASP_A01.png](OWASP_A01.png)

![OWASP_A001.png](OWASP_A001.png)

View the page source and search for the `users` entry.

view-source:[http://10.82.176.169:5005/api/messages/user2](http://10.82.176.169:5005/api/messages/user2)

![OWASP_A1.png](OWASP_A1.png)

view-source:[http://10.82.176.169:5005/api/messages/user1](http://10.82.176.169:5005/api/messages/user1)

![OWASP_A2.png](ce0d1c9c-0960-4a85-b4bd-19354b9ba2eb.png)

view-source:[http://10.82.176.169:5005/api/messages/admin](http://10.82.176.169:5005/api/messages/admin)

![OWASP_A3.png](0d5ec3d0-9e67-45d6-833a-d2e5491d8cb7.png)
