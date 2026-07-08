# Walking an Application

## Project Overview

In this project, I performed a manual security assessment of a web application using only the built-in developer tools available in a modern web browser. Rather than relying on automated scanners, I explored the application's functionality, inspected its source code, analyzed client-side behavior, and monitored network traffic to identify security weaknesses and hidden resources.

The assessment demonstrated how valuable browser-based reconnaissance can be during the early stages of a penetration test. By manually reviewing the application, I uncovered sensitive information exposed through HTML comments, hidden links, directory listings, client-side JavaScript, and network requests.

---

## Objectives

- Perform manual web application reconnaissance.
- Identify hidden functionality within a web application.
- Analyze HTML source code for sensitive information.
- Use browser developer tools to inspect and manipulate page elements.
- Debug JavaScript to reveal hidden application behavior.
- Capture and analyze HTTP requests using browser network tools.
- Understand common web application misconfigurations that expose sensitive information.

---

## Skills Learned

- Manual web application reconnaissance
- Web application enumeration
- HTML source code analysis
- Browser Developer Tools usage
- DOM inspection
- JavaScript debugging
- Client-side security analysis
- Network traffic inspection
- Directory enumeration
- Information disclosure identification

---

## Tools & Technologies

- Google Chrome Developer Tools
- Firefox Developer Tools
- HTML
- CSS
- JavaScript
- Browser Network Inspector

---

# Methodology

## 1. Application Reconnaissance

I began by manually exploring the web application and documenting every accessible page and feature.

During reconnaissance I identified:

- Homepage
- News section
- Contact page
- Customer login
- Registration page
- Password reset
- Customer dashboard
- Ticket submission page
- Account management
- Logout functionality

Creating an inventory of application functionality helped establish a clear attack surface before testing individual components.

---

## 2. Reviewing the HTML Source

Using the browser's **View Page Source** feature, I analyzed the raw HTML returned by the web server.

During the review I identified:

- Developer comments
- Hidden URLs
- Internal resources
- Framework information
- External asset locations

Several pieces of sensitive information were unintentionally exposed.

### HTML Comments

Developer comments contained references to pages that were not linked from the main website.

Example discovery:

- Temporary development page
- Hidden application resources

This demonstrated why developers should never leave sensitive comments inside production code.

---

### Hidden Links

Additional hidden hyperlinks were discovered inside the HTML source.

Although invisible to normal users, these resources remained accessible simply by reviewing the page source.

This illustrates a common information disclosure issue where hidden functionality is mistakenly assumed to be secure.

---

### Directory Listing

Inspection of linked assets revealed that directory browsing had been enabled.

Instead of returning a **403 Forbidden** response, the server exposed the directory contents, allowing users to browse internal files.

This resulted in access to sensitive files that should not have been publicly accessible.

Security impact included:

- Information disclosure
- File enumeration
- Exposure of internal resources

---

### Framework Enumeration

The page source also disclosed the web framework and version being used.

After reviewing the publicly available release information, I confirmed the application was using an outdated version of the framework.

Exposing framework versions provides attackers with valuable intelligence for vulnerability research and exploit selection.

---

## 3. Inspecting the DOM

Using the browser's **Inspector**, I analyzed the live Document Object Model (DOM).

A premium content overlay prevented access to protected content.

Instead of bypassing authentication, I inspected the HTML elements responsible for rendering the overlay.

By modifying the CSS property:

```
display: block;
```

to

```
display: none;
```

I removed the client-side overlay and exposed the hidden content beneath it.

This exercise demonstrated that client-side restrictions alone should never be relied upon for protecting sensitive information.

---

## 4. JavaScript Analysis

Next, I analyzed the application's JavaScript using the browser debugger.

I reviewed the application's JavaScript resources and formatted a minified script using the browser's **Pretty Print** functionality.

After examining the script logic, I identified the code responsible for automatically hiding an on-screen message.

I inserted a breakpoint before the removal function executed.

Refreshing the page paused JavaScript execution before the message disappeared, revealing hidden application content.

This exercise demonstrated how browser debugging can expose client-side functionality that may otherwise remain unnoticed.

---

## 5. Network Traffic Analysis

Finally, I monitored network activity using the browser's Network tab.

While interacting with the contact form, I observed asynchronous HTTP requests being sent using AJAX.

By inspecting the request and response data, I identified additional application information that was not directly visible through the user interface.

This reinforced the importance of monitoring browser traffic during web application assessments.

---

# Key Findings

I successfully identified multiple client-side security weaknesses, including:

- Sensitive HTML comments
- Hidden application endpoints
- Directory listing enabled
- Public exposure of framework version
- Client-side access control bypass
- Hidden JavaScript functionality
- Information disclosure through AJAX requests

---

# Security Recommendations

Based on the assessment, I recommend the following improvements:

- Remove developer comments from production environments.
- Disable directory listing on web servers.
- Avoid exposing framework versions publicly.
- Store sensitive functionality exclusively on the server.
- Enforce authorization checks server-side.
- Avoid relying on CSS or JavaScript to protect sensitive content.
- Regularly update web frameworks and third-party components.
- Review client-side JavaScript for unnecessary information disclosure.
- Monitor exposed resources during security reviews.

---

# Lessons Learned

This project reinforced that effective web application testing begins with thorough manual reconnaissance.

Without using automated scanners, I was able to identify multiple security weaknesses simply by leveraging browser developer tools.

The assessment also demonstrated that client-side code should never be considered a security boundary, as attackers can freely inspect, modify, and debug everything delivered to the browser.

Manual testing remains an essential component of penetration testing, often uncovering issues that automated tools may overlook.

---

# Outcome

Through this project, I gained practical experience in manually assessing web applications using browser-native tools. I improved my ability to identify information disclosure vulnerabilities, analyze client-side behavior, inspect network traffic, and understand how seemingly minor misconfigurations can expose valuable information to attackers. This project strengthened my web application reconnaissance skills and reinforced the importance of manual analysis during penetration testing engagements.
