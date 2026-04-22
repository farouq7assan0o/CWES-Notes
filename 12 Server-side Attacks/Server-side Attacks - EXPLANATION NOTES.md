# Introduction to Server-side Attacks 

This section introduces the concept of **server-side attacks** and contrasts them with client-side vulnerabilities. The core distinction is where exploitation and impact occur: client-side attacks execute in the user’s browser, while server-side attacks execute on or are mediated by the server itself.

The module groups server-side attacks into four major vulnerability classes, each defined by how attacker-controlled input is processed by the server.

### Server-Side Request Forgery (SSRF)

SSRF occurs when a server makes outbound requests based on user-supplied input without sufficient validation. An attacker abuses this behavior to force the server to issue requests to unintended destinations, such as internal services, localhost interfaces, or cloud metadata endpoints. This can lead to internal network access, firewall bypass, and sensitive data disclosure.

From an attacker mindset, SSRF is about **pivoting through trust**: the server is trusted by internal systems, and the attacker rides that trust boundary.

### Server-Side Template Injection (SSTI)

SSTI arises when user input is embedded directly into server-side templates and evaluated by the template engine. Instead of being treated as plain data, the input is parsed as template logic. Depending on the engine and context, this can expose variables, application secrets, or escalate into arbitrary code execution.

The key idea is **code execution through rendering logic**, not through traditional command execution paths.

### Server-Side Includes (SSI) Injection

SSI injection targets web servers that process SSI directives inside HTML files. If user input is injected into an SSI-parsed context, an attacker may execute directives that include files, read environment variables, or run system commands.

This vulnerability sits at the intersection of **web server configuration** and **dynamic content inclusion**, often overlooked because SSI is considered legacy.

### XSLT Server-Side Injection

XSLT injection occurs when user input influences XSLT transformations on the server. Because XSLT is a powerful language capable of file access, network interaction, and function calls (depending on processor configuration), unsafe handling can allow attackers to execute arbitrary logic or read sensitive server-side resources.

This class highlights that **non-obvious languages** (like XML transformation engines) can still become full execution primitives if misused.

### Methodology Context

This introductory section sets the foundation for later exploitation by:

- Framing where attacker-controlled input meets server-side logic
    
- Emphasizing trust boundaries and execution contexts
    
- Preparing the mindset needed to recognize non-traditional injection points
    

No exploitation steps are shown here; this page is purely conceptual and intended to scope the attack surface before hands-on labs begin.

# Introduction to SSRF 

This section formally introduces **Server-Side Request Forgery (SSRF)** and establishes why it is considered a high-impact vulnerability, including its placement in the OWASP Top 10.

### Core Concept

SSRF occurs when a web application allows user-supplied input to influence server-side requests to other resources. Instead of the user’s browser making the request, the **server itself becomes the requester**, operating with its own network access, trust relationships, and permissions.

The attacker’s goal is to **control where the server sends requests**.

### Why SSRF Is Dangerous

What initially looks like harmless URL fetching can escalate quickly because:

- Servers often have access to internal networks that users cannot reach
    
- Firewalls typically trust outbound traffic from application servers
    
- Internal services often lack authentication or expect trusted callers
    

This turns the vulnerable server into a **proxy for internal reconnaissance and exploitation**.

### URL Scheme Abuse

The section highlights that SSRF severity increases when the application allows flexible or unvalidated URL schemes.

Each scheme implies a different attack surface:

- HTTP/S schemes enable access to internal web services, admin panels, metadata endpoints, or WAF-protected routes
    
- Local file schemes shift SSRF into local file read territory, overlapping with LFI-style impact
    
- Arbitrary byte-oriented protocols enable interaction with non-HTTP services such as mail servers or databases
    

From an attacker mindset, identifying **which schemes are accepted** is often more important than the initial SSRF itself.

### Exploitation Trajectory

This page sets expectations rather than exploitation steps:

1. Identify user-controlled fetch behavior
    
2. Determine allowed URL schemes
    
3. Pivot from external fetches to internal access
    
4. Chain SSRF with other weaknesses (authentication gaps, unsafe services)
    

Advanced techniques like filter bypasses and DNS rebinding are explicitly deferred to later modules, reinforcing that SSRF is often **not a single-step vulnerability**, but a chaining primitive.

### Methodology Placement

This section bridges theory and practice by:

- Defining SSRF precisely
    
- Framing it as a trust-boundary violation
    
- Preparing the reader to think in terms of network reachability, not just endpoints
    

No exploitation mechanics are demonstrated yet; this page exists to anchor the mental model before hands-on SSRF labs begin.



# Identifying SSRF

This section demonstrates how to **confirm and operationalize an SSRF vulnerability** using a realistic web application flow.

### Confirming SSRF

The key indicator is a user-controlled parameter (`dateserver`) that contains a full URL. This strongly suggests that the backend server fetches remote resources based on client input.

Confirmation follows a classic SSRF validation pattern:

- Redirect the server’s request to an attacker-controlled host
    
- Observe an inbound connection from the target server
    

The inbound connection proves that the **server**, not the browser, is making the request.

### Determining SSRF Type

By forcing the application to request its own localhost interface and observing the returned HTML, it becomes clear that:

- The SSRF is **non-blind**
    
- The response body is reflected back to the attacker
    

This dramatically increases impact, as responses can be parsed directly rather than inferred indirectly.

### Internal Enumeration via SSRF

Once SSRF is confirmed and response behavior is understood, the vulnerability becomes an **internal probing primitive**.

By varying the destination port and observing differences in server responses, the attacker can infer:

- Which ports are open
    
- Which services are reachable internally
    

The error message acts as a reliable signal to distinguish open versus closed ports.

### Port Scanning Logic

Instead of scanning externally, the attacker:

- Uses the vulnerable server as the scanner
    
- Automates requests across a port range
    
- Filters based on known failure responses
    

This effectively turns SSRF into an **internal port scanner**, bypassing network segmentation and firewall rules.

### Results Interpretation

Discovered services such as a database port indicate:

- Additional internal attack surface
    
- Potential for chaining SSRF with other vulnerabilities
    
- Possible access to unauthenticated or weakly protected services
    

### Methodology Placement

This section teaches a critical SSRF mindset:

- SSRF is rarely the end goal
    
- It is a pivot and enumeration tool
    
- Response behavior defines exploitation depth
    

By the end of this page, SSRF has transitioned from a theoretical flaw into a **practical internal reconnaissance vector**, setting the stage for deeper exploitation in subsequent sections.
# Exploiting SSRF 

This section demonstrates how SSRF moves from **confirmation** into **impactful exploitation** by abusing trust boundaries, protocol handling, and internal-only services.

### Accessing Restricted Internal Endpoints

Although `dateserver.htb` is inaccessible directly from the attacker’s browser, the vulnerable application can still reach it internally. SSRF bypasses access controls by making the server fetch restricted resources on the attacker’s behalf.

Directory enumeration is performed through SSRF by:

- Sending payloads via the vulnerable parameter
    
- Observing response differences
    
- Filtering default Apache error pages to isolate valid endpoints
    

This reveals hidden internal functionality such as administrative panels.

### Local File Inclusion via URL Schemes

Because the application accepts arbitrary URL schemes, SSRF escalates into **local file read** by switching from `http://` to `file://`. This allows attackers to:

- Read system files
    
- Inspect application source code
    
- Extract credentials or secrets
    

This overlap shows how SSRF often chains directly into LFI impact.

### Limitation of HTTP-Based SSRF

Standard SSRF using `http://` is limited to GET requests. When internal endpoints require POST data, direct access is blocked even though the endpoint is reachable.

This is where protocol abuse becomes critical.

### Gopher Protocol Abuse

The gopher protocol allows attackers to send **raw TCP payloads**, bypassing HTTP method limitations. By manually crafting HTTP requests and encoding them into gopher URLs, attackers can:

- Perform POST requests
    
- Submit credentials
    
- Interact with authenticated endpoints
    

Double URL encoding is required because the gopher payload itself is passed as a URL parameter inside an HTTP POST request.

### Tool-Assisted Gopher Exploitation

Manually constructing gopher payloads is error-prone. Gopherus automates payload generation for common internal services such as:

- Databases
    
- Caches
    
- Mail servers
    

This significantly lowers the barrier for interacting with non-HTTP services discovered via SSRF.

### Methodology Takeaway

This section reinforces that SSRF is:

- A protocol-level vulnerability
    
- A pivot into internal trust zones
    
- A powerful chaining primitive
    

Effective SSRF exploitation relies on understanding:

- URL schemes
    
- Backend request handling
    
- Internal service behavior
    

At this stage, SSRF is no longer reconnaissance — it is **direct control over internal systems**.
# Blind SSRF 

This section introduces **blind SSRF**, where the server-side request still occurs, but the response is **not returned to the attacker**. The vulnerability exists, but visibility is lost.

### Identifying Blind SSRF

Blind SSRF is confirmed the same way as non-blind SSRF:

- Supply a URL pointing to an attacker-controlled system
    
- Observe an inbound connection on a listener
    

However, when pointing the application to itself, the response no longer contains fetched content. Instead, the application returns a generic message, proving that responses are suppressed.

### Why Exploitation Is Limited

Because responses are not reflected:

- Internal endpoints cannot be viewed directly
    
- File contents cannot be exfiltrated
    
- Admin panels cannot be interacted with visually
    

This removes most high-impact exploitation vectors used in non-blind SSRF.

### Still Possible: Behavioral Enumeration

Despite being blind, the SSRF can still leak information **through differences in application behavior**.

The application responds differently based on:

- Open vs closed ports
    
- Existing vs non-existing files
    
- Valid HTTP responses vs non-HTTP services
    

These behavioral differences act as **side channels**.

### Blind Port Scanning

If an internal port:

- Accepts HTTP connections → one application message
    
- Is closed or non-HTTP → a different error message
    

Then attackers can still enumerate **HTTP-based services** on the local system. Services like MySQL that do not speak HTTP cannot be reliably detected this way.

### Blind File Enumeration

Similarly, the application reacts differently when:

- A valid file path is requested
    
- A non-existent file path is requested
    

This allows attackers to **confirm file existence**, even though file contents cannot be read.

### Methodology Takeaway

Blind SSRF:

- Is harder to exploit
    
- Provides less direct impact
    
- Still enables internal reconnaissance
    

Even without response data, attackers can:

- Map internal HTTP services
    
- Identify filesystem structure
    
- Prepare precise follow-up attacks
    

The key mindset shift is moving from **content exfiltration** to **inference through behavior**.
# Preventing SSRF 

This section shifts from an attacker mindset to a **defensive and architectural perspective**, outlining how SSRF vulnerabilities can be prevented or their impact reduced.

### Application-Layer Mitigations

The most effective SSRF defenses start at the application level, where user input is first introduced.

Key principles include:

- **Origin whitelisting**: Only allow requests to explicitly approved domains or IPs. This prevents attackers from redirecting requests to internal or attacker-controlled systems.
    
- **Scheme restriction**: Disallow arbitrary URL schemes. Protocols such as `file://`, `gopher://`, or `ftp://` should never be user-controlled. Ideally, schemes are hardcoded rather than validated.
    
- **Input sanitization**: While sanitization alone is not sufficient, it helps reduce unexpected parsing behaviors that could be abused to bypass filters.
    

The central idea is to **remove attacker control over request destinations**, rather than trying to detect malicious intent.

### Network-Layer Mitigations

Defense-in-depth assumes application-layer controls may fail.

At the network level:

- **Egress filtering** restricts outbound connections to only what the application truly needs
    
- **Firewall rules** block access to internal networks, metadata services, and sensitive ports
    
- **Network segmentation** ensures that even if SSRF exists, internal systems are not reachable from the vulnerable service
    

These controls limit SSRF blast radius rather than eliminating the vulnerability itself.

### Why Blacklists Fail

Implicit in this section is a warning against:

- Blacklisting IP ranges
    
- Blocking keywords like `localhost`
    
- Relying on regex URL checks
    

Attackers routinely bypass these controls using encoding tricks, alternative IP notations, DNS rebinding, or protocol abuse.

### Secure Design Takeaway

SSRF prevention is fundamentally about **trust boundaries**:

- Servers should not blindly trust user input to decide where to send requests
    
- Internal services should not assume requests are legitimate just because they originate internally
    

Proper SSRF prevention requires coordination between:

- Developers (safe request handling)
    
- Infrastructure teams (network controls)
    
- Security teams (threat modeling and testing)
    

This section closes the SSRF lifecycle by reinforcing that **secure defaults and constrained design** are more reliable than reactive filtering.#
# Template Engines

This section introduces **template engines** and builds the conceptual foundation needed to understand **Server-Side Template Injection (SSTI)** later in the module.

### What Template Engines Are

Template engines are used to dynamically generate content by combining:

- A **template** (static structure)
    
- **Data values** (dynamic input)
    

They are most commonly used to avoid duplication of shared components like headers, footers, or layouts while allowing page-specific content to change.

Popular engines such as Jinja (Python) and Twig (PHP) are powerful by design and closely resemble programming languages.

### Rendering Process

Rendering is the act of:

1. Taking a template
    
2. Injecting provided variables
    
3. Producing a final output string
    

In the simplest case, variables are replaced directly with provided values. This is safe **only when the template itself is trusted**.

### Logic Inside Templates

Modern template engines support:

- Loops
    
- Conditions
    
- Expressions
    
- Filters and functions
    

This means templates are not just string substitution systems — they are **logic execution environments**.

From a security perspective, this power is what makes SSTI dangerous when:

- User input is inserted into templates
    
- User input is treated as template code rather than data
    

### Security Implication (Foreshadowing SSTI)

At this stage, nothing is vulnerable yet — but the groundwork is laid:

If an attacker can:

- Control part of the template
    
- Or inject template syntax into rendered content
    

Then the template engine may evaluate attacker-controlled logic on the server.

This page exists to ensure you understand:

- How templates work
    
- What “rendering” means
    
- Why logic inside templates matters
    

All exploitation comes later — this is **mental model setup**, not attack execution yet.

# Introduction to SSTI 

This section introduces **Server-Side Template Injection (SSTI)** and explains _why_ it is one of the most dangerous server-side vulnerabilities.

### What SSTI Is

SSTI occurs when an attacker can inject **template syntax** into a template that is later rendered by the server. Because template engines execute logic during rendering, injected template code may be **executed on the server**, not just displayed.

This can escalate from:

- Information disclosure  
    → to
    
- Arbitrary code execution  
    → to
    
- Full server compromise
    

### Values vs Templates (Critical Distinction)

Template engines are **safe by default** when used correctly.

Safe usage:

- User input is passed as **values**
    
- The template itself is **static and trusted**
    
- Values are substituted, not executed
    

Unsafe usage (SSTI):

- User input becomes part of the **template string**
    
- The template engine evaluates attacker-controlled syntax
    

The vulnerability is not the template engine — it’s **how it’s used**.

### Common SSTI Introduction Patterns

SSTI typically appears when developers:

- Concatenate user input into templates before rendering
    
- Render the same template multiple times
    
- Insert user input into the output of a first render, then re-render it
    
- Allow users to create, edit, or upload templates
    

Each of these cases turns **data into code**.

### Why SSTI Is So Dangerous

Modern template engines support:

- Logic (loops, conditionals)
    
- Object access
    
- Function calls
    
- Filters and helpers
    

If an attacker gains template control, the template engine becomes an **execution engine** running in the server’s security context.

### Methodology Context

This page sets the conceptual boundary for SSTI:

- SSTI is about **template control**, not just input reflection
    
- It depends on **rendering behavior**, not just output
    
- Detection and exploitation require understanding how templates are processed internally
    

This is the foundation. The next sections will focus on **detecting SSTI** and **breaking out of the template sandbox** in real applications.

# Identifying SSTI 

This section explains **how to confirm an SSTI vulnerability and fingerprint the template engine** used by a web application. Unlike SSRF, SSTI exploitation is highly dependent on engine-specific behavior, so identification is mandatory before exploitation.

### Confirming SSTI

The first goal is to determine whether user input is interpreted as **template syntax** instead of plain data.

A special “crash test” payload containing multiple template metacharacters is injected. This payload is designed to:

- Break template parsing
    
- Trigger rendering errors
    
- Increase confidence that input is processed by a template engine
    

An error alone does not prove SSTI, but it is a strong indicator that template rendering is involved.

### Arithmetic Payload Testing

Once suspicion exists, simple arithmetic expressions are used to test execution.

The key idea:

- If the expression is evaluated → template execution
    
- If it is rendered verbatim → no SSTI
    

Different template engines use different delimiters, so multiple syntaxes are tested sequentially.

### Template Engine Fingerprinting

After confirming execution, behavior differences are used to identify the engine.

The critical discriminator shown is string–integer multiplication:

- Some engines treat it as repetition
    
- Others treat it as arithmetic
    

By observing the output of the final payload, the engine can be uniquely identified.

### Why This Matters

Each template engine:

- Uses different syntax
    
- Exposes different objects
    
- Has different sandbox escape paths
    

Incorrect engine identification leads to failed exploitation attempts.

### Methodology Takeaway

SSTI identification follows a clear chain:

1. Trigger parsing errors
    
2. Confirm expression execution
    
3. Fingerprint engine behavior
    
4. Only then move to exploitation
    

This structured approach avoids guesswork and ensures reliable, repeatable SSTI exploitation in later stages.

# Exploiting SSTI – Jinja2 

This section demonstrates **full exploitation of an SSTI vulnerability in a Jinja2-based application**, progressing from information disclosure to local file access and finally remote code execution.

### Assumptions and Context

At this stage, SSTI has already been:

- Confirmed
    
- Fingerprinted as **Jinja2**
    

The focus here is not detection, but **impact escalation**.

Because Jinja2 templates execute in a Python context, template injection effectively grants access to Python objects available in memory.

---

### Information Disclosure via Template Context

Jinja templates expose internal objects that are never meant to be user-facing.

Dumping application configuration reveals:

- Debug flags
    
- Secret keys
    
- Environment-specific settings
    

These values often enable further attacks such as session forgery or cryptographic abuse.

---

### Accessing Python Built-ins

The template object graph allows traversal from the template context into Python internals.

By reaching `__builtins__`, the attacker gains access to:

- File handling
    
- Imports
    
- System interaction primitives
    

This step is critical: once built-ins are reachable, the sandbox is effectively broken.

---

### Local File Inclusion

Using the built-in file handling functions, arbitrary local files can be read.

This enables:

- Credential harvesting
    
- Source code disclosure
    
- Configuration leakage
    

At this point, the attacker has **read access to the filesystem** within the application’s permission scope.

---

### Remote Code Execution

Full RCE is achieved by:

- Importing standard Python libraries
    
- Executing system-level commands
    
- Reading command output back through the template engine
    

This is the highest impact outcome of SSTI and typically results in **complete server compromise**.

---

### Methodology Takeaway

Jinja2 SSTI exploitation follows a reliable chain:

1. Reach internal objects
    
2. Escape the template sandbox
    
3. Access built-ins
    
4. Read files
    
5. Execute commands
    

This section illustrates why SSTI is considered one of the most critical server-side vulnerabilities: **it collapses the boundary between data and code entirely**.
# Exploiting SSTI – Twig 

This section covers **SSTI exploitation in the Twig template engine**, which is commonly used in PHP applications, especially those built with the Symfony framework.

### Context and Assumptions

At this stage:

- SSTI has already been confirmed
    
- The template engine has been identified as **Twig**
    

As with Jinja2, exploitation depends entirely on understanding how Twig exposes internal functionality and how PHP functions can be reached through template features.

---

### Information Disclosure

Twig provides limited introspection compared to Jinja2. The `_self` keyword exposes basic information about the current template but does not directly reveal configuration details or internal objects.

This limitation means attackers typically move quickly from confirmation to more impactful techniques.

---

### Local File Inclusion via Twig Filters

Twig itself does not expose file-reading primitives by default. However, **framework-specific extensions** can introduce dangerous functionality.

In Symfony environments, the `file_excerpt` filter allows reading arbitrary files. This enables:

- Disclosure of sensitive system files
    
- Access to application source code
    
- Leakage of credentials or secrets
    

This highlights a key SSTI lesson: **the framework matters as much as the template engine**.

---

### Remote Code Execution

Twig supports filters that can invoke PHP functions. By abusing the `filter` mechanism, attackers can call dangerous built-in PHP functions such as `system`.

This results in:

- Command execution under the web server user
    
- Direct confirmation of RCE through command output
    
- Full compromise of the application runtime
    

Compared to Jinja2, Twig RCE is often more direct once a callable execution path exists.

---

### General SSTI Takeaway

Although Jinja2 and Twig differ in syntax and exposure:

- The exploitation pattern is the same
    
- User-controlled template logic leads to code execution
    
- Mastery comes from understanding engine features and extensions
    

Attackers unfamiliar with a template engine can usually pivot quickly by:

- Reading official documentation
    
- Using curated SSTI cheat sheets
    
- Testing supported filters and functions
    

This concludes the SSTI exploitation section by reinforcing that **template engines are execution environments**, not just rendering tools.

# SSTI Tools of the Trade & Preventing SSTI

This section covers **automation and defense** for Server-Side Template Injection, completing the SSTI lifecycle from discovery to mitigation.

### SSTImap as an SSTI Framework

SSTImap is a modern successor to tplmap and automates:

- SSTI detection
    
- Template engine identification
    
- Capability enumeration
    
- Exploitation (read, write, execute, shell)
    

Instead of manually crafting payloads, SSTImap probes parameters using a large library of engine-specific payloads and confirms execution paths.

### Automated SSTI Identification

By providing a target URL with a suspected injection point, SSTImap:

- Detects whether SSTI exists
    
- Identifies the template engine (e.g., Twig)
    
- Determines execution context and OS
    
- Maps available exploitation primitives
    

This eliminates guesswork and significantly speeds up real-world exploitation.

### Exploitation Capabilities

Once SSTI is confirmed, SSTImap can:

- Read arbitrary files from the filesystem
    
- Execute system commands
    
- Spawn an interactive OS shell
    
- Write files or evaluate code
    

These features mirror manual exploitation steps shown earlier but at a much faster pace.

### Why Automation Matters

In real engagements:

- SSTI payloads are engine-specific
    
- Manual testing is error-prone
    
- Complex applications have many parameters
    

SSTImap provides consistency, coverage, and reliability, especially when time is limited.

---

### Preventing SSTI (Defensive View)

SSTI prevention is fundamentally about **preserving the boundary between templates and data**.

Key defensive principles:

- User input must never be part of the template string
    
- User input must only be passed as values to the rendering function
    
- Templates should be static and developer-controlled
    

### Handling User-Editable Templates

If business logic requires user-defined templates:

- Remove or restrict dangerous functions
    
- Harden the template execution environment
    
- Assume bypasses are possible
    

The safest approach is **environment isolation**, such as:

- Running template rendering in containers
    
- Sandboxing template execution away from the web server
    

### Core Takeaway

SSTI is not a syntax problem — it is a **design flaw**.

Once user input becomes executable template logic, full compromise is often inevitable. Prevention requires strict separation of:

- Code
    
- Templates
    
- User-controlled data
    

This concludes the SSTI section by tying together **automation, exploitation, and secure design**.

# Introduction to SSI Injection 

This section introduces **Server-Side Includes (SSI)** and explains how improper handling of SSI directives can lead to **SSI Injection**, a server-side code execution vulnerability.

### What SSI Is

SSI is a web server feature that allows dynamic content to be embedded inside otherwise static HTML pages. The server parses special comment-based directives and executes them **before** sending the page to the client.

SSI is supported by common web servers like Apache and IIS and is often (but not always) associated with specific file extensions.

### SSI Directives and Capabilities

SSI directives are powerful and server-executed. They can:

- Read environment variables
    
- Output server metadata
    
- Include other files from the web root
    
- Execute operating system commands
    

This makes SSI a **server-side execution mechanism**, not just a templating convenience.

### Why SSI Injection Is Dangerous

SSI injection occurs when an attacker can:

- Upload a file containing SSI directives into a parsed directory
    
- Inject SSI syntax into a file written to disk by the application
    

When the server processes that file, it executes the attacker’s directives with the web server’s privileges.

This can lead to:

- Information disclosure
    
- Local file inclusion
    
- Command execution
    

### Detection Challenges

SSI usage cannot be reliably identified by file extension alone:

- Servers may enable SSI for arbitrary extensions
    
- SSI parsing may be selectively enabled per directory
    

As a result, SSI injection often hides behind:

- File upload functionality
    
- Logging mechanisms
    
- User-generated content written to disk
    

### Methodology Takeaway

SSI injection sits at the intersection of:

- Web server configuration
    
- File handling logic
    
- User-controlled content persistence
    

The key risk factor is **user input becoming server-parsed content**. Once that boundary is crossed, SSI directives turn static HTML into an execution surface.

This page establishes the conceptual groundwork for identifying and exploiting SSI injection in the sections that follow.
# Exploiting SSI Injection 

This section demonstrates **practical exploitation of an SSI Injection vulnerability**, resulting in full remote code execution.

### Vulnerable Flow

The web application:

- Accepts user input via a form
    
- Writes that input into a `.shtml` file
    
- Serves the file through a web server with SSI enabled
    

Because the input is written **unsanitized** into an SSI-parsed page, attacker-controlled content is interpreted as SSI directives.

### Confirming SSI Injection

Injecting a harmless SSI directive confirms execution:

- Environment variables are printed
    
- Server metadata is exposed
    
- Confirms SSI parsing is active
    

This is equivalent to confirming code execution capability.

### Command Execution

The `exec` directive allows direct execution of operating system commands. Once confirmed, the attacker gains:

- Arbitrary command execution
    
- Execution context of the web server user
    
- Full control over the application environment
    

At this point, SSI injection is functionally equivalent to classic RCE.

### Impact

Successful SSI exploitation allows:

- File disclosure
    
- Credential harvesting
    
- Lateral movement preparation
    
- Complete server compromise
    

Because SSI executes at the **web server level**, exploitation bypasses application-layer defenses entirely.

### Methodology Takeaway

SSI injection typically arises from:

- File upload functionality
    
- User input written to disk
    
- Logging mechanisms rendered via SSI
    

The core failure is allowing **user-controlled input to become server-parsed content**.

This section completes the SSI attack chain by showing how a simple reflection bug escalates into **full remote code execution**.
# Preventing SSI Injection 

This section focuses on **defensive controls** to prevent Server-Side Includes (SSI) from becoming an execution vector.

### Root Cause of SSI Injection

SSI injection happens when:

- User-controlled input is written to files
    
- Those files are parsed by the web server as SSI-enabled content
    
- Malicious SSI directives are executed during page rendering
    

The vulnerability is not SSI itself, but **unsafe trust in user input combined with permissive server configuration**.

---

### Input Validation and Sanitization

As with all injection vulnerabilities:

- User input must be strictly validated
    
- Special characters used in SSI directives should never reach SSI-parsed files
    
- Any user input written to disk must be treated as untrusted
    

This is especially critical when:

- Input is stored in `.shtml`, `.shtm`, or similar files
    
- Input is included in server-generated HTML content
    

---

### Restricting SSI Scope

Web server configuration plays a major role in mitigation.

Effective controls include:

- Enabling SSI only for required file extensions
    
- Limiting SSI parsing to specific directories
    
- Avoiding blanket SSI support across the web root
    

Reducing SSI’s footprint directly reduces the attack surface.

---

### Disabling Dangerous Directives

Not all SSI directives are equally dangerous.

High-risk directives such as:

- `exec`
    

Should be disabled if not explicitly required. While this does not eliminate all risk, it significantly reduces the impact of a successful injection.

---

### Defense-in-Depth Mindset

SSI injection is a **configuration + logic flaw**, not just a coding error.

Strong prevention requires:

- Safe input handling in the application
    
- Restrictive SSI configuration on the server
    
- Minimal feature exposure based on actual need
    

The key takeaway is to **never allow user input to become server-interpreted content**. When SSI is unavoidable, its power must be tightly constrained to prevent escalation into remote code execution.

# Intro to XSLT Injection 

This section introduces **XSLT Injection**, a server-side vulnerability that arises when user-controlled input is embedded into XSLT data and processed by an XSLT engine.

### What XSLT Is

XSLT is a transformation language used to convert XML documents into other formats such as:

- Plain text
    
- HTML
    
- XML with a different structure
    

It operates by applying **templates** to XML nodes and generating output based on rules defined in XSL elements.

### Core XSLT Concepts

Key elements used in XSLT include:

- Templates that define which XML nodes to process
    
- Selectors that extract values from XML nodes
    
- Control structures such as loops, sorting, and conditionals
    

XSLT is powerful enough to produce arbitrary output, making it suitable for dynamic content generation in web applications.

### Where the Risk Comes From

XSLT injection occurs when:

- User input is concatenated into XSLT templates
    
- The XSLT processor evaluates attacker-controlled XSL elements
    
- Injected logic executes during the transformation phase
    

This mirrors SSTI and SSI injection patterns: **user data becomes executable logic**.

### Security Implications

Depending on processor configuration and supported functions, XSLT injection may allow:

- Information disclosure
    
- Access to internal XML data
    
- File access
    
- In some cases, command execution
    

The impact depends heavily on:

- The XSLT engine in use
    
- Enabled extension functions
    
- How the application builds XSLT templates
    

### Methodology Takeaway

XSLT injection is conceptually similar to other server-side injection flaws:

- Trust boundaries are violated
    
- Templating logic is exposed
    
- Execution occurs server-side
    

Understanding basic XSLT syntax and execution flow is essential before moving on to **identification and exploitation**, which are covered in the next sections.


# Exploiting XSLT Injection 

This section demonstrates **end-to-end exploitation of an XSLT Injection vulnerability**, progressing from detection to full remote code execution.

### Identifying XSLT Injection

The application reflects user input into XSLT-processed output. Injecting malformed XML causes a server error, indicating that user input is parsed before transformation.

This is a strong signal that:

- XML/XSLT processing is occurring
    
- User input is not sanitized
    
- Injection into XSLT logic is possible
    

### Confirming Injection via XSLT Functions

Injecting valid XSLT elements confirms execution when the processor evaluates them instead of treating them as plain text.

Using `system-property()` reveals:

- XSLT version
    
- Processor vendor
    
- Library implementation
    

This fingerprinting step is critical because exploitation techniques depend on processor capabilities.

### Local File Inclusion

File disclosure depends on:

- XSLT version
    
- Enabled extension functions
    

Although `unparsed-text()` is unavailable in XSLT 1.0, PHP extension functions are enabled. This allows direct invocation of PHP file handling functions, resulting in arbitrary file reads.

### Remote Code Execution

Once PHP functions are callable, the boundary between transformation logic and system execution collapses.

Invoking PHP’s `system()` function executes OS commands in the context of the web server, achieving full RCE.

### Methodology Takeaway

XSLT injection exploitation follows a structured path:

1. Trigger XML/XSL parsing errors
    
2. Confirm XSLT execution
    
3. Fingerprint the processor
    
4. Abuse extension functions
    
5. Escalate to file read or command execution
    

XSLT injection is especially dangerous because its impact depends less on application code and more on **XSLT engine configuration**, which is often overlooked during development and hardening.
# Preventing XSLT Injection 

This section concludes the module by focusing on **defensive strategies** to prevent XSLT injection and reduce its potential impact.

### Root Cause of XSLT Injection

XSLT injection occurs when:

- User input is embedded into XSL data
    
- The XSLT processor evaluates that data as executable logic
    
- Injected XSL elements are executed during transformation
    

As with SSTI and SSI, the core issue is **user input crossing from data into code**.

---

### Primary Prevention Strategy

The most effective prevention measure is architectural:

- **Never insert user input into XSL templates**
    
- XSL documents should be static and developer-controlled
    
- User input should only be supplied as data to the transformation process
    

This preserves the trust boundary between transformation logic and untrusted input.

---

### Safe Reflection of User Input

If business logic requires user input to appear in output:

- User data must be sanitized before insertion
    
- Encoding must match the output format
    

For HTML output, **HTML encoding** is especially effective:

- `<` becomes `&lt;`
    
- `>` becomes `&gt;`
    

This prevents attackers from injecting valid XSLT elements into the document.

---

### Hardening the XSLT Environment

Even with proper input handling, additional controls should be applied to limit blast radius:

- Run the XSLT processor with **low privileges**
    
- Disable support for **external or extension functions**
    
- Prevent PHP function access inside XSLT
    
- Keep XSLT libraries fully **patched and up to date**
    

These measures ensure that even if an injection occurs, escalation paths are limited.

---

### Defense-in-Depth Takeaway

XSLT injection prevention mirrors the broader lesson of this module:

- Server-side injection flaws are design failures
    
- Sanitization alone is fragile
    
- Isolation, least privilege, and strict data boundaries are essential
    

By treating XSLT as executable code—not just a formatting tool—developers can avoid turning XML transformations into a full server compromise vector.

#
#
#
#
#
#
#