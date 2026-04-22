# Bug Bounty Programs 

---

**BBP vs VDP**

These two terms are frequently confused but are distinct. A Vulnerability Disclosure Program provides a structured channel for third parties to report vulnerabilities — it establishes process but offers no monetary incentive. A Bug Bounty Program adds financial rewards for valid findings, which motivates active hunting rather than passive reporting. Understanding this distinction matters when evaluating whether a target organization offers compensation or merely acknowledgment.

---

**Private vs Public Programs**

Most programs begin private and transition to public once the organization's triage process is mature enough to handle volume. Private invitations on HackerOne are merit-based — track record, consistency of valid findings, and absence of violations all factor in. This creates an incentive structure where professional behavior and consistent output directly expand access to more programs. Some programs additionally require background checks before granting access.

---

**Parent/Child Programs**

Large organizations with subsidiaries may run linked programs where a shared bounty pool and security team cover both parent and child entities. This is relevant for scoping — a finding in a subsidiary may route through the parent program's triage team and bounty structure.

---

**Program Structure and Policy**

Reading the policy section thoroughly before testing is non-negotiable. Scope and out-of-scope definitions determine what is legally and programmatically permissible to test. Testing out-of-scope assets is a violation regardless of the severity of any finding, and violations affect future invitation eligibility. Vendor Response SLAs give a realistic timeline expectation, and eligibility criteria (such as first-reporter rules) define whether a duplicate finding yields any reward.

---

**Code of Conduct**

Violations accumulate and are tracked by platforms. Unprofessional communication, testing out-of-scope targets, or disclosing findings outside the responsible disclosure timeline are all recordable violations. The code of conduct is not advisory — it is the behavioral contract that governs participation. Hunters who violate it lose access to private programs and may be removed from the platform entirely.

---

**Finding Programs**

HackerOne's directory is the primary resource for identifying active programs. It also serves as a contact index for organizations that do not run formal bounty programs but do accept responsible disclosure reports. Filtering by program type, reward range, and response efficiency allows hunters to target programs that match their skill set and time investment expectations.
# Writing a Good Report 

**COMMANDS**

No commands are present because this section focuses entirely on reporting methodology rather than exploitation.

**PAYLOADS**

There are no payloads included. Instead, the emphasis is on documenting how a vulnerability can be reproduced via a Proof of Concept (POC), which would normally include payloads in real reports.

**ENDPOINTS**

Endpoints are indirectly referenced as part of the vulnerability title and description. A good report clearly identifies affected domains, parameters, or endpoints to help triage teams reproduce the issue.

**TOOLS**

The CVSS v3.1 Calculator is used to standardize vulnerability severity scoring. It ensures consistency across reports and helps organizations prioritize remediation based on risk.

**PARAMETERS**

These represent the CVSS Base Score metrics:

- Attack Vector defines how the attack is performed. The key answer to the lab question is **Adjacent (A)**, meaning the attacker must be on the same physical or logical network (e.g., LAN or VPN).
    
- Attack Complexity determines how difficult exploitation is
    
- Privileges Required specifies the level of access needed
    
- User Interaction indicates whether a victim must act
    
- Scope evaluates whether exploitation impacts other components
    
- Confidentiality, Integrity, and Availability define the impact on data and system behavior
    

These parameters combine to produce a severity score that reflects real-world risk.

**SNIPPETS**

No reusable snippets exist; the focus is on structuring a clear and effective report.

**DOM ELEMENTS**

No DOM-specific elements are discussed.

**CONCEPTUAL FLOW**

This section teaches how to write high-quality bug bounty reports. A strong report includes:

- A precise vulnerability title
    
- CWE classification and CVSS score
    
- A clear vulnerability description
    
- Step-by-step POC for reproducibility
    
- Impact analysis explaining business risk
    
- Optional remediation guidance
    

It also explains how CVSS metrics are used to quantify severity.

**ATTACKER MINDSET**

A skilled bug bounty hunter:

- Thinks like both an attacker and a developer
    
- Focuses on clarity and reproducibility
    
- Communicates impact in both technical and business terms
    
- Makes the triage team’s job easier
    

**COMMON PITFALLS**

- Missing or unclear reproduction steps
    
- Incorrect CVSS scoring
    
- Weak impact explanation
    
- Poor formatting or structure
    
- Overly technical reports without business context
    

**METHODOLOGY FIT**

This belongs to the **reporting phase** of the bug bounty process:

1. Discover vulnerability
    
2. Validate exploitation
    
3. Document clearly
    
4. Submit report


# Interacting with Organizations/BBP Hosts 

---

**After Submission: Do Nothing First**

The immediate instinct after submitting a report is to follow up, but the correct behavior is to wait. Security and triage teams operate under their own workloads and SLAs. Spamming them signals unprofessionalism and can damage the relationship. Many bug bounty programs publish response efficiency metrics that give a realistic expectation of turnaround time — these should be checked before deciding that a non-response is unreasonable.

---

**Escalating Non-Response**

If a reasonable amount of time passes without acknowledgment and the submission was made through a platform like HackerOne, the mediation resource in Output A is the appropriate escalation path. Contacting the organization through unofficial channels such as social media is explicitly discouraged and undermines the professional standing of the researcher.

---

**Tracking Communication**

Once the triage team responds, note the username of the team member who replies and tag them in all subsequent messages. This keeps the thread consistent and avoids the report being handed off between team members without context. All communication should remain within the official platform channel.

---

**Disagreements on Severity or Bounty**

Severity disputes are common. The correct approach is to walk the triage team through the CVSS calculator metric by metric — attack vector, complexity, privileges required, user interaction, and impact scores — explaining the rationale for each value chosen. This is a technical conversation, not an emotional one. If the disagreement involves the bounty amount, the program's published policy is the reference document. If both approaches fail to reach resolution, mediation is again the appropriate escalation.

---

**Professionalism as a Constant**

The section's underlying theme is that professional communication is not optional — it is part of what defines a credible security researcher. Remaining calm during disagreements, using official channels, and allowing reasonable time for responses are behaviors that build long-term reputation within the bug bounty community.

# Example 1: Reporting Stored XSS
---

**Vulnerability Mechanism**

The file upload feature at the path in Output A reflects the uploaded file's filename back to the browser and simultaneously stores it in the database without sanitization. Because the filename is user-controlled and unsanitized, any JavaScript embedded in the filename executes when the filename is rendered in another user's browser. This makes it both a reflected and stored XSS — reflected at upload time for the uploader, and stored for every other administrator who later views the file list.

---

**Why the Payload Works**

The payload in Output A breaks out of whatever HTML context the filename is rendered in using `">` to close any surrounding tag and attribute, then injects an SVG element with an `onload` event handler. When the browser parses the SVG, it fires the `onload` event and executes `alert(document.cookie)`, demonstrating that arbitrary JavaScript runs in the victim's session context. The `.docx` extension at the end makes the filename appear legitimate at a glance.

---

**Attack Chain**

A malicious administrator uploads a file with the crafted filename. The payload is stored in the database tied to that file entry. When any other administrator navigates to the file management page and the filename is rendered, their browser executes the injected script. Since all administrators can view all uploaded files, every administrator account is a potential victim — the attacker does not need to target a specific individual.

---

**CVSS Scoring Rationale**

Privileges Required is High because only an administrator can access the upload panel — this caps the score significantly. User Interaction is None because victims are affected simply by browsing a page they would routinely visit, requiring no deliberate action on their part. Scope is Changed because the vulnerable component is the web server while the impacted component is the victim's browser — a security boundary is crossed. Confidentiality and Integrity are both Low rather than High because XSS gives access to DOM content and session cookies but does not grant full control over all data on the system. Availability is None because XSS cannot deny service. These factors combine to produce a Medium score of 5.5 despite the attack being network-exploitable with no user interaction required.

---

**Reporting Value of This Example**

This report demonstrates how to document a multi-administrator impact scenario clearly. The POC section walks through two discrete steps — upload and trigger — with specific UI navigation paths, making independent reproduction straightforward. The impact section explicitly identifies who can be attacked (any administrator) and what the consequences are (credential theft, session hijacking, defacement, redirection), satisfying the requirement to articulate business impact beyond technical description.

# Example 2: Reporting CSRF 

---

**Vulnerability Mechanism**

The consumer registration endpoint accepts a POST request with no anti-CSRF token or other state-validation mechanism. Because the browser automatically attaches session cookies to any cross-origin request targeting the same domain, an attacker can craft an HTML page that submits a forged POST to `/consumer-registration` on behalf of any authenticated victim who visits it. The server cannot distinguish this forged request from a legitimate one because the session credentials are present.

---

**Attack Chain**

The attacker intercepts the legitimate registration request using a proxy to capture the exact parameters. A malicious HTML page is then crafted that auto-submits those same parameters to the target endpoint. The attacker delivers this page to an authenticated victim — via phishing, a malicious link, or by chaining with an XSS vulnerability (as noted in the report, referencing finding 6.1.1). When the victim's browser loads the page, the cross-site request fires silently, and a new fintech application is registered under the victim's account with the attacker's specified details, including the attacker's developer email, granting them access to the generated API key.

---

**XSS Chaining Note**

The report explicitly notes that this attack could occur entirely in the background if combined with an XSS vulnerability elsewhere in the application. XSS removes the User Interaction requirement — the attacker can inject the CSRF payload into a page the victim already trusts and visits, eliminating the need to trick the victim into clicking an external link. This is a critical observation for impact escalation.

---

**CVSS Scoring Rationale**

Privileges Required is None because the attacker needs no account to craft and deliver the malicious page. User Interaction is Required because the victim must visit the attacker's page, which caps the score. Scope is Unchanged because both the vulnerable and impacted component are the same web server — no security boundary is crossed. Confidentiality and Integrity are both Low because the attacker can create a resource and obtain an API key but cannot read arbitrary data or fully compromise the system. Availability is None. This produces a Medium score of 5.4, slightly lower than the stored XSS example due to the User Interaction requirement and Unchanged scope.

---

**Comparison with Stored XSS Example**

The two examples together illustrate how different vulnerability classes score similarly but for different reasons. The XSS scored 5.5 with High Privileges Required but No User Interaction and Changed Scope. The CSRF scores 5.4 with No Privileges Required but Required User Interaction and Unchanged Scope. Understanding these trade-offs is essential for accurately scoring findings rather than inflating or deflating severity.

# Example 3: Reporting RCE 

---

**Vulnerability Mechanism**

Java object deserialization vulnerabilities occur when an application accepts serialized Java objects from untrusted input and deserializes them without validation. During deserialization, Java executes gadget chains — sequences of method calls triggered automatically by the deserialization process. When the application uses a vulnerable library like Apache Commons Collections, attacker-controlled serialized objects can chain these gadgets to execute arbitrary OS commands. The vulnerability requires no authentication because the deserialization happens before any access control check.

---

**Identifying the Attack Surface**

The `rO0` header in Output A is the base64-encoded representation of the Java serialized object magic bytes (`AC ED`). Seeing this prefix in any HTTP request or response body is an immediate indicator that serialized Java objects are being transmitted. In this case, the WebSphere server on port 8880 was accepting SOAP requests containing such objects.

---

**Attack Chain**

A SOAP request is crafted containing a malicious serialized Java object. The object embeds a command — in this case `ping` directed at the attacker's host — wrapped in a gadget chain compatible with the Apache Commons Collections library version in use. When WebSphere deserializes the object, the gadget chain fires and the OS executes the embedded command. Success is confirmed by capturing the ICMP ping traffic arriving at the attacker's machine via Wireshark, proving the server executed the command and demonstrating outbound connectivity from the target.

---

**Why Ping as the POC Command**

Using `ping` rather than a reverse shell or destructive command is standard practice in bug bounty reporting. It demonstrates code execution conclusively — an ICMP packet arriving at the attacker's host can only originate from the server executing the command — while causing zero harm to the target system. Triage teams accept this as sufficient proof of RCE without requiring the researcher to demonstrate more impactful payloads.

---

**CVSS Scoring Rationale**

This scores 9.8 Critical because it is remotely exploitable with no authentication and no user interaction required, and successful exploitation yields full RCE — meaning all three impact dimensions (Confidentiality, Integrity, Availability) are High. The only reason it does not reach a perfect 10.0 is that Scope is Unchanged — the vulnerable component and the impacted component are both the same web server. If exploitation could affect additional components beyond the web server, Scope would be Changed and the score would reach 10.0.

---

**Comparison Across All Three Examples**

The three report examples together cover the full severity spectrum. CSRF at 5.4 is constrained by User Interaction Required and low impact values. Stored XSS at 5.5 gains from Changed Scope but is constrained by High Privileges Required. Java Deserialization RCE at 9.8 maximizes exploitability metrics (no privileges, no user interaction, network-accessible) and all three impact dimensions, held back only by Unchanged Scope. Understanding what drives each score up or down is the core skill for accurate CVSS assessment.



#

#
#
#
#
#