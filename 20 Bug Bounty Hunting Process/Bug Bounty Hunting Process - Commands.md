
# Bug Bounty Programs 
---

**Key URLs**

```
https://www.hackerone.com/
https://hackerone.com/bug-bounty-programs
https://hackerone.com/directory/programs
https://hackerone.com/alibaba?type=team
https://hackerone.com/amazonvrp?type=team
https://docs.hackerone.com/hackers/invitations.html
https://docs.hackerone.com/organizations/vdp-vs-bbp.html#gatsby-focus-wrapper
https://web.archive.org/web/20230128203334/https://docs.hackerone.com/programs/policy-and-scope.html
https://web.archive.org/web/20230128202952/https://docs.hackerone.com/programs/defining-scope.html
https://www.hacker101.com/resources/articles/code_of_conduct
https://docs.hackerone.com/en/articles/8368957-parent-child-programs
```

**Bug Bounty Program Elements**

```
Vendor Response SLAs
Access
Eligibility Criteria
Responsible Disclosure Policy
Rules of Engagement
Scope
Out of Scope
Reporting Format
Rewards
Safe Harbor
Legal Terms and Conditions
Contact Information
```

# Writing a Good Report 


**CVSS v3.1 Calculator**

```
https://www.first.org/cvss/calculator/3.1
```

**CWE Reference**

```
https://cwe.mitre.org/
```

**CVSS Reference**

```
https://www.first.org/cvss/
```

**HackerOne Report Submission Docs**

```
https://docs.hackerone.com/hackers/submitting-reports.html
```

**Good Report Examples**

```
https://hackerone.com/reports/341876
https://hackerone.com/reports/783877
https://hackerone.com/reports/520518
https://hackerone.com/reports/980511
https://hackerone.com/reports/691611
https://hackerone.com/reports/474656
```

**Bug Report Elements**

```
Vulnerability Title
CWE & CVSS score
Vulnerability Description
Proof of Concept (POC)
Impact
Remediation
```

**CVSS Base Score Metrics**

```
Attack Vector:        Network (N) / Adjacent (A) / Local (L) / Physical (P)
Attack Complexity:    Low (L) / High (H)
Privileges Required:  None (N) / Low (L) / High (H)
User Interaction:     None (N) / Required (R)
Scope:                Unchanged (U) / Changed (C)
Confidentiality:      None (N) / Low (L) / High (H)
Integrity:            None (N) / Low (L) / High (H)
Availability:         None (N) / Low (L) / High (H)
```

**CVSS Example 1**

```
Title:               Cisco ASA Software IKEv1 and IKEv2 Buffer Overflow Vulnerability (CVE-2016-1287)
CVSS 3.1 Score:      9.8 (Critical)
Attack Vector:       Network
Attack Complexity:   Low
Privileges Required: None
User Interaction:    None
Scope:               Unchanged
Confidentiality:     High
Integrity:           High
Availability:        High
```

**CVSS Example 2**

```
Title:               Stored XSS in an admin panel (Malicious Admin -> Admin)
CVSS 3.1 Score:      5.5 (Medium)
Attack Vector:       Network
Attack Complexity:   Low
Privileges Required: High
User Interaction:    None
Scope:               Changed
Confidentiality:     Low
Integrity:           Low
Availability:        None
```

---

---


# Interacting with Organizations/BBP Hosts 

**Mediation Resource**

```
https://docs.hackerone.com/hackers/hacker-mediation.html
```

# Example 1: Reporting Stored XSS 

---

**XSS Payload (Filename)**

```
`"><svg onload = alert(document.cookie)>.docx
```

**CWE Reference**

```
https://cwe.mitre.org/data/definitions/79.html
```

**Report Fields**

```
Title:       Stored Cross-Site Scripting (XSS) in X Admin Panel
CWE:         CWE-79: Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting')
CVSS Score:  5.5 (Medium)
```

**CVSS Breakdown**

```
Attack Vector:        Network
Attack Complexity:    Low
Privileges Required:  High
User Interaction:     None
Scope:                Changed
Confidentiality:      Low
Integrity:            Low
Availability:         None
```

**Injection Point**

```
Admin Info -> Secure Data Transfer -> Load of Data
```

---

---


# Example 2: Reporting CSRF 

---

**CWE Reference**

```
https://cwe.mitre.org/data/definitions/352.html
```

**Report Fields**

```
Title:       Cross-Site Request Forgery (CSRF) in Consumer Registration
CWE:         CWE-352: Cross-Site Request Forgery (CSRF)
CVSS Score:  5.4 (Medium)
```

**Vulnerable Endpoint**

```
POST /consumer-registration
```

**Vulnerable Parameters**

```
app type
name
developer email
description
```

**CVSS Breakdown**

```
Attack Vector:        Network
Attack Complexity:    Low
Privileges Required:  None
User Interaction:     Required
Scope:                Unchanged
Confidentiality:      Low
Integrity:            Low
Availability:         None
```

---

---


# Example 3: Reporting RCE 

---

**CWE Reference**

```
https://cwe.mitre.org/data/definitions/502.html
```

**Report Fields**

```
Title:       IBM WebSphere Java Object Deserialization RCE
CWE:         CWE-502: Deserialization of Untrusted Data
CVSS Score:  9.8 (Critical)
```

**Target Port**

```
8880
```

**Serialized Java Object Identifier (base64 header)**

```
rO0
```

**Command Executed via Payload**

```
ping
```

**Vulnerable Library**

```
Apache Commons Collections (ACC)
```

**Protocol**

```
HTTPS
SOAP
```

**Traffic Capture Tool**

```
Wireshark
```

**CVSS Breakdown**

```
Attack Vector:        Network
Attack Complexity:    Low
Privileges Required:  None
User Interaction:     None
Scope:                Unchanged
Confidentiality:      High
Integrity:            High
Availability:         High
```

---

---


#
#
#
#