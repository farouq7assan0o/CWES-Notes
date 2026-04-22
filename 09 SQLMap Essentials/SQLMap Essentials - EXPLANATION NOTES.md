# SQLMap Overview 

**What SQLMap Is**

SQLMap is an open-source Python tool that automates detection and exploitation of SQL injection vulnerabilities. It covers the full attack lifecycle: fingerprinting the DBMS, detecting injectable parameters, and extracting or manipulating data. It also supports file system access and OS command execution in advanced scenarios.

**Installation Options**

The apt method gives a stable packaged version. The git clone method pulls the latest development branch directly from the project repository, which is preferred in lab environments when cutting-edge payloads or newly supported DBMSes matter.

**The -hh Flag**

This flag outputs the extended help, which is where the technique string `BEUSTQ` is documented. Understanding this string lets you constrain SQLMap to specific injection types when stealth, speed, or compatibility with the target requires it.

**Technique String BEUSTQ**

Each letter maps to a fundamentally different exploitation strategy:

- B (Boolean-based blind) is the most common. It infers data one bit at a time by comparing TRUE vs FALSE server responses. Slow but widely applicable.
- E (Error-based) leverages DBMS-specific error messages to reflect query results directly. Faster than blind methods, limited to ~200 bytes per chunk.
- U (Union query-based) is the fastest type. It appends a full SELECT via UNION and reads results directly from the rendered page response, potentially dumping an entire table in one request.
- S (Stacked queries) allows non-SELECT statements like INSERT, UPDATE, DELETE, and OS command execution. Requires DBMS and platform support (e.g., MSSQL, PostgreSQL).
- T (Time-based blind) uses conditional sleep functions to distinguish TRUE from FALSE via response delay. Used when boolean-based blind is not viable, such as on non-query statements.
- Q (Inline queries) embeds a subquery inside the original query. Uncommon and app-specific, but supported.

**Out-of-Band (OOB) SQLi**

The LOAD_FILE DNS exfiltration payload is the most advanced technique. It forces the database server to make DNS lookups to an attacker-controlled domain, encoding query results as subdomain labels. This is used when in-band channels (page response, timing) are completely unavailable. It requires control of a DNS server for the attacker domain.

**Attack Chain Logic**

SQLMap tries techniques in order of speed and reliability. It starts with heuristics to detect whether a parameter is injectable, then fingerprints the DBMS, then systematically applies payloads. Understanding which technique is in use tells you the underlying mechanism of data leakage, which matters for interpreting results, tuning performance, and understanding why some techniques fail against certain targets.


# Getting Started with SQLMap

**Help Levels**

The `-h` flag provides a concise listing covering the most common options, enough for the majority of engagements. The `-hh` flag exposes the full advanced listing, which includes input source options, request customization, and enumeration controls. Knowing both exists allows efficient reference during labs or exams without needing external documentation.

**The Vulnerable PHP Code**

The code directly concatenates the user-supplied `$_GET["id"]` value into an SQL query string with no sanitization or parameterization. Error reporting is also active via `mysqli_error()`, meaning any malformed query returns a raw database error to the browser. This combination - unsanitized input plus visible errors - is the ideal scenario for error-based SQLi and makes detection trivial even manually.

**Why --batch Matters**

During a scan, SQLMap prompts the user for decisions at several points (skip other DBMS tests, extend test depth, continue after finding one injection). The `--batch` flag suppresses all prompts and accepts the default answer automatically. This is essential for scripted or time-constrained scenarios and is standard practice in HTB labs.

**What the Scan Output Demonstrates**

The scan against the example URL confirms four simultaneous injection types in the same parameter: boolean-based blind, error-based, time-based blind, and UNION query. This is common in real applications because no single mitigation is applied. SQLMap reports all confirmed types together, each with its specific payload, so the operator can understand exactly what technique is being leveraged and can reference or replicate those payloads manually if needed.

**Payload Anatomy**

The boolean-based payload uses a tautology (`AND 8814=8814`) to produce a TRUE result with no visible change, confirming the parameter is injectable. The error-based payload forces a FLOOR/RAND collision inside a GROUP BY clause, causing MySQL to emit an error carrying extracted data. The time-based payload uses a conditional SLEEP to confirm injection when no error or content difference is observable. The UNION payload appends a three-column SELECT, using hex-encoded delimiters (`0x7170706a71`, `0x71707a7871`) to reliably frame extracted data within the response.

**Output Log Location**

SQLMap automatically saves all fetched data and session state to `/home/user/.sqlmap/output/<target-host>/`. This allows resuming interrupted scans and reviewing previously extracted data without re-running the full attack.

# SQLMap Output Description 

**Why Output Interpretation Matters**

SQLMap's log messages form a real-time narrative of the detection process. Reading them correctly tells the operator what SQLMap has confirmed versus what it is still probing, which avoids misreading noise as findings or missing actual vulnerabilities.

**Stability and Dynamism**

A stable URL means the response content is consistent across repeated identical requests, which gives SQLMap a reliable baseline to detect changes caused by injected payloads. A dynamic parameter means the server actually processes and reflects the parameter value in some way, which is a prerequisite for any injection to have an observable effect. Static parameters are not useless but are lower priority.

**Heuristic Injection Detection**

Before running the full payload suite, SQLMap sends a deliberately malformed value (such as the example `?id=1",)..).))'`) to trigger a DBMS error. An error response is not proof of injection but is a fast pre-filter to decide whether the full test suite is worth running. The XSS heuristic runs alongside this for free and can surface additional findings during broad scans.

**DBMS Narrowing and Level/Risk Extension**

When a DBMS is identified early, SQLMap offers to skip payloads designed for other database engines. Accepting this saves significant time. The follow-up prompt to extend level and risk for that specific DBMS means running a wider, deeper payload set for the confirmed engine, improving coverage at the cost of more requests.

**Reflective Values**

When injected payload fragments appear verbatim in the response body, this is called reflection. It can corrupt comparison logic if not filtered. SQLMap detects and strips these automatically, but seeing this message in the log confirms that the target reflects input, which is itself useful context for manual testing.

**The --string Anchor**

When SQLMap identifies a constant string (like `luther`) that appears in TRUE responses but not FALSE ones, it uses that string as a reliable discriminator for boolean-based blind injection. This is a stronger signal than fuzzy content comparison and eliminates a major source of false positives. The presence of this message in output means the boolean detection is high confidence.

**Time-based Statistical Model**

Time-based blind injection is sensitive to network latency. SQLMap collects a baseline of normal response times first, then uses statistical analysis to determine whether a SLEEP delay is genuinely caused by the payload or by network variance. This is why there is a visible wait during time-based testing.

**UNION Column Count via ORDER BY**

Before firing full UNION payloads, SQLMap tests whether ORDER BY is usable. If it is, SQLMap can binary-search for the exact column count in the underlying query, dramatically reducing the number of requests needed versus a linear probe. This is a heuristic optimization specific to UNION-based detection.

**Injection Point Confirmation**

The message confirming a vulnerable parameter is the definitive finding. Everything before it is detection work; this message means SQLMap has proven exploitability. The final summary lists each confirmed technique, its title, and its exact payload, which is directly usable for manual reproduction or report documentation.

**Session Files and Resume Behavior**

The output directory stores session state after the first successful run. On subsequent runs against the same target, SQLMap reads this state to skip re-detection steps it has already completed, minimizing unnecessary traffic against the target.


# Running SQLMap on an HTTP Request

**Why cURL Conversion Works**

The "Copy as cURL" feature in browser DevTools captures the exact request the browser sent, including all headers, cookies, and encoding. Replacing `curl` with `sqlmap` preserves that entire context, so SQLMap operates under identical conditions to the browser. This is the fastest and most accurate way to replicate a real authenticated or stateful request without manually reconstructing headers.

**GET vs POST Parameter Targeting**

The `-u` flag handles GET parameters naturally since they appear in the URL. For POST bodies, `--data` is required to pass the form-encoded string. SQLMap automatically tests all parameters found in the data string unless a specific one is isolated with `-p` or marked with the `*` injection marker. Using `-p` is useful when only one parameter is suspected, reducing noise and request count.

**The Asterisk Injection Marker**

Placing `*` after a value in `--data` or inside a saved request file explicitly tells SQLMap where to inject. This is critical when dealing with non-standard parameter formats, custom delimiters, or when SQLMap's automatic detection might target the wrong parameter. The same marker works anywhere in a request file, including inside URL paths.

**Why -r is Preferred for Complex Requests**

Reproducing a complex multi-header authenticated POST as a command-line string is error-prone. The `-r` flag offloads that complexity to a plain text file captured directly from Burp. The request file is authoritative - SQLMap parses it as-is - which eliminates quoting issues, encoding mistakes, and missed headers that commonly break injection attempts in command-line setups.

**Header Injection**

SQLMap does not test headers for injection by default, only parameters. To test a header value, the `*` marker must be placed explicitly inside that header's value via `--cookie`, `-H`, or inside the request file. This matters when the application uses cookie values, custom headers, or referrer values in SQL queries.

**User-Agent Evasion**

SQLMap's default User-Agent string is well-known and blocked by many WAFs and security appliances. The `--random-agent` switch substitutes a real browser User-Agent from a built-in database, significantly reducing the chance of being fingerprinted. The `--mobile` switch does the same but specifically mimics a smartphone browser, which can also bypass certain mobile-vs-desktop filtering logic.

**JSON and XML Body Support**

When SQLMap detects JSON or XML in the POST body from a request file, it prompts for confirmation to process it. This relaxed parsing means injection points inside structured data formats are tested the same way as standard form fields, without any special configuration beyond providing the request file. For short JSON payloads `--data` can carry the body directly; for longer or nested ones `-r` is cleaner.

**Custom HTTP Methods**

Some APIs use PUT, DELETE, or PATCH instead of GET/POST. The `--method` flag forces SQLMap to use any arbitrary HTTP method while keeping all other request settings intact. This is necessary when the vulnerable endpoint only responds to non-standard methods.

# Handling SQLMap Errors 

**--parse-errors**

By default, DBMS error messages may be silently swallowed or not prominently displayed. The `--parse-errors` flag forces SQLMap to extract and print any database error messages it encounters in server responses. This is useful for diagnosing why payloads are failing - a syntax error message reveals the current quoting context, comment style needed, or structure of the underlying query, which can guide manual tuning.

**-t Traffic Logging**

The `-t` flag writes every HTTP request and response exchanged during the scan to a flat file. This is the offline equivalent of proxying through Burp. It allows post-run forensic review of exactly what SQLMap sent and what the server returned at each step, which is invaluable when a scan produces unexpected results or when you need to manually reproduce a specific request that triggered a finding.

**-v Verbosity Levels**

The `-v` flag accepts values from 0 to 6. At level 6, every outbound request and inbound response is printed to the terminal in real time, including full headers and body content. Lower levels progressively reduce output. Level 6 is the most powerful for debugging because it shows both DEBUG messages and full traffic inline, eliminating the need to open a separate file or proxy. The tradeoff is high terminal noise, so it is used for targeted diagnosis rather than routine scans.

**--proxy Routing**

Routing SQLMap traffic through Burp via `--proxy` integrates the automated scan into an interactive workflow. All SQLMap requests appear in Burp's HTTP history, where they can be individually inspected, sent to Repeater for manual modification, or have Burp's active scanner applied. This is particularly useful when a payload is close to working but needs manual adjustment, or when the tester wants to confirm exactly what payloads were used in a finding before reporting.

**Combining These Options**

In practice, `--parse-errors`, `-t`, `-v 6`, and `--proxy` address different diagnostic needs and can be combined. A typical debugging workflow starts with `--parse-errors` to catch obvious DBMS feedback, escalates to `-v 6` for full traffic visibility, and uses `--proxy` when Burp-level interaction is needed to tune the attack manually.


# Attack Tuning 

**Payload Structure: Vector and Boundary**

Every SQLMap payload has two components. The vector is the functional SQL code that extracts data or triggers a detectable condition. The boundary is the prefix and suffix wrapped around the vector that closes open quotes, parentheses, or other syntax left open by the vulnerable query. Without the correct boundary, the injected SQL is syntactically invalid and the DBMS rejects it.

**--prefix and --suffix**

These are used when the vulnerable query has a non-standard structure that SQLMap's built-in boundary set does not cover. The PHP example in the source illustrates this: the query wraps the input in `(('...'))`, so a standard single-quote or unquoted boundary will not close the expression correctly. By supplying `%'))` as the prefix and `-- -` as the suffix, the tester manually accounts for the nested parentheses and quotes, producing a syntactically valid injected statement.

**--level and --risk**

Level controls how many boundary variations are tried. Raising it tests increasingly unlikely but valid injection contexts, at the cost of significantly more requests. Risk controls which vector categories are included. Risk 1 avoids payloads that could modify or destroy data. Risk 3 enables OR-based payloads, which are required for certain injection points like login forms where the WHERE clause must evaluate to true, but dangerous on UPDATE or DELETE statements because a true OR condition could affect every row.

The payload count jump from 72 (default) to 7,865 (level 5, risk 3) illustrates why these should only be raised when the default run fails or the target specifically requires it.

**Advanced Detection Switches**

The `--code`, `--titles`, `--string`, and `--text-only` switches address scenarios where response differentiation between TRUE and FALSE is subtle or buried in noise. `--code` is the sharpest: it anchors detection to a single HTTP status code, eliminating all content-based comparison. `--string` is the next most reliable because it looks for one specific token. `--titles` works when the page title changes predictably. `--text-only` is useful on pages with heavy JavaScript or dynamic HTML that otherwise floods the comparator with irrelevant diffs.

**--technique**

Restricting to specific technique letters (from the BEUSTQ set) prevents SQLMap from running technique types that are either known to be inapplicable or actively causing problems. Time-based payloads (`T`) are the most common culprit for scan hangs and false positives in high-latency environments. Excluding them with `--technique=BEU` keeps the scan moving when timing-based detection is unreliable.

**UNION Tuning**

UNION injection requires the injected SELECT to have the same number of columns as the original query. When ORDER BY binary search fails to determine this automatically, `--union-cols` provides it directly. `--union-char` replaces the default NULL or integer fill values when the original query enforces type constraints that NULL violates. `--union-from` handles Oracle specifically, where every SELECT must include a FROM clause - `FROM dual` being the canonical case - and failing to supply it causes every UNION payload to be rejected by the DBMS.

# Database Enumeration 

**queries.xml and How SQLMap Knows What to Ask**

SQLMap does not hardcode retrieval logic for each DBMS into its Python source. Instead, it reads from a structured XML file that maps each enumeration goal (banner, current user, table list, etc.) to the correct SQL expression for each database engine. This design means the same SQLMap flag works across all supported DBMSes without the operator needing to know the correct query for each one. The `inband` and `blind` query variants ensure that the right extraction method is chosen automatically based on which injection type was confirmed.

**Basic Enumeration Flags**

Running `--banner --current-user --current-db --is-dba` together in a single command is the standard opening move after confirming injection. These four data points establish whether the DB user has administrative privileges, which determines what further exploitation is possible. A DBA-level user (as in the example, where `root@%` is confirmed DBA) opens paths to file system access and OS command execution that a restricted user would not.

**--tables and -D**

Listing tables requires knowing which database to query. The `-D` flag scopes all subsequent enumeration to a named database. Without it, SQLMap uses the current database returned by `--current-db`. Combining `--tables -D <dbname>` maps the entire schema before any data is pulled, which helps the operator select high-value targets.

**--dump with Scope Flags**

The `--dump` flag retrieves the actual row data from a table. Its scope is narrowed progressively with `-T` (table), `-C` (columns), `--start`/`--stop` (row range), and `--where` (SQL condition). This granularity is critical in real engagements where pulling an entire large table generates excessive traffic and time. The `--where` option directly injects a SQL WHERE condition, which is the most precise filter when the operator already knows what value to look for.

**Full Database vs All Databases**

`--dump -D <dbname>` retrieves every table in one named database. `--dump-all` retrieves every table in every accessible database. System databases (information_schema, mysql, performance_schema, etc.) contain DBMS metadata rather than application data, so `--exclude-sysdbs` filters them out to focus output on what is operationally useful.

**Dump Format and Local Storage**

All dumped data is saved automatically as CSV by default. The `--dump-format` option switches output to HTML or SQLite, the latter being useful for running SQL queries locally against the exfiltrated data without needing a running database server. Knowing the local output path allows the operator to reference or process previous dumps without re-running the scan.

# Advanced Database Enumeration

**--schema**

This switch retrieves the full column-level structure of every table across all accessible databases. The output maps every table to its columns and data types without pulling row content. This is the reconnaissance step before targeting specific tables for data extraction - it reveals which tables contain credential fields, blobs, or other high-value data types by structure alone.

**--search with -T and -C**

The `--search` flag uses SQL LIKE pattern matching against object names rather than content. Combined with `-T`, it scans all accessible databases for tables whose names match the keyword. Combined with `-C`, it scans for column names. This is critical in large, unfamiliar schemas where manually reviewing dozens of table names is impractical. Searching for keywords like `user`, `account`, `pass`, or `token` quickly surfaces credential-related targets without needing a full schema dump first.

**Automatic Hash Detection and Cracking**

When SQLMap encounters values in dumped columns that match known hash formats, it automatically identifies the hash type and offers to crack them using its built-in dictionary. The cracking runs in parallel across all available CPU cores and uses a 1.4 million entry wordlist compiled from real password leak databases. This means credentials with weak or common passwords are frequently recovered in the same operation as the dump, without requiring a separate tool like hashcat or john.

**--passwords**

This flag targets the DBMS's own internal credential store rather than application tables. On MySQL, this retrieves password hashes from `mysql.user`. These are the database-level login credentials, distinct from application user credentials stored in custom tables. Cracking these yields direct database access independent of the web application, which is a significant privilege escalation path if the DBMS is network-accessible.

**--all --batch**

This combination instructs SQLMap to enumerate everything it can access: schema, users, databases, tables, columns, and row content, accepting all defaults without prompting. It is a comprehensive but time-consuming and noisy operation. The practical use case is thorough post-exploitation documentation when stealth is no longer a concern. Because the output volume is large, findings must be located manually in the output files afterward.


# Bypassing Web Application Protections 

**Anti-CSRF Token Bypass**

CSRF tokens are single-use values embedded in forms to prove the request originated from a legitimate page load. Automation tools fail because they reuse stale tokens. The `--csrf-token` flag tells SQLMap to re-fetch the target page before each request and extract a fresh token value by name. SQLMap also auto-detects parameters containing common infixes (`csrf`, `xsrf`, `token`) and prompts to handle them even without the flag being specified.

**Unique Value Randomization**

Some applications reject repeat requests with the same value in a tracking parameter to prevent replay and automation. The `--randomize` flag generates a new random value for the named parameter on every request, making each request appear unique from the application's perspective without altering the injection parameter itself.

**Calculated Parameter Bypass**

When an application computes a checksum or hash of one parameter and requires it to be submitted alongside (e.g., `h=MD5(id)`), any modification of `id` by SQLMap invalidates the hash and the server rejects the request. The `--eval` flag runs arbitrary Python code immediately before each request is sent, recalculating the dependent parameter dynamically so the integrity check passes even as SQLMap mutates the injection parameter.

**IP Concealment and Tor**

Direct IP blacklisting is a common WAF response after detecting automated scanning. `--proxy` routes all traffic through a single proxy. `--proxy-file` cycles through a list automatically when one proxy gets blocked. `--tor` uses the local Tor SOCKS proxy (port 9050 or 9150) to route through exit nodes, changing the apparent source IP across requests. `--check-tor` verifies Tor is actually working before proceeding, preventing accidental unmasked scanning.

**WAF Detection and --skip-waf**

SQLMap probes for WAF presence by sending an obviously malicious payload on a non-existent parameter at scan start. A 406 or similar anomalous response reveals WAF presence. The identYwaf library then attempts to fingerprint which WAF product is deployed based on its response signature. `--skip-waf` suppresses this initial probe to reduce noise when WAF presence is already known or stealth is prioritized.

**User-Agent Blacklisting**

SQLMap's default User-Agent string is a direct advertisement of the tool being used. Many WAFs and IDS signatures match on it explicitly. `--random-agent` replaces it with a value sampled from a pool of real browser User-Agent strings, eliminating this trivial detection vector.

**Tamper Scripts**

Tamper scripts intercept and transform payloads before transmission. They address WAF signature matching by changing the syntactic representation of SQL keywords without altering their semantics. For example, `between` eliminates `>` and `=` operators that WAF signatures commonly target. `randomcase` breaks keyword matching by making case inconsistent. `space2comment` substitutes spaces with comment sequences, defeating whitespace-based tokenization. Scripts are chained with `--tamper` and execute in priority order to avoid conflicts where one script's output would break another's logic.

**Chunked Transfer and HPP**

Both are payload fragmentation techniques. Chunked encoding splits the POST body at the TCP level so a complete SQL keyword never appears in a single inspectable chunk. HTTP Parameter Pollution splits the payload across repeated instances of the same parameter name, relying on the server-side concatenation behavior of certain platforms (notably ASP) to reassemble the full SQL string after it has passed through the WAF without triggering any single-chunk signature match.


# OS Exploitation 

**Prerequisite: DBA Privileges**

File read and write through SQLi depend heavily on DBMS privilege level. On MySQL, reading files requires the `LOAD DATA` and `INSERT` privileges, while writing requires `INTO OUTFILE` to be permitted and the `--secure-file-priv` restriction to be disabled. DBA-level access makes both significantly more likely. The `--is-dba` check should be run before attempting OS operations to understand what is realistically possible without wasting requests on operations that will be denied.

**--file-read**

SQLMap abstracts the underlying `LOAD DATA LOCAL INFILE` mechanism behind a single flag. It attempts to load the remote file content into a temporary table via the injection point, then retrieves it. The file is saved locally under the sqlmap output directory with the path encoded into the filename (e.g., `/etc/passwd` becomes `_etc_passwd`). SQLMap verifies success by comparing the retrieved file size against the remote file size.

**--file-write and --file-dest**

Writing files is more restricted than reading. When it succeeds, it enables writing a web shell directly into the web server document root. The combination of `--file-write` (local source) and `--file-dest` (remote destination path) uploads the file via the SQL injection channel. The PHP web shell payload `<?php system($_GET["cmd"]); ?>` is the minimal code needed for arbitrary command execution once uploaded.

**curl for Shell Interaction**

After writing the shell, direct HTTP requests with a `cmd` parameter drive command execution on the server. This confirms the file was written with correct permissions and is executable by the web server process.

**--os-shell**

This flag automates the entire shell deployment process: it writes a file stager and a backdoor to the remote server, then presents an interactive prompt. SQLMap tries to auto-detect the web server language and document root. On `--batch`, defaults (PHP, common webroot locations) are accepted automatically. When UNION-based technique fails to return output, switching to error-based (`--technique=E`) uses a different data retrieval channel that produces visible output in the error response, allowing the shell interaction to work correctly.

**UDF Escalation**

For `--os-shell` on MySQL, SQLMap can also deploy a User-Defined Function (UDF) shared library (`sys_exec`, `sys_eval`) that executes OS commands directly from within the DBMS rather than through a web shell. This path is used when writing to the webroot is not possible but the DBMS process has OS-level command execution capability. The UDF approach is more powerful but requires the DBMS to have write access to its plugin directory.

**Technique Selection for Shell Output**

The UNION technique retrieves data by appending results to the HTTP response body. When the response structure does not accommodate this cleanly, output is empty. Error-based retrieval forces data into DBMS error messages, which are returned in the response regardless of page structure, making it more reliable for OS shell interaction on targets where UNION output is suppressed or truncated.


# New/Missing Commands

**SQL Injection Fundamentals Code**

The unsafe PHP block is the canonical example of direct string concatenation into a query with no sanitization, the root cause of every injection in this module. The `mysqli_real_escape_string` version shows the inadequate mitigation that escapes special characters but still builds queries through concatenation, which is bypassable in some contexts. The regex filter demonstrates allowlist validation as a stronger defense, and the prepared statement block is the correct fix, separating SQL structure from user data entirely so no input value can ever alter query logic.

**DB User Privilege Commands**

The `CREATE USER` and `GRANT` sequence demonstrates principle of least privilege applied at the DBMS level. The verification commands (`SHOW TABLES`, `SELECT SCHEMA_NAME`, `SELECT * FROM credentials`) confirm what a restricted user can and cannot access, which maps directly to what SQLMap can enumerate when running under that privilege level.

**--no-cast**

This flag disables SQLMap's default behavior of wrapping retrieved values in CAST expressions for type safety. Some DBMS configurations or injection contexts reject or mishandle the cast syntax, causing retrieval failures. Disabling it falls back to raw value extraction and can resolve cases where `--dump` returns empty or corrupted results.

**Lab-specific Patterns and New Tricks Observed**

The case exercises introduce several patterns not explicitly shown in the core theory sections. Combining `--union-cols` with `--union-char=1` (integer fill instead of NULL) solves UNION injections where the query rejects NULL values due to type constraints. Using `--prefix='`)`with backtick-parenthesis combination handles queries wrapped in backtick-quoted identifiers. The`--randomize `flag with a typo (`--randomize==uid`) in one command still works because SQLMap parses the parameter name after the equals, but the correct form is` --randomize=uid`. The` --data-raw`flag combined with`-X POST`and a JSON Content-Type header is the correct pattern for submitting raw JSON bodies directly from the command line rather than via a request file. The skills assessment command against`action.php`with`--data-raw '{"id":1}'`and`--tamper=between` represents the full real-world chain: POST, JSON body, WAF bypass tamper, and targeted table dump in one command.
