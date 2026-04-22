# Introduction 

**What Web Fuzzing Is**

Web fuzzing is the automated process of sending unexpected, malformed, or random inputs to a web application to observe how it responds and surface hidden vulnerabilities. It differs from brute-forcing in scope and intent: fuzzing explores broadly with diverse, unexpected payloads, while brute-forcing targets a specific value (such as a password) through systematic enumeration.

**Why It Matters**

Manual testing cannot realistically cover all input combinations a web application might receive. Fuzzing automates this coverage gap, simulating attacker behavior at scale. It is particularly useful for exposing flaws in input validation, which underpin common vulnerability classes like SQL injection and XSS.

**Core Concepts to Internalize**

A wordlist is the fuel for fuzzing — it defines the space of payloads the fuzzer will try. Payloads are the actual values sent; a wordlist entry becomes a payload once delivered in a request. Response analysis is how you distinguish noise from signal: anomalies in status codes, response sizes, or error messages indicate something worth investigating.

False positives (benign responses flagged as interesting) and false negatives (real vulnerabilities missed entirely) are both inherent risks. Tuning filtering and understanding expected application behavior reduces both.

**Tooling Context**

The three tools listed — ffuf, wfuzz, and Burp Suite Intruder — all serve the same core function but differ in interface and flexibility. ffuf and wfuzz are CLI-driven and fast. Burp Suite Intruder is GUI-based and tightly integrated with proxy interception, making it well-suited for targeted, manually-guided fuzzing campaigns.

**Where This Fits in the Module**

This section establishes vocabulary and rationale. The concepts here (wordlists, payloads, response analysis, scope) will be applied directly in the directory fuzzing, parameter fuzzing, virtual host fuzzing, and filtering sections that follow.


# Tooling 
**Dependency Setup**

Go is required for ffuf and Gobuster, both written in Go. Python and pipx are required for wenum. pipx is preferred over pip for wenum because it installs Python applications into isolated virtual environments, preventing dependency conflicts with other system packages. The `ensurepath` calls update shell PATH so installed binaries are accessible.

**FFUF**

ffuf is the primary fuzzer used throughout this module. Written in Go, it is fast and flexible, supporting directory enumeration, parameter discovery, and brute-force scenarios. The `FUZZ` keyword acts as the injection point in requests, replaced by each wordlist entry at runtime.

**Gobuster**

Gobuster is simpler and faster for straightforward directory and DNS subdomain enumeration. It is less flexible than ffuf for parameter fuzzing but preferred when speed and simplicity are the priority.

**FeroxBuster**

FeroxBuster is written in Rust and distinguishes itself with recursive scanning - it automatically follows discovered directories deeper into the site tree without manual configuration. It is better described as a forced browsing tool than a general fuzzer.

**wenum**

wenum is a maintained fork of wfuzz and is syntax-compatible with it - commands are interchangeable by substituting `wenum` for `wfuzz`. It is particularly suited for parameter fuzzing. The pipx installation method and the subsequent `runpip` call for setuptools ensure the tool installs cleanly without conflicting with system Python packages.

# Directory and File Fuzzing 

**Why Hidden Assets Matter**

Web servers routinely expose directories and files that are never linked from the public interface. These may include backup files with credentials, old vulnerable scripts, admin panels, or staging environments. Because they are unlinked, they often receive less security scrutiny, making them high-value targets.

**How Wordlists Work Here**

ffuf has no built-in wordlist. The `-w` flag points it to an external file. Each line in the wordlist replaces the `FUZZ` keyword in the URL, generating one HTTP request per entry. Wordlist choice directly determines what gets discovered - a narrow list misses more, a large list takes longer but covers more ground.

**Directory Fuzzing Logic**

The first command targets the root of the application, substituting `FUZZ` with directory names. A 301 response indicates the directory exists and the server is redirecting to it, confirming a valid resource. This is how `w2ksvrus` was discovered in the lab example.

**File Fuzzing Logic**

Once a directory is confirmed, file fuzzing drills into it. The `-e` flag appends file extensions to every wordlist entry, multiplying the request count by the number of extensions specified. For example, the wordlist entry `config` becomes `config.php`, `config.html`, `config.txt`, `config.bak`, and `config.js` - all sent as separate requests. The `-v` flag enables verbose output, showing the full URL alongside each match.

**Chaining the Steps**

Directory fuzzing first, then file fuzzing within discovered directories is the standard methodology. You map the structure before you enumerate content. This two-phase approach keeps scans targeted and avoids flooding paths that don't exist.

**Extension Selection Rationale**

The extensions chosen reflect what the target stack likely serves. `.php` for server-side logic, `.html` for static pages, `.txt` for logs or notes, `.bak` for backups left by developers, and `.js` for client-side scripts. Backup files (`.bak`) are particularly valuable because they often contain source code or credentials that the live file does not expose directly.


# Recursive Fuzzing 

**How It Works**

Recursive fuzzing extends standard directory fuzzing by automatically creating new fuzzing jobs for every directory discovered. When ffuf finds a valid directory (typically signaled by a 301 redirect), it appends that directory to the base URL and queues it as a new target. This process repeats for each subsequent discovery, following the tree downward without manual intervention.

**Why It Matters**

In complex web applications, sensitive content is rarely at the root level. Multi-level nested directories are common, and manually re-running ffuf at each level is impractical. Recursive fuzzing automates this entirely, ensuring no branch is skipped.

**The `-ic` Flag**

Many wordlists include comment lines beginning with `#`. Without `-ic`, ffuf treats these as valid entries and sends requests for them, generating noise. The flag strips commented lines before fuzzing begins, keeping results clean.

**The `-recursion-depth` Flag**

Unlimited recursion can be destructive. A deep or circular directory structure could cause ffuf to run indefinitely and flood the target server with requests. Setting `-recursion-depth 2` caps exploration at two levels below the starting point, which covers most real-world scenarios while staying responsible.

**The `-rate` Flag**

Recursive fuzzing multiplies the total request count significantly. Without rate limiting, ffuf will send requests as fast as possible (tens of thousands per second), which can crash or trigger defenses on the target. `-rate 500` caps this at 500 requests per second, balancing speed against server stability.

**Chaining to Prior Steps**

This section extends the methodology from the previous section. The workflow is: root directory fuzzing → discover first-level directories → recursive fuzzing expands into each discovered branch automatically → file fuzzing (with `-e`) runs at every level simultaneously. The result is a full map of the application's directory tree in a single command.

# Parameter and Value Fuzzing 

**GET vs POST Parameters**

GET parameters are appended to the URL after `?` and are visible in the browser and server logs. POST parameters are transmitted in the request body, hidden from the URL, and typically used for sensitive data like credentials. Both are equally fuzzable but require different syntax in the tooling.

**Why Probe Manually First**

The `curl` probes before fuzzing establish a baseline. Knowing the application returns "Invalid parameter value" for wrong inputs tells you exactly what the error state looks like. This is essential for filtering: you want to hide this noise and surface the one response that differs, which signals the valid value was found.

**GET Fuzzing with wenum**

The `FUZZ` keyword replaces the parameter value in the URL. The `--hc 404` flag hides 404 responses, but in this case the baseline error is a 200 with an "invalid" message - so the filter here reduces noise by hiding irrelevant status codes. The one result with a different response size or content is the hit.

**POST Fuzzing with ffuf**

The key difference from directory fuzzing is the `-X POST` flag (sets the HTTP method), `-H "Content-Type: application/x-www-form-urlencoded"` (tells the server how the body is encoded), and `-d "y=FUZZ"` (places the payload in the request body instead of the URL). The `-mc 200` filter shows only responses with a 200 status, cutting out the invalid-value noise and surfacing only the valid match.

**Validation with curl**

After fuzzing identifies a candidate value, `curl` confirms it manually. This step separates a true positive from a fuzzer artifact and is standard practice before reporting or continuing exploitation.

**Chaining to Prior Sections**

Directory and file fuzzing (prior sections) reveals the existence of endpoints like `get.php` and `post.php`. Parameter fuzzing then probes those endpoints for valid input values. Together, the techniques form a progressive reconnaissance chain: structure first, then behavior.



# Virtual Host and Subdomain Fuzzing

**Virtual Hosts vs Subdomains**

Virtual hosts are identified by the HTTP `Host` header, not DNS. Multiple vhosts can share a single IP address - the server routes requests to the correct site based on the `Host` header value alone. Subdomains are different: they require actual DNS records pointing to an IP. A subdomain that resolves in DNS is publicly registered; a vhost that isn't in DNS is only reachable if you manually set the `Host` header or add it to your hosts file.

This distinction matters for fuzzing: vhost fuzzing manipulates the `Host` header directly against a known IP, while subdomain fuzzing queries DNS to find names that actually resolve.

**Why Add the Entry to /etc/hosts**

Because the target vhost is not registered in public DNS, the attack machine has no way to resolve `inlanefreight.htb` without a manual hosts file entry. The `tee -a` command appends the IP-to-hostname mapping so that all subsequent requests to that hostname route correctly to the target.

**Gobuster vhost Mode**

`gobuster vhost` sends HTTP requests to the target URL with a modified `Host` header for each wordlist entry. The `--append-domain` flag is critical: without it, Gobuster sends bare words (e.g., `admin`) as the Host header. With it, it sends fully qualified names (e.g., `admin.inlanefreight.htb`), which is what web servers actually match against. A 200 response indicates the server recognized and served content for that vhost.

**Gobuster DNS Mode**

`gobuster dns` works differently from vhost mode. It generates candidate subdomain names from the wordlist, prepends them to the target domain, and attempts DNS resolution for each. A successful DNS resolution confirms the subdomain exists. This does not test HTTP responses - it only confirms DNS records are present.

**Version Note**

In newer versions of Gobuster, `-d` was repurposed to set request delay rather than specify the domain. The correct flag for the target domain in current versions is `--domain`. Using `-d` on a newer build will silently set a delay value instead of targeting the domain, producing no useful results.

**Chaining to Prior Sections**

vhost and subdomain fuzzing expands the attack surface discovered so far. Directory fuzzing maps content within a known host; vhost fuzzing reveals entirely separate applications running on the same server that would never appear in directory scans. Each discovered vhost or subdomain becomes a new target for all prior techniques.

# Filtering Fuzzing Output

**Why Filtering Is Essential**

A full wordlist scan against even a modest endpoint generates thousands of responses. Without filtering, the valid findings are buried in noise - typically hundreds or thousands of 404 responses that all look the same. Filtering is not optional cleanup; it is what makes fuzzing output readable and actionable.

**Default ffuf Behavior**

ffuf's default matcher includes status codes 200-299, 301, 302, 307, 401, 403, 405, and 500. This is a deliberate design choice that eliminates 404 noise automatically. The `-mc all` demonstration in the section shows what happens without this filter: every single wordlist entry produces a result, making it nearly impossible to identify the hit. Understanding the default is important so you know when to override it and when to rely on it.

**Match vs Filter Logic**

Every tool covered uses two complementary approaches: matching (allowlist - show only these) and filtering (denylist - hide these). They are logically opposite. Using match flags is better when you know exactly what a valid response looks like. Using filter flags is better when you know what noise looks like but can't fully characterize valid responses in advance. Combining both in the same command is valid and often produces the cleanest output.

**Size and Word Count Filters**

When a server returns the same error page for every invalid input, all those responses share identical size and word count. Filtering on `-fs` or `-fw` (ffuf) or `--hs`/`--hw` (wenum) effectively removes the entire error-response class in a single flag, even when they return a 200 status code - a common pattern in applications that return 200 with an "invalid input" body rather than a 404.

**Regex Filters**

wenum's `--sr` and `--hr` flags (and FeroxBuster's `-X`) allow filtering by response body content rather than metadata. This is useful when the distinguishing signal is text in the response rather than a status code or size difference, such as filtering for responses containing the word "admin" or excluding responses containing "Access Denied."

**FeroxBuster's `--dont-scan`**

This flag is unique to FeroxBuster's recursive mode. Because FeroxBuster automatically follows discovered directories, `--dont-scan` prevents it from descending into known-irrelevant paths (like an uploads directory full of images) that would waste time and generate noise without useful findings.



# Validating Findings 

**Why Validation Exists as a Separate Step**

Fuzzing produces candidates, not confirmed vulnerabilities. A 200 response to a directory name means the path exists - it does not automatically mean the content is sensitive, accessible, or exploitable. Validation closes that gap by confirming the finding is real, understanding what it exposes, and producing evidence without causing harm.

**Directory Listing Confirmation**

The first `curl` call against `/backup/` checks whether the server returns a browsable directory index. If it does, the HTML response will contain file names, sizes, and modification dates. This alone constitutes a directory listing vulnerability because unauthenticated users can enumerate the directory's contents.

**Header-Only Validation with `-I`**

Once files are identified in a directory listing, the responsible next step is to confirm their existence and size without reading their contents. The `-I` flag sends a HEAD request, which retrieves only response headers. The two headers of interest are `Content-Type` (confirms what kind of file it is) and `Content-Length` (a non-zero value confirms the file has actual data). This technique provides strong evidence for a vulnerability report without directly reading or exfiltrating sensitive content.

**Why This Approach Is Preferred**

Directly downloading a `backup.sql` or `password.txt` file in a real engagement crosses from confirming a vulnerability into accessing data you are not necessarily authorized to read. The header-only approach demonstrates the vulnerability exists and the data is accessible without consuming the data itself, which is the appropriate boundary for a proof of concept.

**Chaining from Prior Sections**

Fuzzing (directory, recursive, or file fuzzing from earlier sections) surfaces candidates like `/backup/`. Validation then confirms those candidates. This two-phase workflow - discover with fuzzing, confirm with manual `curl` - is the complete methodology and applies to every finding type covered in this module.

# Web APIs 

**What a Web API Is**

A Web API is a contract between a server and a client that defines how data and functionality can be accessed over the network. Unlike a traditional web server that serves HTML pages for humans to read in a browser, an API serves structured data for applications to consume programmatically.

**REST**

REST is the most common API style in modern web applications. It maps HTTP methods directly to CRUD operations: GET retrieves data, POST creates it, PUT updates it, DELETE removes it. Resources are identified by URLs. Data is exchanged in JSON or XML. Because REST is stateless and uses standard HTTP, it is the most frequent target in web security assessments.

**SOAP**

SOAP is older and more rigid, using XML envelopes transmitted over HTTP or SMTP. It is common in enterprise and financial systems where strict data integrity and error handling are required. SOAP endpoints are less common in modern applications but still appear in legacy systems.

**GraphQL**

GraphQL exposes a single endpoint and lets the client specify exactly what data it wants in a query. This flexibility also creates a larger attack surface: a single endpoint handles all operations, introspection can reveal the entire schema, and poorly restricted queries may allow clients to access more data than intended.

**Why This Matters for Fuzzing**

The shift from web server to API changes what you target. Directory and file fuzzing finds paths on a web server. API fuzzing targets endpoints, HTTP methods, and parameter values within structured data payloads. The data format (JSON, XML, GraphQL query syntax) must be respected when constructing payloads, and filters must be tuned to distinguish valid API responses from errors. This section establishes the conceptual foundation for the API-specific fuzzing techniques covered in the following sections.


# Identifying Endpoints 

**Why Endpoint Discovery Comes Before Fuzzing**

You cannot fuzz an API endpoint you do not know exists. This section covers the reconnaissance phase specific to APIs: finding what endpoints are available, what parameters they accept, and what data formats they expect. Without this, fuzzing is blind.

**REST Endpoint Structure**

REST endpoints follow a resource-hierarchy URL pattern. Collections use plural nouns (`/users`), and specific items append an ID (`/users/123`). Parameters come in three forms: query parameters in the URL (for filtering/sorting), path parameters embedded in the URL (for resource identification), and request body parameters in POST/PUT/PATCH bodies (for creating or updating data). Each form requires a different fuzzing approach.

**SOAP Discovery via WSDL**

SOAP's single-endpoint model means URL fuzzing is less relevant. The WSDL file is the primary discovery target - it defines every available operation, its input parameters, data types, and the endpoint URL. Obtaining the WSDL (often accessible at `?wsdl` appended to the service URL) gives a complete map of the API before any fuzzing begins.

**GraphQL Introspection**

GraphQL's introspection system is its most powerful discovery feature. Sending an introspection query to the `/graphql` endpoint returns the entire schema: all types, fields, queries, mutations, and their arguments. This is equivalent to receiving complete API documentation automatically. If introspection is enabled (it often is in development environments), it eliminates most guesswork about what to fuzz.

**Network Traffic Analysis as a Universal Method**

When documentation and introspection are unavailable, intercepting live traffic with Burp Suite or Wireshark reveals real API calls as they happen. This works for all three API types and captures the exact request structure, parameter names, and data formats that the legitimate client uses - which becomes the template for fuzzing payloads.

**Chaining to the Next Section**

Endpoint discovery feeds directly into API fuzzing. The endpoints, parameter names, and data formats identified here become the targets and templates for the fuzzing commands covered in the following interactive section.


# API Fuzzing 

**How API Fuzzing Differs from Web Fuzzing**

Standard web fuzzing targets directories, files, and parameter values on traditional web servers. API fuzzing targets endpoints, HTTP methods, headers, and structured request bodies on APIs. The core mechanic (wordlist + injection point + response analysis) is the same, but the context and targets are different.

**Three Types of API Fuzzing**

Parameter fuzzing injects unexpected values into query params, headers, or request bodies to expose injection vulnerabilities and input validation flaws. Data format fuzzing manipulates the structure or encoding of JSON/XML payloads to trigger parsing errors or buffer issues. Sequence fuzzing tests chains of API calls in different orders or timings to expose race conditions, IDOR, or authorization bypasses.

**The /docs Endpoint**

FastAPI and similar frameworks auto-generate a Swagger/OpenAPI specification accessible at `/docs`. This gives an attacker a complete map of documented endpoints before any fuzzing begins. However, undocumented endpoints - those intentionally or accidentally omitted from the spec - will not appear here. This is what the fuzzer is designed to find.

**Interpreting Fuzzer Output**

The 404 responses represent the noise - paths that do not exist. The 200 responses are valid endpoints. The 405 (Method Not Allowed) for `/items` is significant: the endpoint exists, but the fuzzer used the wrong HTTP method (likely GET against a POST-only endpoint). This is a common API signal that tells you the endpoint is real but requires a different method to interact with correctly.

**Why Undocumented Endpoints Matter**

Hidden endpoints bypass the security review process that documented endpoints receive. They may lack authentication, input validation, or rate limiting because they were never intended for public use or were forgotten. Fuzzing surfaces these endpoints regardless of whether they appear in any documentation.

**Chaining from Prior Sections**

The workflow mirrors earlier sections: discover first (endpoint fuzzing), then investigate with curl (validation). The same two-phase methodology - automated discovery followed by manual confirmation - applies to APIs exactly as it does to directories and files.
# Skills Assessment 

**Full Attack Chain**

This assessment chains every technique from the module in sequence. Each step's output feeds the next, forming a complete reconnaissance and exploitation workflow.

**Step 1 - Directory Discovery**

Standard directory fuzzing against the web root using the medium-sized wordlist. The goal is to find the first non-obvious path, which turns out to be `/admin`.

**Step 2 - File Discovery**

Once `/admin` is confirmed, file fuzzing with extensions drills into it. The `-e` flag multiplies every wordlist entry across five extensions. This surfaces `panel.php`.

**Step 3 - Parameter Baselining**

Manually probing `panel.php?accessID=1` establishes the baseline response for an invalid parameter value. The response size (58 bytes in the ffuf variant) becomes the filter target.

**Step 4 - Parameter Value Fuzzing**

Both wenum and ffuf are shown as equivalent approaches. The wenum variant uses `--hc 404` to reduce noise. The ffuf variant uses `-fs 58` to filter out the known invalid-response size, surfacing only the one hit that differs. The valid value is `getaccess`.

**Step 5 - Validation**

Accessing the URL with `accessID=getaccess` confirms the parameter value produces a distinct response, completing the GET parameter fuzzing chain.

**Step 6 - vhost Fuzzing**

The `-H "Host: FUZZ.fuzzing_fun.htb"` flag injects wordlist entries as the Host header value. The `-fs 250-350` filter eliminates the default vhost response size range, leaving only the valid vhost hit: `hidden.fuzzing_fun.htb`.

**Step 7 and 8 - Deep Directory and Recursive Fuzzing**

The discovered vhost has a `/godeep/` path. Directory fuzzing maps it, then FeroxBuster's recursive mode (`-r`) automatically descends into every subdirectory found. The `-t 50` flag sets 50 concurrent threads for speed, and `-k` disables TLS certificate verification. This combination is the most thorough approach for deeply nested content.

