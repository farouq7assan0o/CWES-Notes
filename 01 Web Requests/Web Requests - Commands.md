
# HyperText Transfer Protocol (HTTP) 
---

**cURL Basic Request**

```
curl inlanefreight.com
```

**cURL Download (Remote Filename)**

```
curl -O inlanefreight.com/index.html
```

**cURL Download (Custom Filename)**

```
curl -o <filename> inlanefreight.com/index.html
```

**cURL Silent Download**

```
curl -s -O inlanefreight.com/index.html
```

**cURL Help**

```
curl -h
curl --help all
curl --help category
curl -h http
man curl
```

**cURL Key Flags**

```
-d, --data <data>               HTTP POST data
-h, --help <category>           Get help for commands
-i, --include                   Include protocol response headers in the output
-o, --output <file>             Write to file instead of stdout
-O, --remote-name               Write output to a file named as the remote file
-s, --silent                    Silent mode
-u, --user <user:password>      Server user and password
-A, --user-agent <name>         Send User-Agent <name> to server
-v, --verbose                   Make the operation more talkative
```

**URL Structure Reference**

```
http://admin:password@inlanefreight.com:80/dashboard.php?login=true#status
```

**URL Components**

```
Scheme:        http://  https://
User Info:     admin:password@
Host:          inlanefreight.com
Port:          :80
Path:          /dashboard.php
Query String:  ?login=true
Fragment:      #status
```

**DNS Hosts File**

```
/etc/hosts
```

**Default Ports**

```
HTTP:   80
HTTPS:  443
```

---

---


# Hypertext Transfer Protocol Secure (HTTPS)

---

**cURL HTTPS Request (Skip Certificate Check)**

```
curl -k https://www.inlanefreight.com
```

**cURL Flag**

```
-k    Skip SSL certificate verification
```

**Default Ports**

```
HTTP:   80
HTTPS:  443
```

**HTTP to HTTPS Redirect Response Code**

```
301 Moved Permanently
```

**Encrypted DNS Servers**

```
8.8.8.8
1.1.1.1
```

---

---


# HTTP Requests and Responses 

---

**cURL Verbose Request**

```
curl inlanefreight.com -v
```

**cURL Extra Verbose**

```
curl inlanefreight.com -vvv
```

**Browser DevTools Shortcut**

```
CTRL+SHIFT+I
F12
```

**DevTools Tab**

```
Network
```

**HTTP Request Structure**

```
GET /users/login.html HTTP/1.1
Host: inlanefreight.com
User-Agent: Mozilla/5.0
Cookie: PHPSESSID=c4ggt4jull9obt7aupa55o8vbf
```

**HTTP Response Structure**

```
HTTP/1.1 200 OK
Date:
Server: Apache/2.4.41
Set-Cookie: PHPSESSID=m4u64rqlpfthrvvb12ai9voqgf
Content-Type: text/html; charset=UTF-8
```

**Example Request Line Fields**

```
Method:   GET
Path:     /users/login.html
Version:  HTTP/1.1
```

**Example Response First Line Fields**

```
Version:  HTTP/1.1
Code:     200 OK
```

---

---


# HTTP Headers 

---

**cURL — View Response Headers Only (HEAD request)**

```
curl -I https://www.inlanefreight.com
```

**cURL — View Headers + Response Body**

```
curl -i https://www.inlanefreight.com
```

**cURL — Set Custom Header**

```
curl -H 'Header-Name: value' https://www.inlanefreight.com
```

**cURL — Set User-Agent**

```
curl https://www.inlanefreight.com -A 'Mozilla/5.0'
```

**cURL Flags**

```
-I    Send HEAD request, display response headers only
-i    Display response headers + body
-H    Set a custom request header
-A    Set User-Agent header
```

**General Headers**

```
Date: Wed, 16 Feb 2022 10:38:44 GMT
Connection: close
Connection: keep-alive
```

**Entity Headers**

```
Content-Type: text/html
Media-Type: application/pdf
boundary="b4e4fbd93540"
Content-Length: 385
Content-Encoding: gzip
```

**Request Headers**

```
Host: www.inlanefreight.com
User-Agent: curl/7.77.0
Referer: http://www.inlanefreight.com/
Accept: */*
Cookie: PHPSESSID=b4e4fbd93540
Authorization: BASIC cGFzc3dvcmQK
```

**Response Headers**

```
Server: Apache/2.2.14 (Win32)
Set-Cookie: PHPSESSID=b4e4fbd93540
WWW-Authenticate: BASIC realm="localhost"
```

**Security Headers**

```
Content-Security-Policy: script-src 'self'
Strict-Transport-Security: max-age=31536000
Referrer-Policy: origin
```

**Full Header Example Output**

```
Host: www.inlanefreight.com
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_5) AppleWebKit/605.1.15 (KHTML, like Gecko)
Cookie: cookie1=298zf09hf012fh2; cookie2=u32t4o3tb3gg4
Accept: text/plain
Referer: https://www.inlanefreight.com/
Authorization: BASIC cGFzc3dvcmQK
Date: Sun, 06 Aug 2020 08:49:37 GMT
Connection: keep-alive
Content-Length: 26012
Content-Type: text/html; charset=ISO-8859-4
Content-Encoding: gzip
Server: Apache/2.2.14 (Win32)
Set-Cookie: name1=value1,name2=value2; Expires=Wed, 09 Jun 2021 10:18:14 GMT
WWW-Authenticate: BASIC realm="localhost"
Content-Security-Policy: script-src 'self'
Strict-Transport-Security: max-age=31536000
Referrer-Policy: origin
```

**Reference URLs**

```
https://tools.ietf.org/html/rfc7231#section-5
https://tools.ietf.org/html/rfc7231#section-7
https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers
```

**Browser DevTools**

```
Network tab -> click request -> Headers tab -> Raw button
Network tab -> click request -> Cookies tab
```

---

---



# HTTP Methods and Codes 

---

**HTTP Methods**

```
GET
POST
HEAD
PUT
DELETE
OPTIONS
PATCH
```

**HTTP Status Code Classes**

```
1xx    Informational
2xx    Success
3xx    Redirection
4xx    Client Error
5xx    Server Error
```

**Common Status Codes**

```
200    OK
302    Found (Redirect)
400    Bad Request
403    Forbidden
404    Not Found
500    Internal Server Error
```

**Reference URLs**

```
https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods
https://developer.mozilla.org/en-US/docs/Web/HTTP/Status
https://support.cloudflare.com/hc/en-us/articles/115003014432-HTTP-Status-Codes
https://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/APIError.html
```

---

---


# GET 

---

**cURL GET with Response Headers**

```
curl -i http://<SERVER_IP>:<PORT>/
```

**cURL Basic Auth (-u flag)**

```
curl -u admin:admin http://<SERVER_IP>:<PORT>/
```

**cURL Basic Auth (URL inline)**

```
curl http://admin:admin@<SERVER_IP>:<PORT>/
```

**cURL Basic Auth (Manual Authorization Header)**

```
curl -H 'Authorization: Basic YWRtaW46YWRtaW4=' http://<SERVER_IP>:<PORT>/
```

**cURL Verbose with Auth**

```
curl -v http://admin:admin@<SERVER_IP>:<PORT>/
```

**cURL GET with Query Parameter + Auth Header**

```
curl 'http://<SERVER_IP>:<PORT>/search.php?search=le' -H 'Authorization: Basic YWRtaW46YWRtaW4='
```

**Base64 Encoded Credential (admin:admin)**

```
YWRtaW46YWRtaW4=
```

**Default Credentials**

```
admin:admin
```

**Authorization Header Format**

```
Authorization: Basic YWRtaW46YWRtaW4=
```

**GET Parameter Pattern**

```
/search.php?search=le
```

**Browser DevTools Shortcuts**

```
CTRL+SHIFT+E    Network tab
CTRL+SHIFT+K    JavaScript console tab
```

**Browser DevTools Actions**

```
Right-click request -> Copy -> Copy as cURL
Right-click request -> Copy -> Copy as Fetch
```

---

---


# POST 

---

**POST Login Request**

```bash
curl -X POST -d 'username=admin&password=admin' http://<SERVER_IP>:<PORT>/
```

**POST Login — Follow Redirect**

```bash
curl -X POST -d 'username=admin&password=admin' http://<SERVER_IP>:<PORT>/ -L
```

**POST Login — View Response Headers**

```bash
curl -X POST -d 'username=admin&password=admin' http://<SERVER_IP>:<PORT>/ -i
```

**Use Cookie (-b flag)**

```bash
curl -b 'PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' http://<SERVER_IP>:<PORT>/
```

**Use Cookie (Header flag)**

```bash
curl -H 'Cookie: PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' http://<SERVER_IP>:<PORT>/
```

**POST JSON Data with Cookie and Content-Type**

```bash
curl -X POST -d '{"search":"london"}' -b 'PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' -H 'Content-Type: application/json' http://<SERVER_IP>:<PORT>/search.php
```

**POST Data Format (Form)**

```text
username=admin&password=admin
```

**POST Data Format (JSON)**

```json
{"search":"london"}
```

**Captured Request Headers**

```http
POST /search.php HTTP/1.1
Host: server_ip
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:97.0) Gecko/20100101 Firefox/97.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://server_ip/index.php
Content-Type: application/json
Origin: http://server_ip
Content-Length: 19
DNT: 1
Connection: keep-alive
Cookie: PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1
```

**Cookie Value (Example)**

```text
PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1
```

**cURL Flags**

```text
-X POST       Specify POST method
-d            POST data body
-b            Send cookie
-L            Follow redirects
-i            Show response headers
```

**Browser DevTools Shortcuts**

```text
SHIFT+F9      Storage tab
```

**Browser DevTools Actions**

```text
Network tab -> Request -> Raw
Right-click request -> Copy -> Copy as cURL
Right-click request -> Copy -> Copy as Fetch
Right-click request -> Copy -> Copy Request Headers
Storage tab -> Cookies -> select site -> add/edit cookie
```

---

---


# CRUD API 

---

**CRUD Operations Reference**

```text
Create  POST    Adds data to the database table
Read    GET     Reads entity from the database table
Update  PUT     Updates data in the database table
Delete  DELETE  Removes row from the database table
```

**Read - Specific Entry**

```bash
curl http://<SERVER_IP>:<PORT>/api.php/city/london
```

**Read - Formatted with jq**

```bash
curl -s http://<SERVER_IP>:<PORT>/api.php/city/london | jq
```

**Read - Partial Search**

```bash
curl -s http://<SERVER_IP>:<PORT>/api.php/city/le | jq
```

**Read - All Entries**

```bash
curl -s http://<SERVER_IP>:<PORT>/api.php/city/ | jq
```

**Create - POST New Entry**

```bash
curl -X POST http://<SERVER_IP>:<PORT>/api.php/city/ -d '{"city_name":"HTB_City", "country_name":"HTB"}' -H 'Content-Type: application/json'
```

**Update - PUT Existing Entry**

```bash
curl -X PUT http://<SERVER_IP>:<PORT>/api.php/city/london -d '{"city_name":"New_HTB_City", "country_name":"HTB"}' -H 'Content-Type: application/json'
```

**Verify Update - Read Old Entry**

```bash
curl -s http://<SERVER_IP>:<PORT>/api.php/city/london | jq
```

**Verify Update - Read New Entry**

```bash
curl -s http://<SERVER_IP>:<PORT>/api.php/city/New_HTB_City | jq
```

**Delete - Remove Entry**

```bash
curl -X DELETE http://<SERVER_IP>:<PORT>/api.php/city/New_HTB_City
```

**Verify Delete - Confirm Empty**

```bash
curl -s http://<SERVER_IP>:<PORT>/api.php/city/New_HTB_City | jq
```

**API URL Pattern**

```http
http://<SERVER_IP>:<PORT>/api.php/city/<entry_name>
```

**POST/PUT JSON Payload Examples**

```json
{"city_name":"HTB_City", "country_name":"HTB"}
{"city_name":"New_HTB_City", "country_name":"HTB"}
```

**Required Header for JSON Data**

```text
Content-Type: application/json
```

---

---

