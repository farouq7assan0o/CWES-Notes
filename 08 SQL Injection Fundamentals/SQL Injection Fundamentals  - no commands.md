# SQL Injection Fundamentals — EXPLANATION NOTES

**Introduction**

Modern web applications rely on databases to dynamically generate content and manage data such as users, posts, and application state. User HTTP(S) requests are processed by the application layer, which constructs SQL queries to interact with the database. When user-controlled input is embedded into these queries without proper handling, it opens the door to SQL injection. This commonly occurs in a three-tier architecture where the client interacts with an application server, which then communicates with a DBMS.

**SQL Injection (SQLi)**

SQL injection is a class of injection vulnerability affecting relational databases (e.g., MySQL). It occurs when an attacker manipulates user input so that it alters the structure or logic of the SQL query sent to the database. The first step is breaking out of the intended input context, often by injecting quote characters to escape string boundaries. Once injection is possible, attackers can modify the original query or append new logic using techniques such as UNION queries or stacked queries. The attacker then relies on the application’s response behavior to observe or extract the results of the injected query.

**Use Cases and Impact**

The impact of SQL injection can be severe. Attackers may extract sensitive data such as usernames, passwords, and financial information, leading to breaches and credential reuse attacks. SQLi can also be used to bypass authentication, escalate privileges, or access restricted functionality like admin panels. In cases where database or server privileges are misconfigured, attackers may read or write files on the back-end server, implant backdoors, and potentially gain full control over the application and underlying system.

**Prevention**

SQL injection vulnerabilities stem from insecure coding practices and excessive database privileges. Preventive measures focus on proper input handling, including sanitization and validation, as well as secure coding patterns such as parameterized queries. Restricting database user privileges limits the damage if an injection does occur. These mitigation strategies are covered in later sections of the module.

# SQL Injection Fundamentals — Intro to Databases 

**Intro to Databases**

This section establishes the role of databases in web applications, explaining that back-end databases store application assets, content, and user data. It frames SQL as the language used to query these databases and highlights why understanding database fundamentals is required before learning SQL injection. The transition from slow file-based storage to DBMS is introduced as a scalability and performance necessity.

**Database Management Systems**

This section defines a DBMS as the software layer responsible for creating, managing, and interacting with databases. It outlines different DBMS models (file-based, relational, NoSQL, graph, key/value) and emphasizes why RDBMSs are common in web applications. The listed features explain how DBMSs handle real-world requirements such as simultaneous users, data integrity, access control, recovery, and standardized querying via SQL.

**Architecture**

This section explains the multi-tier architecture used by most database-backed applications. It clarifies how client-side applications (Tier I) send user-driven events to middleware (Tier II), which translates them into database queries. The DBMS executes these queries and returns results or errors. The explanation highlights where SQL injection typically occurs: at the boundary where user input is transformed into database queries. It also notes deployment choices, such as separating the application server and database for performance and scalability.

# **SQL Injection Fundamentals — Types of Databases — EXPLANATION NOTES**

**Types of Databases**

This section introduces the high-level classification of databases into relational and non-relational systems. It establishes that SQL is exclusive to relational databases, while non-relational databases use alternative query and interaction models. This distinction is critical for understanding why SQL injection applies only to certain back-end technologies.

**Relational Databases**

This section explains relational databases as schema-driven systems that store data in tables composed of rows and columns. It describes how entities are linked through keys, allowing structured relationships between data sets such as users, posts, orders, and products. The explanation emphasizes how relational integrity enables predictable updates, efficient querying, and centralized data management. It also introduces RDBMSs as the mechanism that enforces these relationships and highlights MySQL as the primary relational database used throughout the module.

**Non-relational Databases**

This section explains NoSQL databases as schema-less systems designed for flexibility and scalability. It describes how data is stored using alternative models such as key-value, document-based, wide-column, and graph structures. The JSON example demonstrates how records are stored as nested objects rather than rows in tables. The section also clearly differentiates SQL injection from NoSQL injection, noting that NoSQL systems have their own distinct injection techniques that are addressed separately in later material.
# **SQL Injection Fundamentals — Intro to MySQL — EXPLANATION NOTES**

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

# **SQL Injection Fundamentals — SQL Statements — EXPLANATION NOTES**

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

# **SQL Injection Fundamentals — Query Results — EXPLANATION NOTES**

**Sorting Results**

This section explains how query output order can be controlled using `ORDER BY`. It covers default ascending order, explicit ascending and descending sorting, and multi-column sorting to resolve duplicate values. These mechanics are important in SQL injection for enumerating data predictably and identifying hidden or sensitive records.

**LIMIT Results**

This section explains how `LIMIT` restricts the number of returned rows and how offsets can be used to paginate through result sets. In SQL injection, this is commonly used to enumerate database contents row-by-row when full output is not directly visible.

**WHERE Clause**

This section explains conditional filtering of query results using `WHERE`. It shows numeric and string comparisons and highlights proper quoting rules for data types. The `WHERE` clause is a primary target in SQL injection, as attackers often manipulate or bypass conditions to access unauthorized data.

**LIKE Clause**

This section explains pattern matching using `LIKE`, `%`, and `_`. It demonstrates how partial matches and fixed-length matches work. In SQL injection, `LIKE` is frequently abused for blind enumeration of values when exact matches are unknown.
# **SQL Injection Fundamentals — SQL Operators — EXPLANATION NOTES**

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
# **SQL Injection Fundamentals — Intro to SQL Injections — EXPLANATION NOTES**

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
# **SQL Injection Fundamentals — Subverting Query Logic — EXPLANATION NOTES**

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
# **SQL Injection Fundamentals — Using Comments — EXPLANATION NOTES**

**Comments**

This section explains how SQL comments can be used to ignore parts of a query during execution. MySQL supports `--` and `#` as line comments, allowing attackers to truncate the original query and remove security checks such as password validation. The requirement for a trailing space after `--` is critical for successful exploitation.

**Auth Bypass with Comments**

By injecting a comment after a valid condition, the rest of the SQL query is ignored. This allows authentication checks that rely on additional conditions (like password matching) to be bypassed entirely. The database evaluates only the remaining valid portion of the query.

**Parenthesis and Advanced Logic**

This section demonstrates how applications may use parentheses to enforce stricter logic and prevent simple bypasses. When parentheses are involved, attackers must balance them correctly to avoid syntax errors. Closing the open parenthesis before commenting out the rest of the query restores valid SQL syntax and allows successful authentication bypass.

**Attacker Mindset**

The attacker adapts payloads based on query structure, ensuring syntax validity while neutralizing security conditions. Comments are a powerful tool for removing unwanted logic without needing to satisfy it, especially when combined with operator precedence and parenthesis handling.

# **SQL Injection Fundamentals — Union Clause — EXPLANATION NOTES**

**Union**

This section introduces the SQL `UNION` clause as a way to combine the output of multiple `SELECT` statements into a single result set. For SQL injection, this is important because it enables extracting data from other tables/databases by appending an attacker-controlled `SELECT` to the original query.

**Even Columns**

This section explains the key constraint for using `UNION`: both `SELECT` statements must return the same number of columns, and corresponding columns should be compatible in data type. If the column counts differ, MySQL throws an error like `ERROR 1222`, which is commonly used by attackers to infer the correct column count during exploitation.

**Un-even Columns**

This section explains the attacker workaround when they want fewer “real” columns than the original query returns. The attacker pads the remaining required columns with filler values (“junk”), numbers, or `NULL` to satisfy the column count requirement. Using numbers is convenient because it also helps track which output column is being reflected on the page (useful later for finding the injectable/visible column).

# **SQL Injection Fundamentals — Union Injection — EXPLANATION NOTES**

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
# **SQL Injection Fundamentals — Database Enumeration — EXPLANATION NOTES**

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
# **SQL Injection Fundamentals — Reading Files — EXPLANATION NOTES**

**Privileges**

This section explains that file read/write via SQLi depends on DB privileges, especially the MySQL `FILE` privilege, and also on the OS permissions of the user running the DBMS. Reading files is more common; writing files is more dangerous and typically restricted.

**DB User**

This section shows how to identify the active database user. Knowing the DB user helps estimate privilege level (e.g., `root@localhost` is often highly privileged), which informs whether file operations may be possible.

**User Privileges**

This section demonstrates checking for high-level privileges (like `super_priv`) and enumerating all granted privileges via `information_schema.user_privileges`. Filtering by the current `grantee` allows focusing on the privileges of the active user. The presence of `FILE` indicates potential local file read/write capabilities through SQL.

**LOAD_FILE**

This section introduces `LOAD_FILE()` as the MySQL/MariaDB function used to read files from disk. It highlights two requirements: the DB user must have the needed privilege (often `FILE`), and the OS user running MySQL must be able to read the target file. It also shows how attackers can use this to read sensitive system files (e.g., `/etc/passwd`) or web application source code (e.g., `/var/www/html/search.php`), potentially exposing credentials and further attack paths.


# SQL Injection Fundamentals — EXPLANATION NOTES

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