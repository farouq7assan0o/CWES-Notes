# Introduction 

**Tools**

ffuf wfuzz Burp Suite Intruder

---

# Tooling 

**Install Dependencies**

```bash
sudo apt update
```

```bash
sudo apt install -y golang
```

```bash
sudo apt install -y python3 python3-pip
```

```bash
sudo apt install pipx
pipx ensurepath
sudo pipx ensurepath --global
```

```bash
go version
python3 --version
```

**Install FFUF**

```bash
go install github.com/ffuf/ffuf/v2@latest
```

**Install Gobuster**

```bash
go install github.com/OJ/gobuster/v3@latest
```

**Install FeroxBuster**

```bash
curl -sL https://raw.githubusercontent.com/epi052/feroxbuster/main/install-nix.sh | sudo bash -s $HOME/.local/bin
```

**Install wenum**

```bash
pipx install git+https://github.com/WebFuzzForge/wenum
pipx runpip wenum install setuptools
```

---

# Directory and File Fuzzing 

**Wordlist Paths**

```text
/usr/share/seclists/Discovery/Web-Content/common.txt
/usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
/usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt
/usr/share/seclists/Discovery/Web-Content/big.txt
```

**URL Pattern**

```http
http://IP:PORT/FUZZ
```

**Directory Fuzzing**

```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://IP:PORT/FUZZ
```

**File Fuzzing**

```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u http://IP:PORT/w2ksvrus/FUZZ -e .php,.html,.txt,.bak,.js -v
```

---


# Recursive Fuzzing 

**Wordlist Path**

```text
/usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
```

**Recursive Fuzzing - Basic**

```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -ic -v -u http://IP:PORT/FUZZ -e .html -recursion
```

**Recursive Fuzzing - Depth + Rate Limited**

```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -ic -u http://IP:PORT/FUZZ -e .html -recursion -recursion-depth 2 -rate 500
```

**Flags Reference**

```text
-recursion
-recursion-depth 2
-rate 500
-ic
-v
-e .html
```

---


# Parameter and Value Fuzzing 

**Wordlist Path**

```text
/usr/share/seclists/Discovery/Web-Content/common.txt
```

**Install wenum**

```bash
pipx install git+https://github.com/WebFuzzForge/wenum
pipx runpip wenum install setuptools
```

**Probe GET Endpoint Manually**

```bash
curl http://IP:PORT/get.php
```

```bash
curl http://IP:PORT/get.php?x=1
```

**Fuzz GET Parameter Value - wenum**

```bash
wenum -w /usr/share/seclists/Discovery/Web-Content/common.txt --hc 404 -u "http://IP:PORT/get.php?x=FUZZ"
```

**Validate GET Result**

```bash
curl http://IP:PORT/get.php?x=OA...
```

**Probe POST Endpoint Manually**

```bash
curl -d "" http://IP:PORT/post.php
```

**Fuzz POST Parameter Value - ffuf**

```bash
ffuf -u http://IP:PORT/post.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "y=FUZZ" -w /usr/share/seclists/Discovery/Web-Content/common.txt -mc 200 -v
```

**Validate POST Result**

```bash
curl -d "y=SU..." http://IP:PORT/post.php
```

**POST Request Pattern**

```http
POST /login HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded

username=your_username&password=your_password
```

# Virtual Host and Subdomain Fuzzing 

**Wordlist Paths**

```text
/usr/share/seclists/Discovery/Web-Content/common.txt
/usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
```

**Add vhost to /etc/hosts**

```bash
echo "IP inlanefreight.htb" | sudo tee -a /etc/hosts
```

**Gobuster vhost Fuzzing**

```bash
gobuster vhost -u http://inlanefreight.htb:81 -w /usr/share/seclists/Discovery/Web-Content/common.txt --append-domain
```

**Gobuster Subdomain Fuzzing**

```bash
gobuster dns -d inlanefreight.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
```

**Gobuster Subdomain Fuzzing (latest version flag)**

```bash
gobuster dns --domain inlanefreight.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
```

---


# Filtering Fuzzing Output 

**Gobuster Filters**

```bash
gobuster dir -u http://example.com/ -w wordlist.txt -s 200,301 --exclude-length 0
```

**ffuf Filters - Combined Examples**

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

**ffuf - Default Matcher (no filter flag)**

```bash
ffuf -u http://IP:PORT/post.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "y=FUZZ" -w /usr/share/seclists/Discovery/Web-Content/common.txt -v
```

**ffuf - Match All Status Codes**

```bash
ffuf -u http://IP:PORT/post.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "y=FUZZ" -w /usr/share/seclists/Discovery/Web-Content/common.txt -v -mc all
```

**wenum Filters - Combined Examples**

```bash
wenum -w wordlist.txt --sc 200,301,302 -u https://example.com/FUZZ
```

```bash
wenum -w wordlist.txt --hc 404,400,500 -u https://example.com/FUZZ
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

**FeroxBuster Filters - Combined Example**

```bash
feroxbuster --url http://example.com -w wordlist.txt -s 200 -S 10240 -X "error"
```

**Filter Flag Quick Reference**

```text
# gobuster
-s             include status codes (dir mode only)
-b             exclude status codes (dir mode only)
--exclude-length

# ffuf
-mc            match status code
-fc            filter status code
-ms            match size
-fs            filter size
-mw            match word count
-fw            filter word count
-ml            match line count
-fl            filter line count
-mt            match time (TTFB)

# wenum
--sc           show code
--hc           hide code
--sl           show length (lines)
--hl           hide length (lines)
--sw           show word count
--hw           hide word count
--ss           show size (bytes)
--hs           hide size (bytes)
--sr           show regex match
--hr           hide regex match
--filter       show regex (with plugin processing)
--hard-filter  hide regex (blocks plugin processing)

# feroxbuster
-s / --status-codes     allowlist status codes
-C / --filter-status    denylist status codes
-S / --filter-size      filter by size
-W / --filter-words     filter by word count
-N / --filter-lines     filter by line count
-X / --filter-regex     filter by body/header regex
--filter-similar-to     filter near-duplicate pages
--dont-scan             exclude URLs from scanning
```

---


# Validating Findings 

**Check Directory Listing**

```bash
curl http://IP:PORT/backup/
```

**Retrieve Headers Only (no body)**

```bash
curl -I http://IP:PORT/backup/password.txt
```

**Endpoints / Paths**

```text
/backup/
/backup/password.txt
/backup/backup.sql
```

---


# Web APIs 

**REST API Example Request**

```http
GET /users/123
```

**SOAP API Example Request**

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

**GraphQL Example Query**

```graphql
query {
  user(id: 123) {
    name
    email
  }
}
```

# Identifying Endpoints 

**REST Endpoint Patterns**

```http
/users
/users/123
/products
/products/456
/users?limit=10&sort=name
```

**REST Request Body Parameter Example**

```http
POST /products HTTP/1.1
Content-Type: application/json

{ "name": "New Product", "price": 99.99 }
```

**SOAP Endpoint Pattern**

```text
/soap
/api
/service
```

**SOAP SearchBooks Request**

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

**GraphQL Endpoint Pattern**

```text
/graphql
```

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

**Tools for Endpoint Discovery**

```text
Burp Suite
Wireshark
tcpdump
GraphiQL
GraphQL Playground
ffuf
wfuzz / wenum
```

---

# API Fuzzing 

**API Documentation Endpoint**

```http
http://IP:PORT/docs
```

**Documented API Endpoints**

```text
GET /
GET /items/{item_id}
DELETE /items/{item_id}
PUT /items/{item_id}
POST /items/
```

**Clone and Install API Fuzzer**

```bash
git clone https://github.com/PandaSt0rm/webfuzz_api.git
cd webfuzz_api
pip3 install -r requirements.txt
```

**Run API Fuzzer**

```bash
python3 api_fuzzer.py http://IP:PORT
```

**Validate Discovered Endpoint**

```bash
curl http://localhost:8000/cz...
```

# Skills Assessment 

**Wordlist Path**

```text
/usr/share/seclists/Discovery/Web-Content/common.txt
/usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
/usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
```

**Step 1 - Directory Fuzzing**

```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://154.57.164.70:30580/FUZZ
```

**Step 2 - File Fuzzing in Discovered Directory**

```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u http://154.57.164.70:30580/admin/FUZZ -e .php,.html,.txt,.bak,.js -v
```

**Step 3 - Probe Discovered Endpoint**

```http
http://154.57.164.70:30580/admin/panel.php?accessID=1
```

**Step 4 - Fuzz GET Parameter Value - wenum**

```bash
wenum -w /usr/share/seclists/Discovery/Web-Content/common.txt --hc 404 -u "http://154.57.164.70:30580/admin/panel.php?accessID=FUZZ"
```

**Step 4 (alt) - Fuzz GET Parameter Value - ffuf**

```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt:FUZZ -u http://154.57.164.70:30580/admin/panel.php?accessID=FUZZ -fs 58
```

**Step 5 - Validate Parameter Value**

```http
http://154.57.164.70:30580/admin/panel.php?accessID=getaccess
```

**Step 6 - vhost Fuzzing**

```bash
ffuf -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://fuzzing_fun.htb:30580/ -H "Host: FUZZ.fuzzing_fun.htb" -fs 250-350
```

**Step 7 - Directory Fuzzing on Discovered vhost**

```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://hidden.fuzzing_fun.htb:30580/godeep/
```

**Step 8 - Recursive Fuzzing on Discovered Path**

```bash
feroxbuster -u http://hidden.fuzzing_fun.htb:30580/godeep/ -w /usr/share/seclists/Discovery/Web-Content/common.txt -r -t 50 -k
```

**URL Pattern for vhost**

```http
http://FUZZ.fuzzing_fun.htb:30580
```

---

