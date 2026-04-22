# Intro to Web Proxies

**What Web Proxies Are**

Web proxies sit between a client (browser or mobile app) and a back-end server, intercepting and recording all HTTP/HTTPS traffic. Unlike packet sniffers such as Wireshark, which capture all network traffic indiscriminately, web proxies focus specifically on web ports (80, 443) and operate at the application layer, making them far more useful for web application testing.

**Why They Matter in Pentesting**

The ability to see, modify, and replay individual HTTP requests is the foundation of web application penetration testing. A proxy gives the tester full visibility into what data is being sent and received, and the ability to manipulate that data before it reaches the server. This underpins nearly every web attack technique covered in other modules.

**Use Cases**

Beyond simple request capture, proxies support vulnerability scanning, fuzzing, crawling, application mapping, request analysis, configuration testing, and code review workflows. These use cases span both reconnaissance and active exploitation phases of a pentest.

**Burp Suite vs ZAP**

Burp Suite is the industry-standard tool with a polished interface and a built-in Chromium browser. Its free community edition covers most testing scenarios, but features like the active scanner, faster Intruder, and extension loading require a paid Pro/Enterprise license. A free trial is available via the PortSwigger link for those with educational or business email addresses.

ZAP is the leading open-source alternative, maintained by the OWASP community with no paid tier. Its main advantage is the absence of throttling or feature restrictions that affect Burp's free edition. ZAP is gaining parity with Burp Pro features over time through community contributions.

**Choosing Between Them**

Both tools share overlapping capabilities and learning either transfers well to the other. The practical choice depends on context: ZAP for fully open, unrestricted engagements; Burp Pro where maturity and advanced features justify the cost. Familiarity with both is the recommended approach.


# Setting Up 

**Installation Overview**

Both tools are cross-platform and pre-installed on PwnBox, Parrot, and Kali. For custom VMs, OS-specific installers are available from their respective download pages. Both also offer a universal JAR file as an alternative, requiring only a Java Runtime Environment to run. JRE is bundled in modern installers but may need manual installation in minimal environments.

**Starting Burp Suite**

On first launch, Burp prompts for a project type. The community edition restricts users to temporary projects only - no disk-based saving. The Pro and Enterprise editions unlock persistent projects, which matter for large-scope engagements or long Active Scanner runs. For lab and module work, temporary projects are sufficient. After selecting the project type, Burp offers default configurations or a custom config file - defaults are appropriate for standard use.

**Starting ZAP**

ZAP's startup prompt differs from Burp's free edition in a meaningful way: even the free version of ZAP offers session persistence. For short lab sessions, selecting no persistence (temporary session) is the practical choice. The persistence option is relevant only for multi-day engagements where continuity between sessions is needed.

**Java Dependency**

Both tools depend on JRE. Installers typically bundle this automatically. Manual installation from Oracle's documentation page is only needed on custom or minimal systems where JRE is absent.

**Dark Theme**

Both tools support dark themes through their respective settings menus. This is a cosmetic preference with no functional impact on testing capability.

# Proxy Setup 

**Pre-Configured Browser**

Both Burp and ZAP ship with a pre-configured browser that has proxy settings and CA certificates already in place. This is the fastest way to start intercepting traffic and requires no additional configuration. It is sufficient for most lab and module work.

**Manual Firefox Proxy Configuration**

When a real browser is needed instead of the embedded one, Firefox must be pointed at the proxy's listening address and port. Both tools default to port 8080 on localhost. If that port is occupied, the proxy will fail to start. The listening port can be changed in each tool's settings, but the Firefox proxy configuration must match whatever port is chosen.

**FoxyProxy Extension**

Manually toggling Firefox's proxy settings in preferences is cumbersome. FoxyProxy solves this by allowing instant switching between proxy profiles from the toolbar icon. On PwnBox it is pre-installed and pre-configured. On a custom VM it must be installed from the Firefox Extensions page and configured with the 127.0.0.1:8080 entry manually.

**CA Certificate - Why It Matters**

HTTPS traffic is encrypted using TLS. For a proxy to intercept and display HTTPS requests in plaintext, it performs a MITM against the TLS handshake by presenting its own certificate to the browser. Without installing the proxy's CA certificate as a trusted authority in Firefox, the browser will either reject the connection outright or prompt for acceptance on every HTTPS request. Installing the CA cert prevents this by making Firefox trust the proxy's dynamically generated certificates.

**CA Certificate - Burp vs ZAP**

Burp's certificate is retrieved by browsing to the special URL `http://burp` while FoxyProxy is routing traffic through Burp. ZAP's certificate is exported directly from within the ZAP application settings. Both are then imported into Firefox through the Certificate Manager under the Authorities tab, with both trust options checked.

**Chain Summary**

FoxyProxy routes browser traffic to the proxy port, the proxy intercepts and decrypts HTTPS using its CA cert (trusted by Firefox), and all requests become visible and modifiable in Burp or ZAP before forwarding to the target server.



# Intercepting Web Requests

**How Interception Works**

When interception is enabled, the proxy holds each outgoing request in a paused state before forwarding it to the server. This gives the tester full read/write access to the raw HTTP request, including headers, parameters, cookies, and body content, before the server ever sees it.

**Burp vs ZAP Toggle Behavior**

Burp has interception enabled by default and uses a toggle button in the Intercept sub-tab. ZAP defaults to interception off, indicated by a green pass-through button on the toolbar. Either CTRL+B or clicking the button toggles it. ZAP's HUD additionally allows toggling interception from directly within the browser viewport, which is useful for in-browser workflows.

**ZAP HUD**

The HUD overlays ZAP controls onto the pre-configured browser window, removing the need to switch between browser and ZAP application windows. The step button forwards one request and re-arms the break for the next, while continue forwards all remaining requests without further interception. Step is useful for tracing a full page load request by request; continue is used when only one specific request is of interest.

**The Manipulation Example**

The ping page enforces numeric-only input through client-side JavaScript validation. This validation only exists in the browser and has no effect once the request is intercepted. By changing `ip=1` to `ip=;ls;` directly in the proxy, the payload bypasses the front-end restriction entirely. Because the back-end performs no independent validation, it executes the injected shell command, demonstrating a command injection vulnerability. The response changes from ping output to a directory listing, confirming successful injection.

**Why This Matters**

Front-end validation is cosmetic from a security standpoint. Any control enforced only in the browser can be trivially bypassed through a proxy. This example establishes the core attacker mindset for web testing: the proxy eliminates the browser as a gatekeeper and allows direct communication with the back-end, exposing the true attack surface of the application.


## Intercepting Requests

This section shows the core value of a web proxy: pausing a live browser request before it reaches the server. That pause gives the tester a controlled point where they can inspect the request, understand what the application is sending, and decide whether to let it continue unchanged or alter it first.

In Burp, interception is enabled from the Proxy area. In ZAP, interception is toggled from the top bar, and the browser-facing HUD can expose similar controls directly inside the pre-configured browser.

The important idea is not the button location but the workflow: browser sends request, proxy catches it, tester reviews it, then forwards, drops, or edits it.

## Why Interception Matters

Without interception, you only see what the front end allows you to send. With interception, you see what the server actually receives.

That distinction is critical because many applications apply restrictions only in client-side JavaScript. If validation exists only in the browser, a tester can bypass it by modifying the request after the browser generates it but before the server processes it.

This makes interception one of the most important steps in discovering server-side weaknesses.

## The Example Request

The captured request in Output A shows a form submission sent to the ping functionality. The important parts are:

- the request method
    
- the target path
    
- the headers
    
- the body parameter carrying user input
    

The body contains a single parameter that represents the value entered into the page’s IP input field. That parameter is the attacker-controlled input and therefore the main point of interest.

## Front-End Restriction vs Back-End Reality

The page only allows numeric input in the browser interface. That makes it look as if the field is safe.

But that restriction is only cosmetic unless the server repeats the same validation. Once the request is intercepted, the original numeric value can be replaced with arbitrary characters. This tests whether the server trusts the client too much.

The attacker mindset here is simple: never trust what the UI appears to enforce. Always verify what the server accepts.

## Manipulating the Parameter

The lab demonstrates replacing the original value with a command-injection style payload. The goal is to see whether the server concatenates unsanitized input into a system command.

If the server-side code builds something like a shell ping command directly from the submitted parameter, special shell characters can break out of the intended argument context and append a second command.

That is why this works in the lab: the server is implicitly trusting input that should have been validated and safely handled.

## Why the Payload Works

The replacement value shown in Output A uses shell metacharacters to terminate the intended argument and append another operating system command. If the application passes user input into a shell without proper sanitization or without using safe APIs, the injected command runs on the server.

The changed response confirms that the server executed something other than the intended ping behavior. That difference in behavior is the evidence of successful request manipulation.

## How This Fits Into Web Exploitation Methodology

This is a classic progression:

First, capture the legitimate request.  
Then, identify the user-controlled parameter.  
Then, modify only that parameter.  
Then, observe how the response changes.

That pattern applies across many vulnerability classes, not just command injection. The same proxy-driven methodology is used to test for SQL injection, XSS, authentication weaknesses, file upload issues, deserialization flaws, and more.

The proxy is not the exploit itself. It is the mechanism that lets the tester turn a normal request into a security test case.

## Burp, ZAP, and HUD Usage

Burp and ZAP perform the same core role here. Both allow the request to be paused and edited before continuing. ZAP’s HUD adds convenience by exposing interception controls inside the browser itself, which can help when stepping through application flows visually.

The purpose of stepping versus continuing is also important. Stepping is useful when you want to inspect each stage of a multi-request workflow. Continuing is better when you only care about one specific request and want the rest of the page to load normally.

## Common Pitfalls

One common mistake is editing too many parts of the request at once. That makes it hard to tell which change caused the result.

Another is assuming a blocked UI field means the back end is secure. Client-side restrictions are never sufficient evidence of real validation.

A third is forgetting that many background browser requests may be intercepted before the one you actually want. You may need to forward unrelated traffic until the target request appears.

## Key Takeaway

This section demonstrates the essential penetration-testing habit of moving beyond the browser UI and testing the raw HTTP request directly. By intercepting and modifying the submitted parameter, the tester can determine whether the server validates input securely or blindly trusts what the client sends.


# Intercepting Responses 

**Why Intercept Responses**

Request interception allows modifying what is sent to the server. Response interception allows modifying what the server sends back to the browser before it is rendered. This is useful when the page itself imposes front-end restrictions through HTML attributes, JavaScript, or hidden/disabled fields. Editing the response before render effectively removes those restrictions from the browser's perspective.

**The HTML Attribute Attack**

In this example, the input field uses `type="number"` to restrict entry to numeric characters only, and `maxlength="3"` to cap the input length. Both are purely client-side constraints enforced by the browser's rendering of the HTML. By intercepting the server's response and changing `type="number"` to `type="text"` and extending `maxlength` to 100, the browser renders an unrestricted input field. The payload `;ls;` can then be typed directly without needing to intercept and modify the outgoing request separately. This approach is more convenient when repeatedly testing the same field.

**Burp vs ZAP Response Interception**

In Burp, response interception must be explicitly enabled in proxy settings - it is off by default. In ZAP, clicking the Step button during request interception automatically captures the corresponding response in the same workflow, making it more seamless.

**ZAP HUD Show/Enable Feature**

Rather than intercepting the full response to enable a disabled field, ZAP's HUD provides a one-click shortcut via the light bulb icon. This injects enabling changes directly into the live DOM without a full response intercept cycle or page refresh. It is faster for the common case of simply unlocking disabled or hidden inputs.

**HTML Comments Feature**

The Comments indicator in ZAP HUD reveals HTML comment positions directly in the browser viewport. Developers sometimes leave sensitive information in comments (credentials, internal paths, logic notes) that are invisible in the rendered page but present in the source. This feature surfaces them without requiring manual source inspection.

**Chain with Previous Section**

Response manipulation extends the capability established in the request interception section. Instead of always injecting through an intercepted request, the tester can now also modify the page itself to remove front-end gatekeeping, then submit payloads directly through the altered browser interface. Both approaches ultimately bypass the same client-side controls and reach the same vulnerable back-end.



# Automatic Modification 

**Purpose of Automatic Modification**

Manual interception requires catching and editing each individual request or response, which becomes impractical when testing repeatedly or across multiple page loads. Automatic modification rules instruct the proxy to apply predefined find-and-replace operations to every matching request or response without tester intervention. This turns one-time manual changes into persistent, session-wide modifications.

**Request Modification - User-Agent Example**

The User-Agent header identifies the browser and OS to the server. Some applications filter or behave differently based on this value. By creating a match-and-replace rule targeting the entire User-Agent line using a regex pattern, every outgoing request will carry the custom value instead of the real browser identifier. This is useful for evading basic User-Agent-based filters or testing how the application behaves under different client identities.

**Response Modification - Persistent Field Unlocking**

In the previous section, editing `type="number"` to `type="text"` in an intercepted response was a one-time change that reverted on page refresh. An automatic response body rule applies the substitution every time the page is loaded, making the change persistent across refreshes without requiring repeated manual interception. Adding a matching rule for `maxlength` extends this by also removing the character limit, together fully unlocking the input field for arbitrary payloads.

**Burp vs ZAP Rule Configuration**

Both tools expose the same underlying capability through slightly different interfaces. Burp uses a single Match and Replace dialog with an explicit type selector (request header, response body, etc.) and a regex toggle. ZAP's Replacer uses named Match Types and a separate regex checkbox. The logical structure of both is identical: define what to find, define what to replace it with, and choose whether the match is literal or regex-based.

**Initiators in ZAP**

ZAP adds the concept of Initiators, which scopes where a Replacer rule applies - specific tools (scanner, spider, manual browse) or all messages. Keeping the default of all HTTP(S) messages ensures rules apply regardless of how the traffic is generated, which is appropriate for most testing scenarios.

**Exercise 2 Implication**

Automatically injecting `;ls;` into the request body of the Ping request on every submission means the command injection fires without any manual interception step. This demonstrates how automatic modification rules can be used to embed attack payloads persistently into the request flow, which scales well when testing multiple variations or running repeated automated interactions against the same endpoint.


# Repeating Requests 

**Why Request Repeating Matters**

Intercepting, modifying, and forwarding a request every time a payload needs to change is a slow, multi-step process. The Repeater workflow decouples payload testing from the interception cycle entirely. Once a request is sent to the Repeater, it can be edited and resent instantly as many times as needed without touching the browser or toggling interception, making iterative testing (different commands, different parameter values, different injection strings) practical at speed.

**Proxy History as a Source**

Both Burp and ZAP maintain a full log of every request that has passed through the proxy. This history is the starting point for repeating: the tester finds the relevant request in the log, sends it to the repeating tool, and works from there. Burp additionally preserves both the original and edited versions of any modified request, which is useful for auditing what was actually sent versus what the browser originally generated.

**Burp Repeater**

CTRL+R sends any selected history entry directly to the Repeater tab. From there, the request is fully editable and can be sent with a single click. The response appears in the same pane immediately. The Change Request Method option is a convenience feature that rewrites the request structure for GET/POST switching without manual reconstruction.

**ZAP Request Editor and HUD**

ZAP's equivalent is the Request Editor, opened via right-click from the history. It provides the same edit-and-send loop. The HUD adds two replay modes: Console returns the raw response within the HUD overlay, while Browser renders the response in the browser viewport. Browser replay is useful when the response contains HTML that is more readable when rendered than as raw markup.

**URL Encoding Note**

The section notes that POST body data appears URL-encoded in the repeater. This is relevant for the next section and serves as a reminder that payloads submitted through forms are encoded by the browser, and this encoding must be accounted for when crafting or modifying requests manually in the repeater.


# Encoding/Decoding 

**Why Encoding Matters in HTTP Requests**

HTTP requests transmit data as plain text over a defined format. Certain characters carry special meaning within that format: spaces can terminate data fields, ampersands delimit parameters, and hash symbols mark fragment identifiers. If these characters appear raw in a payload, the server may misparse the request. URL-encoding converts these characters to percent-encoded equivalents (e.g., space becomes `%20`) so they are transmitted as literal data rather than structural delimiters. Failing to encode properly when crafting manual requests is a common source of unexpected server errors.

**Burp vs ZAP URL Encoding Behavior**

In Burp Repeater, URL-encoding must be applied manually either via CTRL+U or the right-click menu. Burp also offers an auto-encode-as-you-type option for convenience. ZAP handles URL-encoding automatically in the background before sending, so the tester does not need to manually trigger it, though this means the encoding is not always visibly reflected in the editor.

**Decoding for Analysis**

Web applications frequently encode data in cookies, tokens, and parameters. Encountering an encoded value requires decoding it to understand what it contains before attempting to manipulate it. The base64 cookie example demonstrates the practical workflow: decode an opaque value, read its structure, modify it with attacker-controlled values, re-encode it in the original format, and inject the result back into the request. This technique applies broadly to session tokens, JWT payloads, serialized objects, and any other encoded data the application processes.

**Encoding for Injection**

After modifying a decoded value, it must be re-encoded to match the format the server expects. Submitting a raw JSON string where the server expects base64 would cause a parsing failure. The encode-decode-modify-reencode cycle is a core skill for bypassing authentication checks, privilege escalation through tampered cookies, and testing any parameter that contains structured encoded data.

**Burp Inspector**

The Inspector panel is an in-context encoder available directly inside Proxy and Repeater without navigating to the Decoder tab. It allows quick inspection and conversion of selected values inline with the request being worked on, which speeds up the workflow when encoding and request modification are interleaved.

**ZAP Custom Tabs**

ZAP's Encoder/Decoder/Hash tool supports custom tabs where the tester can configure which encoders are shown simultaneously. This is useful when a value needs to be examined in multiple encodings at once or when working repeatedly with a specific encoding type across a session.

# Proxying Tools

**Purpose of Proxying Tools**

Web proxy features (interception, history, repeating, modification) are only useful for traffic that actually passes through the proxy. Browser traffic is easily routed through the proxy via FoxyProxy or the pre-configured browser, but command-line tools and thick client applications use their own network stacks and bypass browser proxy settings entirely. To get visibility into what these tools are sending, their traffic must be explicitly redirected through the proxy.

**Proxychains**

Proxychains is a Linux utility that intercepts network calls from any process and redirects them through a configured proxy. By adding `http 127.0.0.1 8080` to the end of its config file (and commenting out the default socks4 line), all TCP traffic from any tool prefixed with `proxychains` will route through Burp or ZAP. The `-q` flag suppresses connection diagnostic output so the terminal shows only the tool's actual output. This approach requires zero modification to the tool being proxied, making it universally applicable to curl, wget, nikto, and any other CLI utility.

**Metasploit PROXIES Flag**

Metasploit modules that make HTTP requests support a dedicated PROXIES option that accepts a proxy specification in `PROTOCOL:HOST:PORT` format. Setting `HTTP:127.0.0.1:8080` routes the module's HTTP traffic through the web proxy. This is valuable for debugging scanner behavior, verifying what requests an exploit module sends before firing it against a target, and examining responses to understand how the target is reacting. The robots_txt scanner example demonstrates the pattern, which applies identically to other auxiliary modules and exploits that generate HTTP traffic.

**General Principle**

Any tool that supports proxy configuration can be pointed at the web proxy address. The tester gains the same interception, history, and repeating capabilities for that tool's traffic as for browser traffic. This makes the proxy a centralized observation and manipulation point for all HTTP activity during a pentest, regardless of what is generating the traffic.

**Performance Note**

Routing traffic through a proxy adds latency because every request must pass through the proxy process before reaching the server. This is acceptable for manual testing and investigation but slows down tools that generate high request volumes (scanners, fuzzers). Proxying should be enabled only when active inspection is needed, not as a default operational mode.


# Burp Intruder 

**What Intruder Does**

Burp Intruder automates the sending of a request with a varying payload substituted at a defined position on each iteration. It is functionally equivalent to CLI fuzzers like ffuf or gobuster but operates within the proxy environment, giving full visibility into every request and response through the Burp UI.

**Community vs Pro Speed Limitation**

The free Community edition is rate-limited to one request per second. CLI fuzzers can reach tens of thousands of requests per second. This makes Intruder Community edition unsuitable for large wordlists and only practical for short, targeted queries. The Pro edition removes this limit and is competitive with dedicated fuzzing tools in terms of throughput.

**Positions and the Sniper Attack Type**

The Positions tab is where the payload injection point is defined. Wrapping a value with `§` marks it as the substitution target. The Sniper attack type uses a single payload set and cycles through it one item at a time at one position, which is appropriate for directory fuzzing. Other attack types like Cluster Bomb support multiple simultaneous payload positions, useful for credential brute-forcing where both username and password positions need independent wordlists.

**Payload Configuration Options**

Simple List is the most straightforward option: load a file, iterate each line. Runtime file is preferred for large wordlists to avoid loading everything into memory at once. Character Substitution is useful for password mutation attacks.

**Payload Processing**

Processing rules apply transformations or filters to each wordlist item before it is sent. The regex skip rule `^\..*$` filters out lines starting with a dot, which in the common.txt wordlist are typically hidden file or directory names that tend to produce noise. This keeps the attack output cleaner and reduces irrelevant results.

**Grep - Match**

The Grep - Match setting adds a column to the results table that flags any response containing the specified string. Filtering on `200 OK` with HTTP headers included makes it trivial to identify successful directory discoveries at a glance by sorting on that column, without manually reviewing every response status code.

**Attack Flow Summary**

The workflow is: intercept or locate a request in history, send to Intruder (CTRL+I), define the payload position in the Positions tab, configure the wordlist in the Payloads tab, set processing and grep rules, then start the attack. Hits are identified by the Grep - Match column or by sorting on status code or response length.

# ZAP Fuzzer 

**ZAP Fuzzer vs Burp Intruder**

The primary advantage of ZAP Fuzzer over Burp Community Intruder is the absence of rate throttling. Burp Community is capped at one request per second, making it impractical for large wordlists. ZAP Fuzzer has no such restriction and supports concurrent threads, making it far more time-efficient for free users. The tradeoff is that ZAP Fuzzer has fewer advanced configuration options than Burp Pro Intruder.

**Fuzz Location**

The Fuzz Location is functionally identical to Intruder's payload position marker. Selecting a word in the request and clicking Add marks that position for payload substitution. The green marker in the request view confirms the position is set. Visiting a URL with a placeholder word like `/test/` first makes it easy to identify and select that word as the injection point.

**Built-in Wordlists via File Fuzzers**

ZAP ships with built-in fuzzing databases (dirbuster, fuzzdb, etc.) accessible directly through the File Fuzzers payload type. This removes the need to manage external wordlist files for common fuzzing scenarios. Additional databases can be installed from the ZAP Marketplace.

**Processors**

Processors transform each payload item before it is sent. URL Encode is the standard choice for directory fuzzing to prevent special characters in wordlist entries from being misinterpreted by the server. The Script processor is the most flexible option, allowing custom Python or other scripted transformations for advanced mutation scenarios. Prefix and Postfix processors are useful for appending file extensions or other fixed strings to every payload without modifying the wordlist itself.

**Concurrency and Traversal Strategy**

Setting concurrent threads increases throughput significantly. Depth First exhausts all payloads at one position before moving to the next, which is appropriate for credential brute-forcing (all passwords per user). Breadth First cycles through all positions once per payload, which is better when testing all parameters with the same wordlist simultaneously.

**Identifying Hits**

Sorting by response code isolates 200 OK responses, which indicate accessible resources in directory fuzzing. In other attack scenarios, response body size differences reveal hidden content (different page returned), and RTT (round-trip time) anomalies identify time-based blind vulnerabilities like time-based SQL injection where the server delays its response when a condition is true.



# Burp Scanner 

**Pro-Only Feature**

Burp Scanner is exclusively available in the Pro and Enterprise editions. The Community edition has no scanning capability. The feature set justifies it as an enterprise tool, covering crawling, passive analysis, active fuzzing, and JS analysis in a unified workflow.

**Target Scope**

Scope defines the boundary of what Burp will process and scan. Adding targets to scope prevents Burp from wasting resources on out-of-scope URLs and keeps scan results focused. Items can be excluded from scope individually - the common use case is excluding logout endpoints that would terminate the authenticated session mid-scan. Advanced scope control supports regex patterns for granular inclusion and exclusion rules.

**Crawler**

The crawler follows links and forms found on pages to build a comprehensive site map. It does not fuzz for undiscovered paths - it only follows what is explicitly linked. For discovering unreferenced paths, Intruder or external tools like ffuf/dirbuster are needed. The Application Login tab allows providing credentials or a recorded login sequence so the crawler can access authenticated areas of the application, significantly expanding coverage.

**Passive Scanner**

The passive scanner analyzes already-collected page source and response data without sending any new requests. It identifies potential issues such as missing security headers, insecure cookies, and DOM-based vulnerabilities. Because it does not probe or confirm, results are flagged as potential vulnerabilities with a confidence level. It is fast and low-risk but produces more false positives than active scanning.

**Active Scanner**

The active scanner is the most comprehensive option. It performs a crawl, passive scan, active verification of passive findings, JavaScript analysis, and parameter fuzzing for common vulnerability classes (XSS, SQLi, command injection, etc.). It sends a large volume of test requests, making it slower and louder than passive scanning. The `Audit checks - critical issues only` preset focuses the scan on high-impact vulnerabilities to reduce scan time when only critical findings are needed.

**Interpreting Results**

High severity combined with Certain or Firm confidence is the priority tier. The issue detail view shows the specific request that triggered the finding, the response that confirmed it, and advisory information explaining the vulnerability class and remediation guidance. This detail is what distinguishes a scanner finding from a blind alert and allows the tester to verify and contextualize the result.

**Reporting**

Burp generates structured HTML reports filterable by severity and confidence. These are appropriate as supplementary appendix material in a penetration test report but should never be submitted as a standalone deliverable. The tester's own analysis, context, and remediation recommendations must accompany any tool-generated output in a professional engagement.


# ZAP Scanner 

**ZAP Spider**

ZAP Spider functions identically to Burp's Crawler: it traverses the site by following links and validating them, building a tree of all discovered pages and endpoints. If the target is not already in scope when the Spider is launched, ZAP prompts to add it automatically. The resulting Sites Tree provides a visual map of the application's structure.

**Ajax Spider**

The standard Spider follows static HTML links. Ajax Spider additionally processes JavaScript AJAX requests that execute after page load, which are invisible to the standard Spider. Running Ajax Spider after the standard Spider completes maximizes endpoint discovery coverage on modern single-page and JavaScript-heavy applications, at the cost of additional scan time.

**Passive Scanner Integration**

ZAP's passive scanner runs automatically and continuously as the Spider makes requests. By the time the Spider finishes, the passive scan is already partially or fully complete for the discovered pages. This means alerts begin appearing before any active scanning is initiated. Passive alerts in the HUD left pane are scoped to the current page, while the right pane aggregates all alerts across the entire application.

**Active Scanner**

The active scanner sends crafted attack payloads against all identified pages and parameters to probe for exploitable vulnerabilities. It is more thorough than passive scanning but significantly slower and louder. If no Spider scan has been run first, ZAP runs one automatically to establish a target tree before beginning active testing. The scan progress and live requests are visible in the main ZAP UI during the run.

**Alert Triage**

High alerts represent the findings most likely to lead to direct compromise. The alert detail view provides the specific attack payload used, the evidence returned in the response, and the URL where the vulnerability was found. The request can be replayed directly from the alert detail window via ZAP HUD or the Request Editor, allowing immediate manual verification.

**Reporting**

ZAP's HTML report presents all findings organized by severity across all scan types. Like Burp's report, it is appropriate as appendix material in a formal penetration test deliverable but should not be submitted as a standalone report to a client. The XML export format is useful for importing findings into tracking or ticketing systems.


# Extensions 

**Burp BApp Store**

The BApp Store is Burp's community extension repository. Extensions integrate directly into Burp and can add new tabs, modify requests automatically, add scanner checks, or extend encoding and decoding capabilities. Sorting by popularity surfaces the most widely used and battle-tested extensions. Some extensions require Jython (for Python-based extensions) or other runtimes not installed by default, which must be configured in Burp's extension settings before installation. Some BApp extensions are Pro-only.

**Decoder Improved**

Decoder Improved replaces and extends Burp's built-in Decoder tab with additional encoding formats, hashing algorithms, and a hex editor. It demonstrates the pattern: install an extension, get a new tab, use it alongside existing Burp features. Each extension's GitHub page or BApp Store documentation page describes its specific usage and options.

**ZAP Marketplace**

ZAP's Marketplace provides community add-ons categorized by stability tier: Release builds are considered stable, while Beta and Alpha builds may have functional issues. The Marketplace is the mechanism for extending ZAP's built-in wordlists, scanners, and features beyond their defaults.

**FuzzDB Add-ons**

Installing FuzzDB Files and FuzzDB Offensive adds a large collection of security-focused wordlists directly into ZAP's File Fuzzers payload type. The OS command injection wordlist under `fuzzdb > attack > os-cmd-execution` is specifically useful for command injection testing and provides a variety of bypass variants that can succeed against WAF-protected applications where simple payloads like `;ls;` are blocked. This demonstrates the practical value of extending ZAP's built-in capabilities through the Marketplace.

**General Extension Mindset**

Both tools' extension ecosystems exist because no single tool covers every scenario. Extensions allow the community to contribute specialized capabilities - scanner checks for specific frameworks, wordlists for specific vulnerability classes, decoders for specific encoding schemes - that the core tool does not include. Familiarity with the available extensions in both stores is part of building an effective web pentesting toolkit.

