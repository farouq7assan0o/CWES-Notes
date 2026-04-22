
# Intro to Web Proxies 

**Tools**

Burp Suite ZAP (OWASP Zed Attack Proxy) Wireshark

**Ports / Protocols**

```text
HTTP/80
HTTPS/443
```

**URLs / References**

```text
https://portswigger.net/burp
https://www.zaproxy.org/
https://owasp.org/
https://portswigger.net/burp/pro/trial
```

---
# Setting Up 

**Launch Commands**

```shell
burpsuite
```

```shell
zaproxy
```

```shell
java -jar </path/to/burpsuite.jar>
```

**Download URLs**

```text
https://portswigger.net/burp/releases/
https://www.zaproxy.org/download/
https://docs.oracle.com/goldengate/1212/gg-winux/GDRAD/java.htm
```

**Burp Dark Theme Path**

```text
Burp > Settings > User interface > Display > theme > dark
```

**ZAP Dark Theme Path**

```text
Tools > Options > Display > Look and Feel > Flat Dark
```

---


# Proxy Setup

**Default Proxy Port**

```text
8080
```

**Proxy Listener Settings Paths**

```text
Burp > Proxy > Proxy settings > Proxy listeners
```

```text
ZAP > Tools > Options > Network > Local Servers/Proxies
```

**Burp Pre-configured Browser Path**

```text
Proxy > Intercept > Open Browser
```

**FoxyProxy Configuration Values**

```text
IP: 127.0.0.1
Port: 8080
Name: Burp / ZAP
```

**Burp CA Certificate URL**

```http
http://burp
```

**ZAP CA Certificate Path**

```text
Tools > Options > Network > Server Certificates > Save
```

**Firefox CA Certificate Import Path**

```text
about:preferences#privacy
```

```text
Firefox > about:preferences#privacy > View Certificates > Authorities > Import
```

**Firefox Trust Options (on import)**

```text
Trust this CA to identify websites
Trust this CA to identify email users
```

**FoxyProxy Extension URL**

```text
https://addons.mozilla.org/en-US/firefox/addon/foxyproxy-standard/
```

---


# Intercepting Web Requests

**Burp - Intercept Toggle Path**

```text
Proxy > Intercept > Intercept is on/off
```

**ZAP - Intercept Toggle Shortcut**

```text
CTRL+B
```

**ZAP HUD Tutorial Path**

```text
Configuration button (bottom right) > Take the HUD tutorial
```

**Intercepted Request**

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

**Manipulated Parameter**

```text
ip=;ls;
```

**Target Endpoint**

```text
/ping
```

---




# Intercepting Responses 

**Burp - Enable Response Interception Path**

```text
Proxy > Proxy settings > Response interception rules > Intercept Response
```

**Burp - Unhide Hidden Form Fields Path**

```text
Proxy > Proxy settings > Response modification rules > Unhide hidden form fields
```

**Browser Force Refresh**

```text
CTRL+SHIFT+R
```

**HTML - Original Restricted Input**

```html
<input type="number" id="ip" name="ip" min="1" max="255" maxlength="3">
```

**HTML - Modified Input (after response interception)**

```html
<input type="text" id="ip" name="ip" min="1" max="255" maxlength="100"
    oninput="javascript: if (this.value.length > this.maxLength) this.value = this.value.slice(0, this.maxLength);"
   required>
```

**ZAP HUD - Show/Enable Disabled Fields**

```text
HUD left pane > third button (light bulb icon) > Show/Enable
```

**ZAP HUD - Show HTML Comments**

```text
HUD left pane > + button > Comments
```

---

# Automatic Modification 

**Burp - Match and Replace Path**

```text
Proxy > Proxy settings > HTTP match and replace rules > Add
```

**Burp - Automatic Request Modification (User-Agent)**

```text
Type: Request header
Match: ^User-Agent.*$
Replace: User-Agent: HackTheBox Agent 1.0
Regex match: True
```

**Burp - Automatic Response Modification (input type)**

```text
Type: Response body
Match: type="number"
Replace: type="text"
Regex match: False
```

**Burp - Automatic Response Modification (maxlength)**

```text
Type: Response body
Match: maxlength="3"
Replace: maxlength="100"
Regex match: False
```

**ZAP - Replacer Shortcut**

```text
CTRL+R
```

**ZAP - Replacer Path**

```text
ZAP Options > Replacer > Add
```

**ZAP - Automatic Request Modification (User-Agent)**

```text
Description: HTB User-Agent
Match Type: Request Header (will add if not present)
Match String: User-Agent
Replacement String: HackTheBox Agent 1.0
Enable: True
```

**ZAP - Automatic Response Modification (input type)**

```text
Match Type: Response Body String
Match Regex: False
Match String: type="number"
Replacement String: type="text"
Enable: True
```

**ZAP - Automatic Response Modification (maxlength)**

```text
Match Type: Response Body String
Match Regex: False
Match String: maxlength="3"
Replacement String: maxlength="100"
Enable: True
```

**Browser Force Refresh**

```text
CTRL+SHIFT+R
```

**ZAP - Enable Request Interception**

```text
CTRL+B
```

---

# Repeating Requests 

**Burp - Proxy History Path**

```text
Proxy > HTTP History
```

**Burp - Send to Repeater**

```text
CTRL+R
```

**Burp - Go to Repeater Tab**

```text
CTRL+SHIFT+R
```

**Burp - Change Request Method**

```text
Right-click request > Change Request Method
```

**ZAP - Resend Request**

```text
Right-click request > Open/Resend with Request Editor > Send
```

**ZAP HUD - Resend Options**

```text
History pane > click request > Replay in Console
History pane > click request > Replay in Browser
```

---


# Encoding/Decoding 

**Burp - URL-Encode Selected Text**

```text
CTRL+U
```

```text
Right-click > Convert Selection > URL > URL-encode key characters
```

**Burp - Decoder Tab Path**

```text
Decoder tab
```

**Burp - Inspector Tool Location**

```text
Burp Proxy > Inspector
Burp Repeater > Inspector
```

**ZAP - Encoder/Decoder/Hash Shortcut**

```text
CTRL+E
```

**Base64 Encoded Cookie (example)**

```text
eyJ1c2VybmFtZSI6Imd1ZXN0IiwgImlzX2FkbWluIjpmYWxzZX0=
```

**Decoded Cookie Value (example)**

```text
{"username":"guest", "is_admin":false}
```

**Modified Cookie Value (before re-encoding)**

```text
{"username":"admin", "is_admin":true}
```

**Supported Encoding Types**

```text
URL
Full URL-Encoding
Unicode URL
HTML
Unicode
Base64
ASCII hex
```

---


# Proxying Tools 

**Proxychains Config File**

```text
/etc/proxychains.conf
```

**Proxychains Config - Comment Out and Add**

```text
#socks4         127.0.0.1 9050
http 127.0.0.1 8080
```

**Proxychains with curl**

```shell
proxychains -q curl http://SERVER_IP:PORT
```

**Metasploit - Launch**

```shell
msfconsole
```

**Metasploit - robots_txt Scanner with Proxy**

```shell
use auxiliary/scanner/http/robots_txt
set PROXIES HTTP:127.0.0.1:8080
set RHOST SERVER_IP
set RPORT PORT
run
```

**Proxy Address (universal)**

```text
http://127.0.0.1:8080
```

---


# Burp Intruder 

**Send to Intruder**

```text
CTRL+I
```

**Go to Intruder Tab**

```text
CTRL+SHIFT+I
```

**Payload Position Syntax**

```text
§DIRECTORY§
```

**Attack Type**

```text
Sniper
```

**Wordlist Path**

```text
/opt/useful/seclists/Discovery/Web-Content/common.txt
```

**Payload Processing - Skip Rule (regex)**

```text
Skip if matches regex
^\..*$
```

**Grep - Match Configuration**

```text
Settings > Grep - Match > Clear > Add: 200 OK
Disable: Exclude HTTP Headers
```

**Intruder Payload Types**

```text
Simple List
Runtime file
Character Substitution
```

**Target Directory Fuzz Request Pattern**

```http
GET /§DIRECTORY§/ HTTP/1.1
```

**Result Hit**

```text
/admin/
```

**Intruder Documentation URL**

```text
https://portswigger.net/burp/documentation/desktop/tools/intruder/getting-started
https://portswigger.net/burp/documentation/desktop/tools/intruder/configure-attack/payload-types
```

---

# ZAP Fuzzer 

**Open Fuzzer from History**

```text
Right-click request > Attack > Fuzz
```

**Initial Fuzz URL Pattern**

```http
GET /test/ HTTP/1.1
```

**Fuzz Location - Select and Add**

```text
Select word: test > click Add
```

**Payload Type - File Fuzzers Selection**

```text
Type: File Fuzzers
Wordlist: dirbuster > directory-list-1.0.txt
```

**Payload Type Options**

```text
File
File Fuzzers
Numberzz
```

**Processor - URL Encode**

```text
Type: URL Encode
Encoding: UTF-8
```

**Processor Types**

```text
Base64 Decode/Encode
MD5 Hash
Postfix String
Prefix String
SHA-1/256/512 Hash
URL Decode/Encode
Script
```

**Options - Concurrent Threads**

```text
Concurrent Scanning Threads per Scan: 20
```

**Traversal Strategy Options**

```text
Depth First
Breadth First
```

**Result Hit**

```text
/skills/
```

**Sort Results By**

```text
Response code: 200
```

---


# Burp Scanner 

**Start Scan from Proxy History**

```text
Right-click request > Scan
Right-click request > Passive Scan
Right-click request > Active Scan
```

**New Scan from Dashboard**

```text
Dashboard > New Scan
```

**Add to Scope**

```text
Target > Site map > Right-click target > Add to scope
```

**Remove from Scope**

```text
Right-click in-scope item > Remove from scope
```

**View/Edit Scope**

```text
Target > Scope
```

**View Site Map**

```text
Target > Site map
```

**Crawl Config Preset**

```text
Crawl strategy - fastest
```

**Audit Config Preset**

```text
Audit checks - critical issues only
```

**Scan Type Options**

```text
Crawl and Audit
Crawl
```

**Passive Scan from Site Map**

```text
Target > Site map > Right-click > Do passive scan
Target > Site map > Right-click > Passively scan this target
```

**Active Scan from History**

```text
Right-click request > Do active scan
```

**View Scan Logs**

```text
Dashboard > Tasks > View details > Logger tab
Logger tab (main Burp nav)
```

**Issue Filter (Dashboard)**

```text
Issue activity pane > Filter: High + Certain
```

**Export Report**

```text
Target > Site map > Right-click target > Issue > Report issues for this host
```

**Identified Vulnerability (example)**

```text
OS command injection
Parameter: ip
Severity: High
Confidence: Firm
```

---


# ZAP Scanner 

**Start Spider from History**

```text
Right-click request > Attack > Spider
```

**Start Spider from HUD**

```text
HUD right pane > second button (Spider Start)
```

**View Sites Tree from HUD**

```text
HUD right pane > first button (Sites Tree)
```

**Start Ajax Spider from HUD**

```text
HUD right pane > third button (Ajax Spider)
```

**Start Active Scan from HUD**

```text
HUD right pane > Active Scan button
```

**Generate Report**

```text
Report > Generate HTML Report
```

**Report Export Formats**

```text
HTML
XML
Markdown
```

**High Alert Identified (example)**

```text
Remote OS Command Injection
Risk: High
Confidence: Medium
Attack: 127.0.0.1&cat /etc/passwd&
Evidence: root:x:0:0
```

---


# Extensions 

**Burp - BApp Store Path**

```text
Extensions > BApp Store
```

**Burp - Notable Extensions**

```text
.NET Beautifier
J2EEScan
Software Vulnerability Scanner
Software Version Reporter
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
Decoder Improved
```

**Burp - Decoder Improved Hash Example**

```text
Input: HTB Academy
Hash With > MD5
```

**ZAP - Open Marketplace**

```text
Manage Add-ons button > Marketplace tab
```

**ZAP - FuzzDB Add-ons to Install**

```text
FuzzDB Files
FuzzDB Offensive
```

**ZAP - FuzzDB Command Injection Wordlist Path**

```text
File Fuzzers > fuzzdb > attack > os-cmd-execution > command_execution-unix.txt
```

---

