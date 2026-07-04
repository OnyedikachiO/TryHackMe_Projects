# Data Transformation and Cryptographic Analysis Using Burp Suite Decoder

## Project Overview

This project focused on utilizing the Decoder module within Burp Suite to perform data manipulation, cryptographic hashing, encoding transformations, and file integrity verification tasks. The assessment involved generating multiple cryptographic hashes, converting encoded data representations, and identifying the correct SSH private key through hash comparison techniques.

The exercise demonstrated practical applications of Burp Suite Decoder for penetration testing, digital forensics, and security analysis workflows.

---

## Objectives

* Perform cryptographic hash generation using Burp Suite Decoder.
* Convert hash outputs into alternate encoding formats.
* Analyze the security implications of weak hashing algorithms.
* Verify file integrity using hash comparison techniques.
* Identify authentic cryptographic artifacts through forensic validation methods.

---

## Lab Environment

### Tools Used

* Burp Suite Professional/Community Edition
* Burp Decoder Module
* Kali Linux
* OpenSSH Private Keys
* Linux command-line utilities

---

# Task 1: SHA-256 Hash Generation and Hexadecimal Conversion

## Objective

Generate the SHA-256 hash of a supplied phrase and convert the resulting digest into its ASCII hexadecimal representation.

### Input

```text
Let's get Hashing!
```

---

## Methodology

Using the Burp Suite Decoder module, I:

1. Inserted the supplied text into the Decoder input panel.
2. Applied the SHA-256 hashing operation.
3. Converted the resulting digest into ASCII hexadecimal format.
4. Verified the final output.

---

## Security Relevance

SHA-256 is part of the SHA-2 family of cryptographic hashing algorithms and remains widely used for:

* Password hashing frameworks
* Digital signatures
* File integrity verification
* Certificate validation
* Blockchain technologies

---

# Task 2: MD4 Hashing and Base64 Encoding

## Objective

Generate an MD4 digest and encode the resulting hash using Base64.

### Input

```text
Insecure Algorithms
```

---

## Methodology

Using Burp Decoder, I:

1. Generated the MD4 hash of the supplied phrase.
2. Converted the resulting digest using Base64 encoding.
3. Validated the encoded output.

---

## Security Analysis

This exercise highlighted the security weaknesses of legacy cryptographic algorithms.

### MD4 Characteristics

* Extremely fast computation
* Cryptographically broken
* Vulnerable to collision attacks
* Unsuitable for modern security applications

Despite being obsolete, MD4 remains important during penetration testing because it is still encountered in:

* Legacy Windows environments
* Historical authentication systems
* Password cracking engagements
* Digital forensic investigations

---

# Task 3: SSH Private Key Integrity Verification

## Scenario

A collection of SSH private keys had been modified, and the objective was to identify the authentic key using a known MD5 hash value.

Known hash:

```text
3166226048d6ad776370dc105d40d9f8
```

---

## Methodology

### Step 1: Acquire and Inspect Files

The archive containing multiple SSH private keys was extracted and inspected manually.

```bash
wget <target file>

unzip AlteredKeys.zip
```

---

### Step 2: Analyze Candidate Keys

Each OpenSSH private key file was examined individually:

```bash
cat key1
cat key2
cat key3
cat key4
```

---

### Step 3: Hash Validation Using Burp Decoder

For each key:

* The entire file contents were copied.
* Burp Decoder generated the corresponding MD5 hash.
* The resulting digest was compared against the known reference value.

---

### Step 4: Independent Verification

To validate the findings, Linux hash utilities were used:

```bash
md5sum key1 key2 key3 key4
```

---

## Findings

The MD5 digest comparison identified:

```text
key3
```

as the authentic SSH private key.

---

## Security Impact

This exercise demonstrated several practical security concepts:

* Cryptographic integrity verification
* Hash-based artifact validation
* Detection of file tampering
* Forensic verification workflows
* Practical use of cryptographic functions during security assessments

Hash validation remains a critical technique during:

* Incident response investigations
* Malware analysis
* Digital forensics
* File integrity monitoring
* Supply chain security assessments

---

## Key Lessons Learned

This assessment reinforced several important security principles:

* Cryptographic hashes provide reliable mechanisms for integrity verification.
* Weak algorithms such as MD4 remain relevant due to legacy system usage.
* Burp Suite Decoder provides powerful capabilities beyond simple encoding and decoding tasks.
* Hash comparison remains one of the most effective methods for detecting modified or tampered files.
* Independent verification using command-line tooling improves confidence in investigative findings.

The project demonstrated how data transformation and cryptographic analysis techniques can be applied during penetration testing, incident response, and digital forensic investigations.

---

## Disclaimer

This research was conducted in an authorized laboratory environment for educational, defensive, and security research purposes only.
