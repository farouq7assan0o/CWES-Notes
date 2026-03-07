# Intro to File Inclusions — EXPLANATION NOTES

## PHP FILE INCLUSION FUNCTIONS

These functions load files into the current script. When user input is passed directly to them, the application may allow attackers to control which file is included.

Some functions both read and execute the included file, while others only read the content.

Execution-capable functions are particularly dangerous because they can lead to remote code execution if the attacker can influence which file is loaded.

This category includes functions responsible for loading templates, pages, configuration files, or other dynamic content.

---

## NODEJS FILE ACCESS FUNCTIONS

NodeJS applications frequently read files dynamically using filesystem APIs.

When a request parameter is passed directly into file reading functions, the attacker can manipulate the path and retrieve unintended files from the server.

Functions that render templates may also execute embedded logic, which increases the impact if an attacker can control the path being rendered.

---

## JAVA FILE INCLUSION FUNCTIONS

Java web applications using JSP often dynamically load page fragments or templates.

The include and import mechanisms allow the server to insert external resources into the page output.

If the path is controlled by user input and not sanitized, attackers can manipulate which files are loaded.

Some of these mechanisms also support loading remote URLs, which can extend the vulnerability from local file inclusion to remote file inclusion.

---

## .NET FILE INCLUSION FUNCTIONS

.NET frameworks provide multiple methods for rendering templates and loading files into responses.

Functions that render partial views or write file contents into the response can become vulnerable if the file path comes directly from user-controlled parameters.

Certain include directives may also allow remote file execution depending on server configuration.

---

## PHP VULNERABLE CODE SNIPPET

This code checks if the `language` parameter exists in the request and then directly includes it.

Since there is no validation or sanitization, the application blindly trusts the input.

An attacker can manipulate the parameter to load arbitrary files from the server.

This is the classic pattern that leads to Local File Inclusion vulnerabilities.

---

## NODEJS VULNERABLE CODE SNIPPET

The application retrieves the `language` query parameter and uses it as part of the file path passed to the file-reading function.

The path is constructed dynamically and then read and returned to the user.

If the input is not validated, attackers can manipulate the path to access sensitive files on the server.

---

## NODEJS EXPRESS RENDER SNIPPET

In this scenario, the application constructs the template path based on a URL parameter.

The framework then renders the chosen template.

If attackers control the path segment used in rendering, they may be able to traverse directories and render unintended files.

Because rendering engines may process embedded logic, this can increase the impact of exploitation.

---

## JAVA JSP INCLUDE SNIPPET

This snippet conditionally includes a file based on a request parameter.

Since the file path is taken directly from user input, attackers can manipulate it.

The result is that arbitrary files may be included into the rendered page.

---

## JAVA JSP IMPORT SNIPPET

This function imports content from a specified URL or path.

Because it supports remote URLs, exploitation may escalate from local file access to remote resource loading.

This expands the attack surface significantly.

---

## .NET RESPONSE WRITEFILE SNIPPET

The application retrieves a query parameter and uses it to determine which file should be written into the HTTP response.

If an attacker controls the value, they may retrieve arbitrary files stored on the server.

This leads to file disclosure.

---

## .NET HTML PARTIAL SNIPPET

Partial rendering inserts a file fragment into the current view.

When the path is controlled by user input, attackers may manipulate which view file is rendered.

While this often results in file disclosure, it can sometimes lead to code execution depending on the template engine.

---

## .NET INCLUDE DIRECTIVE

This directive includes another file into the page output.

Because the file path is derived from user input, it creates the same vulnerability pattern seen across other languages.

Some include mechanisms also execute server-side code inside the included file, increasing the severity of the vulnerability.

---

## EXAMPLE HTTP PARAMETERS

These examples illustrate how web applications commonly load dynamic content.

The parameter controls which page, language, or template is loaded.

Attackers focus on these parameters because manipulating them may allow arbitrary file access.

This is why parameters such as `page`, `file`, `template`, and `language` are common entry points for Local File Inclusion exploitation.

---

If you want, I can also show you the **very important mental model HTB expects for LFI enumeration (the 5-step attacker workflow used in real HTB boxes)** which makes the next sections much easier.
# LFI Attacker Workflow — EXPLANATION NOTES

## COMMON LFI PARAMETERS

Attackers first identify parameters that control file loading. These parameters usually determine which page, template, language, or file is rendered.

Common naming conventions include parameters such as page, file, view, template, include, or language. These parameters exist because developers use them to dynamically load content.

Attackers look for these parameters during initial reconnaissance because they often directly influence file paths inside server code.

---

## COMMON LFI ENDPOINT PATTERNS

Many applications follow predictable URL structures when loading pages dynamically.

A typical design pattern is a main controller file that loads different pages depending on the parameter value. For example, a central script may handle navigation and dynamically include other files.

This design greatly simplifies development but becomes dangerous if user input is not validated. If the parameter value directly determines the file path, attackers may manipulate it.

---

## COMMON PATH TRAVERSAL PAYLOADS

Path traversal sequences allow attackers to move up directories in the server's filesystem.

Each "../" moves one directory upward. By chaining multiple traversal sequences, attackers can escape the intended directory and access system-level files.

Attackers typically start with a few traversal levels and increase the depth until they reach the filesystem root.

---

## LINUX SENSITIVE FILE TARGETS

Once traversal works, attackers attempt to read well-known system files.

The most common target is the system user database file because it reliably exists on Linux systems and contains recognizable content. Seeing this file confirms that the vulnerability works.

Other targets provide system information such as hostname, kernel version, and environment variables. These help attackers understand the server environment and identify further exploitation paths.

---

## WINDOWS SENSITIVE FILE TARGETS

When the backend runs on Windows, attackers target different system files.

Configuration files and host resolution files often serve as confirmation targets because they are predictable and readable.

The goal is the same as in Linux environments: confirm that arbitrary files can be accessed.

---

## COMMON LFI TEST REQUESTS

These requests combine the vulnerable parameter with path traversal payloads and sensitive file targets.

If the application includes the file path directly without validation, the server reads the requested system file and returns its contents in the HTTP response.

This confirms the presence of a Local File Inclusion vulnerability.

Once confirmed, attackers move to the next stage: expanding file access, discovering application source code, and potentially escalating the vulnerability into remote code execution.
# Local File Inclusion (LFI) — EXPLANATION NOTES

**TARGET URLS**  
These examples show the exact progression from normal functionality to file inclusion testing. The key idea is that the application changes content based on a user-controlled `language` value, which makes it a natural candidate for inclusion testing.

The sequence also demonstrates the main attacker workflow: observe a dynamic parameter, replace its expected value with a local file path, then escalate to traversal when direct inclusion fails.

**PARAMETERS**  
The `language` parameter is the attack surface in this section. It appears benign because it is meant to switch content between translations, but in practice it may directly control a file path on the server.

That makes it a classic LFI entry point. In real targets, attackers look for similar parameters because developers often trust them as simple display controls.

**FILES AND PATHS**  
These are the important filesystem targets and reference paths shown in the section. Some are used as proof-of-read targets, while others explain how traversal works relative to the application directory.

The Linux account file is the primary confirmation target because it is readable, predictable, and easy to recognize. The Windows boot configuration file serves the same purpose on Windows hosts. The application paths illustrate why traversal works when the vulnerable code prepends a base directory before the supplied value.

The avatar path is included because it introduces second-order LFI, where user-controlled data stored elsewhere is later reused as a file path.

**PHP VULNERABLE CODE**  
This is the simplest LFI case. The application takes user input and passes it straight into the inclusion function with no validation.

Because no prefix or suffix is added, absolute paths can work immediately. This is the easiest form of file inclusion to detect and exploit.

**PHP DIRECTORY APPEND CODE**  
This version prepends a directory before the user-controlled value. That breaks direct absolute-path reads because the requested file is forced under the application’s language directory.

Traversal solves this by backing out of the forced directory until the attacker reaches the filesystem root or another desired location. This is one of the most common real-world LFI patterns.

**PHP PREFIX APPEND CODE**  
This variation adds a filename prefix before the attacker-controlled value. That interferes with straightforward traversal because the supplied input is no longer at the true beginning of the path.

The section’s key lesson is that attackers adapt by trying to turn the prefix into a directory component. Whether that works depends on how the final path resolves on the target system.

**PHP EXTENSION APPEND CODE**  
This version appends `.php` to the input. It is often intended as a safety control because it appears to limit inclusion to PHP files only.

In practice, it complicates reading arbitrary non-PHP files because the server will try to load a filename that does not exist. This is why later bypass techniques become important.

The exercise also highlights a crucial distinction: including a PHP file may execute it rather than reveal its source code.

**PATH TRAVERSAL PAYLOADS**  
These payloads are the core mechanism for escaping application-controlled directories. Each `../` moves one level upward, allowing the attacker to walk from the inclusion directory back toward the root.

The methodology is to increase traversal depth until the target file becomes reachable, then reduce it to the minimum working depth for cleaner exploitation and reporting.

The leading-slash variation is used specifically against prefixed filenames, where the attacker tries to force the prefix into the path structure instead of leaving it attached to the filename.

**SECOND-ORDER LFI PAYLOADS**  
This section introduces a more subtle exploitation model. Instead of sending the malicious path directly to the vulnerable function, the attacker first stores a crafted value in a place the application later trusts, such as a username.

A different feature then uses that stored value to build a file path, causing the inclusion indirectly. This matters because developers often validate direct request input more carefully than database-backed values.

**QUESTION TARGETS**  
These are the specific lab targets shown in the exercise. One question focuses on identifying a username from a system file, while the other targets a flag file in a known directory.

From a methodology perspective, they reinforce the two common LFI goals in early exploitation: confirm file read access using predictable system files, then pivot to sensitive application or lab-specific files once inclusion is proven.
# Basic Bypasses — EXPLANATION NOTES

**PHP FILTER SNIPPETS**  
These examples show two very common defensive patterns: naive substring removal and regex-based path allowlisting.

The first filter tries to break traversal by deleting `../`, but it only does that once per matching substring pattern and does not account for inputs that transform into traversal after replacement. The second filter restricts inputs to an approved base path, but it still trusts the rest of the supplied string, which leaves room for traversal after the approved prefix.

These are classic examples of brittle blacklist logic versus robust path normalization and safe file mapping.

**LFI TEST URLS**  
These requests demonstrate how the attacker progressively adapts once a normal traversal payload stops working.

The sequence moves from a plain traversal attempt, to a recursive-bypass variant, to URL encoding, and then to a payload that satisfies an approved-path regex before escaping that path. This reflects the real methodology in LFI work: do not assume a failed payload means the vulnerability is gone; instead, identify what transformation or validation is being applied and shape the input around it.

**RECURSIVE TRAVERSAL PAYLOADS**  
These payloads work against non-recursive filters because they are designed so that once part of the string is removed, what remains becomes a valid traversal sequence.

The core concept is transformation-after-filtering. The filter removes what it recognizes, but the remaining characters collapse into the dangerous pattern the application was trying to prevent. This is why simple search-and-replace defenses are unreliable.

Different variants exist because applications may treat slashes, backslashes, and dot segments differently depending on implementation details and platform behavior.

**URL-ENCODED PAYLOADS**  
Encoding is useful when the application blocks special characters before the vulnerable file operation occurs.

The idea is that the filter sees encoded bytes instead of literal dots and slashes, while a later decoding step reconstructs the intended traversal path before inclusion happens. Double encoding extends the same idea by pushing the transformation one step further when a single decoding pass happens later in the request handling chain.

This matters because filtering and decoding are often implemented in different layers, and inconsistencies between those layers create bypass opportunities.

**APPROVED PATH PAYLOADS**  
This payload is used when the application only allows paths beginning with a specific directory.

The attacker satisfies the allowlist first, then escapes it using traversal. This works because the validation checks only the start of the string, not the fully normalized final path after traversal is resolved.

This is a strong lesson in why validating raw input strings is weaker than validating the canonical filesystem path after resolution.

**PATH TRUNCATION PAYLOAD**  
This technique targets older PHP behavior where extremely long paths were truncated at a fixed length.

The strategy is to create a path that resolves to the desired file while pushing the automatically appended extension beyond the truncation boundary. When the suffix is dropped, the resulting effective path points to the attacker’s intended target instead of a non-existent filename with `.php` attached.

The repeated current-directory markers are used because they bloat the path length without changing the final resolved location.

**PATH TRUNCATION GENERATION COMMAND**  
This command automates the construction of an overlong payload for the truncation technique.

Its purpose is not exploitation by itself, but repeatable payload generation. In the methodology, automation matters because manual construction of very long bypass strings is error-prone and makes it hard to control exact length.

The key idea is precision: the attacker wants to exceed the path limit enough to remove the forced suffix, but not so much that the desired target itself is damaged.

**NULL BYTE PAYLOAD**  
This is another legacy PHP technique. Older versions treated the null byte as the end of the string, so anything appended after it was ignored during low-level path handling.

That made it possible to neutralize forced suffixes such as `.php`. The application would appear to build one filename, but the underlying system would stop processing at the null byte and open only the attacker’s chosen file.

This is obsolete on modern PHP, but it remains important historically because legacy environments still appear in labs, audits, and older real-world systems.

**FILES AND PATHS**  
These are the important targets and path forms referenced in the section.

Some entries are sensitive files used to confirm file read access, while others illustrate path normalization behavior. Multiple slashes, embedded current-directory markers, and trailing dot segments may all resolve to the same effective file, which is why they are useful in bypass construction.

The `./languages/` path is especially important because it represents an approved or application-controlled base path that the attacker first satisfies, then escapes.

**TOOLS**  
These tools are included because the section explicitly relies on encoding helpers for crafting bypasses.

They are not part of the vulnerability itself, but they support the attacker workflow by making precise transformations easy and repeatable. In practice, encoding tools are most useful when the bypass depends on exact byte representations rather than visually obvious traversal strings.
# PHP Filters — EXPLANATION NOTES

## FUZZING FOR PHP FILES

Before exploiting LFI for source disclosure, attackers first discover which PHP files exist in the application. Directory and file fuzzing reveals hidden or unlinked scripts such as configuration files, admin panels, or internal APIs.

Even files returning redirects or forbidden responses are valuable targets. With LFI, attackers are not limited by HTTP access controls because the vulnerable inclusion function reads the file locally.

## STANDARD LFI REQUEST

When a PHP file is included normally through an LFI vulnerability, the server executes it instead of displaying the source code. The resulting page shows the rendered output rather than the script itself.

This behavior prevents attackers from directly viewing sensitive information contained in the code, such as credentials or API keys.

## PHP FILTER PAYLOAD

PHP filters allow manipulation of streams before they are returned to the application. By applying the Base64 encoding filter to a file resource, the application reads the file and encodes its contents instead of executing it.

Because the output is encoded text, the server treats it as plain data rather than executable code.

## PHP FILTER LFI REQUEST

When the filter payload is supplied to the vulnerable parameter, the server processes the PHP wrapper instead of a standard file path. The file content is passed through the Base64 encoding filter before being returned.

This allows attackers to retrieve the encoded contents of PHP scripts that would otherwise execute normally.

## BASE64 DECODE COMMAND

After receiving the encoded output, attackers decode it locally to recover the original source code.

The decoded script can then be analyzed for sensitive information such as database credentials, secret keys, internal API endpoints, or additional file paths used by the application.

## PHP WRAPPER SCHEME

PHP provides special stream wrappers that allow access to different input and output mechanisms. These wrappers are intended for legitimate development purposes but can be abused during exploitation.

When used in LFI vulnerabilities, wrappers allow attackers to manipulate how files are read and processed.

## FILTER PARAMETERS

The filter wrapper uses parameters that specify which transformation should be applied and which resource should be read.

The resource parameter identifies the target file, while the read parameter defines the transformation applied to its contents.

## FILTER TYPE USED

The Base64 encoding filter is particularly useful for exploitation because it transforms executable code into plain text data.

This bypasses the normal execution behavior of PHP inclusion functions and exposes the underlying source code.

## FILES

Configuration files are high-value targets because they frequently contain database credentials, secret tokens, and other sensitive information required for application functionality.

Reading these files often reveals information that enables further attacks against the system.

## WORDLIST

Wordlists are used during fuzzing to discover hidden files and endpoints. The referenced list is a commonly used discovery dictionary for web applications.

Using such lists significantly increases the chance of finding configuration files or administrative scripts that are not linked in the user interface.

## TOOLS

The fuzzing tool automates file discovery by sending large numbers of requests using different filenames from the wordlist.

The decoding utility is used to convert the encoded file contents back into readable source code for analysis.

# PHP Wrappers — EXPLANATION NOTES

**PHP CONFIGURATION FILE PATHS**  
These are the standard locations used to inspect PHP runtime settings. In this section, they matter because wrapper-based execution depends on configuration options that may or may not be enabled.

The attacker’s first step is not immediate code execution but capability discovery. Reading the configuration file reveals whether the environment supports specific wrapper techniques.

**PHP FILTER PAYLOAD**  
This payload uses the PHP filter wrapper to Base64-encode the target configuration file before returning it. That avoids rendering problems and makes it easier to extract the raw content of the file safely through LFI.

The key idea is reuse of the earlier source-disclosure technique, but this time for environment inspection rather than application code review.

**CURL REQUEST TO READ PHP.INI**  
This request retrieves the encoded PHP configuration file through the vulnerable inclusion point. A command-line client is preferred here because the response can be long and easier to capture cleanly outside the browser.

This is the reconnaissance stage of wrapper exploitation. Before attempting execution, the attacker verifies whether the necessary PHP options are present.

**BASE64 DECODE AND GREP**  
After obtaining the encoded configuration file, the attacker decodes it locally and searches for the relevant directive.

This step turns raw disclosure into actionable information. It confirms whether a wrapper-dependent path is viable before spending time on payload trials that would otherwise fail.

**PHP WEB SHELL**  
This is the minimal PHP code used in the section to demonstrate command execution via a wrapper. It accepts a request parameter and passes it to the system execution function.

Conceptually, it turns a file inclusion primitive into a remote command interface, provided the application will include attacker-controlled data as PHP.

**BASE64 ENCODE COMMAND**  
The wrapper technique expects encoded data, so the attacker first transforms the PHP code into Base64.

Encoding serves two purposes here: it fits the wrapper syntax used later, and it prevents transport issues that could arise from special characters in raw PHP code.

**BASE64-ENCODED WEB SHELL**  
This is the encoded representation of the PHP payload used with the data wrapper.

In the attack chain, this acts as the transport form of the shell. The wrapper decodes it at inclusion time, causing the PHP code to be interpreted by the application.

**DATA WRAPPER PAYLOAD**  
This payload embeds the encoded PHP code directly into a data stream that PHP can include.

The important idea is that no file needs to exist on disk. The attacker provides executable content inline, and the vulnerable inclusion function treats that content as if it were a resource.

**DATA WRAPPER REQUEST**  
This request combines the data-wrapper payload with a command parameter. Once the included payload executes, the supplied command is run on the server.

Methodologically, this is the transition point from read-only LFI to active code execution. The vulnerability no longer just discloses files; it now runs attacker-controlled logic.

**DATA WRAPPER CURL REQUEST**  
This is the same technique performed with a command-line client, with output filtered to confirm successful execution.

The role of this step is validation. The attacker tests whether the wrapper executed and whether command output is present, rather than relying on page rendering alone.

**INPUT WRAPPER REQUEST**  
This version uses the input wrapper instead of inline data. The PHP code is delivered in the body of a POST request and then included by the application.

The attack model is similar to the data wrapper, but the transport channel differs. This is useful when POST data is easier to control or when the application flow suits request-body inclusion better than a long URL parameter.

**INPUT WRAPPER**  
This wrapper references raw request-body content. When the vulnerable code includes it, the posted PHP content is processed by the application.

Its usefulness depends on how the target handles request methods and whether wrapper inclusion is permitted by configuration.

**STATIC PHP COMMAND EXAMPLE**  
This example shows the non-dynamic version of the wrapper payload. Instead of accepting a parameter, it executes a fixed command directly.

This matters when the vulnerable application does not expose the same request channel needed by the dynamic shell. The attacker then hardcodes the command into the payload itself.

**EXPECT CHECK**  
Before using the expect wrapper, the attacker checks the configuration for evidence that the extension is intended to be loaded.

This is another capability-discovery step. It does not prove the wrapper works, but it gives a strong indication that testing it is worthwhile.

**EXPECT WRAPPER REQUEST**  
This request directly invokes a command through the expect wrapper.

Unlike the earlier techniques, no custom PHP shell is embedded. The wrapper itself is designed for command execution, so the exploitation path is shorter if the extension is available and functional.

**EXPECT WRAPPER**  
This is the wrapper form used to execute commands directly.

Its significance in the methodology is simplicity. When present, it removes the need for payload staging and reduces the attack to a direct wrapper invocation.

**PARAMETERS**  
These are the key elements that control the wrapper-based attack flow. Some belong to the vulnerable application, while others are configuration directives or wrapper arguments.

Understanding their role helps map where each attack depends on application behavior versus PHP runtime settings.

**FILES AND PATHS**  
The root path is highlighted here because the lab question asks for a flag located there. In the broader methodology, once code execution is achieved, attackers move from proof-of-execution to targeted post-exploitation objectives such as reading sensitive files.

**TOOLS**  
These tools support the workflow rather than define the vulnerability itself. The request client is used for cleaner interaction, the decoder reconstructs disclosed content, and the pattern matcher helps verify success conditions quickly.

Together, they make wrapper testing faster, more repeatable, and easier to validate during exploitation.


# Remote File Inclusion (RFI) — EXPLANATION NOTES

**RFI-CAPABLE FUNCTIONS**  
These are the inclusion mechanisms from the section that can accept remote resources. The important distinction is whether they only read remote content or also execute it once included.

For exploitation, functions that both fetch and execute remote content are the most dangerous because they can turn a file inclusion bug into direct code execution.

**VERIFY RFI**  
The section shows two ways to verify remote inclusion. One is indirect capability checking through PHP configuration, and the other is direct behavioral testing by attempting to include a URL.

The configuration check is useful reconnaissance because it tells you whether remote URL inclusion may be permitted. The direct inclusion test is stronger because it confirms real runtime behavior. The localhost URL is used first because it reduces the chance that network controls or outbound filtering will interfere with the test.

**WEB SHELL**  
This is the minimal PHP payload used to convert remote inclusion into code execution. Once the application includes the hosted script and evaluates it as PHP, the request parameter becomes a command channel.

The reason this works is that RFI collapses two attacker goals into one chain: delivery of attacker-controlled code and server-side execution of that code.

**HTTP**  
This is the most straightforward hosting method. The attacker places the PHP payload on a web server they control and points the vulnerable application at that URL.

HTTP is commonly chosen first because it is simple to set up and frequently allowed through network controls. The included request demonstrates the full RFI chain: remote script retrieval, server-side PHP execution, and command passing through a request parameter.

**FTP**  
FTP is presented as an alternative transport when HTTP is filtered or when the application blocks certain URL strings.

The underlying idea is that the inclusion primitive may support multiple protocols. If one protocol is restricted by policy or pattern matching, another may still succeed. The credentialed FTP example shows that authentication can be embedded in the resource location if anonymous access is not allowed.

**SMB**  
This technique targets Windows-specific behavior. The section explains that Windows can treat files on remote SMB shares as normal files, which makes UNC paths useful even when typical PHP remote-include settings are not enabled.

This is significant because it shows that RFI is not only about application-level URL handling. Platform behavior can create alternate inclusion paths, especially on Windows systems where remote shares integrate naturally with file access.

**PATHS AND PARAMETERS**  
These values define the attack surface and execution flow. The `language` parameter is the vulnerable inclusion point, while `cmd` is the execution channel used by the remote shell.

The root path matters because the lab objective is to search under top-level directories after command execution is achieved. In methodology terms, once execution is confirmed, the attacker pivots from proving RCE to targeted enumeration.

**TOOLS AND SERVICES**  
These support the exploitation workflow by providing hosting, transport, and verification. The server components deliver the payload, while the command-line utilities help confirm environment configuration and test inclusion behavior.

Their role in the methodology is operational: make payload delivery reliable, adaptable across protocols, and easy to validate when one transport path fails or is filtered.
# LFI and File Uploads — EXPLANATION NOTES

## PHP EXECUTION-CAPABLE FUNCTIONS

These functions execute the content of included files instead of simply reading them. When such functions are used in an LFI vulnerability, they can escalate the attack from file disclosure to code execution.

This distinction is critical. If the inclusion mechanism executes code, an attacker can inject executable code into any file that the application later includes.

## MALICIOUS IMAGE WEB SHELL

The technique combines two concepts: file upload capability and file inclusion vulnerability. The uploaded file contains valid image magic bytes followed by PHP code.

The magic bytes allow the file to pass basic upload validation checks that rely on file headers. However, when the file is included by the server-side function, the PHP interpreter processes the embedded code.

This creates a disguised payload that behaves like an image for upload validation but like executable code during inclusion.

## UPLOADED FILE PATH

To exploit the attack, the attacker must know where the uploaded file is stored. Many web applications expose uploaded content publicly, making the path discoverable through page source or direct access.

Once the path is known, the attacker can reference it through the LFI vulnerability to execute the embedded code.

## HTML IMAGE REFERENCE

The page source reveals the storage location of the uploaded file. Attackers frequently inspect HTML to determine upload directories.

This information is important because LFI exploitation depends on referencing the correct filesystem path.

## LFI EXECUTION REQUEST

This request demonstrates how the uploaded file becomes an execution point. The vulnerable inclusion function loads the file from the upload directory.

Because the file contains PHP code, the interpreter executes it. The command parameter becomes the interface for issuing commands on the system.

This is the most reliable method discussed in the section because it does not rely on special wrappers or uncommon configurations.

## ZIP WRAPPER PAYLOAD CREATION

This technique packages a PHP shell into a compressed archive. The archive is renamed with an allowed file extension to bypass upload restrictions.

If the application later includes the archive using the appropriate wrapper, PHP extracts and executes the internal file.

This method becomes useful when direct execution of uploaded files fails due to file-type restrictions.

## ZIP WRAPPER REQUEST

The wrapper instructs PHP to treat the uploaded archive as a container and access the specified file within it.

The encoded separator identifies the internal file within the archive. When included, PHP processes the internal script and executes the embedded command handler.

## PHAR PAYLOAD SCRIPT

This script constructs a PHAR archive programmatically. The archive contains a file with the command execution payload.

PHAR archives behave like executable containers for PHP and can be processed through specific wrappers.

The stub prevents normal execution while allowing the archive to function as a container for the payload.

## PHAR BUILD COMMAND

This command compiles the PHAR archive and renames it with an image extension. Renaming the file increases the likelihood of bypassing upload restrictions that check only file extensions.

The archive can then be uploaded and later accessed through the wrapper mechanism.

## PHAR WRAPPER REQUEST

This inclusion request accesses the PHAR archive and references the embedded payload file.

The server processes the archive and executes the internal script, enabling command execution through the same parameter used in earlier techniques.

## FILES AND PATHS

These entries represent the artifacts involved in the attack chain: the uploaded payloads, archive containers, and storage directories.

Understanding where uploaded files are stored is essential because LFI exploitation requires accurate file paths.

## PARAMETERS

The inclusion parameter determines which file is loaded by the application. The command parameter provides an interface to send system commands once execution is achieved.

These parameters define the control channel between the attacker and the compromised application.

## TOOLS

The archive utility and PHP runtime are used to construct payload containers for wrapper-based techniques.

While the basic upload technique requires minimal tooling, wrapper-based methods rely on archive creation and packaging steps to prepare the payload.



# Log Poisoning — EXPLANATION NOTES

## EXECUTION-CAPABLE FUNCTIONS

These are the inclusion functions capable of executing the contents of included files. Log poisoning relies on this behavior because the injected PHP code must be interpreted by the server when the log file is included.

If the vulnerable function only reads files without executing them, this technique would only display the injected code rather than run it.

## SESSION FILE PATHS

PHP session files store data associated with a user session. These files are commonly located in standard directories on Linux and Windows systems.

When the application writes user-controlled data to session files, attackers may be able to inject executable PHP code into them.

## SESSION FILE FORMAT

Session files follow a predictable naming pattern. The name typically consists of the prefix `sess_` followed by the value of the user's session identifier cookie.

This predictable structure allows attackers to determine the path to their own session file.

## SESSION FILE EXAMPLE

This represents a typical session file path derived from the session identifier stored in the browser cookie.

The attacker reads this file using the LFI vulnerability to verify whether controllable values are present.

## READ SESSION FILE

This request demonstrates how the attacker accesses their session file through the vulnerable inclusion parameter.

The goal is reconnaissance: confirm that the file is readable and inspect its contents to identify controllable values.

## CONTROL SESSION VALUE

Some applications store request parameters inside session variables. If an attacker-controlled parameter appears in the session file, it can be used as an injection point.

The attacker manipulates the parameter to observe whether the stored value changes.

## URL-ENCODED PHP WEB SHELL

This payload is a URL-encoded PHP command execution snippet.

Encoding is used because the payload must be inserted through a URL parameter without breaking the request format.

## SESSION POISONING PAYLOAD

This request injects the encoded PHP payload into the session file through a parameter that the application records in the session data.

Once stored, the session file contains executable PHP code.

## EXECUTE COMMAND THROUGH SESSION FILE

The attacker includes the poisoned session file through the LFI vulnerability. When the application processes the file, the injected PHP code executes.

The command parameter acts as an interface to run arbitrary system commands.

## APACHE LOG PATHS

These are common log locations for Apache web servers. Access logs record all incoming requests and often include headers that attackers can manipulate.

If the web application has permission to read these logs, they can become targets for log poisoning.

## NGINX LOG PATHS

Nginx uses similar log structures to Apache but stores them in different directories.

The same poisoning technique applies if the server process has read access to these files.

## READ SERVER LOG

This step confirms whether the attacker can access the server logs through the LFI vulnerability.

Reading the logs allows the attacker to verify that their injected payload appears within the file.

## USER-AGENT WEB SHELL

The User-Agent header is controllable by the client. By inserting PHP code into this header, the attacker can cause the payload to be written into the server log.

Once the log is included, the server executes the injected code.

## USER-AGENT HEADER FILE

This step prepares a header containing the malicious payload.

The file is later used as a header input when sending a request to the server.

## POISON LOG WITH CURL

This request sends the crafted User-Agent header to the server. When the request is logged, the injected PHP payload becomes part of the log file.

This step contaminates the log with executable code.

## EXECUTE COMMAND THROUGH LOG

Once the log file contains the injected payload, the attacker includes it through the LFI vulnerability.

The PHP interpreter executes the payload and runs the command supplied through the request parameter.

## PROC FILE TARGETS

These files represent process-related information in Linux systems. Some of them store environment variables or open file descriptors.

If readable, they may contain request headers such as the User-Agent, making them potential targets for poisoning attacks.

## OTHER LOG TARGETS

Various system services maintain logs that may record attacker-controlled input.

If those logs contain injected PHP code and are readable through the LFI vulnerability, they can also be used for code execution.

## PARAMETERS

These parameters control the attack flow. The inclusion parameter selects the file to be loaded, while the command parameter supplies system commands to the injected payload.

The session identifier parameter is used to locate the corresponding session file.

## TOOLS

The request client allows attackers to craft requests with custom headers. The interception proxy enables manipulation of HTTP traffic in real time.

Both tools help inject payloads into logs or session files and verify whether the poisoning attempt was successful.
#
#
#
#