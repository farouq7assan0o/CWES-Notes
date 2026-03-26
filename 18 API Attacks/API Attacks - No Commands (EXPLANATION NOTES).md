# Introduction to API Attacks 

## Purpose of This Section

This section introduces the fundamentals of **Application Programming Interfaces (APIs)** and why they are a major attack surface in modern applications. Before performing attacks, it is necessary to understand **how APIs are designed, how they communicate, and what common weaknesses exist**.

APIs act as **communication layers between systems**, enabling applications, mobile apps, web front-ends, and services to exchange data over the internet.

Because APIs often expose **direct access to backend logic and data**, they are highly attractive targets for attackers.

---

## API Architecture Styles

### REST

REST is the **most widely used API architecture**.

It operates using the **HTTP protocol** and standard methods such as:

- Retrieve data
    
- Create resources
    
- Update resources
    
- Delete resources
    

REST APIs are **stateless**, meaning each request must contain all information needed to process it. Responses are typically returned in **JSON or XML**.

From an attacker’s perspective, REST APIs expose:

- predictable endpoints
    
- structured data
    
- parameterized requests
    

These characteristics make them ideal targets for:

- IDOR attacks
    
- authentication bypass
    
- parameter manipulation
    
- mass assignment
    
- rate limit abuse
    

This module focuses primarily on attacking **REST APIs**.

---

### SOAP

SOAP APIs exchange data using **XML messages**.

They provide:

- built-in standards
    
- strong message structure
    
- enterprise-level security features
    

However, SOAP APIs tend to be **more complex**, which often introduces:

- XML parsing vulnerabilities
    
- XML External Entity (XXE) attacks
    
- misconfigured authentication
    

---

### GraphQL

GraphQL is designed to make data retrieval more flexible.

Instead of returning predefined responses like REST, a client **specifies exactly which fields it wants**.

This means:

- one endpoint handles all queries
    
- responses are dynamically generated
    

From an attacker's perspective, GraphQL can expose:

- excessive data through queries
    
- introspection leaks
    
- authorization flaws
    
- nested query abuse
    

---

### gRPC

gRPC is a **high-performance API framework** that uses **Protocol Buffers** for communication.

It is commonly used in:

- microservices
    
- distributed systems
    
- internal service communication
    

While faster and efficient, gRPC APIs can still suffer from:

- authentication flaws
    
- authorization issues
    
- misconfigured services
    

---

## Why APIs Are a Large Attack Surface

APIs are widely used because they enable:

- mobile applications
    
- microservices
    
- third-party integrations
    
- web frontends
    

However, this also means APIs often expose:

- sensitive data
    
- internal logic
    
- backend functionality
    

Attackers frequently target APIs because they:

- bypass frontend restrictions
    
- interact directly with backend services
    
- reveal internal data structures
    

---

## OWASP API Security Top 10

To standardize API security risks, OWASP created the **API Security Top 10**.

These categories represent the **most common API vulnerabilities**.

---

### API1: Broken Object Level Authorization

Occurs when APIs allow users to access **objects they should not have permission to view**.

Example attacker mindset:

If an API request retrieves:

```
/api/user/123
```

Changing the ID may expose another user:

```
/api/user/124
```

This is a classic **IDOR vulnerability**.

---

### API2: Broken Authentication

Weak authentication allows attackers to **bypass login protections**.

Examples include:

- predictable tokens
    
- weak JWT validation
    
- exposed API keys
    
- session mismanagement
    

---

### API3: Broken Object Property Level Authorization

Even when users are authenticated, APIs may expose **sensitive fields**.

Example:

- admin flags
    
- internal IDs
    
- hidden attributes
    

Attackers can also manipulate these properties when updating objects.

---

### API4: Unrestricted Resource Consumption

APIs that do not implement **rate limiting** can be abused.

Attackers can perform:

- brute-force attacks
    
- enumeration
    
- denial-of-service attempts
    

---

### API5: Broken Function Level Authorization

APIs may expose **admin-level functionality** without proper authorization checks.

For example:

- normal users accessing admin endpoints
    
- role-based access control failures
    

---

### API6: Unrestricted Access to Sensitive Business Flows

Business logic can sometimes be abused.

Examples:

- bypassing payment flows
    
- manipulating order quantities
    
- abusing refund mechanisms
    

These vulnerabilities often lead to **financial losses**.

---

### API7: Server-Side Request Forgery (SSRF)

Occurs when APIs fetch remote resources based on user input.

Attackers may trick the API into accessing:

- internal services
    
- cloud metadata endpoints
    
- internal admin panels
    

---

### API8: Security Misconfiguration

Common configuration issues include:

- debug mode enabled
    
- verbose error messages
    
- exposed documentation
    
- injection vulnerabilities
    

---

### API9: Improper Inventory Management

Organizations sometimes leave **old API versions active**.

Attackers target:

- deprecated endpoints
    
- forgotten test APIs
    
- legacy versions with weaker security
    

---

### API10: Unsafe Consumption of APIs

APIs that integrate with external APIs may trust **unverified responses**, leading to security issues.

This can allow attackers to manipulate upstream data.

---

## What This Module Will Teach

The module focuses on **practical exploitation of API vulnerabilities**.

You will learn how to:

- discover API endpoints
    
- analyze API requests
    
- manipulate parameters
    
- bypass authorization
    
- exploit business logic flaws
    
- abuse API authentication mechanisms
    

Each section will demonstrate **real attack techniques aligned with the OWASP API Security Top 10**.

# Broken Object Level Authorization

## Concept: Broken Object Level Authorization (BOLA)

Broken Object Level Authorization (BOLA) occurs when an API **fails to verify whether the authenticated user has permission to access a specific object**.

It is the **most common API vulnerability** and corresponds to **CWE-639: Authorization Bypass Through User-Controlled Key**.

The issue arises when an API allows users to request resources by directly providing identifiers such as:

- integer IDs
    
- UUIDs / GUIDs
    
- object keys
    

If the server does not validate ownership or authorization, attackers can simply **change the identifier to access other users’ data**.

This is the API equivalent of **IDOR (Insecure Direct Object Reference)**.

---

## Lab Scenario

You are given credentials belonging to a **supplier account**.

The goal is to determine **what API data the user can improperly access**.

The login process uses a supplier authentication endpoint that returns a **JWT token**.

Once authenticated, the JWT is used in requests through the **Authorization header**.

The Swagger interface provides an **Authorize button** where the token can be inserted.

When authentication succeeds, Swagger automatically attaches the token to future requests.

---

## Understanding the Current User Endpoints

Several endpoints include the keyword **current-user**.

These endpoints retrieve information about the authenticated account based on the **JWT token** rather than user-supplied identifiers.

For example:

- retrieving supplier information
    
- retrieving company information
    
- retrieving assigned roles
    

These endpoints reveal key details needed for the attack.

One of the returned values is the **supplier company ID**, which uniquely identifies the company associated with the logged-in user.

---

## Role Discovery

Another endpoint reveals the **roles assigned to the authenticated user**.

The response shows a role named:

SupplierCompanies_GetYearlyReportByID

This role indicates that the user is allowed to access an endpoint that retrieves **yearly company reports**.

The naming convention used by the application directly maps roles to endpoints.

---

## Vulnerable Endpoint Discovery

Inside the **Supplier-Companies** endpoint group, there is a report retrieval endpoint.

This endpoint accepts a numeric **ID parameter**.

The report ID is a simple integer rather than a GUID.

This design is a major red flag during API testing because:

- sequential integers are easy to enumerate
    
- authorization checks may be missing
    

---

## Exploiting the BOLA Vulnerability

When requesting a report using an integer ID, the API returns a report belonging to **another supplier company**.

The company ID returned in the response does not match the company ID of the authenticated user.

This proves that the API **does not verify ownership of the requested resource**.

Because of this missing validation, the user can access reports from **any supplier company**.

---

## Mass Exploitation

Since the endpoint uses sequential IDs, the vulnerability can be abused at scale.

A loop can be used to automatically request multiple report IDs.

This allows attackers to collect a large number of sensitive records quickly.

The example demonstrates retrieving the **first twenty reports** by iterating through IDs.

The output is formatted with a JSON processor to make it readable.

In real-world attacks, this technique is used to:

- scrape databases
    
- collect financial data
    
- enumerate users or resources
    

---

## Why the Vulnerability Exists

The application performs **authentication but not proper authorization**.

The server verifies that the request is coming from an authenticated user with the required role.

However, it **does not verify that the requested report belongs to the user's company**.

Because the ID is user-controlled, the attacker can access any record.

---

## Proper Mitigation

To prevent this vulnerability, the API must enforce **object-level authorization checks**.

Before returning a report, the server should:

1. Extract the authenticated user's company ID from the JWT.
    
2. Compare it with the company ID associated with the requested report.
    
3. Only return the report if both IDs match.
    

If they do not match, the request should be denied.

This ensures that each supplier can only access **their own company data**.

---

## Pentester Mindset

When testing APIs, always look for endpoints that include:

- object identifiers
    
- record IDs
    
- UUID parameters
    
- resource IDs
    

If changing the identifier returns another user's data, it indicates a **BOLA vulnerability**.

Typical high-value targets include:

- financial records
    
- user profiles
    
- reports
    
- orders
    
- invoices
    

These vulnerabilities often lead to **major data breaches**.

# Broken Authentication

## Concept: Broken Authentication

Broken Authentication occurs when the authentication mechanism of an API can be **bypassed, brute-forced, or manipulated** to gain unauthorized access.

In this lab the vulnerability maps to:

**CWE-307 — Improper Restriction of Excessive Authentication Attempts**

This happens when an application **does not limit login attempts**, allowing attackers to attempt thousands of passwords without being blocked.

---

## Initial Access and JWT Authentication

The tester begins with credentials for a **customer account**.

After logging in through the customer authentication endpoint, the API returns a **JWT token**.

The JWT represents the authenticated session and is used to access protected endpoints such as:

- retrieving user data
    
- retrieving roles
    
- interacting with customer resources
    

The token is inserted into Swagger through the **Authorize button**, allowing authenticated requests.

---

## Understanding the Assigned Roles

After authentication, the endpoint that returns the user's roles reveals three permissions:

- ability to update their own account
    
- ability to retrieve customer records
    
- ability to retrieve all customers
    

The endpoint that retrieves all customers returns a list of all registered users.

Although this exposes sensitive data like emails and phone numbers, it is not directly exploitable for account takeover.

However, it **provides a valuable target list** for brute-force attacks.

---

## Weak Password Policy

The endpoint used to update account information allows the password to be changed.

Testing this endpoint reveals the password requirements.

The API only enforces:

- minimum password length of six characters
    

This is considered **extremely weak security**.

When a simple password like "123456" is accepted, it confirms that users are allowed to set easily guessable passwords.

Weak password policies dramatically increase the success rate of brute-force attacks.

---

## Brute-Forcing Customer Accounts

Because the API does not implement login attempt restrictions, it becomes possible to brute-force passwords.

The brute-force attack uses a tool designed for web fuzzing.

Two parameters are tested simultaneously:

- customer email
    
- password
    

A large password wordlist is used to test thousands of potential passwords against multiple accounts.

The attacker filters out responses containing the failure message.

Any response that does not contain this failure indicator signals a **successful login**.

This technique eventually reveals the password for one of the target accounts.

---

## Account Takeover

Once valid credentials are discovered, the attacker can authenticate through the API and receive a **JWT token belonging to the compromised user**.

With this token, the attacker gains full access to the victim's account.

This allows access to:

- personal details
    
- account data
    
- potentially financial information
    

In real-world scenarios this could lead to:

- identity theft
    
- payment fraud
    
- unauthorized purchases
    

---

## Other Authentication Attack Vectors

If password brute forcing is not feasible, attackers may target other authentication mechanisms such as:

### OTP Brute Force

If one-time passwords are short or numeric, attackers can attempt many combinations unless rate limiting is enforced.

### Security Question Guessing

Security questions often have predictable answers such as:

- birthplace
    
- school name
    
- pet name
    

These answers can sometimes be found through social media or guessing.

---

## Proper Security Mitigations

To prevent Broken Authentication vulnerabilities, the API should implement several protections.

### Rate Limiting

Limit login attempts per:

- IP address
    
- user account
    
- time window
    

This prevents automated brute-force attacks.

### Strong Password Policies

Secure APIs enforce strong password requirements such as:

- minimum length of twelve characters
    
- uppercase and lowercase letters
    
- numbers and special characters
    
- rejection of common passwords
    
- prevention of password reuse
    

### Multi-Factor Authentication (MFA)

Adding an additional verification step such as an OTP significantly increases account security.

Even if a password is compromised, attackers cannot log in without the second authentication factor.

---

## Pentester Mindset

When testing APIs, authentication endpoints are always high-value targets.

Look for signs such as:

- unlimited login attempts
    
- weak password policies
    
- exposed user email lists
    
- predictable authentication flows
    

Combining these weaknesses often leads to **successful account compromise**.

In many real-world breaches, attackers exploit exactly this combination:

1. user enumeration
    
2. weak password policy
    
3. lack of rate limiting
    
4. brute-force automation.


# Broken Object Property Level Authorization

## Concept Overview

Broken Object Property Level Authorization occurs when an API **improperly exposes or allows modification of object properties that should not be accessible to the user**.

This category includes two major vulnerability types:

1. **Excessive Data Exposure**
    
2. **Mass Assignment**
    

Both occur because APIs fail to properly control **which fields are visible or editable by different roles**.

---

## Excessive Data Exposure

## What It Means

Excessive Data Exposure happens when an API returns **more data fields than necessary** to the client.

Even if the user is authenticated and authorized to access a resource, they may receive **sensitive attributes that should remain hidden**.

This vulnerability maps to:

**CWE-213 — Exposure of Sensitive Information Due to Incompatible Policies**

---

## Scenario in the Lab

You authenticate as a **customer**.

The roles assigned allow viewing supplier information.

In an e-commerce system this is normal because customers must be able to:

- browse suppliers
    
- view available products
    
- compare vendors
    

However, when retrieving the supplier list, the API response includes additional fields such as:

- supplier email
    
- supplier phone number
    

These fields should **not be visible to customers**.

---

## Why This Is Dangerous

Customers could now bypass the marketplace by contacting suppliers directly.

This allows them to:

- negotiate lower prices
    
- avoid platform transaction fees
    
- create off-platform deals
    

This harms the business model of the marketplace.

From a security perspective, it is also a **privacy leak of supplier contact information**.

---

## Proper Mitigation

The API should return only the fields required for the customer interface.

This is typically solved by implementing **response DTOs (Data Transfer Objects)**.

DTOs act as **safe response models** that expose only approved fields.

For example, the response could include only:

- supplier name
    
- supplier ID
    
- company information
    

Sensitive attributes such as email or phone numbers should be excluded.

---

## Mass Assignment

## What It Means

Mass Assignment occurs when APIs allow users to **modify object properties that they should not control**.

This vulnerability maps to:

**CWE-915 — Improperly Controlled Modification of Dynamically Determined Object Attributes**

Many frameworks automatically bind incoming JSON fields to backend objects.

If developers do not restrict which fields are allowed, attackers can **modify internal properties**.

---

## Scenario in the Lab

You authenticate as a **supplier**.

Your company profile includes a field indicating whether the company must pay marketplace fees.

The field:

isExemptedFromMarketplaceFee

is initially set to **false**.

This means the supplier must pay the platform commission on every sale.

---

## Vulnerable Update Endpoint

The API provides an endpoint that allows suppliers to update their company information.

However, the update request accepts the field controlling marketplace fee exemption.

Because of this mistake, suppliers can change the value of this property.

---

## Exploitation Impact

By modifying this field, the supplier can mark their company as **exempt from marketplace fees**.

This allows them to:

- sell products through the marketplace
    
- avoid paying the platform commission
    

From a security perspective, this is a **business logic manipulation attack**.

It does not expose data but directly affects **platform revenue**.

---

## Proper Mitigation

The API should restrict which fields suppliers are allowed to modify.

This is typically implemented using **request DTOs**.

Instead of binding the entire company object, the update endpoint should allow only fields such as:

- company name
    
- contact details
    
- certificate URI
    

Sensitive fields like:

- fee exemptions
    
- internal flags
    
- financial properties
    

should never be modifiable by suppliers.

---

## Pentester Mindset

When testing APIs for property-level authorization flaws, always inspect:

### Response Data

Look for fields such as:

- email addresses
    
- phone numbers
    
- internal IDs
    
- financial metrics
    
- admin flags
    

If unnecessary sensitive data appears, it indicates **Excessive Data Exposure**.

---

### Update Requests

Look for endpoints that accept large JSON objects.

Test whether you can modify fields such as:

- roles
    
- admin flags
    
- verification status
    
- account balances
    
- payment settings
    

If these fields can be changed, the API is vulnerable to **Mass Assignment**.


# Unrestricted Resource Consumption 

## Concept: Unrestricted Resource Consumption

This vulnerability occurs when an API fails to **limit how much resources a user can consume**.

These resources include:

- disk storage
    
- CPU
    
- memory
    
- bandwidth
    

It maps to:

**CWE-400 — Uncontrolled Resource Consumption**

The main issue is the absence of:

- file size validation
    
- file type validation
    
- rate limiting
    

---

## File Upload Abuse

The lab focuses on a file upload endpoint that allows suppliers to upload a **certificate of incorporation**.

The endpoint stores uploaded files **permanently on disk**.

This creates two major attack surfaces:

1. **Storage exhaustion (DoS)**
    
2. **Malicious file upload**
    

---

## Large File Upload (DoS Vector)

The attacker generates a large file filled with random data.

This file is uploaded successfully because:

- there is **no file size restriction**
    
- the backend stores files indefinitely
    

By repeatedly uploading large files, an attacker can:

- fill disk storage
    
- degrade performance
    
- crash the system
    

This leads to **financial and operational damage**.

---

## File Type Validation Bypass

The endpoint does not validate file extensions.

Even though it expects a PDF, it accepts executable files.

This allows attackers to upload:

- executables
    
- scripts
    
- malware
    

This is dangerous because it enables **payload delivery through the API itself**.

---

## Public File Exposure (wwwroot Abuse)

Uploaded files are stored inside:

wwwroot/SupplierCompaniesCertificatesOfIncorporations

In ASP.NET Core, the **wwwroot directory is publicly accessible by default**.

This means:

- uploaded files can be directly downloaded via URL
    
- no authentication is required to access them
    

This creates multiple risks:

### Malware Hosting

Attackers can use the API as a **malware distribution server**.

### Sensitive Data Exposure

If file names are predictable or enumerable, attackers can:

- access other users' uploaded files
    
- retrieve sensitive documents
    

---

## Remote Code Execution Risk (Indirect)

If a system administrator downloads and executes a malicious file:

- the attacker may gain system access
    
- reverse shells could be triggered
    

This is a **social engineering + file upload attack chain**.

---

## Additional Abuse: Enumeration

If attackers can guess or brute-force file names, they can:

- enumerate stored files
    
- access internal documents
    
- scrape sensitive data
    

This turns the API into a **public data leak vector**.

---

## Proper Mitigation

### File Size Validation

Limit upload size to a strict threshold.

This prevents:

- storage exhaustion
    
- DoS attacks
    

---

### File Type Validation

Validate:

- file extension
    
- MIME type
    
- actual file content
    

Only allow safe file types such as:

- PDFs
    
- images
    

---

### Storage Security

Do not store files in publicly accessible directories like wwwroot.

Instead:

- store files outside the web root
    
- require authentication for access
    

---

### Antivirus Scanning

Scan uploaded files using tools such as:

- ClamAV
    

This helps detect:

- malware
    
- suspicious payloads
    

---

### Rate Limiting

Limit how frequently users can upload files.

This prevents:

- automated abuse
    
- mass uploads
    

---

## Pentester Mindset

When testing file upload endpoints, always check:

### Size Controls

Try uploading:

- very large files
    
- repeated uploads
    

---

### File Type Restrictions

Try uploading:

- executables
    
- scripts
    
- unexpected formats
    

---

### File Accessibility

Check if uploaded files are:

- publicly accessible
    
- predictable in URL structure
    

---

### Storage Location

If files are stored in:

- /uploads
    
- /static
    
- /wwwroot
    

they are often directly accessible.

---

These vulnerabilities are highly impactful because they can lead to:

- denial of service
    
- malware distribution
    
- data leakage
    
- potential system compromise


# Broken Function Level Authorization 

## Concept: Broken Function Level Authorization (BFLA)

Broken Function Level Authorization occurs when an API allows users to **access endpoints (functions) they are not authorized to use**.

Key distinction:

- **BOLA** → user accesses data they shouldn't
    
- **BFLA** → user accesses functionality they shouldn't
    

This vulnerability maps to:

**CWE-200 — Exposure of Sensitive Information to an Unauthorized Actor**

---

## Scenario Overview

You authenticate as a **customer user**.

After logging in, you retrieve your assigned roles.

The API returns that the user has:

- **no roles assigned**
    

This is important because it means the user should have **minimal or no access to protected endpoints**.

---

## Identifying Target Endpoints

While exploring the API, you look for endpoints that:

- require authentication
    
- specify role requirements
    
- return sensitive or business-critical data
    

One such endpoint is:

- retrieving all product discounts
    

This endpoint explicitly requires a role that the user **does not have**.

---

## Exploitation

Despite lacking the required role, when the endpoint is invoked:

- the API still returns valid data
    
- no authorization check is enforced
    

This confirms that:

- authentication exists (JWT required)
    
- authorization is **not enforced properly**
    

This is the core of BFLA.

---

## Why This Happens

The developers:

- defined role requirements in documentation or Swagger
    
- but **did not implement enforcement in backend code**
    

This results in:

- security being assumed
    
- but not actually applied
    

---

## Impact

This vulnerability allows unauthorized users to access:

- sensitive business data
    
- privileged operations
    
- internal system functionality
    

In this case:

- product discount data is exposed
    

In real-world systems, BFLA can lead to:

- admin panel access
    
- financial data exposure
    
- user management abuse
    
- system configuration changes
    

---

## Proper Mitigation

To fix BFLA, the API must enforce **server-side authorization checks**.

This includes:

1. Validating the user's roles from the JWT
    
2. Checking if the required role is present
    
3. Denying access if the role is missing
    

Authorization must always be enforced **in backend logic**, not just in:

- frontend restrictions
    
- Swagger documentation
    

---

## Pentester Mindset

When testing APIs for BFLA:

### Look for Role-Protected Endpoints

Endpoints that:

- specify required roles
    
- handle sensitive data
    
- perform privileged actions
    

---

### Compare Roles vs Access

Check:

- what roles the user has
    
- what endpoints they can still access
    

If access is granted without proper roles → vulnerability.

---

### Always Test Anyway

Even if an endpoint says:

- "requires admin"
    
- "requires specific role"
    

Always try calling it.

Never trust:

- documentation
    
- Swagger labels
    
- frontend restrictions
    

---

BFLA vulnerabilities are extremely common because developers often:

- implement authentication correctly
    
- but forget to enforce authorization consistently across endpoints

# Unrestricted Access to Sensitive Business Flows

## Concept Overview

Unrestricted Access to Sensitive Business Flows occurs when an API exposes **critical business logic or data** that can be abused to gain unfair advantage or cause financial damage.

This is not just about data exposure — it’s about **how that data enables abuse of the system’s business model**.

---

## Connection to Previous Vulnerability

This section builds directly on the previous vulnerability:

- **Broken Function Level Authorization (BFLA)** allowed access to product discounts
    
- That data is **business-sensitive**, not just informational
    

Now the impact becomes clearer.

---

## Why This Is a Business Logic Flaw

The discounts endpoint reveals:

- product IDs
    
- discount percentages
    
- start and end dates
    

This allows attackers to:

- identify high-value discounts
    
- time purchases precisely
    
- maximize profit
    

Example attack:

- wait for a 70% discount
    
- buy large quantities
    
- resell later at full price
    

This directly impacts the platform’s **revenue and pricing strategy**.

---

## Chaining Vulnerabilities

This vulnerability is powerful because it is **not standalone**.

It becomes dangerous when combined with other issues:

### With BFLA

Unauthorized users gain access to discount data.

### With Unrestricted Resource Consumption

Attackers can:

- automate bulk purchases
    
- exhaust inventory
    
- dominate supply
    

---

## Extending the Attack (Lab Context)

The lab question pushes you to go further.

You are given:

- a specific **user ID**
    

Your task is to retrieve:

- the **street address** of that user
    

This implies:

- there exists an endpoint exposing customer data
    
- access control is weak or missing
    

---

## Attacker Mindset

At this stage, the attacker thinks:

1. I already have unauthorized access to sensitive business data
    
2. What other endpoints expose related data?
    
3. Can I pivot into user data?
    

Typical next steps:

- enumerate customer endpoints
    
- query users by ID
    
- inspect returned fields
    

If sensitive data like **addresses** is returned → vulnerability confirmed.

---

## Why This Happens

Developers often:

- focus on functionality
    
- overlook how data can be abused
    

They may assume:

- “this data is harmless”
    

But in reality:

- combining multiple data points enables exploitation
    

---

## Impact

This vulnerability can lead to:

- financial abuse (reselling, arbitrage)
    
- privacy violations (user data exposure)
    
- market manipulation
    
- business model bypass
    

---

## Proper Mitigation

### Restrict Access to Business-Sensitive Data

Endpoints exposing:

- discounts
    
- pricing strategies
    
- inventory
    

should be limited to:

- authorized roles
    
- internal users
    

---

### Apply Least Privilege

Users should only access:

- data necessary for their role
    
- no internal or strategic information
    

---

### Combine Authorization + Business Logic Controls

Security should not only check:

- “can the user access this endpoint?”
    

But also:

- “should the user be allowed to use this business feature?”
    

---

## Pentester Mindset

When testing APIs, always ask:

- Does this data affect business decisions?
    
- Can this data be abused for profit?
    
- Can I chain this with other vulnerabilities?
    

The most impactful API bugs are often **business logic flaws**, not just technical misconfigurations.

# Server Side Request Forgery 

## Concept: SSRF (Server-Side Request Forgery)

SSRF occurs when a server **fetches a resource based on user-controlled input without validation**.

Instead of the attacker directly accessing internal resources, they trick the **server itself** into doing it.

This bypasses:

- firewalls
    
- internal network protections
    
- access controls
    

This vulnerability maps to:

**CWE-918 — Server-Side Request Forgery**

---

## Core Weakness in This Lab

The API allows users to modify a field:

certificateOfIncorporationPDFFileURI

This field is supposed to point to a **valid uploaded file**.

However:

- it accepts arbitrary URIs
    
- no validation is performed
    

This turns it into an SSRF sink.

---

## Attack Flow

### Step 1 — Authentication

You authenticate as a supplier and obtain a JWT.

---

### Step 2 — Identify Controllable Field

The update endpoint allows modifying the certificate file URI.

This is a red flag because:

- file paths should not be user-controlled
    
- especially not URIs
    

---

### Step 3 — Inject Malicious URI

Instead of a valid file path, you supply:

- a local file URI
    

This instructs the server to fetch a file from its own filesystem.

---

### Step 4 — Trigger File Retrieval

Another endpoint retrieves the certificate file.

The server:

- reads the file from the URI
    
- encodes it (base64)
    
- returns it in the response
    

---

### Step 5 — Extract Data

The response contains base64 data.

Decoding it reveals:

- contents of system files
    

This confirms successful SSRF exploitation.

---

## Why This Works

The backend:

- trusts user input
    
- does not validate URI schemes
    
- allows access to local file paths
    

As a result, the attacker can read:

- system files
    
- configuration files
    
- sensitive data
    

---

## Targeting Sensitive Files

Common targets include:

- system user files
    
- password hashes
    
- configuration files
    

In the lab, the goal is to retrieve:

- a specific flag file
    

---

## Impact

SSRF can lead to:

- local file disclosure
    
- internal network access
    
- cloud metadata exposure
    
- credential theft
    
- full system compromise
    

In advanced scenarios, SSRF can be chained into:

- remote code execution
    
- privilege escalation
    

---

## Proper Mitigation

### Strict Input Validation

Only allow:

- predefined paths
    
- trusted file locations
    

Block:

- file://
    
- http:// (external if not needed)
    
- internal IP ranges
    

---

### Whitelisting

Restrict URIs to:

- specific directories
    
- known safe sources
    

---

### Output Restrictions

The file retrieval endpoint should:

- only serve files from allowed directories
    
- reject anything outside the intended path
    

---

### Defense in Depth

Even if one endpoint fails validation:

- downstream endpoints must enforce restrictions
    

---

## Pentester Mindset

When testing for SSRF, always look for:

### User-Controlled URLs or Paths

Fields like:

- file paths
    
- image URLs
    
- document links
    

---

### Fetching Behavior

If the server:

- retrieves external or local resources
    
- processes URIs
    

→ test for SSRF

---

### Try Different URI Schemes

Common attack vectors:

- local file access
    
- internal services
    
- cloud metadata endpoints
    

---

SSRF is one of the most powerful API vulnerabilities because it turns the server into a **proxy attacker inside the infrastructure**.


# Security Misconfiguration 

## Concept: Security Misconfiguration

Security Misconfiguration refers to improperly configured systems that expose vulnerabilities.

In this section, the focus is on:

- **SQL Injection (CWE-89)**
    
- **Insecure HTTP headers**
    

---

## SQL Injection in APIs

The vulnerable endpoint accepts user input and uses it to query the database.

Instead of safely handling input, it likely builds a query like:

- searching for products where name contains a substring
    

When special characters (like a quote) are introduced, the query breaks.

This is a classic sign that:

- input is directly concatenated into SQL
    
- no parameterization is used
    

---

## Identifying the Vulnerability

When a normal value is used:

- the API returns expected results
    

When a quote is added:

- an error appears
    

This indicates that:

- the backend is not sanitizing input
    
- the database query is being disrupted
    

---

## Exploiting the Injection

By injecting a condition that always evaluates to true:

- the query returns all rows
    

Instead of filtering by name, the database returns:

- the total count of all records
    

This confirms that the attacker can manipulate SQL logic.

---

## Why This Works

The backend likely constructs queries like:

- search where name matches input
    

But without protection, user input can:

- terminate the original query
    
- inject new conditions
    
- alter execution logic
    

---

## Impact

SQL Injection can lead to:

- full database disclosure
    
- data modification
    
- authentication bypass
    
- database destruction
    

In this lab, the impact is:

- retrieving total number of products
    

But in real scenarios, it can escalate much further.

---

## HTTP Header Misconfiguration

APIs must also use secure HTTP headers.

Misconfigurations can expose vulnerabilities such as:

- Cross-Origin attacks
    
- data leakage
    
- session hijacking
    

A common issue is misconfigured **CORS headers**, where:

- any origin is allowed
    
- credentials are improperly shared
    

---

## Proper Mitigation

### Parameterized Queries

Instead of inserting input directly into SQL:

- use prepared statements
    
- bind variables safely
    

This prevents input from being interpreted as SQL code.

---

### Input Validation

Validate input format and reject:

- unexpected characters
    
- malformed values
    

However, validation alone is not sufficient.

---

### ORM Usage

Using an Object Relational Mapper:

- abstracts SQL queries
    
- reduces injection risk
    

---

### Secure HTTP Headers

Ensure headers are properly configured, such as:

- restricting allowed origins
    
- disabling unnecessary exposure
    
- enforcing security policies
    

---

## Pentester Mindset

When testing APIs:

### Look for Input Fields

Any parameter used in:

- search queries
    
- filtering
    
- database lookups
    

is a potential injection point.

---

### Trigger Errors

Introduce special characters:

- quotes
    
- logical operators
    

If errors appear → investigate further.

---

### Modify Logic

Try altering query behavior:

- bypass filters
    
- expand results
    

---

### Inspect Headers

Always review response headers for:

- overly permissive CORS
    
- missing security headers
    

---

Security Misconfiguration vulnerabilities are extremely common because they result from:

- insecure defaults
    
- lack of validation
    
- improper backend implementation
    

They often lead to **high-impact exploits with minimal effort**.


# Improper Inventory Management 

## Concept Overview

Improper Inventory Management occurs when outdated or deprecated API versions remain accessible and expose functionality that should no longer be available.

This is especially dangerous when:

- legacy endpoints are forgotten
    
- authentication is removed or missing
    
- sensitive historical data is still accessible
    

---

## Key Issue in This Lab

The API exposes an older version:

- **v0**
    

This version is described as:

- legacy
    
- unmaintained
    
- containing deleted data
    

However, it is still publicly accessible.

---

## Critical Weakness

Unlike the newer API version:

- v0 endpoints do **not require authentication**
    

This means:

- anyone can access them
    
- no JWT or roles are needed
    

This dramatically increases the attack surface.

---

## Data Exposure

The deprecated endpoints expose:

- deleted customer records
    
- deleted supplier companies
    
- sensitive fields (including password hashes)
    

This is a combination of:

- **Improper Inventory Management**
    
- **Excessive Data Exposure**
    

---

## Exploitation Strategy

The task requires retrieving information about a **deleted supplier company**.

Since:

- the API exposes deleted records
    
- no authentication is required
    

You can directly query:

- the endpoint listing deleted supplier companies
    
- or retrieve a specific record by ID
    

The goal is to extract:

- the email field of the target company
    

---

## Why This Is Dangerous

### Exposure of Historical Data

Deleted data is often assumed to be inaccessible.

However, if exposed:

- attackers can retrieve old records
    
- sensitive information may still be valid
    

---

### Password Reuse Risk

Exposed password hashes can be cracked.

If users reused passwords:

- attackers can compromise active accounts
    

---

### Increased Attack Surface

Old API versions often:

- lack security controls
    
- contain unpatched vulnerabilities
    
- expose internal logic
    

---

## Proper Mitigation

### Remove Deprecated Versions

Old API versions should be:

- completely removed
    
- or disabled
    

---

### Restrict Access

If removal is not possible:

- restrict access to internal use only
    
- enforce strict authentication
    

---

### Secure Legacy Data

Ensure that:

- deleted data is not accessible
    
- sensitive fields are removed
    

---

### Maintain API Inventory

Organizations should:

- track all API versions
    
- audit exposed endpoints
    
- regularly clean up unused services
    

---

## Pentester Mindset

When testing APIs:

### Always Check for Older Versions

Look for:

- /v0
    
- /v1
    
- /v2
    
- /beta
    

Older versions often contain vulnerabilities.

---

### Compare Security Between Versions

Check:

- authentication differences
    
- exposed endpoints
    
- missing protections
    

---

### Look for Deleted or Legacy Data

Endpoints containing terms like:

- deleted
    
- archive
    
- backup
    

are high-value targets.

---

Improper Inventory Management is dangerous because it often exposes **forgotten attack surfaces that bypass all modern security controls**.

# Unsafe Consumption of APIs 

## Concept Overview

Unsafe Consumption of APIs occurs when one API **trusts data received from another API without proper validation or security checks**.

This vulnerability maps to:

**CWE-1357 — Reliance on Insufficiently Trustworthy Component**

---

## Core Idea in This Lab

The application has:

- a secure API version (v1)
    
- an insecure legacy API (v0)
    

The v0 API exposes:

- deleted data
    
- sensitive fields
    
- password hashes
    

If v1 consumes data from v0 **without validation**, then:

- insecure data from v0 becomes trusted in v1
    

---

## Attack Chain

This vulnerability builds on the previous section:

1. **Improper Inventory Management**
    
    - v0 exposes sensitive data
        
2. **Unsafe Consumption**
    
    - v1 trusts that data
        

This creates a chain where:

- insecure data flows into secure systems
    

---

## Exploitation Logic

The question asks:

- what would the password hash be in v1
    

This implies:

- v1 may reuse or import data from v0
    
- the attacker can retrieve that data directly from v0
    

So instead of attacking v1 directly, the attacker:

- queries the insecure v0 endpoint
    
- extracts the required user data
    

---

## Why This Works

Developers often assume:

- internal APIs are trustworthy
    
- legacy systems are safe
    

Because of this, they may:

- skip validation
    
- reuse data blindly
    
- propagate insecure data
    

This creates a **trust boundary violation**.

---

## Impact

Unsafe API consumption can lead to:

- data integrity issues
    
- injection vulnerabilities
    
- propagation of malicious input
    
- exposure of sensitive data
    
- privilege escalation
    

---

## Real-World Example

Imagine:

- API A fetches user data from API B
    
- API B is compromised or insecure
    

If API A trusts that data:

- malicious or sensitive data enters the system
    
- attacks can spread across services
    

---

## Proper Mitigation

### Validate External Data

Never trust data from another API blindly.

Always:

- sanitize inputs
    
- validate structure
    
- enforce constraints
    

---

### Authenticate API-to-API Communication

Ensure:

- APIs verify each other
    
- only trusted sources are accepted
    

---

### Isolate Legacy Systems

Do not allow:

- deprecated APIs to feed production systems
    

---

### Monitor Data Flow

Track:

- where data comes from
    
- how it is used
    

---

## Pentester Mindset

When testing APIs:

### Look for API Relationships

Check if:

- one endpoint depends on another
    
- data flows between versions
    

---

### Target Weakest Link

Find:

- older APIs
    
- insecure integrations
    

Exploit them to impact:

- newer systems
    

---

### Think in Chains

Many real-world exploits are not single vulnerabilities.

They are:

- chains of weaknesses
    
- combined into high-impact attacks
    

---

Unsafe Consumption is dangerous because it allows **insecure components to compromise otherwise secure systems through trust assumptions**.

