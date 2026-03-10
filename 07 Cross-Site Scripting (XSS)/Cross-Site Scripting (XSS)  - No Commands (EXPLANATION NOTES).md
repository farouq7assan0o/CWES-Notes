# Intro to XSS 

**Introduction**  
Cross-Site Scripting (XSS) is one of the most common web application vulnerabilities and stems from improper handling of user-controlled input. Although XSS does not directly compromise the backend server, its prevalence and ability to impact users at scale make it a consistently medium-risk issue that must be addressed proactively.

**What is XSS**  
XSS occurs when an application inserts unsanitized user input into HTML or JavaScript that is rendered by the browser. The injected code executes in the victim’s browser context, inheriting their session, permissions, and trust relationship with the site. This makes XSS a client-side vulnerability with server-side root causes (poor input handling and output encoding).

**XSS Attacks**  
XSS enables attackers to execute arbitrary JavaScript in a victim’s browser. Typical abuse includes session hijacking, credential theft, forced actions via authenticated API calls, DOM manipulation, phishing overlays, and browser-based malware delivery. While modern browsers sandbox JavaScript, XSS remains dangerous due to same-origin trust and its ability to chain into more severe exploits if browser vulnerabilities exist.

**Types of XSS**  
Stored XSS is the most severe form because the payload is persisted and automatically delivered to multiple users. Reflected XSS relies on immediate reflection of input in server responses and typically requires user interaction (e.g., clicking a crafted link). DOM-based XSS occurs entirely in client-side JavaScript, where user input is processed and rendered without server involvement, making it harder to detect with traditional server-side testing.

Understanding these distinctions is critical because each XSS type requires different detection techniques, exploitation methods, and prevention strategies.#
# Stored XSS

**Stored XSS**  
Stored (persistent) XSS happens when your injected input is saved server-side (commonly in a database) and later rendered back into a page without proper output encoding/sanitization. Because it re-triggers on refresh and affects every visitor, it’s typically the highest-impact XSS type.

**XSS Testing Payloads**  
A quick “did my JS execute?” test uses a visible side-effect like `alert(window.origin)` so you can confirm the execution context (useful if the input is handled inside an iframe). If the payload appears in “View Page Source” and triggers again after refresh, that confirms persistence.

Browsers or contexts sometimes block `alert()`, so alternate “obvious effect” payloads include:

- `<plaintext>` to break rendering and expose raw HTML after it (very noticeable signal).
    
- `print()` to open the print dialog (often not blocked).
    

For the lab’s cookie-based flag prompt, you use the same script tag pattern but swap the JavaScript expression to show cookies (i.e., `document.cookie`) instead of origin.
# Stored XSS 

**XSS Testing Payloads**  
Stored XSS occurs when attacker-controlled input is saved server-side and later rendered to users without proper output encoding.  
`alert(window.origin)` confirms JavaScript execution and reveals the execution origin, which is useful when inputs are processed via iframes or subdomains.  
`<plaintext>` halts HTML parsing, making injection visually obvious.  
`print()` provides an alternate execution confirmation when alerts are restricted.

**View Page Source**  
Viewing the page source verifies that the payload is embedded in the returned HTML, confirming lack of sanitization rather than client-side rendering tricks.

**Injected HTML Evidence**  
The payload appears directly inside the DOM output, proving persistent storage and execution on page load.

**Lab Target**  
This instance is shared across users; persistent payloads will execute for any visitor until removed.

**Flag Payload**  
Replacing `window.origin` with `document.cookie` extracts cookies accessible to JavaScript for the current origin, demonstrating the real impact of Stored XSS (session theft).
# Reflected XSS

**Hack The Box Academy**  
This section is about non-persistent XSS, specifically Reflected XSS, where the payload is processed by the server and reflected back in the response without being properly sanitized.

**XSS Testing Payloads**  
Reflected XSS happens when input is immediately included in a response (often error/confirmation text). It executes only in that request/response cycle and disappears on refresh/navigation, unlike Stored XSS.

**View Page Source**  
Source inspection confirms the payload is actually included in returned HTML (server reflection) rather than only being displayed safely.

**Developer Tools**  
The Network tab shows the request method used to submit input. If it’s a GET, the payload is placed in the URL query string.

**Network Copy URL**  
Because the payload is in the URL (GET parameters), targeting a victim is done by sending them the crafted URL; when they load it, the server reflects it and the browser executes it.

**Injected HTML Evidence**  
The payload appears inside the error message response HTML, which is why it executes. The displayed message can show empty quotes because `<script>` content isn’t rendered as text.

**Flag Payload**  
Swapping `window.origin` for `document.cookie` makes the alert display the cookie value accessible to JavaScript for that origin.

# DOM XSS

**Hack The Box Academy**  
This section covers DOM-based XSS, a non-persistent XSS type fully handled in the browser without server interaction.

**Developer Tools**  
The Network tab shows no HTTP requests when input is submitted, indicating client-side processing. The Inspector shows the rendered DOM after JavaScript execution.

**View Page Source**  
The base HTML does not contain user input because the DOM is modified after page load by JavaScript.

**DOM Source**  
The source is where attacker-controlled input originates. Here, it is taken directly from `document.URL` via the `task=` fragment parameter.

**DOM Sink**  
The sink is where the input is written into the DOM. Using `innerHTML` without sanitization allows attacker-controlled HTML/JavaScript execution.

**DOM XSS Payload**  
`<script>` tags are blocked by `innerHTML`, so event-handler–based payloads (e.g., `onerror`) are used to achieve execution.

**Flag Payload**  
Replacing `window.origin` with `document.cookie` causes the payload to display cookies accessible to JavaScript, demonstrating session compromise via DOM-based XSS.
# XSS Discovery 

**Hack The Box Academy**  
This section is about finding XSS, not exploiting it: understanding _where_ inputs enter, _how_ they get transformed, and _where_ they end up in the browser.

**Automated Discovery Tools**  
Scanners typically do:  
Passive scanning to spot risky client-side patterns (especially DOM sources/sinks).  
Active scanning to inject payloads and look for reflection/execution indicators.  
Paid tools often catch more real-world cases because they handle edge cases like filtering/WAF behavior and context-specific injection.

**XSStrike**  
XSStrike is an open-source XSS finder that focuses on parameter testing and payload generation matched to reflection context. It identifies candidate parameters, finds reflections, and proposes payloads ranked by likelihood. Tool output can be “suggestive,” so manual verification is still required because reflection does not always mean execution.

**Other XSS Discovery Tools**  
BruteXSS and XSSer are other automation options to help enumerate inputs and try payloads, useful when you have many fields/parameters to triage quickly.

**Payload Lists**  
Large payload lists cover many injection contexts (inside attributes, inside JS strings, after quotes, etc.) and many evasion variants. Most won’t work on any given target because they’re not tailored to the exact context your input lands in.

**Detected Payload Example**  
This is an example of a non-`<script>` vector using an HTML event handler. Tools often suggest payloads like this when they detect HTML injection contexts where event handlers can fire.

**HTTP Headers Mentioned**  
XSS isn’t only form fields and URL params. If the app reflects request metadata (like User-Agent or Cookie) into HTML without encoding, those can become injection points too.

**Courses Mentioned**  
These are deeper follow-ons for code review and whitebox approaches, where you trace input handling end-to-end to craft a context-perfect payload—often the most reliable way to find XSS in mature apps.
# Defacing 

**Hack The Box Academy**  
This section demonstrates practical exploitation of Stored XSS by visually altering a web page for all visitors.

**Defacement Elements**  
These JavaScript-accessible properties control the page’s appearance and content. Manipulating them via stored XSS allows persistent visual changes.

**Background Color Payload**  
Directly modifies CSS styling of the page body, commonly used to create a dark or attention-grabbing defaced look.

**Background Image Payload**  
Replaces the page background with an external image, often used for branding, messages, or signatures by attackers.

**Page Title Payload**  
Changes the browser tab title, reinforcing the defacement even when users are not actively viewing the page content.

**DOM Text Modification**  
Using `innerHTML` replaces visible content in specific elements or the entire page. Targeting the `body` element fully overrides what users see.

**Defacement HTML**  
Attackers typically inject simple, high-contrast HTML with a short message and logo rather than complex layouts.

**Final Defacement Payload**  
The HTML is minified into a single line and injected via XSS, making the change persistent and visible to all visitors while the original source code remains underneath.
# XSS Phishing 

**Target Paths**  
`/phishing` reflects user input into HTML, enabling reflected XSS.  
`send.php` delivers the malicious URL to a victim.  
`login.php` validates stolen credentials for flag access.

**XSS Payload**  
Initial payloads test JavaScript execution. Source inspection reveals which contexts allow script execution versus broken image rendering.

**Injected Login Form**  
HTML is injected to visually replace the application with a fake authentication prompt, exploiting user trust in the site.

**Injected JavaScript**  
`document.write()` fully overwrites page content, enabling UI takeover rather than simple script execution.

**DOM Cleanup**  
Removing the original form eliminates conflicting UI cues and increases phishing success.

**HTML Comment Truncation**  
Commenting out remaining markup prevents broken layouts and visual artifacts after injection.

**Credential Capture**  
GET-based form submission exposes credentials directly in the request URI, making interception trivial.

**Netcat vs PHP Server**  
Netcat confirms credential exfiltration.  
PHP enables silent logging and redirection, maintaining realism and avoiding suspicion.

**Attack Chain Summary**  
Reflected XSS → DOM overwrite → fake login → credential exfiltration → account takeover → flag retrieval.

# 


#
#
#
#
#
#
#
