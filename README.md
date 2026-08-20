# SOC SIEM Phishing Investigation

## Overview

This project documents a phishing investigation performed in a simulated SOC environment using a SIEM platform.

The investigation involved analyzing inbound phishing email alerts, identifying Indicators of Compromise (IoCs), correlating email events with firewall logs, determining whether alerts were True Positive (TP) or False Positive (FP), and recommending appropriate remediation actions.

**Platform:** TryHackMe
**Environment:** Simulated SOC / SIEM
**Role:** SOC Analyst L1
**Incident Type:** Phishing / Malicious URL
**Severity:** Medium–High

---

## Investigation Objectives

The main objectives were:

1. Analyze suspicious inbound emails.
2. Identify phishing indicators.
3. Extract relevant IoCs.
4. Search firewall/proxy logs for related URL activity.
5. Correlate email and network events.
6. Determine whether alerts were True Positive or False Positive.
7. Assess potential user impact.
8. Recommend remediation actions.
9. Escalate confirmed malicious activity.

---

# Alert 8815 — Amazon Phishing Email

## Alert Information

**Alert ID:** 8815
**Alert Type:** Inbound Email Containing Suspicious External Link
**Category:** Phishing
**Severity:** Medium
**Timestamp:** August 20, 2026 15:51
**Datasource:** Email
**Direction:** Inbound

### Email Details

**Subject:**
`Your Amazon Package Couldn’t Be Delivered – Action Required`

**Sender:**
`urgents@amazon.biz`

**Recipient:**
`h.harris@thetrydaily.thm`

**Attachment:** None

**URL:**
`http://bit.ly/3sHkX3da12340`

---

## Initial Analysis

Several indicators suggested that this email was a phishing attempt.

### Suspicious Indicators

* The sender claimed to represent Amazon.
* The sender domain was suspicious: `amazon.biz`.
* The message created urgency by stating that the package would be returned within 48 hours.
* The recipient was asked to confirm shipping information.
* A shortened Bitly URL was used.
* The real destination of the shortened URL was hidden.
* The email used a common package-delivery phishing scenario.

### Classification

**True Positive**

### Reason for Classification

The email contained multiple phishing characteristics and attempted to persuade the recipient to access a suspicious shortened URL while providing personal shipping information.

### Escalation Reason

The email represented a potential credential or information theft attempt. Further investigation was required to determine whether the recipient accessed the URL and whether the endpoint was affected.

### Recommended Remediation

* Remove the phishing email from the user's mailbox.
* Block the malicious URL.
* Investigate the recipient's endpoint.
* Search for similar emails across the organization.
* Check firewall and proxy logs.
* Identify whether credentials or personal information were submitted.
* Provide phishing-awareness guidance to the affected user.

---

# Alert 8816 — Malicious URL Blocked

## Alert Information

**Alert ID:** 8816
**Alert Type:** Access to Blacklisted External URL Blocked by Firewall
**Category:** Firewall
**Severity:** High
**Timestamp:** August 20, 2026 15:52
**Datasource:** Firewall

### Network Details

**Action:** Blocked

**Source IP:**
`10.20.2.17`

**Source Port:**
`34257`

**Destination IP:**
`67.199.248.11`

**Destination Port:**
`80`

**URL:**
`http://bit.ly/3sHkX3da12340`

**Application:** Web browsing

**Protocol:** TCP

**Firewall Rule:** Blocked Websites

---

## Correlation

This alert was correlated with Alert 8815.

The URL in the firewall event:

`http://bit.ly/3sHkX3da12340`

was the same URL contained in the Amazon phishing email.

This correlation provided evidence that a user on internal IP address `10.20.2.17` attempted to access the URL.

The firewall successfully blocked the connection.

### Classification

**True Positive**

### Reason for Classification

The user attempted to access a URL contained in a phishing email, and the firewall identified the destination as a known malicious or blacklisted website.

The connection was successfully blocked by the security control.

### Important SOC Observation

A blocked connection does not mean the event should be ignored.

The firewall prevented the connection, but the attempted access indicates that the user interacted with the phishing campaign and therefore requires investigation.

### Escalation Reason

The user attempted to access a known malicious URL. Although the firewall blocked the connection, the endpoint should be investigated for additional suspicious activity.

### Recommended Remediation

* Keep the malicious URL blocked.
* Investigate endpoint `10.20.2.17`.
* Identify the user associated with the source IP.
* Search for additional connections to related indicators.
* Search the environment for similar phishing emails.
* Review endpoint logs for suspicious activity.
* Educate the affected user about phishing.

---

# Alert 8817 — Microsoft Phishing Email

## Alert Information

**Alert ID:** 8817
**Alert Type:** Inbound Email Containing Suspicious External Link
**Category:** Phishing
**Severity:** Medium
**Timestamp:** August 20, 2026 15:53
**Datasource:** Email
**Direction:** Inbound

### Email Details

**Subject:**
`Unusual Sign-In Activity on Your Microsoft Account`

**Sender:**
`no-reply@m1crosoftsupport.co`

**Recipient:**
`c.allen@thetrydaily.thm`

**Attachment:** None

**URL:**
`https://m1crosoftsupport.co/login`

---

## Initial Analysis

The email contained multiple strong indicators of a phishing attack.

### Suspicious Indicators

#### 1. Typosquatting

The sender used:

`m1crosoftsupport.co`

instead of a legitimate Microsoft domain.

The domain uses the number `1` in place of the letter `i`:

`m1crosoft`

This is a common typosquatting technique used to make malicious domains look legitimate.

#### 2. Brand Impersonation

The attacker impersonated Microsoft's security team.

#### 3. Urgency

The email claimed that an unusual sign-in had occurred and instructed the user to secure the account immediately.

#### 4. Suspicious Login Page

The provided URL pointed to:

`https://m1crosoftsupport.co/login`

This could potentially be used to harvest Microsoft account credentials.

#### 5. Social Engineering

The attacker used fear of unauthorized account access to encourage the recipient to click the link.

### Classification

**True Positive**

### Reason for Classification

The email impersonated Microsoft and used a typosquatted domain to direct the recipient to a suspicious login page.

The combination of domain impersonation, urgency, account-security messaging, and a credential-oriented login URL is consistent with a phishing attack.

### Escalation Reason

The email could potentially lead to credential theft. Further investigation should determine whether the recipient accessed the URL or entered credentials.

### Recommended Remediation

* Block `m1crosoftsupport.co`.
* Remove the phishing email.
* Search for the domain across the organization.
* Check firewall/proxy logs for access attempts.
* Investigate the recipient endpoint if the URL was accessed.
* Reset credentials if credentials were submitted.
* Revoke active sessions/tokens if account compromise is suspected.
* Verify MFA protection.
* Search for similar phishing emails.

---

# Alert Classification Summary

| Alert | Type           | Classification | Key Finding                                   |
| ----- | -------------- | -------------- | --------------------------------------------- |
| 8815  | Phishing Email | True Positive  | Amazon impersonation + suspicious Bitly URL   |
| 8816  | Firewall       | True Positive  | User attempted to access the malicious URL    |
| 8817  | Phishing Email | True Positive  | Microsoft impersonation + typosquatted domain |

---

# Indicators of Compromise

## Domains

`m1crosoftsupport.co`

`amazon.biz`

## URLs

`http://bit.ly/3sHkX3da12340`

`https://m1crosoftsupport.co/login`

## IP Addresses

`10.20.2.17` — Internal source IP

`67.199.248.11` — Destination IP associated with the blocked request

`102.89.222.143` — IP address mentioned inside the phishing email

---

# Investigation Methodology

The investigation followed a basic SOC L1 incident-response workflow:

### Step 1 — Alert Triage

Reviewed the alert severity, datasource, timestamp, sender, recipient, subject, and message content.

### Step 2 — IoC Extraction

Extracted:

* Email addresses
* Domains
* URLs
* IP addresses
* Suspicious keywords
* Usernames
* Network destinations

### Step 3 — Phishing Analysis

Analyzed the emails for:

* Urgency
* Impersonation
* Suspicious domains
* External links
* Credential requests
* Social-engineering techniques
* Typosquatting

### Step 4 — SIEM Correlation

Correlated email alerts with firewall events.

Alert 8815 contained:

`http://bit.ly/3sHkX3da12340`

Alert 8816 showed an attempted connection to the same URL.

### Step 5 — Network Analysis

Reviewed:

* Source IP
* Destination IP
* Destination port
* Protocol
* URL
* Firewall action
* Firewall rule

### Step 6 — Classification

Determined whether activity represented:

**True Positive**

or

**False Positive**

### Step 7 — Escalation

Confirmed malicious activity was escalated for additional investigation.

### Step 8 — Remediation

Recommended blocking malicious indicators, investigating affected endpoints, removing phishing emails, and checking for additional compromise.

---

# MITRE ATT&CK Mapping

The observed activity can be associated with several MITRE ATT&CK techniques.

### T1566.002 — Phishing: Spearphishing Link

The attacker used malicious links in emails to persuade users to visit external websites.

### T1583.001 — Acquire Infrastructure: Domains

The use of suspicious/typosquatted domains can be associated with attacker-controlled domain infrastructure.

### T1036 — Masquerading

The attackers attempted to impersonate trusted organizations such as Amazon and Microsoft.

### T1204.001 — User Execution: Malicious Link

The phishing campaign attempted to convince users to click a malicious link.

---

# Key SOC Lessons

## 1. Alert Does Not Automatically Mean Attack

A SIEM alert is an indication that something requires investigation.

The analyst must validate the activity before determining whether it is malicious.

## 2. IoC Correlation Is Important

A suspicious email becomes much more significant when its URL appears in firewall or proxy logs.

## 3. Blocked Does Not Mean Ignore

A firewall blocking a malicious URL is a successful security control, but the user's attempted access still requires investigation.

## 4. Look for Relationships Between Alerts

Alert 8815 and Alert 8816 were related because they contained the same URL.

This type of correlation is an important SOC analyst skill.

## 5. Phishing Uses Social Engineering

The emails used:

* Urgency
* Fear
* Brand impersonation
* Account-security warnings
* Package-delivery problems

These techniques are designed to make users act without thinking.

---

# Final Incident Assessment

The investigation identified multiple phishing-related events in the simulated environment.

Alert 8815 was identified as a phishing email impersonating Amazon.

Alert 8816 provided network-level evidence that an internal endpoint attempted to access the URL contained in the phishing email. The firewall successfully blocked the request.

Alert 8817 was identified as another phishing campaign impersonating Microsoft through a typosquatted domain.

The investigation demonstrated the importance of correlating email security alerts with firewall and proxy logs to determine user interaction and potential compromise.

---

# Skills Demonstrated

Through this investigation, I practiced:

* SIEM alert triage
* SOC L1 investigation
* Phishing analysis
* Email header/content analysis
* IoC identification
* URL analysis
* Firewall log analysis
* Event correlation
* True Positive / False Positive classification
* Incident escalation
* Remediation planning
* MITRE ATT&CK mapping
* Security incident documentation

---

# Conclusion

This project provided practical experience investigating phishing alerts from a SOC analyst perspective.

The investigation demonstrated how a SOC analyst can move from an initial SIEM alert to identifying IoCs, correlating security events, determining the severity of an incident, confirming malicious activity, and recommending remediation.

The most important lesson was that effective SOC analysis requires **correlation and evidence**, rather than simply trusting the initial alert classification.

**Lab:** TryHackMe
**Focus:** SIEM / SOC L1 / Phishing Investigation
**Role:** SOC Analyst L1
**Status:** Completed
