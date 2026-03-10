# HTTP Fundamentals 

**cURL Commands**

```
curl inlanefreight.com
```

```
curl -O inlanefreight.com/index.html
```

```
curl -s -O inlanefreight.com/index.html
```

```
curl -h
```

```
curl --help all
```

```
man curl
```

**cURL Flags**

```
-d
--data
```

```
-h
--help
```

```
-i
--include
```

```
-o
--output
```

```
-O
--remote-name
```

```
-s
--silent
```

```
-u
--user
```

```
-A
--user-agent
```

```
-v
--verbose
```

**File Paths**

```
/etc/hosts
```

---


# Hypertext Transfer Protocol Secure (HTTPS)

**cURL Commands**

```
curl https://inlanefreight.com
```

```
curl -k https://www.inlanefreight.com
```

**cURL Flags**

```
-k
```

---


# HTTP Requests and Responses

**cURL Commands**

```bash
curl inlanefreight.com -v
```

```bash
curl inlanefreight.com -vvv
```

**HTTP Request Example**

```http
GET /users/login.html HTTP/1.1
Host: inlanefreight.com
User-Agent: Mozilla/5.0
Cookie: PHPSESSID=c4ggt4jull9obt7aupa55o8vbf
```

**HTTP Response Example**

```http
HTTP/1.1 200 OK
Date: Tue, 21 Jul 2020 05:20:15 GMT
Server: Apache/2.4.41
Set-Cookie: PHPSESSID=m4u64rqlpfthrvvb12ai9voqgf
Content-Type: text/html; charset=UTF-8
```

**Verbose cURL Request/Response Example**

```http
GET / HTTP/1.1
Host: inlanefreight.com
User-Agent: curl/7.65.3
Accept: */*
Connection: close
```

```http
HTTP/1.1 401 Unauthorized
Date: Tue, 21 Jul 2020 05:20:15 GMT
Server: Apache/X.Y.ZZ (Ubuntu)
WWW-Authenticate: Basic realm="Restricted Content"
Content-Length: 464
Content-Type: text/html; charset=iso-8859-1
```

**Browser DevTools**  
CTRL+SHIFT+I  
F12  
Network  
Filter URLs  
Response  
Raw

**HTTP Fields**  
GET  
/users/login.html  
HTTP/1.1  
200 OK  
401 Unauthorized

**Headers**  
Host  
User-Agent  
Cookie  
Date  
Server  
Set-Cookie  
Content-Type  
Accept  
Connection  
WWW-Authenticate  
Content-Length

**Cookie Values**  
PHPSESSID=c4ggt4jull9obt7aupa55o8vbf  
PHPSESSID=m4u64rqlpfthrvvb12ai9voqgf

---


# HTTP Headers

**cURL Commands**

```bash
curl -I https://www.inlanefreight.com
```

```bash
curl https://www.inlanefreight.com -A 'Mozilla/5.0'
```

```bash
curl https://www.inlanefreight.com -I -A 'Mozilla/5.0'
```

```bash
curl https://www.inlanefreight.com -v -A 'Mozilla/5.0'
```

**cURL Flags**

```bash
-I
```

```bash
-i
```

```bash
-H
```

```bash
-A
```

```bash
-v
```

**General Headers**

```http
Date: Wed, 16 Feb 2022 10:38:44 GMT
```

```http
Connection: close
```

```http
Connection: keep-alive
```

**Entity Headers**

```http
Content-Type: text/html
```

```http
Media-Type: application/pdf
```

```http
boundary="b4e4fbd93540"
```

```http
Content-Length: 385
```

```http
Content-Encoding: gzip
```

**Request Headers**

```http
Host: www.inlanefreight.com
```

```http
User-Agent: curl/7.77.0
```

```http
Referer: http://www.inlanefreight.com/
```

```http
Accept: */*
```

```http
Cookie: PHPSESSID=b4e4fbd93540
```

```http
Authorization: BASIC cGFzc3dvcmQK
```

**Response Headers**

```http
Server: Apache/2.2.14 (Win32)
```

```http
Set-Cookie: PHPSESSID=b4e4fbd93540
```

```http
WWW-Authenticate: BASIC realm="localhost"
```

**Security Headers**

```http
Content-Security-Policy: script-src 'self'
```

```http
Strict-Transport-Security: max-age=31536000
```

```http
Referrer-Policy: origin
```

**Example Header Dump**

```http
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

**Browser DevTools**  
Network  
Headers  
Raw  
Cookies

---


# HTTP Methods and Codes 

**HTTP Request Line Examples**

```http
GET / HTTP/1.1
```

```http
GET /resource?param=value HTTP/1.1
```

```http
POST / HTTP/1.1
```

```http
HEAD / HTTP/1.1
```

```http
PUT / HTTP/1.1
```

```http
DELETE / HTTP/1.1
```

```http
OPTIONS / HTTP/1.1
```

```http
PATCH / HTTP/1.1
```

**HTTP Methods**  
GET  
POST  
HEAD  
PUT  
DELETE  
OPTIONS  
PATCH

**Query String Example**

```http
?param=value
```

**HTTP Status Codes**  
200 OK  
302 Found  
400 Bad Request  
403 Forbidden  
404 Not Found  
500 Internal Server Error

**HTTP Status Code Classes**  
1xx  
2xx  
3xx  
4xx  
5xx

---


# GET

**URLs**

```text
http://<SERVER_IP>:<PORT>/
```

```text
http://admin:admin@<SERVER_IP>:<PORT>/
```

```text
http://<SERVER_IP>:<PORT>/search.php?search=le
```

**cURL Commands**

```bash
curl -i http://<SERVER_IP>:<PORT>/
```

```bash
curl -u admin:admin http://<SERVER_IP>:<PORT>/
```

```bash
curl http://admin:admin@<SERVER_IP>:<PORT>/
```

```bash
curl -v http://admin:admin@<SERVER_IP>:<PORT>/
```

```bash
curl -H 'Authorization: Basic YWRtaW46YWRtaW4=' http://<SERVER_IP>:<PORT>/
```

```bash
curl 'http://<SERVER_IP>:<PORT>/search.php?search=le' -H 'Authorization: Basic YWRtaW46YWRtaW4='
```

**HTTP Requests**

```http
GET / HTTP/1.1
Host: <SERVER_IP>
Authorization: Basic YWRtaW46YWRtaW4=
User-Agent: curl/7.77.0
Accept: */*
```

```http
GET /search.php?search=le HTTP/1.1
Authorization: Basic YWRtaW46YWRtaW4=
```

**HTTP Headers**

```http
WWW-Authenticate: Basic realm="Access denied"
```

```http
Authorization: Basic YWRtaW46YWRtaW4=
```

**Credentials**

```text
admin:admin
```

**Encoded Values**

```text
YWRtaW46YWRtaW4=
```

**Parameters**

```text
search=le
```

**Paths**

```text
/search.php
```

**Browser Shortcuts / Tools**  
CTRL+SHIFT+E  
CTRL+SHIFT+K  
Network  
Copy  
Copy as cURL  
Copy as Fetch  
JavaScript console


# POST

**URLs**

```text
http://<SERVER_IP>:<PORT>/
```

```text
http://server_ip/index.php
```

```text
http://<SERVER_IP>:<PORT>/search.php
```

**POST Data**

```bash
username=admin&password=admin
```

```json
{"search":"london"}
```

**cURL Commands**

```bash
curl -X POST -d 'username=admin&password=admin' http://<SERVER_IP>:<PORT>/
```

```bash
curl -X POST -d 'username=admin&password=admin' http://<SERVER_IP>:<PORT>/ -i
```

```bash
curl -b 'PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' http://<SERVER_IP>:<PORT>/
```

```bash
curl -H 'Cookie: PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' http://<SERVER_IP>:<PORT>/
```

```bash
curl -X POST -d '{"search":"london"}' -b 'PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' -H 'Content-Type: application/json' http://<SERVER_IP>:<PORT>/search.php
```

**cURL Flags**

```bash
-X POST
```

```bash
-d
```

```bash
-L
```

```bash
-v
```

```bash
-i
```

```bash
-b
```

```bash
-H
```

**HTTP Request**

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

**HTTP Headers**

```http
Set-Cookie: PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1; path=/
```

```http
Cookie: PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1
```

```http
Content-Type: application/json
```

```http
Referer: http://server_ip/index.php
```

```http
Origin: http://server_ip
```

**Cookie Names and Values**

```text
PHPSESSID
```

```text
PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1
```

**Paths**

```text
/search.php
```

```text
/index.php
```

**Browser DevTools / Shortcuts**  
Network  
Request  
Raw  
Copy  
Copy as cURL  
Copy Request Headers  
Copy as Fetch  
Storage  
Cookies  
Delete All  
SHIFT+F9  
Console


#
#