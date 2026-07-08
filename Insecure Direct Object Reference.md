# IDOR

## Project Overview

In this project, I explored **Insecure Direct Object Reference (IDOR)** vulnerabilities and learned how improper access control can allow unauthorized users to access sensitive resources by manipulating object identifiers.

The assessment covered identifying IDOR vulnerabilities using sequential IDs, encoded identifiers, hashed values, and API endpoints. I also performed practical testing against a vulnerable web application to demonstrate how insecure object references can expose other users' information without proper authorization checks.

---

## Objectives

- Understand how IDOR vulnerabilities occur.
- Identify insecure object references within web applications.
- Test sequential, encoded, and hashed identifiers.
- Enumerate API endpoints for vulnerable parameters.
- Exploit an IDOR vulnerability to access unauthorized data.
- Understand secure access control practices.

---

## Skills Learned

- IDOR identification
- Access control testing
- API enumeration
- HTTP request analysis
- Parameter manipulation
- Base64 analysis
- Hash identification
- Browser Developer Tools
- Web application reconnaissance
- Authorization testing

---

## Tools & Technologies

- Browser Developer Tools
- Firefox
- JSON API Responses
- Base64 Encoding/Decoding
- MD5 Hash Analysis
- CrackStation
- HTTP Requests

---

# Methodology

## 1. Understanding IDOR

I began by studying how **Insecure Direct Object Reference (IDOR)** vulnerabilities occur when an application trusts user-supplied object identifiers without verifying ownership on the server side.

An application that references resources using predictable identifiers can unintentionally expose other users' information if proper authorization checks are not implemented.

---

## 2. Exploiting a Basic IDOR

I accessed the vulnerable application and modified the object identifier within the URL to request another user's resource.

By changing the supplied identifier, I successfully accessed unauthorized content that should not have been available to my account.

### Result

- Successfully exploited an IDOR vulnerability
- Retrieved the application flag:

---

## 3. Identifying Encoded Identifiers

I examined how applications sometimes encode identifiers before transmitting them between client and server.

Rather than providing additional security, encoding simply transforms data into another format that can often be reversed.

The assessment covered Base64 encoding, where identifiers can be:

- Decoded
- Modified
- Re-encoded
- Resubmitted to the application

This demonstrated why encoding should never be relied upon as an access control mechanism.

---

## 4. Identifying Hashed Identifiers

Next, I explored applications that replace identifiers with hash values.

I learned that some implementations simply hash predictable numeric IDs using algorithms such as MD5.

Known hashes can often be identified using public hash databases such as CrackStation, allowing attackers to infer the original identifier values.

---

## 5. Testing Unpredictable Identifiers

For applications using less predictable identifiers, I learned the recommended testing methodology of creating multiple user accounts.

By comparing object identifiers between separate accounts and attempting cross-account access, it becomes possible to determine whether proper authorization controls exist.

---

## 6. Enumerating API Endpoints

Using the browser's Developer Tools, I monitored network traffic while navigating the application.

The **Network** tab revealed an API endpoint responsible for loading customer account information.

The endpoint returned JSON data using the following request format:

```http
GET /api/v1/customer?id={user_id}
```

This immediately identified the **id** parameter as a potential target for IDOR testing.

---

## 7. Exploiting the API IDOR

After identifying the vulnerable API endpoint, I modified the user ID parameter to request information belonging to other users.

Example requests:

```http
GET /api/v1/customer?id=1
```

```http
GET /api/v1/customer?id=3
```

The server returned information for both users without verifying authorization.

### Information Retrieved

**User ID 1**

- Username: `adam84`

**User ID 3**

- Email Address:

```
j@fakemail.thm
```

This confirmed that the API endpoint was vulnerable to an Insecure Direct Object Reference attack.

---

# Key Findings

During the assessment, I successfully identified:

- An exploitable IDOR vulnerability
- Predictable object identifiers
- API endpoints exposing user information
- Authorization weaknesses
- Sequential user enumeration
- Encoded identifier usage
- Hash-based identifier implementations

---

# Security Recommendations

Based on the assessment, I recommend the following improvements:

- Enforce server-side authorization for every object request.
- Never rely on client-supplied object identifiers for access control.
- Validate object ownership before returning sensitive information.
- Use indirect object references where appropriate.
- Avoid exposing predictable sequential identifiers.
- Regularly perform access control testing during development.
- Secure API endpoints with proper authorization checks.

---

# Lessons Learned

This project demonstrated that authentication alone does not guarantee authorization.

Even when users are properly logged in, insecure object references can expose sensitive information if applications fail to verify resource ownership.

I also gained practical experience analyzing API requests, manipulating identifiers, and validating authorization controls using browser developer tools.

