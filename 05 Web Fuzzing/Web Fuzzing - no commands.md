# Web Fuzzing — EXPLANATION NOTES

**Introduction**  
Web fuzzing is an automated testing approach that sends unexpected, malformed, or varied inputs to a web application to observe how it behaves. The goal is to expose weaknesses that may not appear during normal usage, making it a foundational technique in web application security testing.

**Fuzzing vs. Brute-forcing**  
Fuzzing is broad and exploratory, focusing on how applications handle strange or invalid inputs across many parameters and entry points. Brute-forcing is narrow and systematic, targeting a specific value space such as passwords, tokens, or IDs. In practice, brute-forcing is a subset-like technique often performed with fuzzing tools but with a constrained goal.

**Why Fuzz Web Applications?**  
Web applications are complex and constantly changing, which makes manual testing insufficient. Fuzzing helps uncover hidden vulnerabilities, automates large-scale input testing, simulates attacker behavior, validates input handling, improves overall code robustness, and supports continuous security when integrated into CI/CD pipelines.

**Essential Concepts**  
Wordlists define what values are tested, payloads are the actual injected inputs, and response analysis is how results are interpreted to spot anomalies. Fuzzers automate this entire process, but results must be carefully reviewed to avoid false positives and false negatives. Clearly defining fuzzing scope ensures testing remains focused, efficient, and legally safe within an engagement.

| Concept             | Description                                                                                                                                                          | Example                                                                                                                   |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| `Wordlist`          | A dictionary or list of words, phrases, file names, directory names, or parameter values used as input during fuzzing.                                               | Generic: `admin`, `login`, `password`, `backup`, `config`  <br>Application-specific: `productID`, `addToCart`, `checkout` |
| `Payload`           | The actual data sent to the web application during fuzzing. Can be a simple string, numerical value, or complex data structure.                                      | `' OR 1=1 --` (for SQL injection)                                                                                         |
| `Response Analysis` | Examining the web application's responses (e.g., response codes, error messages) to the fuzzer's payloads to identify anomalies that might indicate vulnerabilities. | Normal: 200 OK  <br>Error (potential SQLi): 500 Internal Server Error with a database error message                       |
| `Fuzzer`            | A software tool that automates generating and sending payloads to a web application and analyzing the responses.                                                     | `ffuf`, `wfuzz`, `Burp Suite Intruder`                                                                                    |
| `False Positive`    | A result that is incorrectly identified as a vulnerability by the fuzzer.                                                                                            | A 404 Not Found error for a non-existent directory.                                                                       |
| `False Negative`    | A vulnerability that exists in the web application but is not detected by the fuzzer.                                                                                | A subtle logic flaw in a payment processing function.                                                                     |
| `Fuzzing Scope`     | The specific parts of the web application that you are targeting with your fuzzing efforts.                                                                          | Only fuzzing the login page or focusing on a particular API endpoint.                                                     |


# Web Fuzzing — EXPLANATION NOTES

**Installing Go, Python and PIPX**  
These runtimes are prerequisites for the fuzzing ecosystem used in this module. Go is required for high-performance fuzzers like ffuf and gobuster. Python is required for parameter-focused fuzzers like wfuzz/wenum. pipx isolates Python tooling so fuzzers do not break system Python or each other, which is critical on pentest VMs used across many engagements.

**FFUF**  
ffuf is a fast, general-purpose web fuzzer optimized for speed and flexibility. It fits early and mid-stage reconnaissance when you want rapid discovery of directories, files, parameters, and values. It is commonly used as the default fuzzer due to its balance of performance and control.

**Gobuster**  
Gobuster is a simpler, more opinionated content discovery tool. It is especially useful for directory enumeration, DNS subdomain brute-forcing, and CMS-specific discovery such as WordPress. It is often favored when you want quick results with minimal configuration.

**FeroxBuster**  
FeroxBuster focuses on forced browsing and recursive discovery. Unlike pure fuzzers, it aggressively follows discovered paths to enumerate deeply nested content. This makes it ideal after initial discovery when you want to map hidden application structure and unlinked resources.

**wfuzz/wenum**  
wfuzz and its maintained fork wenum excel at parameter and value fuzzing. They are best suited for discovering hidden parameters, testing injection points, and brute-forcing values within requests. In modern workflows, wenum replaces wfuzz when installation issues arise, while preserving identical syntax and usage patterns.

Together, these tools form a layered web fuzzing toolkit: ffuf and gobuster for breadth, feroxbuster for depth, and wenum for precision targeting of parameters and inputs.
# Directory and File Fuzzing — EXPLANATION NOTES

**Uncovering Hidden Assets**  
Directory/file fuzzing is “forced browsing”: you probe for resources that exist but aren’t linked anywhere. Those tend to be high-value because they’re often forgotten (old versions), misconfigured (exposed configs/logs), or intended for internal use (admin panels, staging/test). Even when you don’t get an instant exploit, every discovered path expands your map of the app and usually reveals stack clues (framework/CMS patterns, naming conventions, extensions, backup habits).

**Wordlists**  
Wordlists are your “search vocabulary.” General lists (like `common.txt`) are fast and good for initial coverage; larger lists (directory-list, raft, big) trade time for depth. Two practical ideas:

- Start smaller → confirm you’re getting signal (real hits, not all 404/soft-404) → scale up.
    
- Match the list to the target: if you find a naming pattern (e.g., `/w2ksvrus`), pivot into deeper fuzzing under that directory and add extensions relevant to what the server seems to run.
    

Also note the path detail: some systems use `/usr/share/seclists/` (all lowercase), others may differ—so when a wordlist “isn’t found,” it’s often just a path mismatch.

**ffuf**  
ffuf works by replacing the `FUZZ` marker in a URL with each wordlist entry and measuring response differences. The key mental model is: you’re not just looking for `200 OK`—you’re looking for “interesting deviations” (redirects like 301/302, forbidden 401/403, and sometimes even 500s) that indicate a real route exists.

Typical flow reflected in your examples:

- Fuzz directories first (`http://IP:PORT/FUZZ`) to find candidate folders.
    
- Then fuzz files inside discovered folders (`http://IP:PORT/<found>/FUZZ`) and add extensions (`-e`) to match common web file types.
    
- Use verbosity (`-v`) when you need to see exactly what’s being requested and returned, especially during troubleshooting (soft-404s, uniform sizes, etc.).
    

For the lab question (“`/webfuzzing_hidden_path/` then find flag”), the same two-stage approach applies: fuzz folders under that base path, then fuzz files inside whatever directory you discover (and consider adding extensions appropriate to what you see).
# Recursive Fuzzing — EXPLANATION NOTES

**How Recursive Fuzzing Works**  
Recursive fuzzing is “directory fuzzing that fans out automatically.” Instead of you discovering `/admin/` and then manually rerunning a scan against `/admin/FUZZ`, the tool treats every discovered directory like a new starting point and queues it for scanning. The tree analogy is accurate: root → discovered dirs → scan inside each dir → discover deeper dirs → repeat, until depth is reached or the queue is empty.

**Recursive Fuzzing with ffuf**  
ffuf recursion hinges on one behavior: it decides a “directory exists” based on response signals (commonly 301/302 redirects to a trailing slash, or valid codes you allow). When it sees that, it enqueues `http://.../<dir>/FUZZ` as another job and continues.

Key switches from your snippet:

- `-recursion` turns on the queueing/branching behavior.
    
- `-recursion-depth` prevents runaway scans on deep apps.
    
- `-e .html` broadens discovery by trying extensions (useful when you expect files like `index.html`).
    
- `-ic` avoids wasting requests on commented wordlist lines that start with `#`.
    
- `-v` helps you debug what is being requested and how redirects/paths are forming.
    

For the lab prompt targeting `http://IP:PORT/recursive_fuzz/`, the practical workflow is: start recursion from that base path (not from `/`) so your scan stays scoped; then follow up on “bigger/different” results (status/size/words) because the flag often sits in a slightly different `index.html` or similarly named file deeper in the tree.

**Be Responsible**  
Recursive fuzzing can hammer servers fast because every found directory multiplies work. Use depth limits to bound expansion, rate limiting to avoid DoS-like behavior, and timeouts to keep scans from stalling on slow endpoints.

# Parameter and Value Fuzzing — EXPLANATION NOTES

**GET Parameters**  
GET parameters are exposed directly in the URL and are ideal candidates for discovery and value fuzzing. Because they are visible and often loosely validated, they frequently leak logic flaws, hidden functionality, or developer backdoors. Parameter fuzzing here focuses on finding _valid values_, not just parameter names.

**POST Parameters**  
POST parameters live in the request body and are commonly used for sensitive actions like authentication or state changes. Although hidden from the URL, they are equally fuzzable once identified. POST fuzzing often uncovers authorization bypasses, hidden modes, or alternate code paths that GET requests do not expose.

**wenum**  
wenum excels at value fuzzing when a parameter name is known but the accepted value is not. The key signal is _response deviation_: a different status code, size, or message compared to baseline “invalid” responses. Hiding noisy status codes (like 404) helps isolate meaningful hits quickly.

**curl**  
Manual interaction with curl establishes baseline behavior. This step is critical: it tells you which parameter is required, how the application reacts to missing vs invalid values, and what “normal” looks like before automation. Automation without baselining often leads to false conclusions.

**ffuf**  
ffuf is used for POST value fuzzing by injecting payloads into the request body. Unlike wenum, ffuf gives you fine-grained control over HTTP method, headers, and body format, making it ideal for form-based workflows. Filtering on response codes (e.g., `-mc 200`) is the fastest way to isolate valid parameter values in lab-style and real-world targets.

Overall, parameter and value fuzzing shifts you from _mapping surface area_ to _breaking logic_. Once directories and endpoints are known, parameters become the most common path to real vulnerabilities.
# Virtual Host and Subdomain Fuzzing — EXPLANATION NOTES

**Virtual Hosts vs Subdomains**  
Virtual hosts are differentiated at the HTTP layer using the `Host` header, allowing multiple sites to live on the same IP and port. Subdomains are differentiated at the DNS layer and resolve to IP addresses before any HTTP request is made. This distinction matters because vhost fuzzing bypasses DNS entirely, while subdomain fuzzing depends on DNS resolution.

**/etc/hosts**  
Manually adding an entry to `/etc/hosts` forces your system to resolve a hostname to a specific IP. This is required for vhost fuzzing labs because DNS will not resolve internal lab hostnames, but the web server still responds based on the `Host` header.

**Gobuster**  
Gobuster is a multi-purpose brute-force discovery tool. In vhost mode, it mutates the `Host` header while keeping the same IP and URL, watching for response changes that indicate a valid virtual host. In DNS mode, it brute-forces subdomain names and checks whether they resolve.

The `--append-domain` flag is critical for vhost fuzzing because most servers expect a fully qualified hostname (e.g., `admin.inlanefreight.htb`), not a bare label.

**Wordlists**  
Vhost fuzzing typically uses generic web content wordlists (`common.txt`) because vhost names often mirror directory or application naming conventions. Subdomain fuzzing relies on DNS-specific wordlists ranked by real-world frequency, since DNS resolution is slower and noisier than HTTP-based fuzzing.

In a real engagement, vhost fuzzing often reveals internal admin panels or staging apps, while subdomain fuzzing expands the external attack surface and can lead to takeover or forgotten services.
# Filtering Fuzzing Output — EXPLANATION NOTES

**Gobuster**  
Gobuster filtering is primarily about reducing obvious noise during directory scans. Status-code allowlisting (`-s`) and denylisting (`-b`) quickly remove useless responses like 404s, while length filtering helps eliminate uniform error pages. Gobuster filtering is simple but effective for early discovery.

**FFUF**  
ffuf provides the most granular filtering controls. You can filter or match on status codes, response size, word count, line count, or timing behavior. The key idea is _baseline comparison_: once you identify what “normal” responses look like, you filter those out to expose anomalies. Time-based filtering (`-mt`) is especially useful for detecting logic branches, delays, or potential blind vulnerabilities.

**wenum**  
wenum filtering is designed for parameter and value fuzzing, where most responses are invalid and repetitive. Hiding common codes, sizes, or word counts lets you immediately surface values that trigger different logic paths. Regex-based filters are powerful when you know keywords or error strings to hunt for.

**Feroxbuster**  
Feroxbuster filtering is optimized for recursive, high-volume scans. It focuses on excluding known-bad patterns (status codes, sizes, regex matches) so recursion doesn’t explode with useless data. Similarity filtering is especially useful for suppressing templated error pages that otherwise flood results.

**Why Filtering Matters**  
Fuzzing without filtering produces overwhelming noise. Effective filtering turns fuzzing from brute-force guessing into signal detection. In real engagements, most real findings stand out not because they are obvious—but because they are _different_ from the baseline you’ve filtered away.

|Flag|Description|Example Scenario|
|---|---|---|
|`--dont-scan` (Request)|Exclude specific URLs or patterns from being scanned (even if found in links during recursion).|You know the `/uploads` directory contains only images, so you can exclude it using `--dont-scan /uploads`.|
|`-S`, `--filter-size`|Exclude responses based on their size (in bytes). You can specify single sizes or comma-separated ranges.|You've noticed many 1KB error pages. Use `-S 1024` to exclude them.|
|`-X`, `--filter-regex`|Exclude responses whose body or headers match the specified regular expression.|Filter out pages with a specific error message using `-X "Access Denied"`.|
|`-W`, `--filter-words`|Exclude responses with a specific word count or range of word counts.|Eliminate responses with very few words (e.g., error messages) using `-W 0-10`.|
|`-N`, `--filter-lines`|Exclude responses with a specific line count or range of line counts.|Filter out long, verbose pages with `-N 50-`.|
|`-C`, `--filter-status`|Exclude responses based on specific HTTP status codes. This operates as a denylist.|Suppress common error codes like 404 and 500 using `-C 404,500`.|
|`--filter-similar-to`|Exclude responses that are similar to a given webpage.|Remove duplicate or near-duplicate pages based on a reference page using `--filter-similar-to error.html`.|
|`-s`, `--status-codes`|Include only responses with the specified status codes. This operates as an allowlist (default: all).|Focus on successful responses using `-s 200,204,301,302`.|
# Validating Findings — EXPLANATION NOTES

**Manual Verification**  
Validation is the “prove it, safely” step after fuzzing. Fuzzers surface anomalies, but anomalies aren’t automatically vulnerabilities. Manual verification confirms whether the resource actually exists, whether it’s accessible, and what the practical impact is—without overstepping into destructive or unnecessary data access.

A clean validation workflow is:

1. Reproduce the exact request that produced the interesting result.
    
2. Inspect the response carefully for evidence (directory listing, sensitive filenames, unusual headers, tech stack clues).
    
3. If the issue looks real, create a minimal PoC that demonstrates risk without dumping sensitive contents.
    

**Example**  
The example shows a classic “directory listing + backups” scenario. A discovered `/backup/` directory returning `200` becomes significant because backup directories often contain high-impact artifacts (dumps, configs, source). The responsible move is to confirm exposure while minimizing data handling:

- Requesting the directory (`curl http://.../backup/`) tells you if indexing is enabled and what filenames exist.
    
- Requesting headers only (`curl -I http://.../backup/password.txt`) is a low-impact way to confirm that a suspicious file has non-zero content and an informative `Content-Type`, without downloading or viewing secrets.
    
- `Content-Length` is a key validation data point: non-zero indicates real content; combined with a sensitive filename, it’s strong evidence of risk.
    
- Server headers (e.g., `Server: lighttpd/1.4.76`) give you stack context that may matter for reporting and remediation.
    

For the lab-style question mentioning a `.tar.gz`, the same concept applies: validate via headers (especially `Content-Length` and `Content-Type`) to show that an archive exists and contains data, which supports impact, without extracting it.

# Web APIs — EXPLANATION NOTES

**Representational State Transfer (REST)**  
REST APIs expose resources through distinct URLs and use standard HTTP verbs to operate on them. Each request is stateless, meaning all required context must be provided by the client. For fuzzing, this shifts focus toward endpoint discovery, HTTP method abuse (e.g., unexpected PUT/DELETE), and parameter manipulation within JSON or query strings.

**Simple Object Access Protocol (SOAP)**  
SOAP APIs rely on rigid XML-based message structures and predefined schemas. While less common in modern apps, they are still prevalent in enterprise environments. Fuzzing SOAP often targets XML structure manipulation, unexpected fields, oversized payloads, or logic flaws in defined operations rather than directory or file discovery.

**GraphQL**  
GraphQL exposes a single endpoint with a flexible query language. This changes fuzzing strategy significantly: instead of enumerating endpoints, you enumerate queries, mutations, fields, and object relationships. Introspection (if enabled) can drastically expand the attack surface, making schema discovery a key initial step.

**Advantages of Web APIs**  
APIs enable modular design, third-party integrations, and microservices architectures. From an attacker’s perspective, this increases complexity and trust boundaries. From a tester’s perspective, it creates multiple opportunities for authorization bypasses, excessive data exposure, and logic flaws between services.

**How APIs are different from a web server**  
Traditional web fuzzing targets visible resources like directories, files, and pages meant for human interaction. API fuzzing targets machine-oriented interfaces, focusing on endpoints, methods, data formats, and business logic. Effective API fuzzing prioritizes parameter structure, data types, authentication context, and response consistency over simple path discovery.
# Identifying Endpoints — EXPLANATION NOTES

**REST**  
REST endpoint discovery is about mapping resources and how they are accessed. Endpoints follow predictable, hierarchical URL patterns that reflect objects and collections. Effective reconnaissance focuses on identifying base resource paths, then expanding into IDs, actions, and optional parameters. Query, path, and body parameters often expose business logic and are prime fuzzing targets once endpoints are known.

Common discovery techniques include reviewing API documentation (OpenAPI/Swagger), observing live traffic in browser dev tools or Burp, and fuzzing parameter names to uncover undocumented behavior.

**SOAP**  
SOAP APIs usually expose a single endpoint, with functionality defined inside XML messages rather than URLs. Endpoint discovery therefore centers on finding and analyzing the WSDL file, which acts as a full blueprint of the service. Operations, parameters, and data types are explicitly defined, making SOAP less about guessing paths and more about understanding message structure.

When WSDL is unavailable, traffic capture and controlled fuzzing of XML elements can reveal accepted operations or hidden parameters.

**GraphQL**  
GraphQL flips the model entirely by using a single endpoint (commonly `/graphql`) and exposing functionality through queries and mutations. Endpoint identification is trivial; capability identification is not. Discovery focuses on schema enumeration: fields, types, relationships, queries, and mutations.

Introspection (when enabled) is the most powerful discovery mechanism, effectively handing you the API map. When disabled, network traffic analysis and inference from client behavior become the primary tools. Fuzzing GraphQL targets schema logic, argument handling, authorization boundaries, and data exposure rather than URL paths.
# API Fuzzing — EXPLANATION NOTES

**Exploring the API**  
API fuzzing starts with knowing what “should” exist so you can spot what “shouldn’t.” Here, `/docs` is the big win because it exposes Swagger/OpenAPI documentation (common with FastAPI). That documentation gives you the intended surface area (methods + paths + schemas). The gap between documented endpoints and what the server actually responds to is where hidden functionality tends to live.

**Fuzzing the API**  
This approach fuzzes _path segments_ to discover undocumented endpoints. The script is essentially doing directory-style fuzzing against an API base URL and classifying results by HTTP status:

- Lots of `404` is normal: most guesses don’t exist.
    
- A `200` suggests a real endpoint with a valid response.
    
- A `405` is also useful: it often means the _path exists_ but the _method_ is wrong (e.g., `GET /items` returns 405 because only `POST /items/` is allowed). That’s an endpoint-discovery signal, not noise.
    

Once a candidate endpoint is found (like `/cz...`), you validate it manually with `curl` and capture the value returned (in this lab, it returns JSON containing a `flag` field). This “fuzz → spot anomaly → manually verify” loop is the core API fuzzing workflow.

Types-wise, this example is “endpoint/path fuzzing” (a form of parameter fuzzing at the URL-path level). After endpoints are found, you pivot into fuzzing request bodies, headers, and sequences to uncover authorization and logic issues (e.g., BOLA/IDOR, BFLA, SSRF).

#
#
#
#
#
#
#
#