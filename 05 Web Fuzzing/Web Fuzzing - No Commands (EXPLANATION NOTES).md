# Web Fuzzing

**Introduction**

Web fuzzing is an automated security testing technique used to discover vulnerabilities in web applications by sending large numbers of unexpected or malformed inputs. Instead of manually testing each input field or endpoint, a fuzzer generates many payloads and observes how the application responds.

The core idea is simple: when software receives input it does not expect, it may behave incorrectly. These abnormal behaviors can expose security flaws such as injections, logic bugs, or improper input validation.

This technique is widely used during penetration testing, bug bounty hunting, and secure development processes.

---

**Fuzzing vs Brute-Forcing**

Although beginners often treat fuzzing and brute-forcing as the same thing, they serve slightly different purposes.

Fuzzing focuses on sending many types of unexpected inputs to discover weaknesses in how an application processes data. These inputs may include malformed characters, random strings, mutated values, or unexpected structures.

Brute-forcing is more targeted. It attempts to discover a correct value by systematically trying possibilities from a predefined list. This is commonly used for password guessing, ID enumeration, or token discovery.

A useful mental model:

Fuzzing = testing how the system reacts to strange inputs.  
Brute-forcing = testing many possible correct values until one works.

---

**Why Web Fuzzing is Important**

Web applications are complex systems handling authentication, payments, APIs, and sensitive data. Because of this complexity, vulnerabilities often appear in places that manual testing may overlook.

Fuzzing helps uncover hidden vulnerabilities by triggering unexpected application behavior. When an application crashes, returns an unusual response code, or displays error messages, it may indicate a security weakness.

Another major benefit is automation. Instead of testing thousands of possibilities manually, fuzzing tools generate and send payloads automatically. This allows security testers to focus on analyzing responses and identifying real vulnerabilities.

Fuzzing also simulates real attacker behavior. Attackers often probe applications with unusual input values to identify weaknesses. By performing fuzzing during testing, defenders can identify and fix vulnerabilities before attackers exploit them.

Finally, fuzzing helps developers strengthen input validation and improve code quality. Bugs discovered through fuzzing often highlight logic errors, improper sanitization, or insecure coding practices.

---

**Essential Concepts**

Understanding a few core concepts is important before performing fuzzing.

A wordlist is a collection of inputs used by the fuzzer. These can include common directories, file names, parameters, or payload values. Wordlists are the backbone of most fuzzing attacks because they provide the variations that will be tested against the application.

A payload is the specific piece of data sent to the application. Payloads can range from simple words to complex injection strings designed to trigger vulnerabilities.

Response analysis is the process of examining how the application reacts to each payload. Security testers look for anomalies such as different response codes, longer responses, error messages, or unexpected page behavior.

A fuzzer is the tool that automates this process. Tools generate payloads, send requests, and collect responses for analysis.

False positives occur when the tool flags something as a vulnerability when it is not actually exploitable. For example, a missing directory returning a standard error page might look suspicious but is harmless.

False negatives occur when a vulnerability exists but the fuzzing process fails to detect it. This can happen when the payload list is incomplete or when vulnerabilities require complex logic rather than simple input testing.

Fuzzing scope refers to which parts of the application are being tested. Instead of fuzzing the entire application blindly, testers usually target specific endpoints such as login forms, file upload pages, API endpoints, or parameterized URLs.

Defining the scope correctly helps focus the fuzzing effort and produce meaningful results.
# Tooling

**Installing Go, Python and PIPX**

These installations prepare the environment required to run the fuzzing tools used in the module.

Go is required because several modern security tools are written in Go. Installing it allows the system to compile and install Go-based tools directly from their repositories.

Python is needed because some fuzzing utilities and security tools rely on Python-based frameworks. The Python package manager is also installed so additional Python dependencies can be handled easily.

pipx is used to install Python applications in isolated environments. Instead of installing packages globally and potentially causing dependency conflicts, pipx creates a dedicated environment for each tool. This keeps the system clean and prevents version clashes.

After installing these components, verifying the versions confirms that the environment is configured correctly before installing fuzzing tools.

---

**FFUF**

FFUF (Fuzz Faster U Fool) is a high-speed web fuzzer written in Go. It is widely used for discovering hidden directories, files, parameters, and other web application components.

Its main strength is speed and flexibility. FFUF sends large volumes of requests quickly and allows testers to inject payloads into various parts of HTTP requests such as URLs, headers, and parameters.

Security professionals commonly use FFUF for:

Directory and file discovery  
Parameter enumeration  
Brute-force testing for authentication fields or IDs

Because it is written in Go, it runs very efficiently and can handle large wordlists with minimal performance issues.

---

**Gobuster**

Gobuster is another Go-based tool used for discovering hidden web content. It is known for its simplicity and ease of use, making it popular among beginners and professionals alike.

The tool primarily focuses on content discovery such as:

Hidden directories and files  
Virtual hosts  
DNS subdomains

Gobuster works by taking a wordlist and systematically testing each entry against a target server. If the server responds differently to certain requests, it may reveal hidden resources.

Its simplicity and reliability make it a standard tool in many penetration testing workflows.

---

**FeroxBuster**

FeroxBuster is a content discovery tool written in Rust and designed for recursive scanning. Unlike some fuzzers that simply test a list of inputs once, FeroxBuster can automatically continue scanning newly discovered directories.

This recursive capability makes it extremely effective at discovering deeply nested content within web applications.

FeroxBuster is particularly useful for:

Recursive directory discovery  
Finding unlinked files and endpoints  
High-performance scanning of large targets

Because it is written in Rust, it provides excellent performance and stability during intensive scanning tasks.

---

**wfuzz / wenum**

wfuzz is a flexible fuzzing tool designed primarily for parameter fuzzing. It allows testers to inject payloads into different parts of HTTP requests and observe how the application responds.

However, due to installation complications, the module recommends using wenum, which is an actively maintained fork of wfuzz. Both tools follow the same syntax and can be used interchangeably.

These tools are particularly useful for testing how web applications handle parameters and input values.

Common use cases include:

Directory and file discovery  
Parameter enumeration  
Credential brute-forcing

Because of their flexibility, these tools are powerful for identifying vulnerabilities related to improper input validation.

# Directory and File Fuzzing 

**Wordlists**

This section explains why wordlists are central to content discovery. They are the source material that the fuzzer uses to guess possible directories and files. Better wordlists improve coverage and increase the chance of discovering hidden assets.

The SecLists collection is highlighted because it is one of the most widely used repositories for security testing wordlists. Different files in that repository serve different discovery goals. Some are broad starter lists, while others are larger and better suited for deeper enumeration.

The note about the lowercase path matters because many failures during labs come from using the wrong directory name for the wordlist location.

**FUZZ Placeholder**

The placeholder marks the exact part of the target URL where each wordlist entry will be inserted. This is the core mechanic of the tool. The fuzzer repeatedly swaps that marker with candidate values and checks how the server responds.

Understanding this pattern is essential because the same idea is reused later for parameters, files, subdomains, and virtual hosts.

**Directory Fuzzing**

This stage is focused on discovering hidden folders. The wordlist contains potential directory names, and the requests are sent to see whether any of them map to real content on the server.

The reason this matters is that hidden directories often expose administrative panels, old application versions, staging content, backups, or internal resources that are not visible through the main site navigation.

The discovery flow here is methodological: start with a general directory scan, identify a valid directory, then pivot into that location for deeper inspection.

**File Fuzzing**

After identifying a directory, the process moves from folders to specific files. This is the natural next step because a directory alone is only a lead. The real value often comes from the files inside it.

The listed extensions reflect common web technologies and risky artifacts. Dynamic files may reveal application logic, backup files may expose previous versions or secrets, and text or script files may disclose internal information.

The use of verbose mode in the demonstrated workflow supports closer response inspection, which helps distinguish meaningful files from noise.

This stage shows the chaining logic of web fuzzing clearly: first uncover structure, then enumerate content within the newly discovered structure.

**Tools**

The tools named here support directory and file discovery, but the focus of this section is mainly on ffuf. The methodology does not depend on one tool alone. The bigger lesson is how to combine a good wordlist, a placeholder location, and response analysis to map hidden application content.

This fits into a broader recon workflow where every new path discovered becomes a candidate for further fuzzing, validation, and eventual exploitation.

# Recursive Fuzzing 

**Recursive Fuzzing with ffuf**

This section extends basic directory fuzzing into nested discovery. Instead of stopping after finding a directory at the web root, the tool automatically treats that new directory as another place to continue searching. That is the key idea behind recursion.

The workflow begins at the root of the application and tests names from the wordlist. When a valid directory is found, the tool creates a new scan branch inside that location. This turns one discovery into a launch point for more discoveries. The process repeats until no more valid directories are found or until a depth limit is reached.

The ignore-comments option matters because some wordlists contain commented lines. Without ignoring them, those comment entries could be treated as test values and create unnecessary noise.

The extension filter shown here narrows the search toward HTML content while recursion handles the directory expansion. Together, this helps map both structure and reachable files within that structure.

**Responsible Recursive Fuzzing**

This part focuses on operational discipline. Recursive fuzzing can generate large request volumes quickly, especially against deep or broad directory trees. That can stress the target, create false signals, or trigger defensive controls.

A recursion depth limit keeps the search bounded and prevents the scan from expanding indefinitely into nested paths. Rate limiting controls how aggressively requests are sent, which is important for stability and stealth. Timeout controls how long the tool waits on slow responses so the scan does not stall.

The larger lesson is that recursion is powerful, but it should be constrained. Effective fuzzing is not just about finding more paths. It is about doing so in a controlled way that balances coverage, speed, and reliability.

**Wordlist**

The wordlist remains the engine of discovery. In recursive fuzzing, its importance increases because the same candidate names are reused at multiple directory levels. A good list improves the quality of every branch explored during the scan.

**URL Patterns**

These patterns illustrate how recursive fuzzing expands from a base target into newly discovered locations. The placeholder is reused at each level, which makes the technique scalable. Once one directory is found, the same probing logic is applied inside it automatically.

**Flags and Parameters**

These options represent control over behavior rather than just syntax. One enables recursive branching, one cleans wordlist input, one increases visibility during testing, one narrows file targeting, and others enforce limits on how deep and how fast the scan runs.

Together, they reflect the transition from simple one-level enumeration to structured, controlled exploration of complex web application paths.

# Parameter and Value Fuzzing

**Wordlist**

The same SecLists wordlist used earlier now serves a different purpose. Instead of guessing directories or files, the list is used to test possible parameter values. Each entry becomes a candidate value sent to the application to observe how the server responds.

Because many applications rely on predictable values such as keywords, configuration names, or identifiers, common wordlists often reveal valid inputs that are not obvious during manual testing.

**wenum Installation**

The fuzzing tool used for parameter testing in this section is wenum. It is a maintained fork of wfuzz and is designed to automate parameter testing in web requests. Installing it with isolated Python environments ensures dependencies do not conflict with other tools on the system.

**GET Request Testing**

Before fuzzing begins, manual probing is performed. This step is important because it reveals how the application behaves when a parameter is missing or invalid. Observing the response helps confirm that the parameter exists and that the server validates its value.

This manual interaction helps define the fuzzing strategy.

**GET Parameter Fuzzing**

Once the parameter is confirmed, automated fuzzing begins. The fuzzing tool replaces the placeholder with values from the wordlist and sends repeated requests to the endpoint.

The purpose is to identify a value that produces a different response from the rest. A different HTTP status code, response length, or message can indicate that the correct or special parameter value has been discovered.

Filtering certain response codes reduces noise in the results so meaningful responses stand out.

**POST Request Testing**

POST parameters differ from GET parameters because they are transmitted inside the HTTP request body rather than in the URL. Testing begins again with manual interaction to determine how the application responds when the expected parameter is missing.

This confirms that the application requires the parameter and is validating its value.

**POST Parameter Fuzzing**

Automated testing of POST parameters follows the same principle as GET fuzzing but requires placing the payload inside the request body. The fuzzing tool sends repeated POST requests with different values to identify a response that differs from the others.

Response filtering is again used to highlight successful results that indicate a valid parameter value.

**POST Parameter Validation**

Once a candidate value is identified, manual verification confirms the finding. This validation step ensures the value genuinely triggers a different application behavior rather than being a false positive caused by response variations.

**GET Parameter Pattern**

The example pattern demonstrates how GET parameters appear in URLs. Understanding this structure is essential because fuzzing targets the value portion of these key-value pairs.

**POST Request Structure**

The POST request example shows how parameters are encoded and transmitted inside the request body. This structure explains why POST fuzzing requires inserting payloads into the request body instead of modifying the URL.

Overall, parameter fuzzing focuses on manipulating how applications process input. Discovering unexpected valid values can reveal hidden functionality, authorization flaws, or injection points that may lead to deeper exploitation.

# Virtual Host and Subdomain Fuzzing 

**Hosts File**

This step makes the local system resolve the target hostname to the spawned target IP. It is required for local vhost testing because the web server decides which site to serve based on the Host header and hostname mapping. Without that mapping, requests to the hostname may not reach the intended target.

**Wordlists**

Two different wordlists are used because vhost discovery and subdomain discovery target different layers.

The web content wordlist is used for vhost fuzzing because candidate names are inserted into hostnames handled by the web server. The DNS subdomain wordlist is used for subdomain enumeration because those guesses are resolved through DNS rather than just tested through HTTP behavior.

**Gobuster VHost Fuzzing**

This mode looks for hidden virtual hosts configured on a web server. Virtual hosts allow one server or IP to host multiple websites, and the server chooses which content to serve based on the Host header in the HTTP request.

The append-domain behavior is essential because it turns each wordlist entry into a full hostname under the target domain. That allows Gobuster to test names such as administrative or internal hostnames that may not be publicly documented.

The main analysis point in this workflow is the server response. A successful response often indicates that the tested hostname maps to a valid virtual host on the target server. This can reveal hidden applications, administrative interfaces, staging environments, or internal panels.

**Gobuster Subdomain Fuzzing**

This mode targets DNS rather than HTTP host routing. Instead of testing how a web server reacts to hostnames, it tests whether candidate subdomains actually resolve in DNS.

This is useful because organizations often expose services on separate subdomains, and some of them may be forgotten, weakly secured, or running outdated applications. Enumerating them expands the visible attack surface.

The process is straightforward conceptually: generate candidate subdomain names from the wordlist, attach them to the main domain, and check whether DNS resolution succeeds. Any successful resolution indicates that the subdomain exists.

**Latest Gobuster Domain Flag**

This note matters because command syntax can change across versions. In this case, the option previously used for the domain can mean something different in newer releases. That can cause failed scans or incorrect behavior if the wrong flag is used.

The lesson is that tool syntax should always be verified against the installed version, especially when following lab material that may reflect an earlier release.

**Targets and Domains**

These values show the distinction between vhost and subdomain contexts.

The vhost target uses a specific URL and port because the HTTP server is being tested directly. The domain for subdomain fuzzing is the DNS namespace being enumerated. Understanding which layer is being targeted prevents confusion between hostname-based web routing and DNS-based name resolution.

**Tools**

Gobuster is used here because it supports both virtual host enumeration and DNS subdomain discovery. The important takeaway is not just the tool itself, but the methodology split:

Virtual host fuzzing discovers hidden sites on the same server through HTTP Host header manipulation.

Subdomain fuzzing discovers separate names in DNS that may point to different services or infrastructure.

Together, these techniques help build a fuller map of the target’s external surface before deeper testing begins.

# Filtering Fuzzing Output

**Gobuster**

This section is about reducing noise so useful findings stand out. Gobuster supports response filtering mainly through status-code and content-length controls. The include option narrows results to only the response codes you care about, while the exclude option removes known-noisy responses such as common errors. Length-based exclusion is useful when many false positives return the same body size.

The main idea is to turn raw enumeration into focused analysis. Instead of reviewing every response, you narrow output to patterns that are more likely to indicate valid content.

**FFUF**

FFUF has one of the most flexible filtering systems among the tools shown. It can filter or match based on status code, size, word count, line count, and timing. This matters because meaningful findings do not always differ by status code alone. Sometimes all responses return the same code, but interesting ones have a different length, fewer words, more lines, or unusual latency.

Matching options keep only what fits the pattern you want. Filtering options remove what you already know is irrelevant. This makes FFUF especially useful for finding subtle anomalies during content discovery and parameter fuzzing.

The examples show how multiple filters can be combined to narrow results based on more than one attribute at once. That is often how real-world fuzzing becomes manageable.

The comparison between default matching behavior and matching all response codes demonstrates why filtering matters. Without sensible defaults, the output becomes flooded with low-value responses, making real findings harder to spot.

**wenum**

wenum provides similar filtering controls but uses a show-versus-hide model across codes, sizes, line counts, word counts, and regex content. This is particularly useful when dealing with endpoints that return many uniform messages. If a valid response differs only slightly, filters based on word count, size, or matching text can isolate it.

Regex-based filtering is especially powerful because it lets you focus on response content rather than only structural metrics. That makes it useful when looking for keywords such as login prompts, admin references, or error messages that indicate interesting behavior.

The general-purpose filter options add another layer of control by deciding which responses are displayed or even post-processed.

**Feroxbuster**

Feroxbuster includes filtering at both request and response levels. Some options suppress known irrelevant targets before they are scanned, while others remove responses based on code, size, words, lines, similarity, or body content.

This is especially valuable during recursive scans, where large amounts of data can accumulate quickly. Filtering prevents repeated error pages, large unhelpful files, and duplicate content from dominating the results.

Its similarity filter is particularly useful when many endpoints return nearly identical custom error pages.

**Flags and Parameters**

These options are the core vocabulary for shaping fuzzing output. Some act as allowlists, some as denylists, and others focus on structural properties of the response. Together they give you the ability to separate signal from noise.

The broader lesson is that fuzzing is not just about sending requests. It is also about interpreting the response set efficiently. Good filtering is what turns a noisy scan into actionable reconnaissance.

**Wordlists and Paths**

The same wordlists remain relevant here because filtering does not replace input generation. It improves how the results of those inputs are interpreted. A strong wordlist plus effective output filtering is what makes fuzzing both broad and practical.

# Validating Findings 

**Validation Requests**

This section is about proving that a fuzzing result is real without overreaching. Fuzzers are good at surfacing anomalies, but they do not prove impact by themselves. Validation confirms whether a discovery is an actual security issue or just a misleading response pattern.

The first request is used to manually verify whether the discovered directory is accessible and whether directory listing is enabled. If the server returns a browsable index page, that confirms exposure of the directory contents.

The second request limits validation to response headers for a specific file. That is an important restraint. It allows confirmation that a file exists and appears to contain data without directly retrieving and viewing its contents. This is a safer proof approach because it demonstrates risk while minimizing unnecessary access to potentially sensitive material.

**Paths and Files**

These entries represent the artifacts that made the finding interesting. A backup directory is often high value because such locations commonly contain database exports, archived credentials, old source code, or configuration snapshots. The file names themselves can be enough to establish risk when combined with accessibility and nonzero size.

The point is not just that a directory exists, but that it exposes content that strongly suggests sensitive material may be available.

**HTTP Headers**

These headers are used as evidence during responsible validation.

Content type helps infer what kind of file is exposed.  
Content length helps determine whether the file likely contains data.  
Last modified can suggest recency and relevance.  
The server header may reveal underlying technology that helps contextualize the exposure.

Together, these details help establish that the finding is real and meaningful without needing to inspect the file body itself.

**HTML Indicators**

These fragments are signs of directory indexing being enabled. They are useful because they distinguish a normal web page from an automatically generated file listing. If these indicators appear, they support the conclusion that the server is exposing directory contents directly.

This is the key lesson of the section: fuzzing finds leads, but validation turns a lead into defensible evidence. The safest validation approach confirms access, confirms file presence, and assesses likely sensitivity while avoiding unnecessary interaction with actual contents.

# Web APIs 

**REST Example**

This example demonstrates a REST-style API request targeting a specific resource. REST APIs organize resources using URLs, and the path identifies the resource being requested. In this case, the request targets a user resource with a specific identifier.

REST APIs rely heavily on HTTP semantics. Each request method corresponds to a specific operation, allowing clients to retrieve or modify server-side data through predictable patterns. During fuzzing or testing, these endpoints become targets for parameter manipulation and endpoint discovery.

**SOAP Example**

SOAP APIs use structured XML messages wrapped in a defined envelope format. The envelope contains both metadata and the operation being requested. Unlike REST APIs, SOAP uses a more rigid protocol that emphasizes formal contracts and strict message structures.

Because SOAP relies on XML, testing often focuses on manipulating XML elements, namespaces, and values within the message body. Many SOAP services expose specific functions, and fuzzing can involve modifying parameters inside these structured messages.

**GraphQL Example**

GraphQL differs from REST and SOAP because it uses a single endpoint where clients specify exactly what data they want. Instead of multiple endpoints for different resources, a single query can retrieve multiple related objects.

The query structure defines which fields should be returned. This flexibility allows applications to avoid over-fetching or under-fetching data. From a testing perspective, fuzzing GraphQL involves exploring available schema objects, manipulating query parameters, and identifying unintended data exposure.

**HTTP Methods**

These methods define how clients interact with resources in REST-style APIs. Each method represents a different action. Retrieving resources, creating new entries, updating existing data, and removing resources all rely on these operations.

Understanding these methods is critical when analyzing API behavior. Many vulnerabilities arise when authorization checks are missing for certain methods or when endpoints accept unexpected actions.

**Data Formats**

APIs exchange structured data rather than full web pages. JSON is the most common format used in modern APIs because it is lightweight and easy for applications to parse. XML remains common in legacy and enterprise environments, particularly with SOAP-based systems.

When testing APIs, fuzzing often targets these structured formats by manipulating field names, values, and nested objects.

**API Endpoint Example**

Endpoints represent the individual access points where API functionality is exposed. Each endpoint typically corresponds to a specific resource or operation. Instead of browsing directories like in traditional web fuzzing, testers enumerate endpoints and test how the API processes parameters and request bodies.

Understanding endpoints and how they map to application functionality is the first step toward effective API fuzzing and vulnerability discovery.

# Identifying Endpoints 

**REST Endpoints**

REST APIs organize application functionality around resources that are accessible through structured URLs. Each endpoint represents a specific resource or collection of resources. Some endpoints represent entire collections, while others represent a single object identified by a unique identifier.

Understanding this hierarchical structure helps identify additional endpoints by pattern recognition. If a collection endpoint exists, a resource-specific endpoint usually exists as well.

**REST Query Parameters**

Query parameters modify how the API returns data. They commonly control filtering, sorting, pagination, or search behavior. When testing APIs, these parameters are important targets because improper validation can expose data or create injection opportunities.

Query parameters often reveal additional functionality such as result limits, sorting fields, or filtering logic that might not be visible through the main application interface.

**REST Path Parameter Pattern**

Path parameters identify specific resources inside an endpoint path. Instead of appearing after a question mark like query parameters, they are embedded directly within the URL path.

Because these identifiers usually represent database objects, testing different values can reveal authorization issues, data exposure, or insecure direct object references.

**REST Request Body Example**

When creating or updating resources, REST APIs typically receive structured input in the request body. This input often uses JSON format. Request body parameters become important fuzzing targets because they influence how the server processes and stores data.

Testing different values inside these structures can expose logic flaws, validation weaknesses, or injection points.

**SOAP Request Example**

SOAP APIs use XML messages wrapped in standardized envelopes. Instead of multiple URLs representing resources, SOAP APIs usually expose one endpoint where the requested operation is determined by the XML body content.

Understanding this structure is essential for endpoint discovery because the operation names inside the XML determine the actions the server performs.

**SOAP Parameters**

SOAP parameters are defined in the XML message body. These parameters represent the inputs required by specific operations. Their structure is usually defined inside a WSDL document, which acts as a contract describing the service capabilities.

Testing these parameters involves modifying XML values or structures to observe how the service handles unexpected input.

**GraphQL Endpoint**

GraphQL APIs typically expose a single endpoint that processes all requests. Instead of different URLs for different resources, all operations are handled through queries or mutations sent to this endpoint.

This design changes how fuzzing works. Instead of discovering endpoints through URL enumeration, testing focuses on understanding the schema and available operations.

**GraphQL Query Example**

Queries retrieve data from the server. They specify exactly which fields should be returned, allowing clients to request only the information they need.

Understanding the structure of queries helps testers identify relationships between objects and discover how data is exposed through the API.

**GraphQL Mutation Example**

Mutations modify data on the server. They represent operations such as creating, updating, or deleting records. Mutations often accept input arguments that define the data being changed.

These operations are high-value testing targets because improper validation or authorization can lead to data manipulation vulnerabilities.

**GraphQL Fields**

Fields represent the individual data elements that can be returned in responses. They define the structure of the objects exposed by the API.

Testing field access can reveal sensitive information disclosure if the API exposes internal fields unintentionally.

**GraphQL Arguments**

Arguments modify how queries or mutations behave. They act similarly to parameters in REST APIs. Arguments may control filtering, object selection, or data creation.

Manipulating these values during testing can reveal hidden behavior, improper access control, or weaknesses in input validation.

# API Fuzzing

**API Documentation Endpoint**

This endpoint exposes the API’s auto-generated documentation. It is the first place to inspect because it reveals the officially documented routes, supported methods, and expected request structures. In API testing, documentation gives you the baseline before you start looking for undocumented behavior.

**Documented Endpoints**

These are the known routes exposed by the application. They describe the normal attack surface of the API and show the expected CRUD-style operations around item resources.

The important idea is that documented endpoints are not the whole story. They tell you what is intended to be public, but not what may also exist behind the scenes.

**Tool Setup**

This stage prepares the custom fuzzer environment. The repository contains the fuzzing logic, and the dependency installation ensures the script can run correctly.

Conceptually, this moves the workflow from passive review into active endpoint discovery. Instead of only trusting the documentation, you begin testing the API for routes that were omitted or forgotten.

**Run Fuzzer**

This is the actual endpoint discovery phase. The fuzzer sends many requests derived from a wordlist and checks how the API responds.

The key principle is anomaly detection. Most guesses fail and return standard invalid responses. What matters are the few results that behave differently, such as valid responses or unusual status codes. Those deviations often reveal hidden endpoints or method restrictions.

The 405 case is also meaningful. It indicates the path likely exists, but the method used was not accepted. That is a valuable clue because it points to a real endpoint that may support another method.

**Validation Request**

After the fuzzer surfaces a promising endpoint, validation confirms whether it is real and interesting. This step is essential because fuzzing only generates leads. Validation determines whether a lead has actual value.

The methodology is simple: discover, isolate, verify. That keeps findings reproducible and separates real exposure from scan noise.

**Discovered / Referenced Endpoints**

These entries represent the most relevant paths from the section.

The documentation route is expected and publicly exposed. The undocumented route is important because it was not listed in the API specification yet still responded successfully. That is exactly the type of finding API fuzzing is meant to uncover.

The item route showing method mismatch is also important because it suggests the endpoint exists even when accessed incorrectly.

**API Methods**

These methods define how the client is allowed to interact with resources. In API fuzzing, method handling matters because vulnerabilities may appear when authorization or validation differs across methods on the same path.

A route that is safe under one method may be vulnerable under another.

**Vulnerability Types**

These are examples of the kinds of issues API fuzzing can help uncover.

Broken object-level authorization involves access to resources that should not be accessible.  
Broken function-level authorization involves performing actions that should be restricted.  
SSRF involves coercing the server into making unintended requests.

The broader lesson of this section is that API fuzzing is not just about finding hidden routes. It is also about understanding how endpoints, methods, and parameters interact so that security weaknesses in logic, authorization, and input handling can be exposed.
#
#