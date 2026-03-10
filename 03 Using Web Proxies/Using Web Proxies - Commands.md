# Intro to Web Proxies 

**COMMANDS**

**PAYLOADS**

**CODE**

**PATHS**

**ENDPOINTS**

**PARAMETERS**

**TOOLS**

Burp Suite  
OWASP Zed Attack Proxy (ZAP)  
Wireshark

**SERVICES**

HTTP  
HTTPS

**PORTS**

80  
443

**DOM IDS**

**ELEMENT NAMES**

**ATTRIBUTES**

**REQUEST PATTERNS**

---


# Setting Up 

**COMMANDS**

burpsuite

```
java -jar </path/to/burpsuite.jar>
```

zaproxy

```
java -jar </path/to/zap.jar>
```

**PAYLOADS**

**CODE**

```
java -jar </path/to/burpsuite.jar>
```

```
java -jar </path/to/zap.jar>
```

**PATHS**

</path/to/burpsuite.jar>  
</path/to/zap.jar>

**ENDPOINTS**

**PARAMETERS**

**TOOLS**

Burp Suite  
OWASP Zed Attack Proxy (ZAP)  
Java Runtime Environment (JRE)  
PwnBox  
Parrot OS  
Kali Linux

**SERVICES**

**PORTS**

**DOM IDS**

**ELEMENT NAMES**

**ATTRIBUTES**

**REQUEST PATTERNS**

---


# Proxy Setup 

**COMMANDS**

**PAYLOADS**

**CODE**

**PATHS**

about:preferences#privacy

**ENDPOINTS**

[http://burp](http://burp/)

**PARAMETERS**

**TOOLS**

Burp Suite  
OWASP Zed Attack Proxy (ZAP)  
Firefox  
FoxyProxy

**SERVICES**

HTTP  
HTTPS

**PORTS**

8080

**DOM IDS**

**ELEMENT NAMES**

**ATTRIBUTES**

**REQUEST PATTERNS**

127.0.0.1:8080

---


# Intercepting Web Requests 

**COMMANDS**

**PAYLOADS**

;ls;

**CODE**

```http
POST /ping HTTP/1.1
Host: 94.237.62.138:32306
Content-Length: 4
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Origin: http://94.237.62.138:32306
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/139.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://94.237.62.138:32306/
Accept-Encoding: gzip, deflate, br
Connection: keep-alive

ip=1
```

```http
ip=1
```

```http
ip=;ls;
```

**PATHS**

/ping

**ENDPOINTS**

http://SERVER_IP:PORT/  
[http://94.237.62.138:32306/](http://94.237.62.138:32306/)  
[http://94.237.62.138:32306](http://94.237.62.138:32306/)

**PARAMETERS**

ip

**TOOLS**

Burp Suite  
OWASP Zed Attack Proxy (ZAP)  
Firefox  
Heads Up Display (HUD)

**SERVICES**

HTTP

**PORTS**

32306

**DOM IDS**

**ELEMENT NAMES**

IP  
Ping

**ATTRIBUTES**

Host  
Content-Length  
Cache-Control  
Accept-Language  
Origin  
Content-Type  
Upgrade-Insecure-Requests  
User-Agent  
Accept  
Referer  
Accept-Encoding  
Connection

**REQUEST PATTERNS**

POST /ping HTTP/1.1  
ip=1  
ip=;ls;


# Intercepting Responses

**COMMANDS**

CTRL+SHIFT+R

**PAYLOADS**

;ls;

**CODE**

```html
<input type="number" id="ip" name="ip" min="1" max="255" maxlength="3"
    oninput="javascript: if (this.value.length > this.maxLength) this.value = this.value.slice(0, this.maxLength);"
    required>
```

```html
<input type="text" id="ip" name="ip" min="1" max="255" maxlength="100"
    oninput="javascript: if (this.value.length > this.maxLength) this.value = this.value.slice(0, this.maxLength);"
    required>
```

**PATHS**

/ping

**ENDPOINTS**

http://SERVER_IP:PORT/

**PARAMETERS**

ip

**TOOLS**

Burp Suite  
OWASP Zed Attack Proxy (ZAP)  
Firefox  
Heads Up Display (HUD)

**SERVICES**

HTTP

**PORTS**

**DOM IDS**

ip

**ELEMENT NAMES**

input

**ATTRIBUTES**

type  
id  
name  
min  
max  
maxlength  
oninput  
required

**REQUEST PATTERNS**

ip=;ls;

---


# Automatic Modification 

**COMMANDS**

CTRL+R  
CTRL+B  
CTRL+SHIFT+R

**PAYLOADS**

;ls;

**CODE**

```
^User-Agent.*$
```

```
User-Agent: HackTheBox Agent 1.0
```

```
type="number"
```

```
type="text"
```

```
maxlength="3"
```

```
maxlength="100"
```

**PATHS**

/ping

**ENDPOINTS**

http://SERVER_IP:PORT/

**PARAMETERS**

ip

**TOOLS**

Burp Suite  
OWASP Zed Attack Proxy (ZAP)  
ZAP Replacer

**SERVICES**

HTTP  
HTTPS

**PORTS**

**DOM IDS**

ip

**ELEMENT NAMES**

input

**ATTRIBUTES**

User-Agent  
type  
maxlength

**REQUEST PATTERNS**

User-Agent: HackTheBox Agent 1.0  
ip=;ls;

---


# Repeating Requests 

**COMMANDS**

CTRL+R  
CTRL+SHIFT+R

**PAYLOADS**

**CODE**

**PATHS**

/ping

**ENDPOINTS**

**PARAMETERS**

**TOOLS**

Burp Suite  
OWASP Zed Attack Proxy (ZAP)  
Burp Repeater  
ZAP Request Editor  
ZAP HUD

**SERVICES**

HTTP  
WebSockets

**PORTS**

**DOM IDS**

**ELEMENT NAMES**

Send  
Replay in Console  
Replay in Browser  
Open/Resend with Request Editor  
Change Request Method

**ATTRIBUTES**

POST  
GET

**REQUEST PATTERNS**


# Encoding/Decoding 

**COMMANDS**

CTRL+U  
CTRL+E

**PAYLOADS**

eyJ1c2VybmFtZSI6Imd1ZXN0IiwgImlzX2FkbWluIjpmYWxzZX0=  
{"username":"guest", "is_admin":false}  
{"username":"admin", "is_admin":true}  
eyJ1c2VybmFtZSI6ImFkbWluIiwgImlzX2FkbWluIjp0cnVlfQ==

**CODE**

```text
eyJ1c2VybmFtZSI6Imd1ZXN0IiwgImlzX2FkbWluIjpmYWxzZX0=
```

```json
{"username":"guest", "is_admin":false}
```

```json
{"username":"admin", "is_admin":true}
```

```text
eyJ1c2VybmFtZSI6ImFkbWluIiwgImlzX2FkbWluIjp0cnVlfQ==
```

**PATHS**

**ENDPOINTS**

**PARAMETERS**

**TOOLS**

Burp Suite  
OWASP Zed Attack Proxy (ZAP)  
Burp Repeater  
Burp Decoder  
Burp Inspector  
ZAP Encoder/Decoder/Hash

**SERVICES**

HTTP

**PORTS**

**DOM IDS**

**ELEMENT NAMES**

Decode as > Base64  
Convert Selection>URL>URL-encode key characters  
Add New Tab

**ATTRIBUTES**

**REQUEST PATTERNS**

URL-encode key characters  
Full URL-Encoding  
Unicode URL encoding  
HTML  
Unicode  
Base64  
ASCII hex


# Proxying Tools 

**COMMANDS**

```shell
#socks4         127.0.0.1 9050
http 127.0.0.1 8080
```

```shell
proxychains -q curl http://SERVER_IP:PORT
```

```shell
msfconsole
```

```shell
use auxiliary/scanner/http/robots_txt
```

```shell
set PROXIES HTTP:127.0.0.1:8080
```

```shell
set RHOST SERVER_IP
```

```shell
set RPORT PORT
```

```shell
run
```

**PAYLOADS**

HTTP:127.0.0.1:8080  
[http://127.0.0.1:8080](http://127.0.0.1:8080/)  
http://SERVER_IP:PORT

**CODE**

```shell
#socks4         127.0.0.1 9050
http 127.0.0.1 8080
```

```shell
proxychains -q curl http://SERVER_IP:PORT
```

```shell
msfconsole

use auxiliary/scanner/http/robots_txt
set PROXIES HTTP:127.0.0.1:8080
set RHOST SERVER_IP
set RPORT PORT
run
```

**PATHS**

/etc/proxychains.conf  
/robots.txt

**ENDPOINTS**

[http://127.0.0.1:8080](http://127.0.0.1:8080/)  
http://SERVER_IP:PORT

**PARAMETERS**

PROXIES  
RHOST  
RPORT

**TOOLS**

Burp Suite  
OWASP Zed Attack Proxy (ZAP)  
proxychains  
curl  
Metasploit  
msfconsole  
auxiliary/scanner/http/robots_txt

**SERVICES**

HTTP  
socks4

**PORTS**

9050  
8080  
PORT

**DOM IDS**

**ELEMENT NAMES**

**ATTRIBUTES**

**REQUEST PATTERNS**

HTTP:127.0.0.1:8080  
GET /robots.txt


# Burp Intruder 

**COMMANDS**

CTRL+I  
CTRL+SHIFT+I

**PAYLOADS**

§DIRECTORY§  
^..*$  
200 OK

**CODE**

```http
GET /§DIRECTORY§/ HTTP/1.1
```

```text
/opt/useful/seclists/Discovery/Web-Content/common.txt
```

```regex
^\..*$
```

```text
200 OK
```

**PATHS**

/opt/useful/seclists/Discovery/Web-Content/common.txt  
/admin/

**ENDPOINTS**

http://SERVER_IP:PORT/admin/

**PARAMETERS**

Payload Position  
Payload Type  
Payload Configuration  
Payload Processing  
Payload Encoding  
Number of retries on network failure  
Pause before retry  
Exclude HTTP Headers

**TOOLS**

Burp Suite  
Burp Intruder  
ffuf  
dirbuster  
gobuster  
wfuzz  
Resource Pool

**SERVICES**

HTTP  
HTTPS  
Active Directory (AD)  
Outlook Web Access (OWA)  
SSL VPN  
Remote Desktop Services (RDS)  
Citrix

**PORTS**

31827

**DOM IDS**

**ELEMENT NAMES**

Send to Intruder  
Start Attack  
Add §  
Load  
Add  
Paste  
Remove  
Clear  
Add new item  
Add from list  
Skip if matches regex  
Grep - Match  
Grep - Extract  
Exclude HTTP Headers

**ATTRIBUTES**

Sniper  
Cluster Bomb  
Simple List  
Runtime file  
Character Substitution  
Regex match  
Response body  
Request header  
200 OK  
404 NOT FOUND

**REQUEST PATTERNS**

GET /§DIRECTORY§/ HTTP/1.1  
/admin/


# ZAP Fuzzer 

**COMMANDS**

**PAYLOADS**

test  
skills

**CODE**

```http
GET /test/ HTTP/1.1
```

```http
GET /skills/ HTTP/1.1
```

**PATHS**

/test/  
/skills/

**ENDPOINTS**

http://SERVER_IP:PORT/test/  
http://SERVER_IP:PORT/skills/

**PARAMETERS**

Fuzz Location  
Payloads  
Processors  
Options  
Concurrent Scanning Threads per Scan  
Retries on IO error  
Limit max errors  
Depth First  
Breadth First

**TOOLS**

OWASP Zed Attack Proxy (ZAP)  
ZAP Fuzzer  
ZAP Marketplace  
dirbuster  
File Fuzzers

**SERVICES**

HTTP

**PORTS**

PORT

**DOM IDS**

**ELEMENT NAMES**

Attack>Fuzz  
Add  
Remove  
Modify  
Generate Preview  
Start Fuzzer

**ATTRIBUTES**

File  
File Fuzzers  
Numberzz  
URL Encode  
Prefix String  
Postfix String  
Script  
Base64 Decode  
Base64 Encode  
MD5 Hash  
SHA-1 Hash  
SHA-256 Hash  
SHA-512 Hash

**REQUEST PATTERNS**

GET /test/ HTTP/1.1  
GET /skills/ HTTP/1.1

---


# Burp Scanner 

**COMMANDS**

**PAYLOADS**

200 OK

**CODE**

**PATHS**

/xmlrpc.php

**ENDPOINTS**

[http://142.93.35.92:30269/](http://142.93.35.92:30269/)  
[http://46.101.23.188:30760/](http://46.101.23.188:30760/)  
[http://142.93.35.92:32729/](http://142.93.35.92:32729/)

**PARAMETERS**

Target Scope  
Include in scope  
Exclude from scope  
Use advanced scope control  
Crawl  
Audit  
Crawl and Audit  
Crawl strategy - fastest  
Audit checks - critical issues only

**TOOLS**

Burp Suite  
Burp Scanner  
Burp Crawler  
Burp Passive Scanner  
Burp Active Scanner  
Burp Logger

**SERVICES**

HTTP  
HTTPS

**PORTS**

30269  
30760  
32729

**DOM IDS**

**ELEMENT NAMES**

Scan  
Passive Scan  
Active Scan  
New Scan  
Add to scope  
Remove from scope  
View details  
Logger  
Issue activity  
Report issues for this host

**ATTRIBUTES**

High  
Medium  
Low  
Information  
Certain  
Firm

**REQUEST PATTERNS**

---


# ZAP Scanner 

**COMMANDS**

**PAYLOADS**

127.0.0.1&cat /etc/passwd&  
root:x:0:0

**CODE**

```text
127.0.0.1&cat /etc/passwd&
```

```text
root:x:0:0
```

**PATHS**

/customer-support.php  
/etc/passwd

**ENDPOINTS**

[http://academy.htb/customer-support.php](http://academy.htb/customer-support.php)  
[http://46.101.23.188:30873/](http://46.101.23.188:30873/)

**PARAMETERS**

Scope

**TOOLS**

OWASP Zed Attack Proxy (ZAP)  
ZAP Scanner  
ZAP Spider  
Ajax Spider  
ZAP HUD  
ZAP Request Editor

**SERVICES**

HTTP  
AJAX  
XML  
Markdown

**PORTS**

30873

**DOM IDS**

**ELEMENT NAMES**

Attack>Spider  
Start  
Sites Tree  
Active Scan  
Alerts  
Report>Generate HTML Report  
Replay in Console  
Replay in Browser

**ATTRIBUTES**

High  
Medium  
Low  
Informational  
Remote OS Command Injection  
X-Frame-Options Header Not Set  
Cross-Domain Misconfiguration  
Directory Browsing

**REQUEST PATTERNS**

127.0.0.1&cat /etc/passwd&  
root:x:0:0


# Extensions 

**COMMANDS**

**PAYLOADS**

HTB Academy  
;id  
/usr/bin/id

**CODE**

```text
HTB Academy
```

```text
;id
```

```text
/usr/bin/id
```

**PATHS**

fuzzdb/attack/os-cmd-execution/command_execution-unix.txt

**ENDPOINTS**

[http://139.59.6.56:31133/ping](http://139.59.6.56:31133/ping)

**PARAMETERS**

**TOOLS**

Burp Suite  
Burp Extensions  
BApp Store  
Decoder Improved  
Active Scan++  
Additional Scanner Checks  
AWS Security Checks  
Backslash Powered Scanner  
Wsdler  
Java Deserialization Scanner  
C02  
Cloud Storage Tester  
CMS Scanner  
Error Message Checks  
Detect Dynamic JS  
Headers Analyzer  
HTML5 Auditor  
PHP Object Injection Check  
JavaScript Security  
Retire.JS  
CSP Auditor  
Random IP Address Header  
Autorize  
CSRF Scanner  
JS Link Finder

OWASP Zed Attack Proxy (ZAP)  
ZAP Marketplace  
FuzzDB Files  
FuzzDB Offensive

**SERVICES**

HTTP

**PORTS**

31133

**DOM IDS**

**ELEMENT NAMES**

Extensions  
BApp Store  
Manage Add-ons  
Marketplace  
Install  
Get more info  
File Fuzzers  
Hash With>MD5

**ATTRIBUTES**

Release  
Beta  
Alpha  
MD5

**REQUEST PATTERNS**

;id  
/usr/bin/id
#
#