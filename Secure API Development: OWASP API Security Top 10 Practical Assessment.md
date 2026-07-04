# Secure API Development: OWASP API Security Top 10 Practical Assessment

## Project Overview

This project involved assessing a deliberately vulnerable API environment designed to demonstrate common API security weaknesses identified in the OWASP API Security Top 10. The objective was to understand how insecure API implementations can lead to unauthorized access, data leakage, service disruption, and privilege escalation.

Using API testing tools, I analyzed vulnerable endpoints, identified security flaws, validated their impact, and reviewed secure implementations designed to mitigate each vulnerability.

---

## Objectives

* Understand how modern APIs operate and why they are frequently targeted.
* Identify common API security vulnerabilities.
* Exploit vulnerable API endpoints in a controlled environment.
* Analyze the security impact of each vulnerability.
* Evaluate secure implementations and mitigation strategies.
* Gain practical experience with API security testing methodologies.

---

## Lab Environment

### Tools Used

* Google Chrome
* Talend API Tester
* Vulnerable API application hosted locally
* HTTP GET and POST requests
* Authorization tokens and custom headers

---

# Vulnerability 1: Broken Object Level Authorization (BOLA)

## Description

Broken Object Level Authorization (BOLA), also known as Insecure Direct Object Reference (IDOR), occurs when an API fails to verify whether a user is authorized to access a requested object.

This vulnerability allows attackers to modify object identifiers such as user IDs and retrieve data belonging to other users.

---

## Vulnerable Endpoint

```http
GET /MHT/apirule1_v/user/{id}
```

Example request:

```http
GET http://localhost:80/MHT/apirule1_v/user/1
```

---

## Security Analysis

During testing, I observed that the endpoint returned employee records solely based on the supplied employee ID. No authorization checks were performed to verify whether the requester was permitted to access the requested record.

By modifying the employee ID parameter, it was possible to enumerate multiple employee records.

### Impact

* Unauthorized access to employee information
* Database enumeration
* Potential account takeover scenarios
* Exposure of sensitive organizational data

---

## Secure Implementation

The secure version introduced authorization token validation:

```http
GET /MHT/apirule1_s/user/{id}
```

Requests without valid authorization tokens received:

```http
403 Forbidden
```

---

## Recommended Mitigations

* Implement strict object-level authorization checks.
* Apply role-based access control (RBAC).
* Use secure authorization tokens.
* Enforce access validation on every request.

---

# Vulnerability 2: Broken User Authentication (BUA)

## Description

Broken User Authentication occurs when flaws in authentication logic allow attackers to impersonate legitimate users or bypass authentication controls entirely.

---

## Vulnerable Endpoint

```http
POST /MHT/apirule2/user/login_v
```

---

## Security Analysis

During testing, I discovered that the authentication mechanism validated only the email address and ignored the password field completely.

As a result, possession of a valid email address alone was sufficient to obtain an authentication token.

Example workflow:

1. Submit a valid employee email.
2. Receive a valid authentication token.
3. Use the token to access protected resources.

Protected endpoint:

```http
GET /MHT/apirule2/user/details
```

---

## Impact

* Account takeover
* Unauthorized access
* User impersonation
* Exposure of sensitive information

---

## Secure Implementation

The secure endpoint validates both the email address and password before issuing an authorization token:

```http
POST /MHT/apirule2/user/login_s
```

---

## Recommended Mitigations

* Validate all authentication credentials.
* Use strong password policies.
* Implement JWT-based authentication.
* Enable multi-factor authentication.
* Apply account lockout policies.
* Store passwords using secure hashing algorithms.

---

# Vulnerability 3: Excessive Data Exposure

## Description

Excessive Data Exposure occurs when APIs return more information than required by the client application.

Developers frequently rely on front-end applications to filter sensitive information, which exposes confidential data to attackers who interact directly with APIs.

---

## Vulnerable Endpoint

```http
GET /MHT/apirule3/comment_v/{id}
```

---

## Security Analysis

During testing, I observed that the endpoint returned all database fields associated with a comment object, including internal metadata that was not required by the client application.

Exposed information included:

* User information
* Device identifiers
* Metadata
* Additional internal attributes

---

## Impact

* Information disclosure
* User profiling
* Device fingerprinting
* Exposure of internal application structures

---

## Secure Implementation

The secure endpoint returns only the fields required by the client:

```http
GET /MHT/apirule3/comment_s/{id}
```

---

## Recommended Mitigations

* Return only necessary data.
* Avoid generic serialization methods.
* Perform regular API response reviews.
* Conduct both manual and automated security testing.

---

# Vulnerability 4: Lack of Resources and Rate Limiting

## Description

This vulnerability occurs when APIs fail to restrict request frequency, resource consumption, or payload size.

Attackers can abuse these weaknesses to exhaust server resources or incur financial costs.

---

## Vulnerable Endpoint

```http
POST /MHT/apirule4/sendOTP_v
```

---

## Security Analysis

The password recovery endpoint generated and sent OTP emails without implementing any rate limiting controls.

This allowed repeated requests to be submitted within seconds, potentially causing:

* Email quota exhaustion
* Financial losses
* Service degradation
* Denial of Service conditions

---

## Impact

* Resource exhaustion
* Increased operational costs
* Service outages
* Availability disruptions

---

## Secure Implementation

The secure endpoint introduced request throttling:

```http
POST /MHT/apirule4/sendOTP_s
```

Users were restricted to one request every two minutes.

---

## Recommended Mitigations

* Implement API rate limiting.
* Deploy CAPTCHA protections.
* Restrict payload sizes.
* Apply network-level throttling controls.
* Monitor API usage patterns.

---

# Vulnerability 5: Broken Function Level Authorization (BFLA)

## Description

Broken Function Level Authorization occurs when users can access functions reserved for higher privileged roles.

This vulnerability commonly affects applications with complex role hierarchies and insufficient access control validation.

---

## Vulnerable Endpoint

```http
GET /MHT/apirule5/users_v
```

---

## Security Analysis

The API relied on a client-controlled header:

```http
isAdmin=1
```

to determine administrative privileges.

During testing, I discovered that any authenticated user could set this header manually and gain access to privileged administrative functionality.

Example authorization token:

```text
YWxpY2U6dGVzdCFAISM6Nzg5Nzg=
```

---

## Impact

* Privilege escalation
* Unauthorized administrative access
* Exposure of sensitive organizational data
* Abuse of privileged functionality

---

## Secure Implementation

The secure endpoint validates user roles directly against the backend authorization system:

```http
GET /MHT/apirule5/users_s
```

Access is granted only when the authenticated user possesses the required administrative privileges.

---

## Recommended Mitigations

* Implement server-side role validation.
* Deny access by default.
* Enforce role-based access control.
* Regularly review authorization logic.
* Conduct authorization-focused security testing.

---

# Key Lessons Learned

This assessment demonstrated that API security failures frequently result from missing authorization checks, weak authentication logic, excessive trust in client-side controls, and insufficient resource protections.

The exercise reinforced several important security principles:

* Never trust client-supplied data.
* Enforce authorization checks on every request.
* Validate authentication mechanisms thoroughly.

---

## Disclaimer

This research was conducted in an authorized laboratory environment for educational, defensive, and security research purposes only.
* Expose only the minimum required data.
* Implement rate limiting and resource controls.
* Validate user roles on the server side.

Understanding and testing these vulnerabilities provides practical experience in identifying and mitigating some of the most common and impactful API security risks affecting modern applications.
