# OWASP Top 10 2025: IAAA Failures

Examine A01, A07, and A09 to understand how each contributes to failures within the applied IAAA model.

## What's IAAA

The IAAA model provides a straightforward framework for verifying users and their actions within applications. Each component is essential, and the process must be followed in order; skipping a step prevents the subsequent steps from functioning correctly. The four components are:

1. **Identity** – The unique account representing a person or service (e.g., user ID or email).
2. **Authentication** – Verifying that identity using mechanisms like passwords, OTPs, or passkeys.
3. **Authorization** – Determining what actions the identity is allowed to perform.
4. **Accountability** – Recording and monitoring actions to track who did what, when, and from where.

The three OWASP Top 10:2025 categories discussed in this room relate to failures in implementing IAAA. Weaknesses in these areas can be highly detrimental, potentially allowing attackers to access other users’ data or escalate privileges beyond what they should have.

## A01: Broken Access Control

Broken Access Control occurs when a server fails to enforce proper access restrictions for every request. A common example is **IDOR (Insecure Direct Object Reference)**: if modifying an identifier in a request (e.g., `?id=7` → `?id=6`) allows a user to view or modify someone else’s data, the access control mechanism is broken

**How to Fix This Vulnerability**

- Implement proper **authentication** and **authorization** checks.
- Ensure the authenticated user has **permission** to access the requested resource.
- Use **indirect references** or **encrypted tokens** instead of predictable IDs.
- **Log and monitor** all access attempts to sensitive resources

## AS07: Authentication Failures

Authentication failures occur when an application cannot reliably verify or bind a user’s identity. Common issues include:

- **Username enumeration** – allowing attackers to discover valid usernames.
- **Weak or guessable passwords** – especially without account lockouts or rate limiting.
- **Logic flaws** – errors in the login or registration flow.
- **Insecure session or cookie handling** – sessions that can be hijacked or misbound.

When these weaknesses exist, attackers can often log in as another user or bind a session to the wrong account.

**Example:** An attacker targeting an `admin` account could exploit the system by registering a username like `aDmiN` to bypass checks, potentially gaining unauthorized access

## A09: Logging & Alerting Failures

When applications fail to record or alert on security-relevant events, defenders cannot effectively detect or investigate attacks. Proper logging is critical for **accountability;** proving who did what, when, and from where.

Common failures include:

- Missing authentication events.
- Vague or incomplete error logs.
- No alerts for brute-force attempts or privilege changes.
- Short log retention periods.
- Storing logs in locations where attackers can tamper with them.

<a href="https://github.com/OnyedikachiO/TryHackMe_Projects/blob/main/Insecure%20Data%20Handling.md">Insecure Data Handling</a>

<a href="https://github.com/OnyedikachiO/TryHackMe_Projects/blob/main/Application%20Design%20Flaws.md">Application Design Flaws</a>
