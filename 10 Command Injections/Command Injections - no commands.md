# Intro to Command Injections — EXPLANATION NOTES

This section introduces **command injection** as a high-impact vulnerability where user input is interpreted as part of a system command executed by the backend server.

### Injection Concept

Injection flaws occur when untrusted input is embedded directly into commands, queries, or code without proper sanitization. The application intends to execute a fixed operation, but the attacker alters its structure or behavior.

Command injection is ranked highly in OWASP because it often leads to full system compromise rather than limited application impact.

### OS Command Injection Context

In OS command injection, user input influences an operating system command. Web applications frequently execute system commands for tasks like file handling or process management, which creates risk when input is not strictly controlled.

### PHP Example Analysis

The PHP example uses the `system` function to execute a shell command. The `filename` parameter from a GET request is concatenated directly into the command string.

Why this works:

- `system()` passes the constructed string directly to the shell.
    
- No validation or escaping is applied to `$_GET['filename']`.
    
- An attacker can inject shell metacharacters to alter command execution.
    

This demonstrates how simple string concatenation can turn intended functionality into arbitrary command execution.

### NodeJS Example Analysis

The NodeJS example uses `child_process.exec` to run a shell command that creates a file.

Why this works:

- `exec` spawns a shell to interpret the command string.
    
- Template literals embed user input directly.
    
- The shell interprets injected operators as part of the command.
    

The vulnerability exists regardless of language because the issue is **how input flows into a command**, not the syntax of the language itself.

### Cross-Language Impact

Different languages provide different APIs, but the attack surface is the same:

- Input reaches a shell-executing function
    
- No sanitization or strict argument handling
    
- Attacker controls command structure
    

This is why command injection techniques transfer easily across PHP, NodeJS, Python, Ruby, and even native binaries.

### Attacker Mindset

An attacker looks for:

- File operations
    
- Diagnostic commands
    
- User-controlled parameters
    
- Backend features that “need” OS access
    

Once found, they test whether input breaks out of the intended command context.

### Common Pitfalls

- Assuming backend code is unreachable by attackers
    
- Relying on input length or format assumptions
    
- Using shell-based execution instead of safer APIs
    
- Filtering instead of eliminating shell interpretation
    

### Methodology Fit

Command injection fits into the broader injection class alongside SQLi and XSS:

- User input
    
- Unsafe execution context
    
- Boundary escape
    
- Control of execution flow
    

Later sections build on this foundation to show detection techniques, exploitation chains, filter evasion, and prevention strategies.
# Detection — EXPLANATION NOTES

### Detection Philosophy

Detection of basic OS command injection mirrors exploitation. The attacker appends additional commands to normal input and observes whether application behavior or output deviates from the expected result.

A change in output, error message, or execution flow indicates that user input is being interpreted as part of a system command.

### Host Checker Behavior

The Host Checker utility accepts an IP address and returns output consistent with a `ping` command. From the response showing a single transmitted packet, the backend command can be inferred as shown in **Observed Command Pattern**.

This inference step is critical: even without source code, output characteristics reveal backend behavior.

### Why Injection Is Possible

If user input is inserted directly into the command string without sanitization or escaping, shell metacharacters can break out of the intended argument context and introduce new commands.

This module assumes:

- Direct user input usage
    
- No filtering or escaping
    
- Shell-based execution
    

### Injection Operators Role

Operators listed in **Injection Operators** define how multiple commands are chained or conditionally executed by the shell.

Different operators:

- Execute commands sequentially
    
- Execute commands conditionally
    
- Suppress or replace output
    
- Spawn subshell execution
    

These operators work across most languages and frameworks because the vulnerability exists at the shell execution layer, not the application language.

### Cross-Platform Notes

Most operators work consistently across Linux, macOS, and Windows environments. The notable exception is the semicolon, which does not function in Windows CMD but does in PowerShell.

### Attacker Workflow

An attacker:

- Submits normal input
    
- Appends operators incrementally
    
- Observes error messages or output changes
    
- Confirms command execution capability
    
- Prepares for exploitation in later stages
    

### Transition to Exploitation

Once any operator alters behavior or triggers an error, command injection is confirmed. Subsequent sections build on this detection phase to execute controlled commands, bypass filters, and escalate impact.
# Other Injection Operators — EXPLANATION NOTES

### AND Operator (&&)

The AND operator executes the second command only if the first command succeeds. In this lab, `ping` returns a success exit code, so the injected command executes immediately afterward. This results in output from both commands.

### OR Operator (||)

The OR operator executes the second command only if the first command fails. When a valid IP is supplied, `ping` succeeds and the injected command does not execute. When the IP is omitted, `ping` fails, triggering execution of the injected command.

This behavior allows attackers to intentionally break the original command to guarantee execution of their payload.

### Output Control

Using the OR operator with a deliberately failing first command produces output only from the injected command. This creates cleaner responses and reduces noise from the original command output.

### Operator Selection Strategy

Different operators provide control over:

- Execution order
    
- Conditional execution
    
- Output visibility
    

Attackers select operators based on how the backend command behaves and how much output they want returned.

### Broader Injection Context

Injection operators are not limited to OS command injection. Similar logic applies across SQL, LDAP, XPath, headers, and code execution contexts. Operator behavior is environment-dependent and varies by interpreter, shell, and backend logic.

### Lab Relevance

This page reinforces that exploitation is not just about execution, but also about precision. Understanding operator semantics enables reliable command execution even when the original command structure interferes.

### Question Resolution

Among the tested operators, the pipe (`|`) operator is the one that only shows the output of the injected command, as it redirects the output stream of the first command into the second.
# Identifying Filters — EXPLANATION NOTES

### Purpose of Filtering

This section introduces backend defenses that attempt to stop command injection by blocking specific characters or commands. Unlike front-end validation, these checks occur on the server side and actively deny requests.

### Filter vs WAF Signals

The error message appears inline within the application response, indicating that the rejection is handled by backend application logic rather than an external WAF. WAFs typically redirect, block entirely, or display branded error pages.

### Blacklist-Based Defense

The PHP snippet demonstrates a common but weak defense pattern: scanning input for forbidden characters. If any blacklisted character is present, the request is rejected without context-aware parsing.

### Identification Strategy

To identify what is being blocked, the attacker reduces payloads to the smallest possible changes and tests one character at a time. Since a plain IP works, additional characters are incrementally appended until rejection occurs.

This isolation technique reveals which operators or characters trigger the filter.

### Operator Testing Logic

Each injection operator is tested individually to determine whether it is included in the blacklist. Operators that are not blocked become viable candidates for bypassing the filter in later stages.

### Lab Outcome

Among the tested operators, the **new-line character (`\n`)** is not blacklisted by the web application, making it a viable injection vector despite other operators being filtered.

### Attacker Takeaway

Blacklists are brittle and incomplete. Attackers do not need every operator to work—only one. Identifying allowed characters is the first step toward filter evasion and successful exploitation.
# Bypassing Space Filters — EXPLANATION NOTES

### Context

This section builds on filter identification by addressing one of the most commonly blacklisted characters: the space. Since command arguments normally require spaces, blocking them appears effective but is easily bypassed.

### Allowed Operator

The newline character (`%0a`) is not blacklisted and functions as a command separator in shells. Its acceptance confirms a viable injection operator even when others are blocked.

### Space Blocking Confirmation

When the newline operator is followed by a space, the request is rejected. Incremental testing isolates the space as the blocked character rather than the command or operator.

### Tab Replacement

Tabs (`%09`) are treated as whitespace by shells but are often overlooked in filters. Replacing spaces with tabs preserves command parsing while bypassing space filters.

### IFS Expansion

`${IFS}` expands to the shell’s internal field separator, which includes spaces and tabs by default. Using it in place of literal spaces allows arguments to be separated without inserting a space character.

### Brace Expansion

Bash brace expansion inserts implicit spacing between arguments. Commands written without spaces are expanded by the shell into valid argument lists, making this an effective space-less execution technique.

### Attacker Mindset

Filters typically block literal characters, not semantic meaning. Attackers exploit shell features that recreate blocked syntax indirectly, turning simple blacklists into unreliable defenses.

### Lab Objective

These techniques enable execution of commands like `ls -la` despite space filtering. The exercise applies the demonstrated bypasses to retrieve file metadata from the target system.

### Defensive Takeaway

Character blacklisting is insufficient. Secure command execution requires strict argument handling, proper escaping, or avoiding shell execution entirely.
# Bypassing Other Blacklisted Characters — EXPLANATION NOTES

### Motivation

Beyond operators and spaces, filters frequently block path separators like `/` and `\` to prevent directory traversal and command execution. This section demonstrates how shell features can recreate blocked characters without typing them directly.

### Linux Environment Variable Substring Technique

Environment variables often contain useful characters. By extracting a substring from variables such as `PATH`, attackers can obtain a single slash character. The shell performs the expansion before execution, bypassing character filters.

The same technique applies to other variables like `HOME` or `PWD`.

### Semicolon Reconstruction

Although there is no dedicated environment variable for a semicolon, some variables contain it as part of their value. Extracting a single character from `LS_COLORS` yields a usable semicolon that can function as an injection operator.

Combining this with `${IFS}` recreates a full command separator without directly using blacklisted characters.

### Windows Equivalents

Windows supports similar substring operations in both CMD and PowerShell:

- CMD uses `:~start,length` slicing
    
- PowerShell treats strings as arrays and allows direct indexing
    

Enumerating environment variables reveals candidates that contain required characters.

### Character Shifting Concept

Instead of extracting characters, attackers can generate them algorithmically. By shifting ASCII values using `tr`, the desired character is produced from a nearby one. This avoids including the forbidden character in the payload entirely.

### Attacker Takeaway

Filters that rely on literal character blocking fail when shells offer multiple ways to reconstruct syntax at runtime. Attackers leverage expansion, substitution, and encoding to reintroduce forbidden characters invisibly.

### Defensive Implication

Effective defense requires eliminating shell interpretation or enforcing strict allowlists with safe APIs. Character blacklists cannot reliably prevent command injection when the execution environment itself is expressive.
# Bypassing Blacklisted Commands

### Command Blacklisting Overview

This section focuses on filters that block specific command names rather than individual characters. These filters typically search for exact string matches, making them vulnerable to simple obfuscation.

### Why Obfuscation Works

Blacklist checks commonly use substring matching. If the literal word does not appear exactly as written, the filter fails, even though the shell ultimately reconstructs and executes the command.

### Quote Insertion Technique

Shells like Bash and PowerShell ignore quotes placed between characters when parsing command names, as long as quoting rules are respected. Inserting single or double quotes between characters changes the string’s appearance while preserving execution.

Key constraints:

- Quotes must be balanced
    
- Quote types cannot be mixed
    

### Linux-Specific Characters

Bash ignores certain characters during command parsing:

- `$@` expands to positional parameters
    
- `\` escapes the following character
    

These can be inserted arbitrarily between characters to break blacklist matching without affecting execution.

### Windows-Specific Characters

In Windows CMD, the caret (`^`) acts as an escape character and is ignored during command execution. This allows similar obfuscation against command filters on Windows systems.

### Attacker Workflow

An attacker:

- Identifies which commands are blocked
    
- Alters command appearance using shell parsing rules
    
- Confirms execution through obfuscated payloads
    
- Chains these techniques with previous filter bypasses
    

### Defensive Implication

Command blacklists are fragile. Shell parsing flexibility makes it trivial to disguise commands. Secure implementations must avoid shell execution or enforce strict allowlists with safe argument handling.

### Lab Continuation

These obfuscation methods enable reading files such as `flag.txt` even when common utilities are blacklisted, completing the exploitation chain established in earlier sections.
# Advanced Command Obfuscation — EXPLANATION NOTES

### Case Manipulation

Changing letter casing can bypass simple blacklist checks. Windows shells are case-insensitive, so altered casing executes directly. Linux is case-sensitive, requiring transformation back to lowercase at runtime using shell utilities.

### Runtime Case Conversion

Using `tr` or parameter expansion converts mixed-case strings into valid lowercase commands during execution. Filters see obfuscated text; the shell executes the normalized result.

### Space Filter Awareness

Advanced techniques often introduce spaces. Replacing spaces with tabs or other previously identified bypasses is necessary to avoid triggering space filters.

### Reversed Commands

Reversing a command string avoids literal blacklist matches. The command is reconstructed and executed at runtime via subshell execution. This removes the original keyword entirely from the payload.

### Cross-Platform Reversal

Both Bash and PowerShell support string reversal and subshell execution, enabling equivalent techniques across Linux and Windows targets.

### Encoded Commands

Encoding payloads (e.g., Base64) hides both commands and filtered characters. The payload is decoded and executed at runtime, often without using pipes or other blocked operators.

### Filter Evasion Strategy

Advanced obfuscation combines:

- Runtime reconstruction
    
- Encoding/decoding
    
- Shell features
    
- Previously identified character bypasses
    

This layered approach significantly reduces detection by application filters and WAFs.

### Lab Objective Context

These techniques enable execution of complex, multi-stage commands that include paths, pipes, and arguments—such as the target `find | grep | tail` pipeline—even under restrictive filtering.

### Defensive Implication

Once execution reaches a shell, expressive features make reliable filtering impractical. Robust defenses must avoid shell execution entirely or strictly control allowed commands and arguments.

# Evasion Tools — EXPLANATION NOTES

### Purpose of Evasion Tools

When manual obfuscation fails against strong filters or WAFs, automated tools generate highly variable payloads that are difficult to signature-match.

### Bashfuscator (Linux)

Bashfuscator programmatically applies multiple mutation layers such as token reordering, string reconstruction, and runtime evaluation. Randomized output makes static detection unreliable.

### Payload Size Considerations

Default settings may generate extremely large payloads. Reducing layers, disabling mangling, and limiting techniques produces shorter commands that are more practical for injection points with length restrictions.

### Runtime Validation

Testing with `bash -c` ensures the obfuscated payload reconstructs and executes the original command correctly before deployment.

### DOSfuscation (Windows)

DOSfuscation is interactive and focuses on CMD/PowerShell environments. It obfuscates commands using environment variable slicing, encoding, and syntax tricks native to Windows shells.

### Encoding Strategy

Environment-variable-based encoding avoids direct inclusion of filtered keywords and characters, reconstructing them at execution time.

### Cross-Platform Usage

PowerShell Core (`pwsh`) allows Windows-focused obfuscation tools to be tested on Linux systems, enabling payload preparation without a native Windows VM.

### Attacker Mindset

Automated tools are used when defenders rely on pattern-based detection. Each generated payload is unique, reducing reuse signatures and increasing bypass success.

### Defensive Implication

The existence of such tools highlights the futility of blacklist-based defenses. Preventing command injection requires eliminating shell execution paths or enforcing strict, parameterized command invocation.
# Command Injection Prevention — EXPLANATION NOTES

### Avoid System Command Execution

The strongest prevention is to avoid executing system commands entirely. Most backend languages provide safe built-in alternatives that perform required functionality without invoking a shell, eliminating injection risk at the root.

### Use Built-In Functions

Functions like `fsockopen` perform networking tasks safely without command execution. Whenever a built-in function exists, it should always be preferred over shell execution.

### Backend Input Validation

Input validation ensures user input matches an expected format before it is processed. Validation must be enforced on the backend, not only the frontend, since client-side checks are trivial to bypass.

Language-native validation helpers and libraries reduce implementation errors and provide standardized checks.

### Input Sanitization

Sanitization removes characters that are unnecessary for valid input. This is performed **after** validation to further reduce attack surface in case validation logic is flawed.

Allowlisting characters is safer than blacklisting. Blacklists are incomplete and easily bypassed, as demonstrated throughout the module.

### Escaping Limitations

Escaping special characters may reduce risk but is not reliable against advanced injection techniques. Escaping should not be the primary defense for command execution contexts.

### Server Hardening

Even with secure code, server misconfiguration can magnify impact. Running services with least privilege, disabling dangerous functions, restricting filesystem access, and deploying WAFs all reduce post-exploitation damage.

### Defense-in-Depth

No single control is sufficient. Secure coding, strict validation, sanitization, hardened server configuration, and continuous penetration testing must work together to prevent command injection vulnerabilities.

### Final Takeaway

Command injection prevention is achieved by **eliminating shell execution**, **validating and sanitizing input**, and **hardening the server environment**. Filtering alone is never enough.#
#



?to=&from=51459716.txt

copy
?to=&from=51459716.txt&finish=1

move 
&from=51459716.txt%26c'a't%09${PATH:0:1}flag.txt&finish=1&move=1
#
#
#