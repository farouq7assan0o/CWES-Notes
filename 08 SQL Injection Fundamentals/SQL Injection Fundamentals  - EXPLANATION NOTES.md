# Introduction

**What SQL Injection Is**

SQL injection occurs when user-supplied input is embedded directly into a SQL query without proper sanitization, allowing an attacker to modify or replace the intended query logic. The attack targets relational databases, with MySQL being the focus of this module.

**How the Injection Entry Point Works**

The single quote and double quote characters are the fundamental injection primitives. They work by breaking out of the string context the developer intended, allowing raw SQL syntax to be appended directly to the query. This is the first step in any injection chain: escaping the input boundary.

**How Exploitation Chains Together**

Once outside the input boundary, the attacker must make the overall query syntactically valid while also achieving a new objective. This is typically done through Union-based injection (appending a second SELECT to the original) or stacked queries (terminating the original query and issuing a new one). The output then surfaces through the application's normal response rendering.

**Impact Scope**

The impact scales with database and server privilege levels. At minimum, injection enables unauthorized data reads (credentials, PII). With elevated privileges, it can subvert application logic (login bypass, access control bypass), and in high-privilege environments, enable direct file read/write on the back-end server, which is a path to full system compromise.

**Attacker Mindset**

The core mindset is: find where user input touches a query, test whether the input boundary can be escaped, then determine what the query structure allows. Prevention failures are usually either missing input sanitization or overprivileged database users, both of which are addressed in later sections of this module.

# Intro to Databases 

**Why This Section Matters for SQLi**

Before exploiting SQL injection, understanding what a DBMS is and how it sits in the application architecture clarifies where the attack surface lives. The database is the back-end store for credentials, content, and application data, which is exactly what SQLi targets.

**DBMS Types**

Multiple DBMS categories exist: file-based (legacy, slow at scale), Relational (RDBMS, the primary target of SQLi), NoSQL, Graph-based, and Key/Value stores. This module focuses on RDBMS, specifically MySQL, because SQL injection is a relational database attack that exploits structured query syntax.

**Key DBMS Features Relevant to Attackers**

Security controls in a DBMS are implemented through user authentication and permissions. When these are misconfigured or overprivileged, the impact of a successful SQLi escalates significantly. The reliability feature (backups and rollbacks) is relevant to defenders, not attackers, but understanding it contextualizes why databases are high-value targets.

**Two-Tier Architecture and the Attack Path**

Tier I is the client-facing layer (the web application). Tier II is the middleware that interprets user input and translates it into database queries using specific libraries and drivers. SQLi attacks target the boundary between Tier I and Tier II: malicious input from the user travels through the API or request layer, gets embedded into a query by the middleware, and is executed by the DBMS as legitimate SQL. The DBMS has no way to distinguish attacker-controlled SQL from developer-intended SQL once it arrives as a query.

**Hosting Implications**

When the application server and DBMS share the same host, a successful SQLi that escalates to file read/write or command execution can compromise both layers simultaneously. Separate hosting reduces blast radius but does not prevent the injection itself.



# Types of Databases 

**Relational vs Non-Relational - Why It Matters for Injection**

SQL injection exclusively targets relational databases because they use SQL as the query language. Non-relational (NoSQL) databases use entirely different communication methods and are vulnerable to a separate class of attacks called NoSQL injections, covered in a separate module.

**How Relational Databases Are Structured**

Relational databases organize data into tables (entities) with defined columns. Tables are linked to each other through keys. The example schema in this section, `users` and `posts`, illustrates a typical relationship: the `id` column in `users` maps to `user_id` in `posts`. This is exactly the kind of structure an attacker targets when enumerating a database through SQLi, because retrieving the `users` table yields credentials and PII.

**Why Schema Knowledge Matters for Exploitation**

Understanding that relational databases use schemas and linked tables informs the SQLi enumeration methodology. An attacker who knows table and column naming conventions (like `id`, `username`, `password`) can craft targeted UNION-based queries without prior knowledge of the specific application. Common table names like `users` are tried first precisely because developers follow predictable conventions.

**NoSQL Key-Value Model**

The JSON example shown represents how Key-Value NoSQL stores work. Each record has a unique key mapped to an object of fields. This structure is fundamentally different from SQL tables, which is why injection techniques do not transfer between the two database types. Python and PHP represent this same pattern as dictionary objects.

**Practical Scope for This Module**

MySQL is the relational database used throughout this module. All SQL syntax, injection payloads, and enumeration techniques covered going forward assume a MySQL back-end unless stated otherwise.


# Intro to MySQL

**MySQL CLI Flags**

The `mysql` utility is the primary command-line interface for interacting with MySQL and MariaDB. The `-u` flag supplies the username, `-p` prompts for the password interactively. Passing the password directly after `-p` with no space (e.g., `-ppassword`) works but is insecure because it gets written to shell history in cleartext. Remote connections require `-h` for the host and `-P` (uppercase) for the port. The lowercase `-p` is exclusively for passwords. Confusing the two is a common pitfall.

**Database and Table Creation Flow**

The logical sequence is: connect to MySQL, create a database with `CREATE DATABASE`, switch into it with `USE`, then create tables with `CREATE TABLE`. The `SHOW DATABASES` and `SHOW TABLES` commands verify state at each step. The `DESCRIBE` command reveals a table's column names and data types, which is directly useful during SQLi enumeration when trying to understand what data a table holds.

**Table Properties and Their Security Relevance**

`NOT NULL` prevents empty fields. `UNIQUE` prevents duplicate values in a column such as usernames. `AUTO_INCREMENT` on the `id` column means IDs are sequential integers starting at 1, which is predictable and useful when enumerating records through injection. `DEFAULT NOW()` auto-populates timestamps. `PRIMARY KEY` uniquely identifies each row and is the basis for foreign key relationships between tables.

**Why the Final CREATE TABLE Matters**

The fully-formed `logins` table with `id`, `username`, `password`, and `date_of_joining` is the canonical example table used throughout this module. Recognizing its structure is important because later injection exercises target exactly this kind of credentials table. Understanding column names and data types informs UNION-based payloads where column count and type must match the original query.

**SQL Case Sensitivity**

SQL statements themselves are case-insensitive (`USE` and `use` are equivalent), but database and table names are case-sensitive on Linux file systems. The convention of writing SQL keywords in uppercase is a readability practice, not a requirement, but following it avoids ambiguity when reading payloads.


# SQL Statements 

**INSERT**

`INSERT INTO` adds new rows to a table. The full-form syntax requires values for every column in order. The selective syntax names only the target columns, allowing columns with defaults (`id` via `AUTO_INCREMENT`, `date_of_joining` via `DEFAULT NOW()`) to be skipped. Attempting to skip a `NOT NULL` column with no default will produce an error. Multiple records can be inserted in a single statement by comma-separating the value tuples, which is more efficient than multiple individual inserts.

**SELECT**

`SELECT *` retrieves all columns from a table. Specifying column names instead of the wildcard limits output to those columns only. During SQLi exploitation, `SELECT` is the most important statement because it is the mechanism used to exfiltrate data. UNION-based injection works by appending a second `SELECT` to the original query and extracting data through the existing output channel.

**DROP**

`DROP TABLE` permanently removes a table and all its data with no confirmation prompt and no undo. It is irreversible. In an injection context, an attacker with sufficient privileges could use a stacked query to drop tables, causing data loss or denial of service.

**ALTER**

`ALTER TABLE` modifies the structure of an existing table rather than its data. The four sub-operations shown are `ADD` (new column), `RENAME COLUMN` (rename), `MODIFY` (change data type), and `DROP` (remove column). These are less commonly used in injection attacks but relevant to understanding how databases evolve and how privilege levels control structural access.

**UPDATE**

`UPDATE` modifies existing records in place based on a `WHERE` condition. Without a `WHERE` clause, every row in the table is updated, which is destructive. In the example, all passwords for `id > 1` are overwritten. From an attacker's perspective, an injectable `UPDATE` statement is dangerous because it allows mass modification of stored data, including overwriting credentials.

**Chain of Operations**

The statements in this section form the complete CRUD surface of SQL: `INSERT` (Create), `SELECT` (Read), `UPDATE` (Update), `DROP`/`ALTER` (Delete/Modify). SQLi can abuse any of these depending on where in the application the injection point occurs and what statement the vulnerable query is part of.

# Query Results

**ORDER BY**

`ORDER BY` sorts query results by a specified column. The default direction is ascending (`ASC`). Appending `DESC` reverses the order. Multiple columns can be specified as a comma-separated list, where the second column acts as a tiebreaker when the first column contains duplicate values. In an injection context, `ORDER BY` is also used during column count enumeration: incrementing the column number in `ORDER BY N` until an error occurs reveals how many columns the original query returns, which is a prerequisite for UNION-based injection.

**LIMIT and Offset**

`LIMIT N` restricts output to the first N rows. `LIMIT offset, count` skips the first `offset` rows (zero-indexed) and returns `count` rows from that point. This is directly relevant to injection because it allows an attacker to page through results one record at a time when output is restricted to a single row, or to target specific records by position.

**WHERE Clause**

`WHERE` filters rows based on a condition evaluated per row. Numeric comparisons use operators like `>`, `<`, `=`. String and date values must be wrapped in single or double quotes. The `WHERE` clause is the primary target of authentication bypass attacks, where an injected condition such as `OR 1=1` causes the clause to evaluate true for every row, returning all records regardless of the intended filter.

**LIKE Clause**

`LIKE` enables pattern matching within `WHERE` conditions. The `%` wildcard matches zero or more of any character. The `_` wildcard matches exactly one character. These are useful for enumeration when partial values are known. In an injection context, `LIKE` can be used to fingerprint data without knowing exact values, for example matching password hashes that begin with a known prefix.

**How These Chain Together**

A complete data extraction query in a real attack often combines all of these: `SELECT` specific columns `FROM` a target table `WHERE` a condition filters rows, `ORDER BY` a column for predictable output, and `LIMIT` with an offset to extract one record at a time. Understanding each clause individually makes complex UNION payloads easier to construct and reason about.

# SQL Operators 
**AND**

`AND` requires both conditions to be true for the overall expression to return true (1). If either condition is false, the result is 0. In MySQL, any non-zero value is treated as true and zero as false. This is critical for injection because authentication bypass payloads must carefully account for how `AND` chains conditions together.

**OR**

`OR` requires only one condition to be true to return true. This is the foundation of the most common authentication bypass payload: injecting `OR 1=1` causes the entire `WHERE` clause to evaluate as true for every row, bypassing credential checks entirely. The second query in this section demonstrates that both conditions being false is the only way `OR` returns false.

**NOT**

`NOT` inverts the boolean value of a condition. `NOT 1=1` evaluates to false (0); `NOT 1=2` evaluates to true (1). The `!` symbol is its equivalent. In injection, `NOT` is less commonly used offensively but is important to understand when reading or constructing complex payloads.

**Symbol Operators**

`&&`, `||`, and `!` are direct equivalents of `AND`, `OR`, and `NOT`. MySQL generates a warning when using `||` because it can conflict with string concatenation depending on SQL mode settings. In injection payloads, symbol operators are sometimes used as obfuscation or to bypass naive keyword filters that block `AND` and `OR` as words.

**Operator Precedence**

Precedence determines evaluation order when multiple operators appear in one query. Arithmetic (`* / % + -`) evaluates before comparisons (`= > < != LIKE`), which evaluates before `NOT`, then `AND`, then `OR`. This means `OR` is always evaluated last. In injection, this is exploited by crafting payloads where an injected `OR` condition encompasses the entire remaining `WHERE` clause, overriding all other conditions. Misunderstanding precedence is a common pitfall when constructing payloads that produce unexpected results.

**Practical Injection Relevance**

The combination of `OR`, `AND`, and precedence rules is the core of Boolean-based SQLi logic. A payload like `' OR 1=1 --` works because `OR 1=1` evaluates to true regardless of what precedes it, and the `--` comment discards any remaining conditions. Understanding why this works requires understanding that `OR` has the lowest precedence and that `1=1` is always true.

# Intro to SQL Injections 

**How Web Applications Create the Vulnerability**

The PHP code blocks show the full chain from database connection to vulnerable query construction. The application takes `$_POST['findUser']` and concatenates it directly into the SQL string with no sanitization. The resulting query treats whatever the user types as part of the SQL syntax, not as a safe string value. This is the root cause of every SQL injection shown in this module.

**What Injection Actually Means**

Injection occurs when user input escapes its intended string context and is interpreted as executable code. The single quote `'` is the primary escape character in SQL string contexts. When the input `1'` is placed inside `'%$searchInput'`, the quote closes the string prematurely, and everything after it is interpreted as raw SQL.

**The Stacked Query Example and Its Limitation**

The `DROP TABLE` example is intentionally simplified to illustrate the concept. It would produce a syntax error because of the trailing unmatched quote, and more importantly, stacked queries using `;` do not work in MySQL. They work in MSSQL and PostgreSQL. MySQL requires different techniques such as UNION-based injection or comment-based injection to achieve multi-statement effects, which are covered in subsequent sections.

**Syntax Errors as a Signal**

A syntax error returned by the application is a positive indicator that injection is occurring. It confirms that the injected character broke the query structure and that the application is not sanitizing input. The presence of an error is more useful to an attacker than a clean failure, because it reveals that the input is reaching and affecting the SQL layer.

**SQL Injection Type Taxonomy**

The classification matters because it determines the exploitation technique. In-band injections (Union and Error based) return output directly in the HTTP response and are the easiest to exploit. Blind injections (Boolean and Time based) return no direct output and require inferring data one bit or character at a time, making them slower. Out-of-band injection is used when there is no observable response channel at all and requires the database to initiate an outbound connection. This module focuses exclusively on Union-based injection.

# Subverting Query Logic 

**Goal of This Attack**

The objective is to bypass authentication without knowing valid credentials. This is achieved by injecting SQL logic into the login form fields so the `WHERE` clause always evaluates to true, causing the database to return a row and the application to grant access.

**SQLi Discovery**

Before attempting bypass, the form must be confirmed as injectable. Inserting a single quote `'` introduces an odd number of quotes into the query, breaking SQL syntax and producing an error. A visible SQL error or a change in page behavior confirms that user input is reaching the SQL layer unsanitized. URL-encoded versions of these characters are used when injecting via GET parameters in the URL directly.

**Operator Precedence is the Key**

`AND` evaluates before `OR`. This means `condition1 OR condition2 AND condition3` is parsed as `condition1 OR (condition2 AND condition3)`. An attacker exploits this by ensuring an `OR` branch resolves to true, which short-circuits the entire `WHERE` clause to true regardless of what `AND` evaluates to.

**Known Username Bypass**

Injecting `admin' or '1'='1` into the username field produces a query where `username='admin'` is one branch of an `OR`. If the admin user exists, that branch is true, and `OR` returns true for the whole clause. The password check becomes irrelevant because the `OR` precedes it in precedence after `AND` resolves the password side to false.

**Unknown Username Bypass**

When no valid username is known, the `OR` in the username field alone is insufficient because `username='notAdmin'` is false and `'1'='1' AND password='something'` is also false. The solution is to inject `OR '1'='1` into the password field as well, ensuring at least one `OR` branch is unconditionally true. The `WHERE` clause then returns all rows and the first user in the table is logged in.

**Full Bypass with No Credentials**

Using `' or '1'='1` as the username (with any password combined with `or '1'='1`) produces a `WHERE` clause that is true for every row. The first row returned by the database becomes the authenticated user. This works regardless of what usernames or passwords exist in the table.

**Quote Balance**

A common pitfall is introducing an unmatched quote that causes a syntax error. The payload `' or '1'='1` is constructed so that the injected single quote closes the original string, and the final `'1` re-opens a string that is closed by the original query's trailing quote. This keeps the total number of quotes balanced and the query syntactically valid.

# Using Comments 

**How SQL Comments Work**

SQL comments cause the database engine to ignore everything that follows them on the same line. MySQL supports two line comment styles: `--` (requires a trailing space, written as `-- -` for clarity) and `#`. The inline comment `/**/` exists but is rarely used in basic injection. In URLs, `#` must be percent-encoded as `%23` because browsers treat bare `#` as a fragment identifier and strip it before the request is sent. Similarly, `--+` is used in URLs because `+` decodes to a space, satisfying the space requirement after `--`.

**Basic Comment Auth Bypass**

Injecting `admin'--` into the username field closes the username string with `'`, then comments out the rest of the query including the `AND password = ...` clause. The database only evaluates `WHERE username='admin'`, and if that row exists the login succeeds. No password knowledge is required. This works in any query where the password check follows the username in the same `WHERE` clause.

**Why Quote Balance Matters**

The comment discards the trailing `'` that originally closed the username string in the query. This is intentional: the injected `'` closes the string, the comment eats the original closing `'` along with everything else after it. The result is a syntactically valid query with no dangling quotes.

**Parenthesis Edge Case**

When the application wraps conditions in parentheses, a comment alone is insufficient. Injecting `admin'--` leaves an unclosed `(` before the username, producing a syntax error. The fix is to inject `admin')--`, which closes both the string and the parenthesis before the comment discards the rest. The resulting query becomes `WHERE (username='admin')`, which is complete and valid.

**Practical Mindset**

When a comment-based bypass produces a syntax error, it means the query structure around the injection point has additional elements such as parentheses, additional conditions, or joins that need to be accounted for. The approach is to read the error, infer the surrounding query structure, and adjust the payload to close all open delimiters before the comment. Visible query output in the application (as shown in this lab) makes this trivial. In black-box scenarios, the error message itself often reveals enough of the query structure to reconstruct it.

# Union Clause 

**What UNION Does**

`UNION` appends the results of a second `SELECT` statement to the first. The combined output is returned as a single result set. In injection, this means an attacker can append a completely attacker-controlled `SELECT` query to whatever the application originally runs, routing arbitrary database content through the existing output channel on the page.

**The Equal Column Count Requirement**

Both `SELECT` statements joined by `UNION` must return the same number of columns. A mismatch produces an error. This is the first constraint an attacker must satisfy before UNION injection can work. The number of columns in the original query must be determined before a valid UNION payload can be constructed.

**Data Type Matching**

Beyond column count, the data types of corresponding columns must be compatible. A string value cannot be placed in a column position that expects an integer. The practical workaround is to use numeric junk values (1, 2, 3...) for filler columns, since numbers are broadly type-compatible. `NULL` is the most flexible option because it is compatible with every data type, making it the preferred filler in advanced scenarios.

**Junk Fill Strategy**

When only one or a few columns of data are needed but the original query selects more, the remaining column positions are filled with constants (numbers or strings). These constants always return the same value and have no effect on the target table being queried. Using sequential numbers (1, 2, 3, 4) as fillers is useful because they appear in the output and act as positional markers, making it easy to identify which column position is rendered on the page.

**Why Column Position Matters**

Not all columns returned by a query are necessarily displayed on the page. An application might only render the first column, or only a specific field. By filling columns with identifiable numbers, an attacker can see which positions appear in the output and target the data extraction to those positions. This is the setup for the column enumeration technique covered in the next section.

**Injection Point Context**

The example query `SELECT * from products where product_id = 'user_input'` shows a typical string-context injection point. The `'1'` closes the original string, the `UNION SELECT` appends the attacker's query, and `-- '` comments out the trailing quote from the original query. This three-part structure (close, inject, comment) is the standard UNION injection template.

# Union Injection 

**Workflow Overview**

Union injection requires three steps before data can be extracted: confirm the injection point, determine the column count, then identify which columns are rendered in the page output. This section covers all three.

**Confirming Injection**

Injecting a single quote into the `port_code` parameter breaks the SQL string context and produces a visible syntax error. This confirms the parameter is injectable and that error output is visible, making Union-based injection viable.

**Column Count - ORDER BY Method**

`ORDER BY N` sorts results by the Nth column. Incrementing N until the database returns an error (unknown column) reveals the total column count. The last value that succeeded is the column count. This method starts from a working state and fails upward. In this lab, `ORDER BY 4` succeeds and `ORDER BY 5` fails, confirming 4 columns.

**Column Count - UNION Method**

Alternatively, inject `UNION SELECT 1,2,3...` with increasing numbers of columns until the query succeeds instead of erroring. This method starts from a failing state and succeeds upward. Both methods arrive at the same answer; ORDER BY is generally faster because it avoids trial-and-error on the UNION syntax itself.

**Identifying Visible Column Positions**

Even after determining column count, not all columns are necessarily rendered on the page. The application in this lab renders columns 2, 3, and 4 but not column 1. Using sequential integers as junk data makes this immediately visible: whichever numbers appear in the page output indicate the usable injection positions. Column 1 is invisible and cannot be used to exfiltrate data.

**Placing Real Data in a Visible Column**

Once a visible column position is confirmed, replace its junk integer with the actual query to extract. Replacing `2` with `@@version` in position 2 returns the database version string in the output. This confirms end-to-end UNION injection is working and data from the database is reaching the page. From this point, any SQL expression or subquery can be placed in that position to extract arbitrary data.



# Database Enumeration 

**MySQL Fingerprinting**

Before enumerating, confirming the DBMS type ensures the correct syntax is used. `@@version` returns a version string that identifies MySQL or MariaDB when full output is available. `POW(1,1)` is used when only numeric output is returned. `SLEEP(5)` is used in blind scenarios where no output is visible but a time delay can be observed. Apache/Nginx on the server suggests Linux and MySQL; IIS suggests Windows and MSSQL, though these are only heuristics.

**INFORMATION_SCHEMA as the Enumeration Foundation**

`INFORMATION_SCHEMA` is a built-in MySQL metadata database present on every server. It stores the names of all databases, tables, and columns across the entire DBMS. It cannot be queried with simple table names alone because it is a separate database. The dot operator (`database.table`) is required to reference it from within a different active database.

**Enumeration Chain**

The enumeration follows a strict top-down hierarchy: databases first, then tables within the target database, then columns within the target table, then the data itself. Skipping any step leaves gaps in the payload construction. The `WHERE` clause at each step filters results to only the relevant database or table, preventing output from being flooded with unrelated entries from the entire DBMS.

**SCHEMATA - Listing Databases**

`INFORMATION_SCHEMA.SCHEMATA` lists every database on the server via the `SCHEMA_NAME` column. The three default MySQL databases (`mysql`, `information_schema`, `performance_schema`) and sometimes `sys` are present on every server and are ignored. Non-default databases like `ilfreight` and `dev` are the actual targets. `database()` returns the name of the currently active database, which identifies which database the application is using.

**TABLES - Listing Tables**

`INFORMATION_SCHEMA.TABLES` maps every table to its parent database via `TABLE_NAME` and `TABLE_SCHEMA`. Filtering with `WHERE table_schema='dev'` isolates only the tables belonging to the target database. The `credentials` table name is immediately high value because it suggests stored authentication data.

**COLUMNS - Listing Columns**

`INFORMATION_SCHEMA.COLUMNS` maps every column to its parent table and database. Filtering with `WHERE table_name='credentials'` reveals the exact column names needed to construct the final data dump query. Without this step, column names would have to be guessed.

**Final Data Dump**

With the database (`dev`), table (`credentials`), and columns (`username`, `password`) known, the final UNION payload places those column names in the visible output positions. The dot operator `dev.credentials` is required because the active database is `ilfreight`, not `dev`. The output returns all rows including password hashes and API keys.

# Reading Files 

**Why Privileges Must Be Checked First**

File read operations require the MySQL `FILE` privilege. Without it, `LOAD_FILE()` returns NULL silently. Before attempting file reads, the DB user identity and privilege set must be confirmed. Running as `root` with `super_priv = Y` is the ideal scenario because it strongly implies `FILE` is also granted. Checking `information_schema.user_privileges` gives the definitive list.

**DB User Enumeration**

Three equivalent methods exist to retrieve the current DB user: `USER()`, `CURRENT_USER()`, and selecting the `user` column from `mysql.user`. All three can be placed in a UNION payload. The result `root@localhost` indicates the web application is running database queries as the MySQL root account, which is a misconfiguration and substantially increases the attacker's capabilities.

**super_priv Check**

`super_priv = Y` in `mysql.user` confirms superuser status. The `WHERE user="root"` filter is added when multiple users exist in the DBMS to avoid returning rows for other accounts. Superuser status in MySQL implies nearly all privileges including `FILE`.

**information_schema.user_privileges**

This table provides a granular view of every privilege assigned to every user. Filtering by `grantee="'root'@'localhost'"` (note the nested quotes matching the stored format) isolates only the current user's privileges. The presence of `FILE` in the output directly confirms that `LOAD_FILE()` will function.

**LOAD_FILE() Mechanics**

`LOAD_FILE()` reads a file from the server's filesystem and returns its contents as a string. It takes a single absolute path argument. The OS user running the MySQL process must have read permission on the target file. Placing `LOAD_FILE()` in a visible UNION column position routes the file contents through the existing page output. Files rendered inside a browser may be interpreted as HTML, requiring view-source to read the raw content.

**Source Code Disclosure Impact**

Reading `/var/www/html/search.php` retrieves the application's PHP source. This can expose database connection credentials (hostname, username, password, database name) hardcoded in the PHP, reveal other injection points, expose business logic, or disclose internal paths and file structure. Source code disclosure often chains directly into further exploitation steps.

**Attacker Mindset**

The sequence is: confirm DB user identity, confirm FILE privilege, then read sensitive files in escalating order of value: `/etc/passwd` for user enumeration, web application source files for credentials and logic, configuration files for further pivot. The Apache default webroot `/var/www/html` is the starting assumption for file path construction when the web server technology is known.


# Writing Files 

**Three Prerequisites for File Write**

File writing through MySQL injection requires all three conditions simultaneously: the `FILE` privilege on the DB user (confirmed in the previous section), `secure_file_priv` set to empty (not NULL, not a restricted path), and OS-level write permission on the target directory. Failing any one of these silently prevents the write with no error.

**secure_file_priv Behavior**

This global variable controls where MySQL is permitted to read and write files. An empty value means unrestricted access across the entire filesystem. A path value restricts operations to that directory only. A NULL value completely disables file operations. MariaDB defaults to empty; MySQL defaults to `/var/lib/mysql-files`, which is outside the web root and therefore useless for web shell delivery. Modern MySQL configurations may default to NULL, making file writes impossible regardless of privileges.

**Querying secure_file_priv via UNION**

`SHOW VARIABLES` cannot be used inside a UNION injection because it is not a `SELECT` statement. The workaround is to query `information_schema.global_variables` where all MySQL global variables are stored as rows with `variable_name` and `variable_value` columns. Filtering with `WHERE variable_name="secure_file_priv"` returns just the one row needed.

**SELECT INTO OUTFILE**

`INTO OUTFILE 'path'` appended to any `SELECT` statement writes the query result to the specified file on the server's filesystem. Arbitrary strings can be written by selecting them as literal values rather than from a table. The file is created and owned by the OS user running MySQL (typically `mysql`). The file must not already exist or the write will fail.

**Proof File Strategy**

Before writing a web shell, writing a harmless test string to the webroot confirms two things: that `INTO OUTFILE` is working and that the MySQL user has write permission to the web directory. Accessing the file over HTTP confirms the path is correct and within the webroot. This avoids wasting attempts on incorrect paths when writing the actual payload.

**Junk Column Values in OUTFILE**

When using UNION injection with `INTO OUTFILE`, all selected column values are written to the file, including junk filler numbers. Using empty strings `""` instead of numbers as fillers produces a cleaner output file, which matters when the file must be valid PHP code. Extraneous numbers before or after the PHP tag would not break execution in most cases, but clean output is better practice.

**Web Shell and RCE**

The PHP payload `<?php system($_REQUEST[0]); ?>` executes any OS command passed via the `0` GET or POST parameter. Accessing `shell.php?0=id` confirms remote code execution as the web server user (`www-data` in this case). From this point the attacker has interactive command execution on the server, which can be used to escalate privileges, pivot, or establish a persistent reverse shell.

**Finding the Web Root**

If the webroot path is unknown, it can be inferred by reading server configuration files with `LOAD_FILE()`, fuzzing with SecLists wordlists, or observing error messages that leak filesystem paths. Apache's config is at `/etc/apache2/apache2.conf`, Nginx at `/etc/nginx/nginx.conf`, and IIS at the Windows path shown above.

# Mitigating SQL Injection 

**Input Sanitization**

`mysqli_real_escape_string()` escapes special characters including single and double quotes before they are placed into the SQL string. This prevents the injected character from breaking out of the string context. The PostgreSQL equivalent is `pg_escape_string()`. Sanitization is a surface-level defense: it addresses the symptom (unescaped characters) rather than the root cause (string concatenation), and can sometimes be bypassed with non-standard encodings or edge cases.

**Input Validation**

Validation enforces a whitelist of allowed characters using `preg_match()`. The regex `/^[A-Za-z\s]+$/` permits only letters and spaces, rejecting any input containing quotes, semicolons, dashes, or other injection characters. The script terminates immediately on rejection. This is a stronger defense than sanitization for fields with predictable input formats, because it rejects entire classes of malicious input before they ever reach the query construction step.

**User Privileges - Principle of Least Privilege**

The database user used by the web application should have only the permissions necessary for that application's function. Creating a `reader` user with `SELECT` only on the specific table means that even a fully successful injection cannot `DROP` tables, write files, or access other databases. The verification queries demonstrate this: the restricted user can query `ports` but is denied access to `credentials`. Running web applications as MySQL root (as seen throughout this module's lab) is a critical misconfiguration.

**Web Application Firewall**

A WAF sits in front of the application and inspects HTTP requests for known attack patterns. It can block requests containing strings like `INFORMATION_SCHEMA`, `UNION SELECT`, or `SLEEP()`. WAFs are a compensating control, not a fix for vulnerable code. They can often be bypassed through encoding, case variation, or comment insertion, but they raise the cost and complexity of exploitation significantly.

**Parameterized Queries**

Parameterized queries (prepared statements) are the most robust defense. The SQL query structure is defined first with `?` placeholders, and then user input is bound to those placeholders via `mysqli_stmt_bind_param()`. The database driver handles escaping and type enforcement. Critically, the user input is never concatenated into the query string at any point. Even if an attacker supplies a quote or SQL keyword, it is treated as a data value and cannot alter the query structure. This addresses the root cause rather than the symptom.

**Defense in Depth**

No single mitigation is sufficient alone. The recommended approach combines all layers: parameterized queries as the primary defense, input validation to enforce expected formats, least-privilege DB users to limit blast radius, and a WAF as an additional detection and blocking layer. Each layer independently reduces risk, and together they make exploitation practically infeasible even when one layer has a gap.























# SQL Injection Fundamentals 

**Introduction**

Modern web applications rely on databases to dynamically generate content and manage data such as users, posts, and application state. User HTTP(S) requests are processed by the application layer, which constructs SQL queries to interact with the database. When user-controlled input is embedded into these queries without proper handling, it opens the door to SQL injection. This commonly occurs in a three-tier architecture where the client interacts with an application server, which then communicates with a DBMS.

**SQL Injection (SQLi)**

SQL injection is a class of injection vulnerability affecting relational databases (e.g., MySQL). It occurs when an attacker manipulates user input so that it alters the structure or logic of the SQL query sent to the database. The first step is breaking out of the intended input context, often by injecting quote characters to escape string boundaries. Once injection is possible, attackers can modify the original query or append new logic using techniques such as UNION queries or stacked queries. The attacker then relies on the application’s response behavior to observe or extract the results of the injected query.

**Use Cases and Impact**

The impact of SQL injection can be severe. Attackers may extract sensitive data such as usernames, passwords, and financial information, leading to breaches and credential reuse attacks. SQLi can also be used to bypass authentication, escalate privileges, or access restricted functionality like admin panels. In cases where database or server privileges are misconfigured, attackers may read or write files on the back-end server, implant backdoors, and potentially gain full control over the application and underlying system.

**Prevention**

SQL injection vulnerabilities stem from insecure coding practices and excessive database privileges. Preventive measures focus on proper input handling, including sanitization and validation, as well as secure coding patterns such as parameterized queries. Restricting database user privileges limits the damage if an injection does occur. These mitigation strategies are covered in later sections of the module.

# SQL Injection Fundamentals

**Intro to Databases**

This section establishes the role of databases in web applications, explaining that back-end databases store application assets, content, and user data. It frames SQL as the language used to query these databases and highlights why understanding database fundamentals is required before learning SQL injection. The transition from slow file-based storage to DBMS is introduced as a scalability and performance necessity.

**Database Management Systems**

This section defines a DBMS as the software layer responsible for creating, managing, and interacting with databases. It outlines different DBMS models (file-based, relational, NoSQL, graph, key/value) and emphasizes why RDBMSs are common in web applications. The listed features explain how DBMSs handle real-world requirements such as simultaneous users, data integrity, access control, recovery, and standardized querying via SQL.

**Architecture**

This section explains the multi-tier architecture used by most database-backed applications. It clarifies how client-side applications (Tier I) send user-driven events to middleware (Tier II), which translates them into database queries. The DBMS executes these queries and returns results or errors. The explanation highlights where SQL injection typically occurs: at the boundary where user input is transformed into database queries. It also notes deployment choices, such as separating the application server and database for performance and scalability.

# Types of Databases 

**Types of Databases**

This section introduces the high-level classification of databases into relational and non-relational systems. It establishes that SQL is exclusive to relational databases, while non-relational databases use alternative query and interaction models. This distinction is critical for understanding why SQL injection applies only to certain back-end technologies.

**Relational Databases**

This section explains relational databases as schema-driven systems that store data in tables composed of rows and columns. It describes how entities are linked through keys, allowing structured relationships between data sets such as users, posts, orders, and products. The explanation emphasizes how relational integrity enables predictable updates, efficient querying, and centralized data management. It also introduces RDBMSs as the mechanism that enforces these relationships and highlights MySQL as the primary relational database used throughout the module.

**Non-relational Databases**

This section explains NoSQL databases as schema-less systems designed for flexibility and scalability. It describes how data is stored using alternative models such as key-value, document-based, wide-column, and graph structures. The JSON example demonstrates how records are stored as nested objects rather than rows in tables. The section also clearly differentiates SQL injection from NoSQL injection, noting that NoSQL systems have their own distinct injection techniques that are addressed separately in later material.
# Intro to MySQL

**Structured Query Language (SQL)**

This section introduces SQL as the standardized language used to interact with relational databases. It explains that while SQL syntax can vary slightly between RDBMS implementations, all follow the ISO standard. The section outlines the core capabilities of SQL, such as querying data, modifying records, managing schemas, and controlling users and permissions, which are foundational for understanding how SQL injection manipulates queries.

**Command Line**

This section explains how to interact with MySQL/MariaDB using the `mysql` command-line client. It describes authentication using usernames and passwords, the security implications of passing passwords directly on the command line, and how to specify remote hosts and ports. It also introduces privilege concepts, noting that different users have different levels of access, which directly affects SQL injection impact.

**Creating a Database**

This section demonstrates how to create and manage databases once authenticated to the DBMS. It explains how SQL statements are terminated, how databases are enumerated, and how the active database context is selected. It also clarifies SQL case sensitivity rules, distinguishing between SQL keywords and database object names.

**Tables**

This section explains how data is stored in tables composed of rows and columns. It introduces column data types and shows how table schemas define what data can be stored. The section demonstrates creating a table, enumerating tables in a database, and inspecting table structure, reinforcing how structured schemas influence query construction and injection opportunities.

**Table Properties**

This section explains common table and column constraints that enforce data integrity, such as auto-incrementing identifiers, required fields, uniqueness constraints, default values, and primary keys. It highlights how these properties define record identity and relationships, which is critical for understanding how attackers target specific columns or records during SQL injection attacks.

# SQL Statements 

**INSERT Statement**

This section explains how new records are added to database tables. It shows both full-row inserts and selective column inserts, highlighting how default values and constraints affect insertion behavior. The examples demonstrate single-row and multi-row inserts, which is important for understanding how attackers may inject or manipulate INSERT queries.

**SELECT Statement**

This section introduces data retrieval using SELECT queries. It explains wildcard selection versus column-specific selection and demonstrates how database contents are exposed through query results. SELECT statements are foundational to SQL injection, as attackers often manipulate them to extract sensitive data.

**DROP Statement**

This section explains how entire tables or databases can be permanently removed. It emphasizes the destructive nature of DROP and why it requires high privileges. From an attacker’s perspective, this highlights the impact of SQL injection when elevated permissions are available.

**ALTER Statement**

This section explains how table structures can be modified after creation. It covers adding, renaming, modifying, and deleting columns. These operations demonstrate how schema manipulation is possible when sufficient privileges exist, which can significantly increase the impact of a successful SQL injection.

**UPDATE Statement**

This section explains how existing records can be modified conditionally. It emphasizes the importance of the WHERE clause in limiting affected rows. UPDATE queries are commonly abused in SQL injection to overwrite credentials, reset passwords, or alter application logic by changing stored values.

# Query Results 

**Sorting Results**

This section explains how query output order can be controlled using `ORDER BY`. It covers default ascending order, explicit ascending and descending sorting, and multi-column sorting to resolve duplicate values. These mechanics are important in SQL injection for enumerating data predictably and identifying hidden or sensitive records.

**LIMIT Results**

This section explains how `LIMIT` restricts the number of returned rows and how offsets can be used to paginate through result sets. In SQL injection, this is commonly used to enumerate database contents row-by-row when full output is not directly visible.

**WHERE Clause**

This section explains conditional filtering of query results using `WHERE`. It shows numeric and string comparisons and highlights proper quoting rules for data types. The `WHERE` clause is a primary target in SQL injection, as attackers often manipulate or bypass conditions to access unauthorized data.

**LIKE Clause**

This section explains pattern matching using `LIKE`, `%`, and `_`. It demonstrates how partial matches and fixed-length matches work. In SQL injection, `LIKE` is frequently abused for blind enumeration of values when exact matches are unknown.
# SQL Operators 

**AND Operator**

This section explains how multiple conditions can be combined so that all expressions must evaluate to true. It clarifies MySQL’s boolean behavior, where non-zero values evaluate to true and zero evaluates to false. This operator is critical in SQL injection for chaining conditions and crafting precise logic-based payloads.

**OR Operator**

This section explains how conditions can be combined such that only one needs to evaluate to true. OR is frequently abused in SQL injection to bypass authentication checks or conditional logic by forcing expressions to evaluate as true.

**NOT Operator**

This section explains logical negation, where boolean results are inverted. NOT is often used in injection scenarios to reverse logic checks, such as bypassing filters or negating validation conditions.

**Symbol Operators**

This section shows the symbolic equivalents of logical operators (`&&`, `||`, `!`, `!=`). These alternatives are important in SQL injection when keyword-based filters are in place, as symbol operators can sometimes bypass input validation or WAF rules.

**Operators in Queries**

This section demonstrates how logical and comparison operators are applied within real SELECT queries. It highlights how combining conditions refines result sets, which mirrors how attackers manipulate WHERE clauses to extract or exclude specific records.

**Multiple Operator Precedence**

This section explains how MySQL evaluates complex expressions based on operator precedence. Understanding evaluation order is essential in SQL injection, as subtle changes in arithmetic or logical grouping can drastically alter query behavior and results.
# Intro to SQL Injections 

**Use of SQL in Web Applications**

This section explains how web applications interact with databases through server-side code, using PHP and MySQL as an example. It shows how SQL queries are constructed and executed inside application logic, and how query results are processed and rendered to users. This establishes where user input enters the database interaction flow.

**What is an Injection**

This section defines injection as a condition where user input is interpreted as executable code rather than data. It explains sanitization as a defensive mechanism and clarifies how special characters like quotes can break out of expected input boundaries, altering program execution.

**SQL Injection**

This section explains SQL injection as a specific form of injection where unsanitized user input is embedded directly into SQL query strings. It walks through how normal input is handled versus how malicious input can terminate the original query context and inject new SQL logic. It also notes DBMS-specific behavior, such as MySQL’s restriction on stacked queries.

**Syntax Errors**

This section explains why malformed injections cause SQL syntax errors and why attackers must ensure the final query remains syntactically valid. It introduces the need for techniques such as comment usage or quote balancing to successfully exploit SQL injection without access to source code.

**Types of SQL Injections**

This section categorizes SQL injection techniques based on how query output is retrieved. It distinguishes between in-band, blind, and out-of-band SQL injection, and further breaks them down into union-based, error-based, boolean-based, and time-based approaches. It clarifies that the module will focus on union-based SQL injection as an introductory exploitation method.

![[Pasted image 20260126005717.png]]
# Subverting Query Logic 

**Authentication Bypass**

This section demonstrates how login systems typically validate users by checking whether a SQL query returns any rows. If a matching username and password exist, authentication succeeds. The logic depends entirely on the boolean result of the SQL `WHERE` clause.

**SQLi Discovery**

Here, basic characters such as quotes and parentheses are injected to test whether user input is interpreted as SQL syntax. Triggering SQL errors instead of normal login failures confirms that the input is being inserted unsafely into a SQL query.

**OR Injection**

This section introduces logic manipulation using the `OR` operator. Because `AND` is evaluated before `OR`, an injected condition that always evaluates to true can override failed authentication checks. The payload carefully balances quotes to preserve valid SQL syntax.

**Auth Bypass with OR Operator**

By injecting an always-true condition into either the username or password field, the `WHERE` clause evaluates to true for at least one row. If no specific user is targeted, the database returns all rows and the application logs in the first user in the result set.

**Attacker Mindset**

The attacker focuses on forcing a true condition rather than guessing credentials. Understanding operator precedence and query structure allows authentication to be bypassed without knowing valid usernames or passwords.
# Using Comments 

**Comments**

This section explains how SQL comments can be used to ignore parts of a query during execution. MySQL supports `--` and `#` as line comments, allowing attackers to truncate the original query and remove security checks such as password validation. The requirement for a trailing space after `--` is critical for successful exploitation.

**Auth Bypass with Comments**

By injecting a comment after a valid condition, the rest of the SQL query is ignored. This allows authentication checks that rely on additional conditions (like password matching) to be bypassed entirely. The database evaluates only the remaining valid portion of the query.

**Parenthesis and Advanced Logic**

This section demonstrates how applications may use parentheses to enforce stricter logic and prevent simple bypasses. When parentheses are involved, attackers must balance them correctly to avoid syntax errors. Closing the open parenthesis before commenting out the rest of the query restores valid SQL syntax and allows successful authentication bypass.

**Attacker Mindset**

The attacker adapts payloads based on query structure, ensuring syntax validity while neutralizing security conditions. Comments are a powerful tool for removing unwanted logic without needing to satisfy it, especially when combined with operator precedence and parenthesis handling.

# Union Clause 

**Union**

This section introduces the SQL `UNION` clause as a way to combine the output of multiple `SELECT` statements into a single result set. For SQL injection, this is important because it enables extracting data from other tables/databases by appending an attacker-controlled `SELECT` to the original query.

**Even Columns**

This section explains the key constraint for using `UNION`: both `SELECT` statements must return the same number of columns, and corresponding columns should be compatible in data type. If the column counts differ, MySQL throws an error like `ERROR 1222`, which is commonly used by attackers to infer the correct column count during exploitation.

**Un-even Columns**

This section explains the attacker workaround when they want fewer “real” columns than the original query returns. The attacker pads the remaining required columns with filler values (“junk”), numbers, or `NULL` to satisfy the column count requirement. Using numbers is convenient because it also helps track which output column is being reflected on the page (useful later for finding the injectable/visible column).

# Union Injection 

**Union Injection Overview**

This section introduces Union-based SQL injection, where an attacker appends a second `SELECT` statement using the `UNION` clause to retrieve arbitrary data from the database. This technique is powerful because it allows direct extraction of database metadata and table contents when query output is reflected on the page.

**Detecting Number of Columns**

Before using `UNION`, the attacker must identify how many columns the original query returns. This can be done using `ORDER BY` (incrementing column indexes until an error occurs) or by testing `UNION SELECT` statements with increasing column counts until the error disappears. The highest successful index or column count reveals the correct number.

**Finding Visible Columns**

Not all returned columns are rendered by the web application. By injecting numeric placeholders (`1,2,3,4`), the attacker can observe which values appear in the response and determine which column positions are visible. Only those columns can be used to display extracted data.

**Placing the Payload**

Once a visible column is identified, the attacker replaces the placeholder value in that position with a SQL function or column name. Using functions like `@@version` or `user()` confirms successful data extraction and reveals database context such as version and execution user.

**Attacker Mindset**

Union injection is about precision: matching column counts, respecting data types, and placing payloads only in reflected columns. Numbers and simple SQL functions are used first to map behavior before attempting full database enumeration.
# Database Enumeration 

**MySQL Fingerprinting**

This section shows quick ways to identify the DBMS so you can use the correct syntax and functions. `@@version` works when output is visible. `POW(1,1)` is useful when only numeric output is reliable. `SLEEP(5)` is used when there’s no visible output, relying on response delay as confirmation.

**INFORMATION_SCHEMA Database**

This section introduces `INFORMATION_SCHEMA` as the metadata database that describes all databases, tables, and columns on the server. Since it’s a separate database, its tables must be referenced explicitly (or via the dot operator when querying objects in non-current databases).

**SCHEMATA**

This section shows how to enumerate database names via `INFORMATION_SCHEMA.SCHEMATA` using `SCHEMA_NAME`. Once database names are known, `database()` can be used to identify the current database context the application is running in.

**TABLES**

This section shows how to enumerate tables within a target database using `INFORMATION_SCHEMA.TABLES`, filtering on `table_schema` to avoid dumping every table on the server. This step identifies interesting tables (e.g., `credentials`) to target next.

**COLUMNS**

This section shows how to enumerate column names for a target table using `INFORMATION_SCHEMA.COLUMNS`. Knowing the exact column names is required to craft a valid `UNION SELECT` that pulls real data.

**Data**

This section demonstrates dumping actual contents from a target table by selecting its columns directly (e.g., `username`, `password`) and referencing it with the database-qualified name (e.g., `dev.credentials`) to avoid ambiguity when the current database differs.
# Reading Files

**Privileges**

This section explains that file read/write via SQLi depends on DB privileges, especially the MySQL `FILE` privilege, and also on the OS permissions of the user running the DBMS. Reading files is more common; writing files is more dangerous and typically restricted.

**DB User**

This section shows how to identify the active database user. Knowing the DB user helps estimate privilege level (e.g., `root@localhost` is often highly privileged), which informs whether file operations may be possible.

**User Privileges**

This section demonstrates checking for high-level privileges (like `super_priv`) and enumerating all granted privileges via `information_schema.user_privileges`. Filtering by the current `grantee` allows focusing on the privileges of the active user. The presence of `FILE` indicates potential local file read/write capabilities through SQL.

**LOAD_FILE**

This section introduces `LOAD_FILE()` as the MySQL/MariaDB function used to read files from disk. It highlights two requirements: the DB user must have the needed privilege (often `FILE`), and the OS user running MySQL must be able to read the target file. It also shows how attackers can use this to read sensitive system files (e.g., `/etc/passwd`) or web application source code (e.g., `/var/www/html/search.php`), potentially exposing credentials and further attack paths.


# SQL Injection Fundamentals 

**CHECKING FILE WRITE CONFIGURATION**

The `secure_file_priv` variable controls whether MySQL can read from or write to the filesystem. If it is empty, file operations are allowed anywhere. If it points to a directory, file operations are restricted there. If it is `NULL`, all file operations are blocked. Because UNION-based SQL injection is being used, the value is extracted from `information_schema.global_variables` instead of using a direct `SHOW VARIABLES` statement.

This step confirms whether file writes are even possible before attempting exploitation.

---

**SELECT INTO OUTFILE — BASIC USAGE**

`SELECT ... INTO OUTFILE` writes the result of a query directly to a file on the database server. This is intended for data export but becomes dangerous when an attacker controls the query. Writing table contents proves the capability to write server-side files. Writing static strings confirms arbitrary file creation.

The file ownership shows that files are written as the `mysql` user, which is expected behavior.

---

**WRITING FILES THROUGH SQL INJECTION**

Once file writes are confirmed, the same mechanism is used through a UNION injection. The injected query appends `INTO OUTFILE` to the UNION result, causing the database to write the query output into a file under the web server’s document root.

Extra columns (`1`, `3`, `4`) are required to match the original query’s column count. Their presence in the output explains the extra characters seen in the resulting file.

---

**WRITING A PHP WEB SHELL**

A minimal PHP web shell is written to the web root. It executes system commands passed through a request parameter. Empty strings are used instead of numeric junk values to keep the file content clean and valid PHP.

This step escalates from file write to remote code execution.

---

**EXECUTING COMMANDS VIA WEBSHELL**

Accessing the PHP file through the browser triggers execution by the web server. Supplying the `0` parameter executes arbitrary system commands. Running `id` confirms command execution context and privileges, showing the process runs as the web server user (`www-data`).

This completes the attack chain: SQL injection → file write → web shell → command execution.

# 
#
#
#
#
#
#
#
#