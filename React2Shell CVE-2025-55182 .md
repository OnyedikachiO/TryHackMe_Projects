# React2Shell: Remote Code Execution in React Server Components (CVE-2025-55182)

## Overview

This project explores CVE-2025-55182, also known as "React2Shell," a critical remote code execution vulnerability affecting React Server Components (RSC) and frameworks built on top of them, including Next.js.

The vulnerability arises from unsafe deserialization within React's Flight protocol implementation, allowing attackers to abuse internal object references and achieve unauthenticated remote code execution through a single crafted HTTP request.

This project examines the root cause of the vulnerability, analyzes the exploitation chain, demonstrates practical exploitation against a vulnerable target, and develops defensive detection strategies.

---

## Vulnerability Information

| Attribute      | Value                 |
| -------------- | --------------------- |
| CVE            | CVE-2025-55182        |
| Name           | React2Shell           |
| Severity       | Critical              |
| CVSS           | 10.0                  |
| Attack Vector  | Network               |
| Authentication | None                  |
| Impact         | Remote Code Execution |

---

## Affected Technologies

| Technology              | Vulnerable Versions      |
| ----------------------- | ------------------------ |
| React                   | 19.0.0                   |
| React                   | 19.1.0                   |
| React                   | 19.1.1                   |
| React                   | 19.2.0                   |
| Next.js                 | 14.x                     |
| Next.js                 | 15.x                     |
| Next.js                 | 16.x                     |
| React Server Components | Multiple implementations |

---

## Objective

The goals of this research project were to:

* Understand React Server Components internals
* Analyze the Flight protocol serialization process
* Investigate the root cause of CVE-2025-55182
* Develop a practical exploitation workflow
* Achieve remote code execution
* Retrieve sensitive data from the target
* Develop detection mechanisms
* Recommend defensive mitigations

---

## Attack Path

```text id="3rmt2f"
Internet Access
        ↓
Next.js Application Discovery
        ↓
React Server Components Detection
        ↓
Flight Protocol Analysis
        ↓
Unsafe Deserialization Abuse
        ↓
Prototype Chain Traversal
        ↓
Function Constructor Access
        ↓
JavaScript Execution
        ↓
Node.js Command Execution
        ↓
Operating System Command Execution
        ↓
Remote Code Execution
        ↓
Data Exfiltration
```

---

## MITRE ATT&CK Mapping

| Tactic         | Technique                                 |
| -------------- | ----------------------------------------- |
| Reconnaissance | T1595 – Active Scanning                   |
| Initial Access | T1190 – Exploit Public-Facing Application |
| Execution      | T1059.007 – JavaScript                    |
| Execution      | T1059.004 – Unix Shell                    |
| Discovery      | T1082 – System Information Discovery      |
| Collection     | T1005 – Data from Local System            |
| Exfiltration   | T1041 – Exfiltration Over Web Services    |

---

# Background

React Server Components (RSC) allow React applications to execute component rendering on the server and transmit serialized component state to clients using the Flight protocol.

The Flight protocol uses internal serialization markers such as:

```text id="x31vqo"
$@
$B
$Q
$1:property:property
```

These markers enable internal object referencing but also introduce attack opportunities when deserialization controls are insufficient.

---

# Root Cause Analysis

The vulnerability exists within the React Server Component deserialization logic.

### Vulnerable Code

```javascript id="1ftxyr"
function requireModule(metadata) {
    var moduleExports = __webpack_require__(metadata[0]);
    return moduleExports[metadata[2]];
}
```

The use of unrestricted property access allows attackers to traverse JavaScript prototype chains.

By exploiting:

```text id="j3m5r5"
$1:constructor:constructor
```

an attacker gains access to JavaScript's global Function constructor.

This effectively provides arbitrary code execution capability.

---

# Phase 1: Target Enumeration

The vulnerable Next.js application was identified.

### Target

```http id="n1m7af"
http://TARGET:3000
```

The application was confirmed to utilize React Server Components.

---

# Phase 2: Flight Protocol Analysis

The React Flight serialization protocol was analyzed to understand:

* Chunk references
* Blob references
* Property traversal syntax
* Object deserialization logic

The analysis identified multiple unsafe deserialization paths.

---

# Phase 3: Prototype Chain Traversal

The exploit leveraged prototype inheritance through:

```text id="6w7jym"
$1:constructor:constructor
```

This traversal path resolved to:

```text id="6rj7g8"
Function()
```

allowing arbitrary JavaScript execution.

---

# Phase 4: Building the Exploit Chain

A malicious multipart HTTP request was crafted containing:

* Fake React chunk objects
* Self-referential object chains
* Blob handler abuse
* Function constructor injection

### Key Components

```text id="9e7l9q"
then
status
value
_response
_prefix
_formData
```

These objects manipulated React's internal deserialization process.

---

# Phase 5: Remote Code Execution

Command execution was verified using:

```javascript id="w5wjb7"
process.mainModule
    .require('child_process')
    .execSync('id')
```

### Result

```bash id="p6ndtx"
uid=1000(ubuntu)
```

This confirmed successful remote code execution.

---

# Phase 6: Data Exfiltration

Additional commands were executed to retrieve sensitive data.

### Payload

```bash id="cgtnva"
cat /etc/*flag*
```

### Result

Sensitive information was successfully retrieved from the target system.

---

# Detection Engineering

Detection rules were developed to identify exploitation attempts.

### Detection Indicators

* Next-Action headers
* Multipart RSC requests
* resolved_model serialization
* **proto** traversal
* Flight protocol abuse
* Blob handler references

---

# Snort Detection Rule

A custom Snort v3 detection signature was developed to identify exploitation attempts.

The rule detects:

* Suspicious React Flight requests
* Multipart exploitation structures
* Prototype traversal patterns
* Serialized payload abuse

---

# OSQuery Detection

An OSQuery detection rule was developed to identify vulnerable packages across:

* Endpoints
* Build pipelines
* CI/CD environments
* Production servers

---

# Impact Assessment

Successful exploitation allows attackers to:

* Execute arbitrary operating system commands
* Access sensitive files
* Establish persistence
* Deploy malware
* Exfiltrate secrets
* Obtain cloud credentials
* Pivot within infrastructure

---

# Defensive Mitigations

Recommended mitigations include:

* Upgrade React to patched versions
* Upgrade Next.js to patched versions
* Implement WAF signatures
* Monitor Flight protocol requests
* Restrict Node.js execution permissions
* Harden container environments
* Monitor child_process execution
* Implement dependency scanning

---

# Skills Demonstrated

* Vulnerability Research
* React Security
* Next.js Security
* Source Code Review
* Unsafe Deserialization Analysis
* Prototype Pollution Concepts
* Remote Code Execution
* Node.js Security
* Burp Suite
* Detection Engineering
* Snort Rule Development
* OSQuery Development
* MITRE ATT&CK Mapping
* Security Research Methodology

---

# Key Lessons Learned

* Modern web frameworks introduce complex attack surfaces.
* Unsafe deserialization remains one of the most dangerous vulnerability classes.
* Prototype chain traversal can lead directly to code execution.
* Dependency management is critical to application security.
* Detection engineering should accompany offensive research.
* Framework abstractions should never be trusted blindly.

---

## Disclaimer

This research was conducted in an authorized laboratory environment for educational, defensive, and security research purposes only.
