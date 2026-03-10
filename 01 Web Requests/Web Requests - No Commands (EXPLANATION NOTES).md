# HTTP Fundamentals 

## cURL Commands

These commands demonstrate how penetration testers interact directly with web servers using command-line HTTP requests instead of relying on browsers.

The first command represents the most basic HTTP request. When a URL is passed to the tool, it sends a default request to retrieve the web resource associated with that domain. The server processes the request and returns the raw content of the page.

Unlike browsers, this tool does not render HTML, JavaScript, or CSS. Instead, it prints the raw response. This is extremely useful in penetration testing because analysts are interested in how requests and responses behave rather than how pages visually render.

The second command downloads a file from a remote web server and stores it locally using the same filename as the remote resource. This is commonly used when collecting web assets during reconnaissance or when retrieving files discovered during testing.

The third command performs the same download but suppresses progress and status information. Silent requests are important when building scripts or automation tools because they prevent unnecessary output from interfering with parsing or logging.

The fourth command shows the built-in help menu. Security testers frequently rely on this to quickly recall available options or identify useful flags for crafting custom HTTP requests.

The final two commands provide deeper documentation. One prints every available option supported by the tool, while the other opens the full manual page. These references are essential when creating complex requests involving headers, authentication, uploads, or custom methods.

## cURL Flags

Each flag modifies how the HTTP request is sent or how the response is handled.

The data flag allows sending data in the body of a request, which typically converts the request into a POST operation. This is essential when testing login forms, APIs, or other endpoints that accept input.

The help flag prints documentation for available options.

The include flag instructs the tool to display the response headers along with the body. This is important during security testing because headers often reveal information such as server technology, cookies, or security configurations.

The output flag writes the response into a file with a user-defined name. This is useful when saving responses for later analysis.

The remote-name flag also writes responses to files, but automatically uses the name of the file on the remote server.

The silent flag removes progress output, making the command cleaner for automation.

The user flag allows sending credentials for HTTP authentication. This is frequently used when testing authenticated endpoints or brute forcing credentials.

The user-agent flag changes the identity string sent to the server. Many servers treat requests differently depending on the user agent, so modifying this value can help bypass filtering or mimic specific clients.

The verbose flag increases the amount of information displayed about the request and response. This is extremely useful for debugging and understanding how a server processes requests.

## File Paths

The hosts file is a local system configuration file used for manual DNS resolution. When a browser attempts to resolve a domain name, it first checks this file before querying external DNS servers.

Security testers often modify this file during assessments to map domain names to custom IP addresses. This technique is commonly used when testing applications hosted in lab environments or when performing attacks that require redirecting domain resolution to a controlled system.

## How This Fits Into Web Exploitation Methodology

Understanding HTTP communication is foundational for web security testing.

Every web attack—such as SQL injection, XSS, file inclusion, and authentication bypass—ultimately relies on manipulating HTTP requests and analyzing HTTP responses.

Tools like browsers hide much of the request structure, while command-line tools allow testers to fully control:

Request methods  
Headers  
Parameters  
Cookies  
Authentication data  
Payloads

This control is critical for identifying vulnerabilities, automating tests, and crafting exploit payloads.

Mastering raw HTTP interaction is therefore one of the first essential skills for any web penetration tester.

# Hypertext Transfer Protocol Secure (HTTPS) 

## cURL Commands

The first request demonstrates how to send an HTTPS request to a web server. When the client contacts a server over HTTPS, a TLS encryption process begins before any HTTP data is exchanged. The client verifies the server’s SSL/TLS certificate to ensure the server is legitimate and that the connection is secure.

If the certificate chain cannot be verified, the request fails. This behavior protects users from Man-in-the-Middle attacks where an attacker intercepts encrypted traffic and presents a fake certificate.

The second command bypasses this certificate validation process. When the certificate check is skipped, the client will proceed with the connection even if the certificate is invalid, self-signed, expired, or improperly configured.

This situation often occurs in penetration testing environments or internal development systems where valid certificates are not configured.

Skipping certificate validation allows testers to interact with such targets without TLS verification blocking the connection.

## cURL Flags

The insecure flag disables SSL/TLS certificate verification during HTTPS communication.

Normally, when a client connects to a secure server, it verifies:

The certificate was issued by a trusted Certificate Authority  
The certificate matches the domain name  
The certificate has not expired

If any of these checks fail, the connection is blocked.

Using this flag forces the connection to continue regardless of certificate validity.

In penetration testing, this is useful when interacting with:

Internal applications  
Lab environments  
Self-signed certificates  
Testing infrastructure

However, this behavior should not be used in production environments because it removes a key protection against Man-in-the-Middle attacks.

## HTTPS Security Model

Standard HTTP transmits all data in plaintext. Anyone with network access between the client and server can read the traffic, including credentials, session cookies, and sensitive data.

HTTPS prevents this by encrypting the entire communication channel using TLS.

During the TLS handshake:

The client sends a hello message identifying supported encryption methods  
The server responds with its certificate and encryption preferences  
Both sides exchange keys securely  
A secure encrypted session is established

After this handshake completes, normal HTTP communication continues inside the encrypted channel.

Because the data is encrypted, intercepted traffic appears as unreadable encrypted streams rather than visible parameters and credentials.

## HTTPS Redirection Flow

Many websites automatically redirect HTTP traffic to HTTPS.

When a user attempts to access a site using HTTP:

The client first sends a request to port 80  
The server responds with a redirection response  
The response instructs the client to reconnect using HTTPS on port 443

After this redirect, the TLS handshake begins and secure communication starts.

This mechanism ensures users are forced to use encrypted connections.

## DNS and Privacy Considerations

Even when HTTPS encrypts web traffic, the domain name being accessed may still be exposed through DNS queries if they are sent to an unencrypted DNS server.

This means observers could still see which domains a user is visiting even though the actual HTTP traffic is encrypted.

Using encrypted DNS services or VPN tunnels helps prevent this type of information leakage.

## HTTPS Attacker Perspective

Although HTTPS encrypts traffic, attackers still attempt several techniques to bypass it.

One example is a downgrade attack. In this scenario, an attacker intercepts the connection and forces communication to fall back from HTTPS to HTTP. If successful, all transmitted data becomes visible again.

Modern browsers and servers mitigate this risk through mechanisms like strict transport security and secure redirection policies.

Understanding how HTTPS works is essential for penetration testers because many attacks focus on misconfigurations around TLS, certificate handling, and traffic interception.

# HTTP Requests and Responses

## HTTP Request Structure

The request line has three parts: method, path, and HTTP version.

The method describes the action the client wants to perform against the resource.

The path identifies the target resource on the server and may include query parameters.

The version tells the server which HTTP format the client is using.

After the request line come header fields. These provide context about the client, the target host, session state, accepted content, and connection preferences.

A request can optionally include a body. This is typically used when sending data to the server.

## HTTP Response Structure

The response begins with the HTTP version and a status code with its text description.

The status code tells the client whether the request succeeded, failed, redirected, or needs authentication.

After that, the server includes response headers. These describe server software, cookies being set, content length, content type, authentication requirements, and related metadata.

The response may also include a body containing the returned resource. This body might be HTML, JSON, scripts, images, or documents.

## cURL Verbose Mode

The verbose request in Output A shows both sides of the HTTP exchange.

This is useful because it exposes exactly what the client sends and what the server returns, which is critical during web testing.

The request section reveals default headers automatically added by the client, such as host identification, accepted content, and connection handling.

The response section reveals authentication challenges, server metadata, and how access control is enforced.

This is especially important when analyzing login flows, restricted content, redirects, cookies, or custom server behavior.

The more verbose variant provides additional transport and connection details, which helps troubleshoot low-level HTTP behavior.

## Browser DevTools

The browser developer tools provide a visual way to inspect web traffic generated by the browser.

The network view helps track every resource requested by the page, including documents, images, scripts, stylesheets, and background requests.

This is valuable because modern web applications rarely make only one request. Attackers need to understand the full request chain to find hidden endpoints, APIs, parameters, and assets.

The response view allows inspection of returned content, while the raw view exposes the unrendered source. That distinction matters because browsers may visually transform content that differs from the actual server response.

The filtering feature becomes important when pages generate many requests and the tester needs to isolate one endpoint or one asset.

## Why This Matters in Web Exploitation

Web attacks are built around understanding request and response behavior.

A tester needs to know what is being sent, what the server expects, how the server reacts, and which headers or cookies affect authorization and session handling.

The request examples in Output A illustrate how authentication state can be embedded in cookies and how the host and user agent influence server behavior.

The response examples show how the server signals access denial, authentication requirements, and content formatting.

This knowledge forms the basis for later tasks such as request manipulation, fuzzing, authentication testing, session analysis, and exploit development.

# HTTP Headers

## General Headers

These headers describe the message context rather than the specific content being transferred.

The date header records when the message originated. This helps correlate requests and responses and is useful during proxy analysis, replay, and timing investigation.

The connection header controls whether the TCP session should remain open after the exchange finishes. This matters because persistent connections can affect how multiple requests are handled and observed during testing.

## Entity Headers

These describe the body content being transferred.

The content type tells the receiver how to interpret the payload. This is critical in web exploitation because servers often process input differently depending on declared type, and some vulnerabilities depend on forcing a specific interpretation.

The media type serves a similar purpose and is especially relevant when the transferred resource is not standard HTML, such as JSON, XML, or PDF content.

The boundary value is used when one message contains multiple parts, such as multipart form submissions. This matters in file upload testing and request tampering because each part must be correctly separated for the server to parse it.

The content length tells the receiver how many bytes to read from the body. This is fundamental to correct parsing and sometimes relevant in request smuggling or malformed request research.

The content encoding indicates transformations applied to the body, such as compression. Attackers and testers need to recognize this so they know whether the response must be decompressed before analysis.

## Request Headers

These are sent by the client and shape how the server interprets the request.

The host header identifies which virtual host is being requested. This is very important in enumeration because multiple applications can share the same IP and be differentiated only by hostname.

The user agent describes the client software. Servers may vary behavior based on this value, so changing it can help bypass filters, mimic browsers, or trigger alternate responses.

The referer indicates where the request came from. Applications sometimes rely on it for weak access control or workflow validation, which is dangerous because it can be spoofed.

The accept header states which response formats the client can handle. This can affect content negotiation and sometimes reveal alternate representations of the same resource.

The cookie header carries session and tracking values. This is central to authentication testing, privilege testing, and session management analysis.

The authorization header carries credentials or tokens. It is commonly involved in protected resource access and API authentication flows.

## Response Headers

These are returned by the server and reveal useful information about server behavior.

The server header can expose web server type and version, which helps with fingerprinting and vulnerability research.

The set-cookie header instructs the browser to store client identifiers or session data. This is a core part of login flows, session persistence, and access control.

The authenticate header tells the client what kind of authentication is required. This helps identify whether the application expects basic authentication or another challenge-based mechanism.

## Security Headers

These headers instruct the browser to enforce client-side protections.

The content security policy restricts where scripts and other resources may be loaded from. It is one of the main defenses against content injection and cross-site scripting.

Strict transport security forces future access over HTTPS and reduces the chance of downgrade attacks or accidental plaintext connections.

The referrer policy limits how much origin information is sent when navigating away from the site, helping prevent leakage of sensitive URLs or internal paths.

## cURL Usage

The header-only request is useful when the tester wants metadata without fetching the full response body. This is efficient for fingerprinting, checking security headers, and confirming server behavior.

The include option differs because it shows headers together with the body for the request being sent, rather than switching to a header-only request type.

The custom header option allows manual control over request fields, which is essential for tampering, replaying, and simulating browser behavior.

The user-agent option is a shortcut for changing one commonly manipulated header. This is frequently used to test whether servers behave differently for browsers, bots, or command-line clients.

The verbose option helps confirm that custom header changes were actually transmitted and shows how the server responded.

## Browser DevTools

The network panel gives a structured view of request and response headers for each browser-generated request.

The headers view is useful for quickly seeing parsed values, while the raw view is better for understanding the exact wire-format structure.

The cookies view helps track session values and browser state, which is important for authentication analysis and debugging multi-step workflows.

## How This Fits Into Exploitation Methodology

Headers are a major part of web attack surface analysis.

They reveal server identity, security posture, session design, routing behavior, and client expectations.

They are also frequent manipulation targets. Host headers are useful for virtual host discovery. Cookies and authorization values drive access control testing. Referer and user-agent values may influence weak server-side logic. Content type and boundary values control how user input is parsed.

Understanding headers is therefore essential before moving into deeper topics like authentication abuse, request smuggling, host header attacks, file upload abuse, and content injection.

# HTTP Methods and Codes 

## HTTP Methods

HTTP methods define the action that the client is requesting the server to perform on a resource.

The retrieval method is used when a client simply wants to obtain data from the server. Parameters can be passed directly within the URL as query values. Because these parameters appear in the URL, they are visible and commonly logged by servers and proxies.

The submission method is designed to send data to the server. Unlike the retrieval method, the transmitted data is placed inside the request body. This allows larger and more complex inputs such as form submissions, file uploads, and structured data.

The header-only method asks the server to return only metadata about the resource. It is typically used to verify resource existence, check size, or inspect headers without downloading the entire content.

The resource creation method instructs the server to create a new resource at a specific location. If misconfigured, this method may allow attackers to upload files or web shells directly to the server.

The removal method instructs the server to delete a specified resource. If access control is weak, attackers may remove files and disrupt application functionality.

The capability discovery method asks the server which methods are allowed for a particular endpoint. This is commonly used during reconnaissance to determine available interaction methods.

The partial update method modifies only specific portions of an existing resource instead of replacing it completely.

## Status Code Classes

HTTP responses are grouped into five major categories based on the first digit of the status code.

Informational responses indicate that the request was received and processing is continuing.

Successful responses indicate that the request was accepted and completed successfully.

Redirection responses inform the client that it must perform an additional request to complete the process, typically by visiting a different location.

Client error responses indicate that something is wrong with the request itself, such as invalid syntax, missing resources, or insufficient permissions.

Server error responses indicate that the server failed while processing a valid request.

## Common Status Codes

A successful response indicates that the requested resource was retrieved correctly and returned to the client.

A redirection response instructs the browser to request another URL, which commonly happens after authentication or when resources are moved.

A malformed request response indicates that the server could not understand the request due to incorrect formatting or missing components.

A forbidden response indicates that the server understood the request but refuses to provide access to the resource.

A missing resource response indicates that the requested path does not exist on the server.

A server failure response indicates that the server encountered an internal problem while processing the request.

## Attacker Mindset

Understanding HTTP methods and status codes is fundamental for web application testing.

Different methods expose different attack surfaces. File upload vulnerabilities may arise from resource creation methods, while weak API controls often involve update or deletion methods.

Status codes also provide valuable feedback during enumeration and fuzzing. For example, distinguishing between missing resources and forbidden access helps testers determine whether a resource exists but is protected.

Attackers often rely on these subtle differences in server responses to map application structure, identify hidden endpoints, and determine how the server processes requests.

# GET

## HTTP Basic Authentication

This section shows a server-protected page that uses webserver-level basic authentication rather than a normal application login form.

In this model, the browser or client sends credentials directly as part of the HTTP exchange. The application does not first display a form and then submit credentials through a typical backend handler. Instead, the webserver challenges the client and expects credentials in an authorization header.

The unauthenticated request in Output A triggers an authentication challenge. The authenticate header tells the client that basic authentication is required and identifies the protection realm.

## Credential Delivery Methods

The examples demonstrate three different ways to authenticate to the same resource.

One approach sends the username and password using the client’s built-in authentication option.

Another embeds the credentials directly inside the URL. That format is supported by some tools and browsers, though it is generally less desirable because credentials may leak through history, logs, or interface exposure.

The third approach manually supplies the authorization header itself. This is especially important in web testing because it proves that authentication is ultimately controlled by header content, not by how the client collected the credentials.

## Authorization Header Behavior

The authorization value shown in Output A is a base64 representation of the username and password pair.

That encoding is not encryption. It is only a transport format. Anyone who intercepts the request can trivially recover the original credentials if the connection is not protected.

This is why basic authentication over plaintext HTTP is dangerous. The credentials are effectively exposed to anyone who can observe the traffic.

## GET Request Behavior

After authentication succeeds, the application exposes a search feature that sends input through a retrieval request.

The key idea is that the user input appears directly in the URL as a query parameter. That means the search term is easy to identify in browser developer tools and easy to replay outside the browser.

This is why GET-based functionality is convenient for testing. Once the request is observed, it can usually be reproduced exactly with minimal effort.

## Request Reproduction Workflow

The section highlights a common tester workflow.

First, use the browser network panel to observe how the application communicates with the backend.

Then identify the endpoint involved and the parameters being sent.

Finally, replay the request directly using a command-line client or browser console tooling.

This matters because direct replay isolates the backend behavior from the user interface. It lets a tester focus on parameters, authentication, headers, and server responses without browser rendering getting in the way.

## Copy as cURL and Copy as Fetch

These browser features are extremely valuable during assessments.

The cURL export provides a near-direct reproduction of the browser request in a terminal-friendly format. This is useful for scripting, automation, and fuzzing.

The fetch export provides the same request logic in browser-side JavaScript form. This is useful for quick in-browser replay and experimentation.

Both features help bridge the gap between passive observation and active testing.

## Why This Matters for Exploitation

This section teaches several core concepts that appear constantly in web assessments.

Authentication may be controlled entirely by headers.

Credentials can sometimes be replayed by reconstructing a single header.

GET parameters are visible and easy to manipulate.

Observed browser requests can often be copied and reproduced exactly for manual testing.

Together, these ideas are foundational for later work involving endpoint enumeration, parameter tampering, authentication bypass attempts, IDOR testing, and automated fuzzing.

# POST

## POST Request Purpose

This section introduces the submission method used when applications need to send data in the request body rather than exposing it in the URL.

That design is useful for login forms, file uploads, and structured payloads because it avoids URL length limits, reduces encoding constraints, and keeps submitted values out of the visible address bar.

## Form Authentication Flow

The login form shown here uses application-level authentication instead of webserver-level basic authentication.

The credentials are submitted as body parameters, and the server checks them through backend logic. Successful authentication changes the server response from the login interface to the authenticated search interface.

The important concept is that the login is just another HTTP request. Once the tester sees the exact submitted fields, the browser is no longer required to repeat the action.

## Replaying a Login Request

The form data in Output A shows the exact parameter names and values sent during authentication.

Replaying that same body outside the browser demonstrates a core assessment technique: once a request is understood, the interface can be bypassed and the tester can interact directly with the backend.

This is a major advantage during testing because it speeds up iteration and makes parameter tampering much easier.

## Redirect Handling

Some applications authenticate and then immediately redirect the user to another page. In those cases, following redirects is necessary to fully reproduce the browser flow.

That behavior matters because the initial authentication response may not itself contain the final authenticated page.

## Authenticated Cookies

After successful login, the server issues a session cookie. That cookie is the real proof of authenticated state in subsequent requests.

This is one of the most important ideas in web testing: many applications stop requiring credentials after login and rely entirely on a session identifier stored in a cookie.

Once that cookie is known, requests can often be replayed indefinitely until the session expires or is invalidated.

## Cookie Reuse

The examples show that the session cookie can be reused directly in later requests.

This demonstrates session persistence and also why cookies are security-sensitive. Anyone who obtains a valid authenticated cookie may be able to impersonate the user without ever knowing the original credentials.

This is why session theft is such a central theme in web exploitation, especially in attacks involving cross-site scripting and insecure transport.

## Browser Storage Editing

The browser storage view shows that cookies can be inspected, removed, and replaced manually.

From an attacker mindset, this is useful for understanding whether authentication is cookie-driven and whether swapping values changes application state.

It also helps confirm whether the server is truly validating the session or merely trusting a client-supplied identifier.

## JSON Request Bodies

The search feature uses a body formatted as structured JSON instead of form-encoded key-value pairs.

That changes how the server expects to parse the request. Without the correct content type declaration, the backend may fail to interpret the payload correctly or may route it through a different parser.

Recognizing body format is therefore essential when reproducing requests.

## Content Type Importance

The header indicating JSON content is not just descriptive. It often controls how server-side frameworks read the incoming data.

This is why repeating the same request body without the correct content type can produce different behavior.

For testers, this means payload reproduction must include not only the data itself, but also the headers that tell the application how to process that data.

## Direct Backend Interaction

The final request shows complete direct interaction with the search endpoint using only the session cookie and the correct JSON content type.

This is the practical goal of web request analysis: isolate the minimum pieces required to talk to the backend without relying on the front-end interface.

Once that is achieved, the tester can fuzz parameters, automate requests, inspect authorization boundaries, and test edge cases far more efficiently.

## Why This Matters for Exploitation

This section ties together several foundational web testing concepts.

Authentication often results in a reusable session token.

Session cookies can be sufficient for full account access.

Backend endpoints may accept either form-encoded or JSON payloads depending on the feature.

Correct reproduction requires matching the original body format and important headers.

These concepts are essential for later work involving login abuse, session hijacking, CSRF analysis, authenticated endpoint testing, API tampering, and automated request replay.


#
#