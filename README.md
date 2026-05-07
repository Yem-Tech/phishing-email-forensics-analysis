# Phishing Email Forensics Analysis
### Incident Review for Suspected Phishing Email — ServiceNow Workflow

> **Scope Notice:** This project documents a structured phishing email forensics investigation conducted as part of a cybersecurity analyst training exercise. The methodology, tools, and reporting structure reflect real-world SOC (Security Operations Centre) analyst workflows used in professional incident response environments.

---

## Case Overview

| Field | Details |
|---|---|
| **Case Title** | Suspected Phishing Email Investigation |
| **Reported Through** | ServiceNow Incident Ticket |
| **Evidence Item** | suspected_email.eml |
| **Analyst** | Olayemi Owoeye |
| **Assessment Status** | Completed — Findings Documented |
| **Report Type** | Email Forensics / Threat Intelligence |

---

## 1. Executive Summary

A suspicious email preserved as `suspected_email.eml` was submitted through a ServiceNow incident ticket and assigned for forensic analysis. The investigation focused on validating whether the message exhibited characteristics consistent with phishing or business email compromise (BEC).

The review covered the following analytical areas:

- Embedded URL and attachment reputation analysis
- Sender IP address abuse history and reputation scoring
- SPF, DKIM, and DMARC email authentication validation
- Header consistency analysis between the visible From address and the Return-Path

All findings were corroborated using industry-standard open-source intelligence (OSINT) and threat intelligence platforms including **VirusTotal**, **URLScan.io**, and **AbuseIPDB**.

---

## 2. Scope and Objectives

- Confirm whether embedded URLs or attachments show malicious, suspicious, or clean results based on external reputation and behavioural analysis platforms
- Assess whether the sender infrastructure has a known history of abuse or malicious activity
- Validate whether email authentication controls — SPF, DKIM, and DMARC — passed and whether results align with the claimed sender domain
- Identify inconsistencies between the From field and Return-Path that could indicate spoofing, impersonation, or relay abuse
- Provide a documented analyst conclusion and recommended response action aligned with SOC escalation procedures

---

## 3. Investigation Methodology

### 3.1 Embedded URLs and Attachments
Links and files contained in the email body were reviewed using **VirusTotal** and **URLScan.io** to determine reputation, detection count, redirect behaviour, hosting indicators, and page rendering outcome.

### 3.2 Sender IP Address Review
The originating IP address was extracted from email headers and investigated using **AbuseIPDB** to determine abuse confidence score, historical reports, category tags, ASN, ISP, and geolocation data.

### 3.3 Email Authentication Review
SPF, DKIM, and DMARC results were verified from the raw email headers to determine whether the message passed domain authentication checks and whether alignment between authentication results and the visible sender domain was achieved.

### 3.4 Header Consistency Review
The visible From field was compared against the Return-Path and related header values to identify impersonation, envelope sender mismatch, or suspicious routing patterns indicative of spoofing or third-party relay abuse.

---

## 4. Findings Summary

| Control / Check | Status | Evidence Source | Analyst Note |
|---|---|---|---|
| Embedded URL Analysis | Analysed | VirusTotal / URLScan.io | Detection count, redirects, landing behaviour, and brand impersonation indicators reviewed |
| Attachment Analysis | Analysed | VirusTotal | File hash, detection ratio, and sandbox indicators reviewed |
| Sender IP Reputation | Analysed | AbuseIPDB | Abuse confidence score, report history, ASN, ISP, and geolocation recorded |
| SPF, DKIM, DMARC | Validated | Header Review | Pass/fail/none recorded for each control with alignment assessment |
| From vs Return-Path | Validated | Header Review | Visible sender and envelope sender alignment or mismatch recorded and assessed |

---

## 5. Detailed Analysis

### 5.1 Embedded URLs and Attachments

The email body was reviewed for embedded hyperlinks and file attachments. **VirusTotal** was used to determine whether URLs or file hashes were flagged by antivirus engines or threat intelligence feeds. **URLScan.io** was used to observe page rendering behaviour, redirect chains, destination infrastructure, and signs of credential harvesting or brand impersonation.

**Tools Used:**
- VirusTotal — `https://www.virustotal.com`
- URLScan.io — `https://urlscan.io`

**Key Indicators Reviewed:**
- Embedded URL destination and redirect chain
- Final landing page behaviour and visual rendering
- Detection ratio across antivirus and threat intelligence engines
- Signs of brand impersonation or credential harvesting page structure

---

### 5.2 Sender IP Reputation Review

The sender IP address was extracted from the email headers and checked against **AbuseIPDB** to identify whether the infrastructure had been previously reported for spam, phishing, malicious scanning, botnet activity, or other abusive behaviour.

**Tool Used:**
- AbuseIPDB — `https://www.abuseipdb.com`

**Key Indicators Reviewed:**
- Abuse confidence score (0–100%)
- Number and category of historical abuse reports
- Network / ISP / ASN attribution
- Geolocation of sending infrastructure

---

### 5.3 SPF, DKIM, and DMARC Validation

Email authentication results were reviewed from the raw message headers to determine whether the message passed SPF, DKIM, and DMARC checks. A pass result supports but does not guarantee legitimacy. A fail, softfail, none, or misalignment result increases suspicion depending on sender domain context and expected mail flow.

| Control | Description | Assessed |
|---|---|---|
| **SPF** | Validates that the sending IP is authorised to send on behalf of the envelope sender domain | ✅ |
| **DKIM** | Validates the cryptographic signature applied by the sending mail server | ✅ |
| **DMARC** | Validates alignment between SPF/DKIM results and the visible From domain | ✅ |

---

### 5.4 From Address and Return-Path Review

The visible From address was compared against the Return-Path and other routing header fields. A mismatch does not always indicate phishing, but unexplained differences can indicate spoofing, third-party relay abuse, or impersonation — particularly when the email attempts to appear as if it originated from a trusted business domain while the envelope sender points elsewhere.

**Key Indicators Reviewed:**
- Visible From address versus envelope Return-Path
- Consistency of routing headers with claimed sending domain
- Presence of forwarding or relay indicators
- Alignment with expected organisational mail flow

---

## 6. Indicators and Evidence Log

| Indicator Type | Indicator Value | Status | Notes |
|---|---|---|---|
| Sender Email | Extracted from headers | Assessed | Claimed sender identity reviewed for spoofing indicators |
| Sender IP | Extracted from headers | Assessed | Originating infrastructure reviewed via AbuseIPDB |
| Embedded URL | Extracted from body | Assessed | Redirect chain and landing page reviewed via URLScan.io |
| Attachment Hash | SHA256 / MD5 | Assessed | File reputation reviewed via VirusTotal |
| Domain(s) | Visible / redirect / hosting | Assessed | All associated domains reviewed for reputation and registration indicators |

---

## 7. Analyst Conclusion

Based on evidence collected from link analysis, attachment review, sender IP reputation, authentication checks, and header consistency analysis, the investigation follows a structured classification framework:

| Classification | Criteria |
|---|---|
| **Benign** | All controls pass, no malicious indicators, sender verified |
| **Suspicious** | Partial failures or anomalies present, further review warranted |
| **Confirmed Phishing** | Malicious URL, harvesting page, authentication failures, spoofed sender |
| **Spam** | Unsolicited but non-malicious, no threat indicators |
| **Escalation Required** | Indicators suggest broader targeting, active compromise, or BEC |

---

## 8. Recommended Response Actions

- Block or monitor the sender address, sending domain, IP address, and embedded URLs where confirmed malicious
- Search for additional recipients of the same message across the environment and quarantine matching emails
- Reset credentials and review sign-in logs if the user interacted with the email or submitted credentials
- Escalate to incident response or threat hunting if indicators suggest broader targeting or active compromise
- Document final disposition in ServiceNow with all screenshots, hashes, and header evidence attached for auditability
- Apply email gateway rules to block future delivery from identified malicious infrastructure

---

## Tools and Platforms Used

| Tool | Purpose | URL |
|---|---|---|
| **VirusTotal** | URL and file hash reputation analysis | https://www.virustotal.com |
| **URLScan.io** | URL rendering, redirect chain, and hosting analysis | https://urlscan.io |
| **AbuseIPDB** | Sender IP abuse history and reputation scoring | https://www.abuseipdb.com |
| **MXToolbox** | Email header analysis and authentication review | https://mxtoolbox.com |
| **Google Admin Toolbox** | SPF, DKIM, DMARC header parsing | https://toolbox.googleapps.com |

---

## Skills Demonstrated

- Email forensics and header analysis
- Open-source threat intelligence (OSINT) investigation
- SPF / DKIM / DMARC authentication validation
- Sender spoofing and impersonation detection
- IOC (Indicator of Compromise) identification and documentation
- SOC Tier 1 incident triage and ServiceNow ticketing workflow
- Professional cybersecurity reporting and evidence documentation

---

## Author

**Cybersecurity Analyst | HypertechAi**
Certifications: CCNA | Microsoft Azure | AWS | CompTIA Security+ | Python | Cybersecurity

---

## Ethical Disclaimer

> This analysis was conducted in an authorised academic and training context. All email samples reviewed were either synthetically generated or sourced from designated training platforms. No real user data, live production systems, or unauthorised email accounts were accessed at any stage of this investigation.

---

*This report follows professional SOC analyst reporting standards and is prepared for cybersecurity portfolio documentation purposes.*

