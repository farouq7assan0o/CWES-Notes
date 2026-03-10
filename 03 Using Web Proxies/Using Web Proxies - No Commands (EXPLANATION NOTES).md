# Intro to Web Proxies 

## Intro to Web Proxies

Modern web and mobile applications constantly communicate with back-end servers to send and receive data. Most of the application's logic and data processing happens on these servers. Because of this architecture, security testing focuses heavily on analyzing the communication between the client (browser or mobile app) and the server.

Penetration testers must be able to observe, capture, and manipulate this communication to understand how the application works and identify vulnerabilities.

## What Web Proxies Are

Web proxies function as **Man-In-The-Middle (MITM)** tools placed between the client application and the web server.

Instead of the browser communicating directly with the server, the traffic passes through the proxy first. This allows the tester to:

- Capture every HTTP request sent by the browser
    
- Capture every response returned by the server
    
- Inspect the contents of these messages
    
- Modify requests before they reach the server
    
- Replay requests to test application behavior
    

This capability is fundamental in web application penetration testing.

## Difference Between Web Proxies and Network Sniffers

Tools like **Wireshark** monitor all network traffic flowing through a network interface. They operate at a lower network level and analyze packets from many protocols.

Web proxies are more specialized. They focus on **web protocols**, primarily:

- HTTP
    
- HTTPS
    

They also provide structured interfaces that make web traffic easier to read and modify.

## Why Web Proxies Are Essential in Pentesting

A web proxy allows testers to see the **exact requests an application sends**, including:

- Parameters
    
- Cookies
    
- Headers
    
- Session tokens
    
- API calls
    
- Authentication flows
    

This visibility is critical for discovering vulnerabilities such as:

- Authentication flaws
    
- Injection vulnerabilities
    
- Broken access control
    
- Logic flaws
    

Without a proxy, many of these vulnerabilities would be extremely difficult to detect.

## Common Uses of Web Proxies

Web proxies provide many capabilities beyond simple request interception.

These include:

### Web Application Vulnerability Scanning

Automated scanning tools can analyze captured requests to identify known vulnerabilities.

### Web Fuzzing

Testers can send large numbers of modified requests to discover hidden parameters, endpoints, or vulnerabilities.

### Web Crawling

The proxy can automatically browse through the application and map all discovered pages and endpoints.

### Web Application Mapping

Proxies help create a structured map of the application, showing directories, endpoints, APIs, and functionality.

### Web Request Analysis

Each request and response can be inspected to understand application behavior and identify weaknesses.

### Web Configuration Testing

Misconfigurations in headers, cookies, authentication, or access control can be identified.

### Code Reviews

Captured requests can reveal how the back-end application logic is structured.

## Burp Suite

Burp Suite is the **most widely used web proxy in penetration testing**.

Key reasons for its popularity:

- Powerful interface
    
- Extensive testing features
    
- Built-in browser
    
- Large ecosystem of extensions
    

Burp comes in multiple versions:

### Burp Community (Free)

The free version includes most essential pentesting features such as:

- Proxy interception
    
- Request modification
    
- Manual testing tools
    

### Burp Pro / Enterprise (Paid)

Paid versions add advanced features including:

- Active vulnerability scanning
    
- Faster attack tools
    
- More extension support
    

These features become useful during advanced assessments and enterprise penetration testing.

## OWASP ZAP (Zed Attack Proxy)

ZAP is a **free and open-source web proxy** maintained by the OWASP community.

Advantages of ZAP include:

- Completely free
    
- No throttling or feature restrictions
    
- Growing community support
    
- Continuous addition of new features
    

ZAP provides many capabilities similar to Burp, including scanning, fuzzing, and crawling.

## Burp vs ZAP

Both tools serve the same core purpose: **intercepting and analyzing web traffic**.

However, they differ slightly in philosophy:

Burp Suite:

- Industry standard
    
- More mature ecosystem
    
- Some advanced features require payment
    

ZAP:

- Fully open source
    
- No feature limitations
    
- Rapidly growing capabilities
    

Many penetration testers learn both tools so they can choose the most suitable one depending on the engagement or environment.

## Key Takeaway

Web proxies are the **central tool of web application penetration testing**. They allow testers to intercept, analyze, modify, and replay HTTP requests, enabling deep analysis of how applications communicate with their back-end servers and where vulnerabilities may exist.

# Setting Up 

## Setting Up

This section explains how to install and launch the two primary web proxy tools used in web application penetration testing: Burp Suite and OWASP ZAP.

Both tools run on multiple operating systems including Windows, macOS, and Linux. They are already installed on environments commonly used for penetration testing such as PwnBox, Parrot OS, and Kali Linux.

Understanding how to install and launch them manually is important when setting up your own lab or testing environment.

---

## Burp Suite

Burp Suite can be installed using platform-specific installers provided on its official download page. The installation process is straightforward and differs slightly depending on the operating system.

Once installed, Burp Suite can be launched directly from the terminal or from the system’s application menu.

Burp can also be distributed as a **cross-platform Java archive file (JAR)**. This allows it to run on any system that has a **Java Runtime Environment (JRE)** installed.

The command referenced in **COMMAND NOTES → COMMANDS** launches Burp by executing the JAR file through Java.

---

## Burp Project Setup

When Burp starts, it asks the user to configure a project.

In the **Community Edition**, only temporary projects are available. These projects do not save progress between sessions.

Temporary projects are usually sufficient for most short-term penetration testing activities.

The **Professional or Enterprise versions** allow disk-based projects, enabling testers to save:

- Scan results
    
- Discovered vulnerabilities
    
- Application maps
    
- Testing progress
    

This feature is useful when performing long-term or large-scale assessments.

---

## Burp Configuration

After selecting a project type, Burp allows loading configuration settings.

Typical options include:

- Default configuration
    
- Custom configuration files
    
- Saved project options
    

For beginners or initial testing, using the **default configuration** is sufficient.

Later, testers may customize settings such as proxy behavior, scanning options, or extensions.

---

## OWASP ZAP

OWASP ZAP is another web proxy tool widely used in penetration testing.

Like Burp, it can be downloaded through installers or as a Java JAR file. The Java execution command referenced in **COMMAND NOTES → COMMANDS** runs the ZAP JAR file.

ZAP is fully open-source and does not have feature restrictions like the free version of Burp.

---

## ZAP Session Setup

When ZAP starts, it prompts the user to decide whether to persist the session.

Options include:

- Persist session with timestamp
    
- Persist session with custom name
    
- Do not persist session
    

For small labs or temporary testing, a non-persistent session is typically sufficient.

Persistent sessions are more useful for large penetration tests that span multiple days.

---

## Java Runtime Requirement

Both Burp Suite and ZAP rely on the **Java Runtime Environment (JRE)**.

Most official installers include Java automatically. However, when running the tools from standalone JAR files, Java must already be installed on the system.

Without Java, the tools cannot execute.

---

## User Interface Customization

Both tools allow customization of their interface themes.

Burp supports dark mode through its user interface settings.

ZAP also supports dark themes through its display configuration options.

These changes are purely cosmetic and help improve usability during long testing sessions.

---

## Key Takeaway

Burp Suite and ZAP are essential web proxy tools used to intercept and analyze web application traffic. Installing and launching them properly ensures that testers can begin capturing and manipulating HTTP requests, which is a fundamental step in web application penetration testing.

# Proxy Setup 

## Proxy Setup

This section explains how to configure a browser so that all web traffic is routed through a web proxy such as Burp Suite or OWASP ZAP. Once the proxy is configured, every HTTP or HTTPS request sent by the browser will pass through the proxy before reaching the server.

This allows penetration testers to observe, intercept, modify, and replay requests.

---

## Pre-Configured Browser

Both Burp Suite and ZAP include a built-in browser that is already configured to route traffic through the proxy.

Using the built-in browser is the fastest way to start testing because:

- Proxy configuration is already completed
    
- Required certificates are already installed
    
- No manual browser configuration is needed
    

In Burp, the embedded browser can be opened from the Proxy tab.

In ZAP, a pre-configured Firefox browser can be launched from the top toolbar.

For simple labs and exercises, using these built-in browsers is usually sufficient.

---

## Using a Real Browser

In real penetration tests, testers often prefer using their normal browser (commonly Firefox). This requires configuring the browser to send traffic through the proxy server.

Both Burp and ZAP run a **local proxy listener** that receives browser traffic. By default, this listener runs on:

- Localhost address referenced in **COMMAND NOTES → REQUEST PATTERNS**
    
- Port referenced in **COMMAND NOTES → PORTS**
    

When the browser sends traffic to this address and port, the proxy intercepts it before forwarding it to the target server.

---

## Changing Proxy Ports

Both Burp and ZAP allow the proxy listener port to be changed.

This is necessary when:

- Another application already uses the default port
    
- Multiple proxies are running simultaneously
    
- Custom network configurations are required
    

If the configured port is already in use, the proxy server will fail to start.

Whenever the proxy port changes, the browser must be updated to use the same port.

---

## Using FoxyProxy

Manually switching browser proxy settings can be slow and inconvenient. The **FoxyProxy** Firefox extension simplifies this process.

FoxyProxy allows users to:

- Store multiple proxy configurations
    
- Enable or disable proxies with one click
    
- Quickly switch between proxies
    

A proxy profile can be created using:

- The localhost address referenced in **COMMAND NOTES → REQUEST PATTERNS**
    
- The default proxy port referenced in **COMMAND NOTES → PORTS**
    

Once configured, the proxy can be activated directly from the FoxyProxy toolbar menu.

In PwnBox environments, this configuration is already set up.

---

## Installing the Proxy CA Certificate

When intercepting HTTPS traffic, the proxy must decrypt and re-encrypt encrypted communications.

To do this, the proxy acts as a trusted certificate authority (CA). The browser must trust this CA certificate; otherwise HTTPS connections will fail or trigger warnings.

The certificate for Burp can be downloaded from the endpoint referenced in **COMMAND NOTES → ENDPOINTS**.

ZAP provides its certificate through its internal settings panel.

---

## Importing the Certificate in Firefox

Once downloaded, the certificate must be imported into Firefox's certificate store.

This is done through the browser security settings referenced in **COMMAND NOTES → PATHS**.

The certificate is imported into the **Authorities** section and trusted for identifying websites.

Without trusting the proxy CA certificate, the browser will treat intercepted HTTPS connections as insecure.

---

## Final Result

After completing the proxy configuration and installing the certificate:

- All browser traffic passes through the proxy
    
- HTTP and HTTPS requests can be captured
    
- Requests and responses can be inspected and modified
    

This setup forms the foundation for most web penetration testing techniques, including request manipulation, fuzzing, vulnerability discovery, and authentication analysis.

# Intercepting Web Requests 

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

## Intercepting Responses

This section explains how a web proxy can intercept server responses before they reach the browser. While request interception allows modification of what the client sends, response interception allows modification of what the browser receives.

This capability allows testers to alter the rendered page behavior locally. The server response remains unchanged on the server, but the browser interprets the modified version sent by the proxy.

## Why Response Interception Is Useful

Web applications frequently enforce restrictions on the client side using HTML attributes or JavaScript. These restrictions may limit input formats, disable fields, or hide sensitive interface elements.

By intercepting and editing the response, a tester can remove these restrictions before the browser renders the page. This makes it easier to test inputs that the interface normally blocks.

The key principle is that front-end restrictions do not equal server-side security.

## Example: Input Restriction

The intercepted response contains HTML for the IP input field. In the original response, the field is configured to only accept numbers and limits the input length.

These restrictions are implemented through HTML attributes. The browser enforces them during user interaction, preventing non-numeric input or longer values.

However, because the response passes through the proxy before reaching the browser, the tester can modify the HTML attributes directly.

## Changing the Input Field Type

The lab demonstrates replacing the numeric input type with a text input type and increasing the allowed length of the field.

Once the browser receives the modified response, it renders the field differently. The restriction on numeric input disappears, and longer input values become possible.

This enables the tester to submit payloads directly through the interface without repeatedly intercepting requests.

## Relationship to the Previous Section

In the previous section, the payload had to be inserted manually by editing the intercepted request.

By modifying the response instead, the tester changes the interface itself so that the browser accepts the payload normally.

Both methods achieve the same goal but response modification can make repeated testing much faster.

## ZAP Response Interception

ZAP allows a similar workflow. After intercepting a request and forwarding it, the tool can pause the corresponding response automatically.

This allows direct editing of the returned HTML before it is rendered by the browser.

ZAP also provides additional user interface tools through its HUD that can automatically reveal hidden elements or enable disabled controls.

## Automatic Field Enabling

Some interfaces intentionally hide or disable input elements to prevent user interaction.

ZAP HUD includes features that automatically reveal hidden fields or activate disabled elements without needing manual HTML editing.

This simplifies testing when the goal is simply to interact with the interface rather than rewrite the response manually.

## Revealing Hidden Comments

Another useful feature allows testers to highlight HTML comments embedded in the page source.

Developers sometimes leave comments containing debugging information, endpoint hints, or hidden functionality references. Revealing these comments can expose useful clues during penetration testing.

## Why This Matters for Web Pentesting

Many vulnerabilities exist because developers rely on client-side restrictions rather than server-side validation.

Response interception allows testers to bypass those restrictions instantly by modifying the page structure before the browser renders it.

This technique is particularly useful for testing:

- Input validation flaws
    
- Hidden functionality
    
- Disabled administrative features
    
- Client-side security controls
    

## Key Takeaway

Response interception allows testers to modify how the browser renders a page by editing the server response before it arrives. This technique bypasses client-side restrictions and enables deeper testing of application behavior, revealing vulnerabilities that might otherwise remain hidden behind front-end controls.


# Automatic Modification 

## Automatic Modification

This section introduces the concept of automatic request and response modification within a web proxy. Instead of manually intercepting every request or response and editing it each time, rules can be defined so the proxy automatically performs the modification whenever matching traffic appears.

This saves time during testing and allows repetitive changes to be applied consistently across all traffic.

## Automatic Request Modification

Automatic request modification alters outgoing requests before they reach the server.

This is useful when testers want to consistently change a header or parameter value. One common example is modifying the User-Agent header.

Some web applications use filtering mechanisms based on the User-Agent string to block scanners, bots, or certain tools. By replacing the User-Agent with a custom value, a tester can bypass such filters or simulate different client environments.

The rule defined in Output A targets the request header that contains the User-Agent value. The matching expression identifies the entire header line, and the replacement string overwrites it with a custom identifier.

Once the rule is active, the proxy modifies every outgoing request automatically.

## Burp Match and Replace

Burp implements this functionality through Match and Replace rules.

Each rule contains several components:

- The location where matching occurs (request header, request body, response header, or response body)
    
- The text or pattern to match
    
- The replacement value
    
- Whether the match should use regular expressions
    

In the User-Agent example, a regular expression is used because the original User-Agent string may vary depending on the browser and environment.

By matching the entire header line, the rule ensures that the header is replaced regardless of the original value.

## ZAP Replacer

ZAP provides a similar feature called Replacer.

Instead of a single interface like Burp's match-and-replace, ZAP organizes these rules under its Replacer configuration. Each rule defines:

- A description
    
- A match type (for example request header)
    
- The header or text to match
    
- The replacement value
    
- Whether the rule is enabled
    

ZAP also allows specifying initiators, which determine where the rule is applied. For example, rules can apply to proxy traffic, scanning traffic, or all HTTP messages.

In the lab scenario, the rule is applied globally so it affects all requests.

## Automatic Response Modification

The same principle can be applied to incoming responses.

In the previous section, the IP input field had to be manually modified every time the page loaded because the server always returned the original HTML.

By creating a response modification rule, the proxy automatically replaces the restrictive HTML attributes each time the page is received.

This means the tester no longer needs to intercept and edit the response manually.

## Removing Front-End Restrictions

The rules demonstrated in Output A replace restrictive HTML attributes in the response.

The numeric input type is converted into a text input type, and the maximum allowed input length is increased.

Once the browser receives the modified response, the interface behaves differently even though the server-side code has not changed.

This allows testers to directly enter payloads through the interface without repeated interception.

## Persisting Modifications

One key advantage of automatic response modification is persistence.

If the page is refreshed, the server still sends the original HTML. However, the proxy immediately rewrites the response again before the browser renders it.

This ensures the testing environment remains modified without manual intervention.

## Combining Request and Response Automation

Request and response modifications can be combined to automate entire attack workflows.

For example:

- Response rules remove input restrictions
    
- Request rules inject payloads automatically
    

This combination allows repeated vulnerability testing with minimal manual effort.

## Attacker Mindset

Attackers and penetration testers often automate repetitive changes during testing.

Manually editing every request becomes inefficient when testing many variations or repeatedly interacting with the same endpoint.

By using proxy automation rules, testers can focus on analyzing results rather than performing repetitive edits.

## Key Takeaway

Automatic modification rules allow web proxies to dynamically rewrite HTTP requests and responses as they pass through the proxy. This enables persistent testing environments, bypasses front-end restrictions, and simplifies repeated vulnerability testing without requiring constant manual interception.

# Repeating Requests 

## Repeating Requests

This section focuses on speeding up testing once a useful request has already been captured. After finding a vulnerable or interesting request, repeatedly re-intercepting it through the browser is inefficient. Request repeating solves that by letting the tester take a previously seen request, edit it directly inside the proxy tool, resend it, and immediately inspect the response.

## Proxy History

The workflow starts in the proxy history. Both tools keep a record of requests that passed through them, which becomes the tester’s working dataset.

That history is valuable because it shows:

the exact request that succeeded before  
the matching response  
the target path and method  
which parameters were submitted

Once the interesting request is located, it can be reused without recreating the browser interaction that generated it.

## Burp Repeater

Burp’s repeater feature is designed for iterative manual testing. A captured request is sent into a dedicated editor where it can be modified and resent as many times as needed.

The key benefit is control. Instead of changing application input through the page, then intercepting, then forwarding, the tester works on the raw request directly. This makes enumeration and payload testing much faster.

The method-switching feature is also useful because it lets the tester explore how the endpoint behaves when the same target is accessed with a different HTTP verb.

## ZAP Request Editor

ZAP provides a similar workflow through its request editor. The idea is the same: open an existing request from history, modify it, resend it, and review the response inside the tool.

This is useful for rapid experimentation because the tester can change one field at a time and immediately observe the effect. That supports careful hypothesis testing rather than noisy guessing.

## ZAP HUD Replay

The HUD adds convenience inside the browser view. Instead of switching fully back into the main interface, the tester can pick a request from history and replay it from the in-browser controls.

The two replay modes matter:

Replay in Console is better when the tester wants to inspect the raw response as data  
Replay in Browser is better when the tester wants to see how the response is rendered visually

That distinction becomes important when testing APIs versus full web pages.

## Why This Matters in Exploitation

Repeating requests is a force multiplier for manual testing. Once a request reaches an interesting code path, the tester can keep reusing that same path with new parameter values instead of rebuilding the interaction each time.

This is especially important when testing:

injection payload variations  
authentication and authorization edge cases  
parameter tampering  
different methods against the same endpoint  
small incremental edits to isolate behavior

The mindset is to reduce friction between hypothesis and result.

## Request Modification During Repetition

The request editors in both tools allow direct modification of the request text. This turns the captured request into a reusable template.

That matters because many vulnerabilities are found through slight variations, not a single payload. Repeater-style testing supports:

changing only one parameter  
changing only one header  
testing multiple encodings  
comparing how responses differ between attempts

That comparison-based workflow is central to effective web exploitation.

## Burp vs ZAP Behavior

An important distinction noted in the source is that ZAP shows the final modified request, while Burp can show both the original request and the edited version. That is useful when tracking how a request evolved during testing, especially when many small changes are made.

This helps with accuracy and reduces confusion during longer attack chains.

## URL Encoding Relevance

The section ends by noting that request data is URL-encoded. This matters because repeated requests often involve changing parameter values, and those values may need proper encoding before the server interprets them as intended.

Understanding encoding is part of accurate manual request crafting. Without that, a tester may think a payload failed when it was actually malformed during transmission.

## Key Takeaway

Request repeating turns captured traffic into an interactive testing workflow. Instead of repeatedly using the browser and proxy interception loop, the tester can edit and resend raw requests directly inside Burp or ZAP, making manual enumeration, payload refinement, and vulnerability validation much faster and more precise.

# Encoding/Decoding 

## Encoding/Decoding

This section explains why encoding and decoding are essential when modifying HTTP requests and analyzing application data. Once traffic is intercepted and edited, the tester often needs to convert values into the format the server expects or decode values the application is trying to conceal or serialize.

## URL Encoding

When request data contains special characters, those characters can change how the server parses the request unless they are encoded first.

The key issue is that some characters have protocol meaning rather than literal meaning. For example, spaces can terminate data unexpectedly, ampersands can split parameters, and hash characters can be interpreted as fragment markers. Because of that, payloads inserted into parameters often need to be URL-encoded before the request is resent.

In practice, this is part of reliable manual testing. A payload may fail not because the vulnerability is absent, but because the modified request was malformed.

## Built-In Proxy Encoding Support

Both proxy tools include built-in helpers for this task. Burp supports inline URL encoding directly from the request editor, while ZAP generally handles request encoding in the background before transmission.

This matters because it reduces friction during testing. Instead of relying on external tools, the tester can work inside the same interception and replay workflow.

## Other Encodings

Web applications do not only use URL encoding. They frequently store or transmit values using other formats for transport, display, or light obfuscation.

The section highlights several common formats supported by the proxy tools:

HTML encoding  
Unicode encoding  
Base64  
ASCII hex

A tester needs to recognize these patterns quickly because encoded values often hide useful application state, user roles, session data, or serialized objects.

## Decoding the Cookie Value

The example demonstrates a cookie value that is Base64-encoded. Once decoded, it reveals structured JSON containing a username field and an administrative flag.

That is important because it shows that the cookie is not random or encrypted. It contains meaningful application state that may be user-controlled. From an attacker’s perspective, that immediately suggests tampering potential.

The real lesson is not just that the value can be decoded, but that decoding tells you whether the application is trusting client-side state in a dangerous way.

## Why This Matters in Testing

When decoded data exposes role or privilege information, it becomes a natural target for authorization testing. If the application accepts the modified encoded value without verifying it server-side, privilege escalation may be possible.

This is a common testing pattern:

observe encoded value  
decode it  
understand the structure  
modify the meaningful fields  
re-encode it  
send it back and observe the result

That workflow is one of the most common uses of proxy-side encoders and decoders.

## Encoding the Modified Value

After changing the decoded JSON, the value must be returned to its original transport format before reuse. That is why re-encoding matters just as much as decoding.

If the application expects the cookie in Base64 form, sending raw JSON would likely fail. The tester must preserve the application’s expected format while changing only the embedded meaning.

This is a core penetration-testing habit: preserve protocol correctness while altering security-relevant content.

## Burp Decoder, Burp Inspector, and ZAP Encoder/Decoder/Hash

Burp provides dedicated decoding views as well as contextual helpers through Inspector. ZAP offers a multi-function encoding and hashing interface and supports customizable tabs for repeated workflows.

These features matter because testers often need to repeatedly transform values while iterating on payloads. Having integrated tooling speeds up analysis and reduces mistakes.

## Common Pitfalls

A common mistake is assuming encoded means secure. Many applications use encoding only for transport or readability, not for protection.

Another is forgetting to restore the original format after editing the decoded content. Even a correct logical change will fail if the server expects a different encoding layer.

A third is confusing encoding with encryption. Encoded data is often reversible immediately and should be treated as readable unless proven otherwise.

## Key Takeaway

Encoding and decoding are not side tasks in web testing. They are part of the main workflow for understanding application behavior, modifying requests safely, and testing whether client-controlled data can be tampered with to change privileges or application logic.

# Proxying Tools 

## Proxying Tools

This section explains how to route the web traffic of command-line tools and other non-browser applications through a web proxy. The goal is the same as with browser proxying: visibility and control over HTTP requests and responses.

Once a tool is configured to use the proxy, its traffic can be inspected, intercepted, modified, replayed, and analyzed inside Burp or ZAP.

## Why Proxy Non-Browser Tools

Many assessments involve tools that generate HTTP traffic outside the browser, such as:

command-line HTTP clients  
security scanners  
exploit frameworks  
custom scripts  
thick client applications

If their traffic is not proxied, the tester loses visibility into exactly what they send and how the target responds.

Proxying these tools allows the same workflow used for browser testing to be applied to automation and external applications.

## General Proxy Configuration

The general principle is simple: configure the application to use the local proxy listener, typically the loopback address and the proxy port shown in Output A.

Once that is done, the application sends its web traffic through Burp or ZAP instead of directly to the target.

This enables inspection of raw requests, including hidden headers, default parameters, request paths, and responses that may not be obvious from the tool’s normal output.

## Proxychains

Proxychains is useful because it can wrap command-line tools and force their traffic through a configured proxy without requiring each individual tool to support proxy options directly.

That makes it especially convenient for Linux-based workflows.

The configuration shown in Output A changes the proxychains configuration to use an HTTP proxy on the local machine instead of the default SOCKS entry. Once that is done, commands launched through proxychains send their traffic through the web proxy.

## Why Quiet Mode Matters

The quiet option suppresses proxychains connection messages. This does not change the proxied behavior, but it reduces terminal clutter.

That is helpful because it makes the real application output easier to read while still allowing the tester to inspect the actual HTTP request inside the proxy tool.

## curl Through the Proxy

The curl example demonstrates a very common use case: sending a simple HTTP request through the proxy and confirming that it appears in proxy history.

This is valuable because curl is often used for:

quick endpoint testing  
manual API interaction  
header testing  
session handling  
proof-of-concept requests

Once proxied, curl becomes easier to debug because the tester can see the exact request structure rather than relying only on terminal output.

## Metasploit Through the Proxy

The Metasploit example shows how to proxy traffic from a framework module by setting its proxy option directly.

This is particularly useful when using HTTP-based modules because it reveals:

which endpoint the module requests  
what headers it sends  
how it formats payloads  
how the target responds  
whether the module is behaving as expected

That makes the proxy valuable not just for attacking, but for debugging and understanding framework behavior.

## Why This Helps During Testing

When a module or script is not working as expected, the proxy can show whether the issue is:

wrong target path  
wrong method  
bad headers  
unexpected redirects  
authentication problems  
incorrect parameter formatting

Without the proxy, the tester may only see vague failure messages from the tool. With the proxy, the exact HTTP conversation is visible.

## Applicability Beyond the Examples

The important lesson is broader than proxychains, curl, or Metasploit. Any application that supports proxies, or can be forced through one, can be inspected this way.

That includes:

custom Python scripts  
Java applications  
desktop clients  
API testing utilities  
other web scanners

The proxy becomes a universal observation point for application-layer traffic.

## Performance Consideration

The section notes that proxying tools slows them down. That matters because the proxy adds an extra hop and may pause traffic for interception or logging.

For that reason, tools are usually proxied when investigation or debugging is needed, not necessarily during normal high-speed usage.

## Key Takeaway

Proxying tools extends web proxy visibility beyond the browser. By routing command-line tools, scanners, frameworks, and other applications through Burp or ZAP, the tester gains full insight into the HTTP traffic they generate and can analyze, debug, and manipulate that traffic using the same techniques applied during browser-based web testing.

# Burp Intruder 

## Burp Intruder

This section introduces Burp Intruder as Burp’s built-in web fuzzer and brute-forcing component. Its main purpose is to automate repeated request variations against a target so the tester can discover valid content, parameters, credentials, or other interesting behavior.

It fills the same general role as common CLI fuzzers, but works directly inside Burp’s request workflow.

## Why Intruder Matters

Manual request editing is useful for isolated testing, but it becomes too slow when the tester needs to try many candidate values. Intruder solves that by taking a known-good request template and automatically inserting payload values into selected positions.

That makes it suitable for tasks such as:

directory and file enumeration  
parameter fuzzing  
value brute-forcing  
credential testing  
content discovery

The key advantage is that the tester works from a real captured request, so the surrounding headers and structure are already valid.

## Sending the Request to Intruder

The workflow starts by identifying a useful request in proxy history and sending it to Intruder. This turns an observed request into a reusable template for fuzzing.

The target details are automatically populated from that source request, reducing setup effort and helping keep the attack aligned with the actual application behavior.

## Positions

The Positions area defines where payload values will be inserted. This is the core of the fuzzing setup.

In the example, a placeholder is inserted into the request path so Intruder can test different directory names. Each candidate from the wordlist replaces that placeholder one by one.

The reason this works is simple: if a tested directory exists, the server should respond differently than it does for non-existent paths. That difference becomes the signal the tester is looking for.

## Attack Type

The source uses the Sniper attack type because only one insertion point is being fuzzed.

Sniper is best when a single position changes at a time. More complex attack types like Cluster Bomb become useful when multiple positions must be combined across several payload sets.

The broader lesson is that attack type determines how Intruder iterates through input combinations.

## Payload Types

Payload type defines how candidate values are generated or loaded.

The basic choice here is Simple List, which works well when the tester already has a wordlist and wants Intruder to try each line directly. Other payload types can generate more dynamic variations, but the simple list is the foundational mode for common content discovery tasks.

This is important because fuzzing quality depends not only on the insertion point, but also on how intelligently the payload source is chosen.

## Wordlist Loading

The selected wordlist contains common web content names. By loading it into Intruder, the tester turns a single request into a directory-enumeration attack.

This is conceptually identical to content discovery with tools like ffuf or gobuster: send requests for many potential paths and watch for responses that differ from the normal “not found” pattern.

The request template stays constant while only the selected directory token changes.

## Payload Processing

Payload processing adds rules that transform or filter the payload list before sending requests.

In this example, entries beginning with a dot are skipped. That is useful because those values are not relevant to the intended discovery task and would waste requests.

This shows an important idea: a good fuzzing attack is not just about loading a big list, but about pruning noise so results become easier to interpret.

## Payload Encoding

Payload encoding controls whether payload characters are URL-encoded before being inserted into the request.

Leaving this enabled helps preserve request correctness when payloads contain characters that may otherwise be misinterpreted by the server or request parser.

That matters because malformed fuzzing requests can create false negatives.

## Settings and Result Filtering

The Settings tab helps define how results should be interpreted.

The important configuration in this example is Grep - Match. Instead of manually reading every response, Intruder flags responses that contain a target pattern. Here, the pattern chosen corresponds to successful responses.

This makes it easier to sort and identify the interesting results from a long attack run.

Disabling exclusion of HTTP headers matters because the success signal appears in the response header, not necessarily in the body content.

## Interpreting Results

Once the attack runs, most tested values return the expected “not found” behavior. The interesting result is the one that differs and matches the configured success indicator.

That differing result suggests a real directory exists and should be investigated manually.

This is the fundamental fuzzing pattern:

send many controlled variations  
compare responses  
find outliers  
investigate the outliers

## Performance Consideration

The section emphasizes a practical limitation: the free Burp version throttles Intruder heavily. That makes it useful for small, targeted attacks but inefficient for large-scale fuzzing.

This is why many testers still prefer CLI fuzzers for speed, while using Intruder for highly contextual or request-specific attacks where Burp’s integration is more valuable than raw throughput.

## Broader Use Cases

Although the example focuses on directory enumeration, the same methodology applies to many tasks:

testing parameter names  
guessing parameter values  
credential brute-forcing  
password spraying in web authentication flows  
discovering hidden functionality

The core mechanism stays the same: capture a legitimate request, choose insertion points, load payloads, and compare responses.

## Key Takeaway

Burp Intruder turns a captured request into a controlled fuzzing engine. By defining payload positions, choosing an appropriate payload source, optionally processing the wordlist, and filtering responses intelligently, the tester can quickly discover meaningful differences in application behavior and identify hidden or vulnerable functionality.

# ZAP Fuzzer

## ZAP Fuzzer

This section introduces ZAP’s built-in fuzzing tool. It serves a similar purpose to Burp Intruder by automating requests that contain varying payload values. The goal is to discover hidden resources, parameters, or application behaviors by sending many controlled request variations.

One major difference from Burp Intruder is that ZAP’s fuzzer is not throttled in the free version, making it significantly faster for larger fuzzing tasks.

## Starting a Fuzzing Attack

The first step is capturing a request that will act as the template for the fuzzing attack. This request is obtained from proxy history after interacting with the target application.

Once the request is identified, the fuzzing interface is opened from the attack menu. This converts the captured request into a fuzzing template where specific parts of the request can be replaced with payload values.

## Fuzz Location

The fuzz location defines where the payload will be inserted inside the request. This is conceptually the same as the payload position feature in Burp Intruder.

In the example scenario, the directory name within the request path is selected as the fuzz location. Each payload value from the wordlist replaces that directory name in the request.

The application’s response then reveals whether the tested directory exists or not.

## Payload Sources

Payloads represent the candidate values used during fuzzing. ZAP provides multiple payload types that generate or load payload data.

One advantage of ZAP is the availability of built-in wordlists through the file fuzzers feature. This allows testers to use curated lists without manually downloading them.

The example uses a common directory enumeration wordlist originally associated with dirbuster. Each entry from this list becomes a directory candidate that ZAP tests against the target.

## Payload Processors

Payload processors modify payload values before they are inserted into the request. These transformations help ensure compatibility with application input formats or apply additional fuzzing logic.

Several processor types exist, including hashing functions, encoding options, and prefix or postfix modifications.

For the example scenario, URL encoding is applied to ensure that payload values containing special characters are properly formatted before being sent in HTTP requests.

## Attack Options

The options configuration controls how the fuzzing process runs. One of the most important settings is the number of concurrent scanning threads.

Increasing thread count allows the tool to send multiple requests simultaneously, dramatically improving fuzzing speed. However, the number of threads should remain within reasonable limits to avoid overwhelming the target server or the testing machine.

Another configuration option determines how payload combinations are tested when multiple insertion points exist. Depth-first testing completes all payload variations for one position before moving to the next, while breadth-first testing cycles through payloads across positions.

## Running the Fuzzer

Once configuration is complete, the attack begins. The fuzzer sends requests containing each payload variation and records the responses.

The results interface displays key attributes of each response such as HTTP status code, response size, and round-trip time. These metrics help identify requests that behave differently from the baseline.

Sorting by status code is often useful because successful resources commonly return different codes than non-existent paths.

## Identifying Valid Results

When performing directory discovery, the most common indicator of success is a response code indicating that the resource exists. However, other indicators may also be useful.

Response size differences may reveal unique pages even when status codes remain identical. Similarly, timing differences can indicate conditions such as time-based injection vulnerabilities.

Therefore, fuzzing analysis involves comparing responses and identifying patterns that deviate from normal behavior.

## Comparing ZAP Fuzzer and Burp Intruder

ZAP Fuzzer and Burp Intruder share the same conceptual workflow:

capture request  
define insertion point  
load payload list  
run automated requests  
analyze response differences

However, they differ in several practical ways. Burp Intruder provides more advanced payload generation features, while ZAP focuses on speed and simplicity. ZAP also includes built-in wordlists that reduce preparation time.

Choosing between them often depends on the testing scenario and personal preference.

## Key Takeaway

ZAP Fuzzer automates large numbers of request variations by inserting payload values into selected parts of a captured request. By analyzing differences in server responses, testers can discover hidden directories, endpoints, or behaviors within a web application. Its lack of throttling makes it especially effective for high-speed fuzzing tasks in web penetration testing.

# Burp Scanner 

## Burp Scanner

This section introduces Burp Scanner, one of the most powerful features in Burp Suite. It is designed to automatically detect security vulnerabilities within web applications. Unlike manual testing features such as the proxy or repeater, the scanner performs large-scale automated analysis using crawling and vulnerability testing techniques.

Burp Scanner is only available in the Professional and Enterprise versions of Burp Suite.

## Scanner Components

Burp Scanner consists of two primary components that work together during automated assessments.

The crawler maps the application by discovering links, forms, parameters, and resources. This process builds a complete site structure so the scanner understands how the application is organized.

The vulnerability scanner then analyzes discovered endpoints and tests them for security issues.

Together these components allow Burp to automatically identify many common web vulnerabilities.

## Defining Target Scope

Before running automated scans, the tester should define a target scope. Scope determines which URLs and hosts Burp should analyze and which should be ignored.

Defining scope is important because:

It prevents unnecessary scanning of unrelated domains.  
It avoids scanning sensitive endpoints such as logout functions.  
It reduces noise and improves performance.

Items can be added to scope directly from the site map when Burp observes traffic through the proxy.

Scope settings can also include exclusions to prevent specific paths or endpoints from being scanned.

## Site Map

The site map displays the application structure discovered through proxy traffic and crawling.

Each directory, endpoint, and file observed by Burp appears within this hierarchical map. This helps testers understand the layout of the application and choose which areas should be included in scanning.

The site map also becomes the main navigation point for selecting targets for scanning operations.

## Crawling

Crawling is the first automated phase in many scans. During this phase Burp navigates the application similarly to a web browser.

The crawler:

follows links found in pages  
submits forms  
observes requests triggered by the application  
builds a map of accessible content

This phase does not actively attack the application. Its purpose is to gather information and expand the known attack surface.

One important limitation is that crawlers only follow existing references within the application. They do not guess hidden resources. Separate tools such as fuzzers are needed to discover unlinked endpoints.

## Passive Scanning

Passive scanning analyzes responses without sending additional requests.

The scanner inspects the HTML, JavaScript, headers, and other response data from previously captured requests. It looks for patterns indicating potential security issues.

Examples include:

missing security headers  
client-side vulnerabilities  
insecure cookie settings  
potential DOM-based injection risks

Passive scanning is safe because it does not actively attempt exploitation. However, its results are only suggestions that must be verified manually.

## Active Scanning

Active scanning is the most powerful feature of Burp Scanner.

During an active scan, Burp sends specially crafted requests designed to trigger vulnerabilities. It actively tests parameters, input fields, and request components to confirm whether vulnerabilities exist.

Active scanning includes several automated processes:

crawling the application  
performing passive analysis  
injecting payloads into parameters  
testing insertion points for vulnerabilities  
analyzing JavaScript for additional issues

The scanner attempts to identify vulnerabilities such as:

command injection  
SQL injection  
cross-site scripting  
server-side vulnerabilities

Because active scans send many requests and may attempt exploitation techniques, they should only be run on systems where permission has been granted.

## Scan Configuration

Burp allows extensive customization of scanning behavior.

Scan configurations control:

which vulnerabilities are tested  
which insertion points are targeted  
how aggressively requests are sent  
how authentication is handled

Predefined configuration profiles simplify common tasks. For example, a configuration may limit testing to critical vulnerabilities to reduce scan time.

These configuration profiles allow testers to adapt scans to the requirements of the engagement.

## Authentication Handling

Many web applications require authentication to access internal functionality.

Burp Scanner can perform scans using authenticated sessions by either providing login credentials or recording a login sequence. This allows the scanner to reach protected sections of the application that would otherwise be inaccessible.

Authenticated scanning significantly increases test coverage.

## Monitoring Scan Progress

During scanning, Burp provides detailed progress information through the dashboard.

Testers can monitor:

requests sent during scanning  
discovered endpoints  
scan duration and progress  
any errors encountered

The logger also records all HTTP traffic generated by the scanner. This provides full visibility into how the scan operates.

## Reviewing Scan Results

When the scan completes, Burp lists discovered vulnerabilities within the issue activity interface.

Each issue includes:

severity rating  
confidence level  
affected location  
technical explanation  
evidence from the request and response

Severity indicates the potential impact of the vulnerability. Confidence indicates how certain Burp is that the vulnerability truly exists.

Reviewing both values helps testers prioritize remediation efforts.

## Reporting

Burp Scanner can generate structured vulnerability reports after scanning is completed.

These reports summarize findings and provide technical details, including proof-of-concept information and remediation recommendations.

However, automated scan reports should never be submitted directly to clients without review. Human analysis is required to validate findings, remove false positives, and provide proper context.

Automated reports should instead be used as supporting documentation within a full penetration testing report.

## Key Takeaway

Burp Scanner combines automated crawling and vulnerability testing to identify common web security issues. By defining scope, mapping the application, performing passive analysis, and executing active vulnerability tests, it provides powerful automation that complements manual penetration testing techniques.

# ZAP Scanner 

## ZAP Scanner

This section explains ZAP’s automated scanning features. Like Burp Scanner, ZAP combines site mapping and vulnerability discovery into a workflow that starts with exploration and ends with issue reporting.

The scanner is made up of three major parts: the spider, the passive scanner, and the active scanner.

## Spider

The spider is ZAP’s crawler. Its purpose is to build a map of the application by following links and discovering reachable resources.

This matters because automated scanning needs a target structure before it can test effectively. The spider collects pages, directories, and files so that later scan stages know where to focus.

The scope is important here because it controls what ZAP is allowed to test. If a site is not in scope, ZAP may prompt to add it. That allows the tester to define boundaries and avoid scanning unintended targets.

## Ajax Spider

The Ajax Spider extends the normal spider by handling content discovered through client-side JavaScript activity. This is useful for modern applications where links or resources may only appear after scripts execute in the browser.

The key difference is that a normal spider follows visible links in responses, while the Ajax Spider can expose content loaded dynamically after page render. That makes it valuable for single-page apps and heavily scripted interfaces.

## Passive Scanner

As ZAP spiders the site, it automatically performs passive analysis on responses. This means it looks for weaknesses without sending attack payloads.

Passive findings often include:

missing security headers  
clickjacking-related header issues  
client-side weaknesses  
potential DOM-based issues

This phase is useful because it produces early findings while remaining low impact. It helps testers prioritize what to investigate further before launching more aggressive checks.

## Active Scanner

The active scanner is where ZAP begins sending attack-oriented requests to verify whether vulnerabilities actually exist.

This is more intrusive than passive scanning because it actively tests parameters and endpoints with crafted inputs. It takes longer because it is doing more than observation; it is probing behavior.

The active scan can uncover serious vulnerabilities such as command injection and other server-side issues. That is why it is one of the most valuable parts of the workflow, but also one that must only be used on authorized targets.

## Alerts and Severity

ZAP organizes findings into alerts with severity levels such as High, Medium, Low, and Informational. This helps testers triage results.

High alerts are usually the most urgent because they often indicate vulnerabilities that can directly compromise the application or its server.

Confidence also matters. A high-severity issue with lower confidence may still require manual validation, while stronger evidence makes prioritization easier.

## Command Injection Example

The section shows an example where ZAP identified a remote OS command injection issue. The important lesson is not just the specific payload, but how ZAP presents the evidence:

the suspected attack input  
the evidence returned by the server  
the associated request and response

That combination helps the tester understand why the tool believes the issue exists and how to verify it manually.

This is a major advantage of scanner tooling: it does not just flag a category, it often shows the exact traffic and evidence chain behind the finding.

## Reviewing Request and Response Evidence

ZAP allows the tester to inspect the request and response used to identify a finding. This is essential because scanner results should never be trusted blindly.

Manual review helps answer:

did the scanner really hit the correct parameter  
did the server response truly confirm exploitation  
is the issue reproducible  
is it exploitable in practice or just indicative

That review step is what turns a scanner alert into a defensible penetration-testing finding.

## Reporting

ZAP can export findings into several report formats. These reports are useful for logging, internal tracking, and supporting a larger assessment.

However, like all automated scanner output, they should be treated as supporting material rather than a final deliverable. The value comes from combining automated findings with analyst validation, prioritization, and remediation context.

## Key Takeaway

ZAP Scanner provides an end-to-end automated workflow: map the site with the spider, identify likely weaknesses passively, then actively test for real vulnerabilities. Its real strength is not just finding issues, but tying those issues to concrete evidence that the tester can inspect, validate, and report.

# Extensions

## Extensions

Web proxies like Burp Suite and ZAP support extensibility through community-developed add-ons. These extensions expand the functionality of the base tool by adding new testing capabilities, automation features, payload libraries, or analysis utilities.

Extensions allow security researchers to rapidly integrate new attack techniques or detection logic into the proxy environment without waiting for official tool updates.

---

## Burp Extensions and the BApp Store

Burp Suite provides an extension ecosystem called the **BApp Store**. This repository contains extensions created by the community and the PortSwigger research team.

Extensions can add functionality such as:

- new vulnerability scanners
    
- custom payload generators
    
- automated attack modules
    
- improved decoders and analyzers
    
- request/response manipulation tools
    

Some extensions are available for both Community and Professional editions, while others require Burp Professional due to dependency on Pro-only scanning APIs.

---

## Installing Burp Extensions

Extensions can be installed directly from the BApp Store within the Burp interface. Once installed, most extensions add a new tab or integrate into existing Burp tools such as:

- Proxy
    
- Repeater
    
- Intruder
    
- Scanner
    
- Decoder
    

Some extensions require additional runtime environments such as **Jython** for Python-based plugins.

After installation, the extension functionality becomes available within Burp's interface.

---

## Example Extension: Decoder Improved

The **Decoder Improved** extension enhances Burp's built-in Decoder tool.

It provides additional features including:

- multiple encoding formats
    
- hashing algorithms
    
- Unicode support
    
- hex editing capabilities
    

This allows testers to perform more complex encoding and decoding operations without leaving Burp.

For example, input text can be hashed using algorithms like MD5 directly inside the extension interface.

---

## Useful Burp Extensions

Burp has a large ecosystem of extensions that support many testing scenarios. Some focus on vulnerability discovery, while others assist with automation, payload generation, or analysis.

Examples include extensions for:

- scanning for common vulnerabilities
    
- detecting insecure configurations
    
- identifying JavaScript security issues
    
- testing cloud storage security
    
- detecting deserialization vulnerabilities
    

These extensions significantly expand Burp’s capabilities beyond its default toolset.

---

## ZAP Marketplace

ZAP also supports extensibility through the **ZAP Marketplace**, which allows users to install community-developed add-ons.

The marketplace contains plugins that extend ZAP with additional:

- scanners
    
- fuzzing payloads
    
- automation scripts
    
- testing modules
    

Add-ons in the marketplace are categorized by stability levels such as:

- Release (stable)
    
- Beta (testing stage)
    
- Alpha (experimental)
    

This helps users decide whether an add-on is safe for production use.

---

## FuzzDB Add-ons

One useful ZAP add-on is **FuzzDB**, which provides extensive payload libraries for fuzzing and exploitation.

FuzzDB includes payload lists for many attack types such as:

- command injection
    
- SQL injection
    
- path traversal
    
- XSS testing
    

Once installed, these payload lists become available within the ZAP Fuzzer interface, allowing testers to quickly select specialized attack payloads.

---

## Example: Command Injection Wordlists

With the FuzzDB extensions installed, ZAP can access payloads designed specifically for command injection attacks.

These payloads contain different variations of command execution patterns that help bypass filters or web application firewalls.

Using these payloads during fuzzing increases the chance of discovering vulnerabilities that might otherwise be missed with generic wordlists.

---

## Benefits of Extensions

Extensions provide several major benefits during web application testing:

They expand the testing capabilities of the proxy.  
They automate complex attack techniques.  
They integrate community research directly into testing tools.  
They reduce the need for external utilities.

This allows testers to perform a wider range of security checks directly inside Burp or ZAP.

---

## Key Takeaway

Extensions significantly enhance the power of web proxy tools. By integrating community-developed plugins, testers can extend Burp and ZAP with new scanners, payload libraries, automation tools, and analysis capabilities, making them far more effective for web application penetration testing.
#
#