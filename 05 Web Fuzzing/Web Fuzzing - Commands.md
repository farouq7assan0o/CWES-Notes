# Web Fuzzing

**Introduction**

ffuf  
wfuzz  
Burp Suite Intruder

admin  
login  
password  
backup  
config

productID  
addToCart  
checkout

' OR 1=1 --


# Tooling

**Installing Go, Python and PIPX**

```
sudo apt update
```

```
sudo apt install -y golang
```

```
sudo apt install -y python3 python3-pip
```

```
sudo apt install pipx
```

```
pipx ensurepath
```

```
sudo pipx ensurepath --global
```

```
go version
```

```
python3 --version
```

**FFUF**

```
go install github.com/ffuf/ffuf/v2@latest
```

**Gobuster**

```
go install github.com/OJ/gobuster/v3@latest
```

**FeroxBuster**

```
curl -sL https://raw.githubusercontent.com/epi052/feroxbuster/main/install-nix.sh | sudo bash -s $HOME/.local/bin
```

**wfuzz / wenum**

```
pipx install git+https://github.com/WebFuzzForge/wenum
```

```
pipx runpip wenum install setuptools
```

---


# Directory and File Fuzzing 

**Wordlists**

/usr/share/seclists/  
/usr/share/seclists/Discovery/Web-Content/common.txt  
/usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt  
/usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt  
/usr/share/seclists/Discovery/Web-Content/big.txt

[https://github.com/danielmiessler/SecLists](https://github.com/danielmiessler/SecLists)

**FUZZ Placeholder**

```http
http://localhost/FUZZ
```

**Directory Fuzzing**

```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://IP:PORT/FUZZ
```
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://20.174.3.2`/FUZZ

`
**File Fuzzing**

```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u http://IP:PORT/w2ksvrus/FUZZ -e .php,.html,.txt,.bak,.js -v
```


ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u http://172.16.16.147:5121//FUZZ -e .php,.html,.txt,.bak,.js -v

**Tools**


ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u https://scc1.jaf.mil.jo/FUZZ  

ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u https://scc1.jaf.mil.jo/FUZZ -e .php,.html,.txt,.bak,.js -v

ffuf  
wfuzz  
SecLists
```
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://154.57.164.73:30183/FUZZ  
```
```
ffuf -w /usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt  -u http://154.57.164.73:30183/FUZZ
```
```
ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u http://94.237.123.185:56611/webfuzzing_hidden_path//flag/FUZZ -e .php,.html,.txt,.bak,.js -v
```

# Recursive Fuzzing 

**Recursive Fuzzing with ffuf**

```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -ic -v -u http://IP:PORT/FUZZ -e .html -recursion
```

**Responsible Recursive Fuzzing**

```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -ic -u http://IP:PORT/FUZZ -e .html -recursion -recursion-depth 2 -rate 500
```

**Wordlist**

/usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt

**URL Patterns**

[http://IP:PORT/FUZZ](http://ip:PORT/FUZZ)  
[http://localhost/admin/FUZZ](http://localhost/admin/FUZZ)

**Flags and Parameters**

-recursion  
-ic  
-v  
-e .html  
-recursion-depth 2  
-rate 500  
-timeout

```
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -ic -v -u http://83.136.251.11:44651/recursive_fuzz/FUZZ -e .html -recursion
```
# Parameter and Value Fuzzing

**Wordlist**

/usr/share/seclists/Discovery/Web-Content/common.txt

**wenum Installation**

```bash
pipx install git+https://github.com/WebFuzzForge/wenum
```

```bash
pipx runpip wenum install setuptools
```

**GET Request Testing**

```bash
curl http://IP:PORT/get.php
```

```bash
curl http://IP:PORT/get.php?x=1
```

```
git clone https://github.com/WebFuzzForge/wenum
cd wenum
pip install -r requirements.txt
python3 wenum.py
```
**GET Parameter Fuzzing**

```bash
wenum -w /usr/share/seclists/Discovery/Web-Content/common.txt --hc 404 -u "http://IP:PORT/get.php?x=FUZZ"
```

**POST Request Testing**

```bash
curl -d "" http://IP:PORT/post.php
```

**POST Parameter Fuzzing**

```bash
ffuf -u http://IP:PORT/post.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "y=FUZZ" -w /usr/share/seclists/Discovery/Web-Content/common.txt -mc 200 -v
```

**POST Parameter Validation**

```bash
curl -d "y=SU..." http://IP:PORT/post.php
```

**GET Parameter Pattern**

```http
https://example.com/search?query=fuzzing&category=security
```

**POST Request Structure**

```http
POST /login HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded

username=your_username&password=your_password
```


# Virtual Host and Subdomain Fuzzing

**Hosts File**

```bash
echo "IP inlanefreight.htb" | sudo tee -a /etc/hosts
```

**Wordlists**

/usr/share/seclists/Discovery/Web-Content/common.txt  
/usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

**Gobuster VHost Fuzzing**

```bash
gobuster vhost -u http://inlanefreight.htb:81 -w /usr/share/seclists/Discovery/Web-Content/common.txt --append-domain
```

**Gobuster Subdomain Fuzzing**

```bash
gobuster dns -d inlanefreight.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
```

**Latest Gobuster Domain Flag**

--do  
--domain

**Targets and Domains**

[http://inlanefreight.htb:81](http://inlanefreight.htb:81/)  
inlanefreight.htb  
inlanefreight.com

**Tools**

Gobuster


# Filtering Fuzzing Output

**Gobuster**

```bash
gobuster dir -u http://example.com/ -w wordlist.txt -s 200,301 --exclude-length 0
```

**FFUF**

```bash
ffuf -u http://example.com/FUZZ -w wordlist.txt -mc 200 -fw 427 -ms >500
```

```bash
ffuf -u http://example.com/FUZZ -w wordlist.txt -fc 404,401,302
```

```bash
ffuf -u http://example.com/FUZZ.bak -w wordlist.txt -fs 0-10239 -ms 10240-102400
```

```bash
ffuf -u http://example.com/FUZZ -w wordlist.txt -mt >500
```

```bash
ffuf -u http://IP:PORT/post.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "y=FUZZ" -w /usr/share/seclists/Discovery/Web-Content/common.txt -v
```

```bash
ffuf -u http://IP:PORT/post.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "y=FUZZ" -w /usr/share/seclists/Discovery/Web-Content/common.txt -v -mc all
```

**wenum**

```bash
wenum -w wordlist.txt --sc 200,301,302 -u https://example.com/FUZZ
```

```bash
wenu -w wordlist.txt --hc 404,400,500 -u https://example.com/FUZZ
```

```bash
wenum -w wordlist.txt --sw 5-10 -u https://example.com/FUZZ
```

```bash
wenum -w wordlist.txt --hs 10000 -u https://example.com/FUZZ
```

```bash
wenum -w wordlist.txt --sr "admin\|password" -u https://example.com/FUZZ
```

```bash
--filter "Login"
```

```bash
--hard-filter "Login"
```

**Feroxbuster**

```bash
feroxbuster --url http://example.com -w wordlist.txt -s 200 -S 10240 -X "error"
```

**Flags and Parameters**

-s  
-b  
--exclude-length  
-mc  
-fc  
-fs  
-ms  
-fw  
-mw  
-fl  
-ml  
-mt  
--hc  
--sc  
--hl  
--sl  
--hw  
--sw  
--hs  
--ss  
--hr  
--sr  
--filter  
--hard-filter  
--dont-scan  
-S  
-X  
-W  
-N  
-C  
--filter-similar-to  
-s

**Wordlists and Paths**

/usr/share/seclists/Discovery/Web-Content/common.txt


# Validating Findings

**Validation Requests**

```bash
curl http://IP:PORT/backup/
```

```bash
curl -I http://IP:PORT/backup/password.txt
```

**Paths and Files**

/backup/  
/backup/password.txt  
backup.sql  
password.txt

**HTTP Headers**

Content-Type  
Content-Length  
ETag  
Last-Modified  
Accept-Ranges  
Date  
Server

**HTML Indicators**

```html
<title>Index of /backup/</title>
```

```html
<h2>Index of /backup/</h2>
```

```html
<a href="backup.sql">backup.sql</a>
```

```html
<div class="foot">lighttpd/1.4.76</div>
```


# Web APIs 

**REST Example**

```http
GET /users/123
```

**SOAP Example**

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:tem="http://tempuri.org/">
   <soapenv:Header/>
   <soapenv:Body>
      <tem:GetStockPrice>
         <tem:StockName>AAPL</tem:StockName>
      </tem:GetStockPrice>
   </soapenv:Body>
</soapenv:Envelope>
```

**GraphQL Example**

```graphql
query {
  user(id: 123) {
    name
    email
  }
}
```

**HTTP Methods**

GET  
POST  
PUT  
DELETE

**Data Formats**

JSON  
XML

**API Endpoint Example**

/users/123


# Identifying Endpoints 

**REST Endpoints**

/users  
/users/123  
/products  
/products/456

**REST Query Parameters**

/users?limit=10&sort=name

**REST Path Parameter Pattern**

/products/{id}

**REST Request Body Example**

```json
{ "name": "New Product", "price": 99.99 }
```

**SOAP Request Example**

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:lib="http://example.com/library">
   <soapenv:Header/>
   <soapenv:Body>
      <lib:SearchBooks>
         <lib:keywords>cybersecurity</lib:keywords>
         <lib:author>Dan Kaminsky</lib:author>
      </lib:SearchBooks>
   </soapenv:Body>
</soapenv:Envelope>
```

**SOAP Parameters**

keywords  
author  
genre

**GraphQL Endpoint**

/graphql

**GraphQL Query Example**

```graphql
query {
  user(id: 123) {
    name
    email
    posts(limit: 5) {
      title
      body
    }
  }
}
```

**GraphQL Mutation Example**

```graphql
mutation {
  createPost(title: "New Post", body: "This is the content of the new post") {
    id
    title
  }
}
```

**GraphQL Fields**

name  
email  
posts  
title  
body

**GraphQL Arguments**

id: 123  
limit: 5  
title: "New Post"  
body: "This is the content of the new post"

---


# API Fuzzing

**API Documentation Endpoint**

/docs

**Documented Endpoints**

GET /  
GET /items/{item_id}  
DELETE /items/{item_id}  
PUT /items/{item_id}  
POST /items/

**Tool Setup**

```bash
git clone https://github.com/PandaSt0rm/webfuzz_api.git
```

```bash
cd webfuzz_api
```

```bash
pip3 install -r requirements.txt
```

**Run Fuzzer**

```bash
python3 api_fuzzer.py http://IP:PORT
```

**Validation Request**

```bash
curl http://localhost:8000/cz...
```

**Discovered / Referenced Endpoints**

[http://IP:PORT/docs](http://ip:PORT/docs)  
[http://localhost:8000/cz](http://localhost:8000/cz)...  
[http://localhost:8000/docs](http://localhost:8000/docs)  
[http://localhost:8000/items](http://localhost:8000/items)

**API Methods**

GET  
POST  
PUT  
DELETE

**Vulnerability Types**

Broken Object-Level Authorization  
Broken Function Level Authorization  
Server-Side Request Forgery (SSRF)


#
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://154.57.164.70:30580/FUZZ

ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u http://154.57.164.70:30580/admin/FUZZ -e .php,.html,.txt,.bak,.js -v

http://154.57.164.70:30580/admin/panel.php?accessID=1

wenum -w /usr/share/seclists/Discovery/Web-Content/common.txt --hc 404 -u "http://154.57.164.70:30580/admin/panel.php?accessID=FUZZ"

ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt:FUZZ -u http://154.57.164.70:30580/admin/panel.php?accessID=FUZZ -fs 58

http://154.57.164.70:30580/admin/panel.php?accessID=getaccess

http://FUZZ.fuzzing_fun.htb:30580

gobuster dns -d http://FUZZ.fuzzing_fun.htb:30580 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

ffuf -c \
-w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ \
-u http://fuzzing_fun.htb:30580/ \
-H "Host: FUZZ.fuzzing_fun.htb" \
-fs 250-350

20000.txt

ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://hidden.fuzzing_fun.htb:30580/godeep/

```
feroxbuster -u http://hidden.fuzzing_fun.htb:30580/godeep/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt -r -t 50 -k
```


#



