# Mediroza General Hospital — Black-Box Penetration Test

## Project Overview

This project documents an authorized **5-day Black-Box Penetration Test and Vulnerability Assessment** conducted against the web infrastructure of Mediroza General Hospital.

The objective was to assess the application from an external attacker's perspective, identify vulnerabilities, demonstrate their potential impact, and document the findings in a professional penetration testing report.

> **Authorization:** Written authorization was provided by the client before testing began.

## Scope

**Target:** Mediroza Hospital web application
**Testing Type:** Black-Box Penetration Testing
**Duration:** 5 days

### Rules of Engagement

* Testing was restricted to the authorized target domain.
* No social engineering was performed.
* No denial-of-service testing was performed.
* No systems outside the agreed scope were tested.
* Findings were kept confidential during the assessment period.

---

## Finding 1 — SQL Injection Authentication Bypass

### Objective

The first objective was to gain access to the patient portal through the application's authentication mechanism.

### Methodology

I used **Burp Suite Intruder** to test the login functionality with a list of SQL injection payloads.

During the attack, one of the tested payloads produced a `302 Found` response, indicating a change in the application's authentication behavior.

The successful payload was:

`admin'--`

This demonstrated that the authentication mechanism was vulnerable to SQL injection and could be bypassed.

### Impact

An attacker capable of exploiting this vulnerability could potentially gain unauthorized access to functionality intended for authenticated users.

### Tools

* Burp Suite
* Burp Suite Intruder
* SQL injection payloads

---

# Finding 2 — Confidential Patient PDF Exposure

Following the authorized access to the patient portal, I identified three downloadable patient reports:

* `patient_report_1.pdf`
* `patient_report_2.pdf`
* `patient_report_3.pdf`

These files contained confidential patient information and therefore represented a significant confidentiality risk.

For the purposes of the assessment, the documents were handled as sensitive material and their contents are not reproduced in this public documentation.

## PDF Password Recovery Testing

The retrieved documents were encrypted. I tested the strength of the document passwords using `pdfcrack` against the `rockyou.txt` wordlist in my controlled environment.

Example command:

`pdfcrack -f ~/Downloads/patient_report_1.pdf -w /usr/share/wordlists/rockyou.txt`

The purpose of this testing was to determine whether the encryption provided an effective additional layer of protection after the files had been exposed.

### Impact

If an attacker could obtain the encrypted documents and recover their passwords, the confidentiality of the patient information contained within the reports could be compromised.

### Tools

* pdfcrack
* rockyou.txt
* Kali Linux

---

# Finding 3 — Sensitive Information Exposure Through `/old/`

## Reconnaissance

As part of the reconnaissance phase, I examined the website's `robots.txt` file.

I used:

`curl -s https://medirozahospital.com/robots.txt`

The file referenced:

`Disallow: /old/`

Although `robots.txt` is not a security control, entries within it can reveal interesting paths and legacy resources that deserve further security assessment.

## Directory Enumeration

I subsequently investigated the referenced `/old/` directory within the authorized scope.

The directory exposed sensitive business information, including:

* Staff salary information
* Shareholder-related information

This represented a significant information disclosure issue.

### Impact

Exposure of sensitive internal business information could assist attackers in reconnaissance, social engineering, targeted attacks, or further compromise.

### Tools

* curl
* Web browser
* Manual directory enumeration

---

# Attack Chain

The assessment demonstrated how multiple weaknesses can potentially be chained together:

```text
External Reconnaissance
        ↓
Authentication Testing
        ↓
SQL Injection
        ↓
Authentication Bypass
        ↓
Patient Portal Access
        ↓
Confidential PDF Exposure
        ↓
PDF Password-Recovery Testing
        ↓
Further Reconnaissance
        ↓
/old/ Directory Discovery
        ↓
Sensitive Business Information Exposure
```

## Key Lessons

This assessment reinforced several important penetration-testing concepts:

1. **Authentication mechanisms must properly handle user input.**
2. **SQL queries should use parameterized statements/prepared statements.**
3. **Sensitive patient documents should have strong access controls.**
4. **Encryption should use strong, unique passwords and appropriate cryptographic protection.**
5. **Sensitive or legacy directories should not be publicly accessible.**
6. **`robots.txt` should never be treated as a security mechanism for hiding sensitive resources.**
7. **A penetration tester should look beyond individual vulnerabilities and investigate how weaknesses can be chained together.**

## Tools Used

* Burp Suite
* Burp Suite Intruder
* curl
* pdfcrack
* Kali Linux
* Wordlists

## Responsible Disclosure

This assessment was conducted with **explicit authorization** and within the agreed scope.

No patient information, credentials, salary figures, shareholder information, or other sensitive client data are included in this public project documentation.

The purpose of this project is to demonstrate penetration-testing methodology and security assessment skills in an authorized environment.

**Educational and authorized security testing only.**
