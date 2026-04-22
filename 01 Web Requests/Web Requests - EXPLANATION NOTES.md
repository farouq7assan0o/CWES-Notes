
# HyperText Transfer Protocol (HTTP)

---

**HTTP Request/Response Model**

HTTP operates as a stateless client-server protocol. The client sends a request specifying a method, a resource path, and optional headers and body. The server processes it and returns a response containing a status code and optionally a body. The default port 80 is used unless specified otherwise in the URL.

---

**DNS Resolution Flow**

Before any HTTP request can be sent, the browser must resolve the hostname to an IP address. It first checks `/etc/hosts` locally — this file can be manually edited to map hostnames to IPs, which is useful in lab environments to point a domain to a target machine without touching DNS. If no local record exists, the query goes to external DNS servers. Once an IP is obtained, the browser sends a GET request to port 80 for the root path `/`, and the server returns the default index file.

---

**cURL as a Penetration Testing Tool**

cURL is preferred over browsers in penetration testing because it exposes the raw HTTP request and response without rendering, allows full control over headers, methods, data, and authentication, and integrates cleanly into scripts and automation pipelines. The flags in Output A cover the most common use cases: sending POST data, saving output to files, controlling verbosity, setting user agents, and authenticating.

---

**Flag Usage Context**

`-v` is essential during testing because it prints both the outgoing request headers and the incoming response headers, giving full visibility into the exchange. `-i` adds response headers to the output without the full verbose mode noise. `-s` suppresses progress output when piping cURL output into other tools. `-u` passes credentials in `user:password` format for basic authentication. `-A` spoofs the User-Agent header, useful for testing application behavior that varies by client type.

---

**URL Structure Relevance**

Understanding each URL component matters for parameter manipulation, path traversal testing, and authentication bypass. The Query String is the most common injection point for SQLi, XSS, and parameter tampering. The User Info component is rarely used in modern applications but can appear in internal tools and is a potential credential exposure risk if URLs are logged. Fragments are client-side only and never sent to the server, making them irrelevant for server-side injection but relevant for DOM-based XSS.

# Hypertext Transfer Protocol Secure (HTTPS)

---

**Why HTTPS Exists**

Plain HTTP transmits all data in cleartext. Any party positioned between the client and server — on the same network segment, at an ISP, or via a rogue access point — can read the full contents of every request and response, including credentials. HTTPS wraps HTTP inside TLS encryption, making intercepted traffic unreadable without the session keys.

---

**TLS Handshake Flow**

When a client connects to an HTTPS server, it first sends a "client hello" advertising supported TLS versions and cipher suites. The server responds with a "server hello" and its SSL certificate. The client validates the certificate against trusted certificate authorities. Both sides then negotiate session keys through a key exchange algorithm. Once the encrypted handshake completes successfully, all subsequent HTTP traffic flows through the encrypted channel. The underlying HTTP protocol is unchanged — only the transport layer is encrypted.

---

**HTTP to HTTPS Redirect**

If a user navigates to `http://` on a server enforcing HTTPS, the server returns a `301 Moved Permanently` response pointing to the `https://` equivalent. The browser follows the redirect and initiates the TLS handshake on port 443. This redirect is unencrypted and visible to network observers, which is why HSTS (HTTP Strict Transport Security) exists to prevent the initial cleartext hop — though that is beyond this section's scope.

---

**DNS Leakage Warning**

Even with HTTPS active, the hostname being visited can be exposed if DNS queries are sent in cleartext. The encrypted DNS servers in Output A (Google's 8.8.8.8 and Cloudflare's 1.1.1.1) support DNS-over-HTTPS or DNS-over-TLS, which encrypts DNS queries. A VPN also encrypts all traffic including DNS, closing this leak. This is relevant in penetration testing environments where traffic analysis is part of the assessment.

---

**HTTP Downgrade Attacks**

An attacker performing a MITM can attempt to strip HTTPS by intercepting the initial HTTP request before the 301 redirect fires, and serving the HTTP version of the site directly to the victim while maintaining an HTTPS connection to the real server. The victim's browser never initiates TLS. Modern browsers and HSTS headers largely mitigate this, but it remains relevant in environments with older or misconfigured servers.

---

**cURL and Invalid Certificates**

In lab and testing environments, web applications often use self-signed or expired certificates. cURL blocks these by default as a security measure, returning an SSL error. The `-k` flag in Output A disables certificate validation, allowing the request to proceed. This flag should only be used in controlled testing environments — using it against production targets removes a protection that detects MITM interception of the testing session itself.





# HTTP Requests and Responses

---

**HTTP Request Structure**

Every HTTP request begins with a request line containing three space-separated fields: the method (what action to perform), the path (which resource to access), and the HTTP version. Following the request line are headers as key-value pairs, each on its own line. A blank line signals the end of headers. An optional body follows for methods like POST that send data to the server.

---

**HTTP Response Structure**

The response mirrors the request format. The first line contains the HTTP version and the status code with its reason phrase. Headers follow, then a blank line, then the optional response body. The body is most commonly HTML but can be JSON, images, stylesheets, scripts, or binary files like PDFs depending on the resource requested.

---

**HTTP Version Differences**

HTTP/1.x transmits as human-readable cleartext with newline-delimited fields, making it straightforward to read in raw captures. HTTP/2 transmits as binary data in a compressed dictionary format, which is more efficient but not directly human-readable. This distinction matters when analyzing raw traffic captures — HTTP/1.1 traffic is immediately interpretable while HTTP/2 requires decoding.

---

**cURL Verbose Mode**

The `-v` flag in Output A prints both the outgoing request headers (prefixed with `>`) and the incoming response headers (prefixed with `<`) in addition to the response body. This is essential during penetration testing to verify exactly what headers are being sent and received, spot authentication challenges (like the `401 Unauthorized` with `WWW-Authenticate` in the example), and understand server fingerprinting data from headers like `Server`. The `-vvv` flag adds even more detail including TLS negotiation information for HTTPS connections.

---

**Browser DevTools Network Tab**

The Network tab provides the same visibility as `-v` in cURL but in a graphical interface. It shows every request the page generates — not just the primary document request but also all secondary requests for images, scripts, stylesheets, and API calls. This is valuable for discovering hidden endpoints, observing authentication tokens in headers, and identifying AJAX requests that don't appear in the URL bar. The Raw view in the Response tab shows unrendered source, useful for finding comments, hidden fields, and server-side data embedded in the HTML.

# HTTP Headers 

---

**Header Categories and Their Purpose**

Headers are divided into five categories based on their scope and function. General headers describe the message itself and apply to both requests and responses. Entity headers describe the content being transferred and appear in POST/PUT requests and responses. Request headers carry client-specific information and intent. Response headers provide server context. Security headers enforce browser-side policies.

---

**General Headers**

`Connection` controls whether the TCP connection should persist after the response. `keep-alive` enables connection reuse for multiple requests, reducing overhead. `close` signals termination. During testing, modifying this header can sometimes affect how a server processes subsequent requests in a session.

---

**Entity Headers**

`Content-Type` and `Media-Type` tell the receiver how to interpret the body. Manipulating `Content-Type` in POST requests is a common technique in file upload bypass attacks — changing the declared type to bypass server-side validation while delivering a different actual content. `Content-Encoding` describes compression applied to the body, and `Content-Length` tells the server how many bytes to read, making it relevant for HTTP request smuggling research.

---

**Request Headers — Penetration Testing Value**

`Host` is critical for virtual host enumeration — changing the Host header to different values can reveal other websites hosted on the same IP. `User-Agent` is frequently used to fingerprint clients, and servers sometimes return different responses based on it, making spoofing useful. `Referer` is trivially manipulated and should never be trusted for access control. `Cookie` carries session identifiers and is the primary target for session hijacking. `Authorization` carries encoded credentials and tokens — the `BASIC` scheme base64-encodes `username:password`, which is trivially decoded.

---

**Response Headers — Information Value**

`Server` reveals the web server software and version, directly informing exploit selection. `Set-Cookie` shows the cookie attributes — missing `HttpOnly` means JavaScript can read the cookie (enabling XSS-based theft), and missing `Secure` means the cookie travels over HTTP. `WWW-Authenticate` identifies the authentication mechanism, which guides credential attack strategies.

---

**Security Headers — What Their Absence Means**

Missing `Content-Security-Policy` means the browser will execute inline scripts and load resources from any origin, making XSS significantly easier to exploit. Missing `Strict-Transport-Security` allows HTTP downgrade attacks. A permissive `Referrer-Policy` leaks URL parameters (including tokens) to third-party sites via the Referer header. Checking for the presence and configuration of these headers is a standard step in web application assessments.

---

**cURL Header Flags**

`-I` sends a HEAD request — the server returns only headers with no body, useful for quick fingerprinting without downloading content. `-i` includes headers in the output of a normal request. `-H` sets arbitrary headers, enabling testing of how applications respond to modified or injected header values. `-A` is a shortcut specifically for User-Agent spoofing.

# HTTP Methods and Codes 

---

**GET vs POST**

GET appends data to the URL as query string parameters, making the data visible in browser history, server logs, and referrer headers. It is intended for data retrieval and should not cause state changes on the server. POST sends data in the request body, keeping it out of the URL, and is used for actions that modify server state — logins, form submissions, file uploads. Most injection testing starts with GET parameters but POST body parameters are equally valid injection targets.

---

**HEAD**

HEAD returns only the response headers with no body. This is useful for fingerprinting — checking `Server`, `Content-Type`, `Content-Length`, and security headers without downloading the full response. It is also used by tools to check whether a resource exists before committing to a full GET.

---

**PUT and DELETE — Security Implications**

PUT creates or replaces a resource at a specified path. If a server accepts PUT requests without proper authorization, an attacker can upload arbitrary files — including webshells — directly to the server. DELETE removes resources; unrestricted access can cause denial of service by removing critical application files. Both methods should be restricted to authenticated and authorized users and are common misconfiguration findings in REST API assessments.

---

**OPTIONS**

OPTIONS returns the HTTP methods the server accepts for a given endpoint. This is a free enumeration call — it reveals what attack surface is available without performing any action. Seeing PUT or DELETE in the OPTIONS response is an immediate flag for further testing.

---

**Status Codes in Penetration Testing**

Status codes provide immediate feedback on the result of a request. `200` confirms a resource exists and was returned. `302` indicates a redirect — login success redirects are common, and following the redirect chain reveals the post-authentication landing page. `403` means the resource exists but access is denied — the path is valid and worth further investigation. `404` means the path does not exist, though some applications return `404` for unauthorized access to obscure resource existence. `500` indicates a server-side error, which often signals that input reached backend processing and caused an exception — a strong indicator for injection vulnerabilities. The distinction between `403` and `404` is critical for directory brute-forcing: `403` confirms existence while `404` does not.



# GET 

---

**HTTP Basic Authentication**

Basic auth is handled at the web server level rather than by the application. The server challenges unauthenticated requests with a `401` response and a `WWW-Authenticate: Basic realm="..."` header. The client responds by including an `Authorization` header containing the word `Basic` followed by a base64-encoded string of `username:password`. Base64 is encoding, not encryption — the value is trivially decoded. This is why basic auth over plain HTTP exposes credentials in cleartext and is flagged as a security issue.

---

**Three Ways to Authenticate with cURL**

The `-u` flag takes `username:password` and handles the base64 encoding automatically. The inline URL format `username:password@host` does the same. The `-H` flag with a manually crafted `Authorization` header gives full control, which is useful when testing token replay, modified credentials, or non-standard authentication values. All three produce the same `Authorization` header in the outgoing request.

---

**GET Parameters**

GET requests pass parameters as query strings in the URL, visible in the address bar, browser history, and server access logs. The `search.php?search=le` pattern in Output A is the standard format — parameter name, equals sign, value. Multiple parameters are separated by `&`. These parameters are the primary targets for injection testing when the server uses them in database queries, file lookups, or command execution.

---

**DevTools Copy as cURL / Copy as Fetch**

These browser features eliminate the need to manually reconstruct requests. Copy as cURL produces a ready-to-execute terminal command including all headers the browser sent — cookies, authorization tokens, content types, and more. This is the fastest way to replicate an authenticated browser request in cURL for further manipulation. Copy as Fetch does the same for JavaScript console execution, allowing request replay directly within the browser's JS environment without leaving the page.

---

**Attack Surface**

The combination of basic auth and GET parameters presents two distinct attack surfaces. The auth layer can be attacked by decoding observed `Authorization` headers, brute-forcing credentials, or replaying captured tokens. The GET parameters feed into backend logic and are the entry point for SQLi, path traversal, and parameter manipulation. Identifying both layers through the Network tab and verbose cURL output is the foundation of GET-based web application assessment.

# POST

---

**Why POST Instead of GET**

POST moves parameters from the URL into the request body. This prevents credentials and sensitive data from appearing in server access logs, browser history, and referrer headers. It also removes URL length constraints and supports binary data, making it necessary for file uploads. From an attacker's perspective, POST parameters are equally injectable as GET parameters — they are simply located in the body rather than the URL.

---

**PHP Login Form vs Basic Auth**

Basic auth is handled at the web server level and uses the `Authorization` header. PHP form-based login uses a POST request with form parameters, and on success the server issues a `Set-Cookie` header containing a session identifier. Subsequent requests use that cookie to prove authentication rather than resending credentials. This distinction matters because testing each requires a different approach — basic auth uses `-u` or `-H Authorization`, while form auth requires capturing the POST parameters and then replaying the session cookie.

---

**Session Cookie Mechanics**

After a successful POST login, the server returns `Set-Cookie: PHPSESSID=...`. The browser stores this and sends it with every subsequent request. The `-b` flag in cURL replicates this behavior. Cookie replay is a foundational technique — a captured valid session cookie grants access without credentials, which is why XSS targeting `document.cookie` is so impactful. The Storage tab in DevTools allows manual injection of any cookie value, enabling session fixation testing directly in the browser.

---

**JSON POST Data**

When an application uses a JavaScript frontend that communicates with a backend API, POST data is often JSON rather than URL-encoded form data. The `Content-Type: application/json` header tells the server how to parse the body. If this header is omitted or incorrect, the server may reject the request or misparse the data. During testing, changing the Content-Type from `application/json` to `application/x-www-form-urlencoded` (or vice versa) sometimes bypasses input validation that only checks one format.

---

**Copy as cURL / Copy as Fetch**

These DevTools actions capture the exact request the browser sent, including all headers, cookies, and body data. This is the most reliable way to replicate a browser interaction in cURL without manually reconstructing headers. The copied cURL command can then be modified — changing parameter values, injecting payloads, removing or adding headers — to test application behavior systematically.



# CRUD API 

---

**API Structure and REST Convention**

The API in this section follows a RESTful pattern where the resource type (table) and the specific record are embedded directly in the URL path. The HTTP method determines what operation is performed on that resource. This separation of resource identification (URL) from operation type (method) is the core REST design principle. Understanding this pattern is essential for API security testing because it makes the attack surface immediately obvious - every endpoint and method combination is a potential target.

---

**Read (GET)**

GET requests retrieve data without modifying state. Appending a specific city name narrows the result to one record. Passing an empty string retrieves all records - this is equivalent to a full table dump and is a significant finding if unauthenticated access is permitted. Piping through `jq` formats the JSON response for readability and is useful when parsing large API responses during assessment.

---

**Create (POST)**

POST to the API endpoint with a JSON body creates a new record. The `Content-Type: application/json` header is mandatory - without it the server may reject the request or misparse the body. During penetration testing, the ability to create records without authentication is a direct data integrity vulnerability. It can also be an injection vector if the created data is later rendered in the application without sanitization.

---

**Update (PUT vs PATCH)**

PUT replaces an entire record at the specified URL path. The target record is identified by the URL - `/api.php/city/london` targets the london entry. The JSON body provides the replacement data. PATCH differs by only modifying specified fields rather than replacing the full record. The OPTIONS method can be used to determine which update methods the server accepts. Unrestricted PUT access allows an attacker to overwrite any record, which is both a data integrity and potential privilege escalation issue depending on what data the API exposes.

---

**Delete (DELETE)**

DELETE removes the record identified in the URL. An empty array response on subsequent GET confirms successful deletion. Unauthenticated DELETE access is a denial of service vector - an attacker can systematically remove all records from the database. This is why DELETE operations should always require authentication and authorization checks.

---

**Authentication Context**

In production APIs, all CRUD operations beyond Read typically require authentication via cookie or Authorization header (Bearer token/JWT). The absence of these controls on any write operation is a finding. During testing, verifying each method independently (GET, POST, PUT, DELETE) against both authenticated and unauthenticated requests maps the full access control surface of the API.