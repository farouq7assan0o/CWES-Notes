# INTRODUCTION 

**TOOLS**

Nmap  
Masscan  
Unicornscan  
Nessus  
OpenVAS  
Nikto  
Traceroute  
Netcat  
curl  
Xprobe2  
Burp Suite Spider  
OWASP ZAP Spider  
Scrapy  
Google  
DuckDuckGo  
Bing  
Shodan  
whois  
dig  
nslookup  
host  
dnsenum  
fierce  
dnsrecon  
Wayback Machine  
LinkedIn  
Twitter  
Facebook  
GitHub  
GitLab

# WHOIS

**WHOIS LOOKUP**

```
whois inlanefreight.com
```

---

# UTILISING WHOIS 

## INSTALLATION

```bash
sudo apt update
sudo apt install whois -y
```

## WHOIS LOOKUPS

```bash
whois facebook.com
```

## TARGETS

```text
inlanefreight.com
facebook.com
```

## SERVICES

```text
whois
```


# DNS 

**HOSTS FILE PATHS**

```text
C:\Windows\System32\drivers\etc\hosts
```

```text
/etc/hosts
```

**HOSTS FILE ENTRY FORMAT**

```text
<IP Address>    <Hostname> [<Alias> ...]
```

**HOSTS FILE EXAMPLES**

```text
127.0.0.1       localhost
```

```text
192.168.1.10    devserver.local
```

```text
127.0.0.1       myapp.local
```

```text
192.168.1.20    testserver.local
```

```text
0.0.0.0       unwanted-site.com
```

**DNS ZONE FILE EXAMPLE**

```dns-zone
$TTL 3600 ; Default Time-To-Live (1 hour)
@       IN SOA   ns1.example.com. admin.example.com. (
                2024060401 ; Serial number (YYYYMMDDNN)
                3600       ; Refresh interval
                900        ; Retry interval
                604800     ; Expire time
                86400 )    ; Minimum TTL

@       IN NS    ns1.example.com.
@       IN NS    ns2.example.com.
@       IN MX 10 mail.example.com.
www     IN A     192.0.2.1
mail    IN A     198.51.100.1
ftp     IN CNAME www.example.com.
```

**DNS RECORD EXAMPLES**

```text
www.example.com. IN A 192.0.2.1
```

```text
www.example.com. IN AAAA 2001:db8:85a3::8a2e:370:7334
```

```text
blog.example.com. IN CNAME webserver.example.net.
```

```text
example.com. IN MX 10 mail.example.com.
```

```text
example.com. IN NS ns1.example.com.
```

```text
example.com. IN TXT "v=spf1 mx -all"
```

```text
example.com. IN SOA ns1.example.com. admin.example.com. 2024060301 10800 3600 604800 86400
```

```text
_sip._udp.example.com. IN SRV 10 5 5060 sipserver.example.com.
```

```text
1.2.0.192.in-addr.arpa. IN PTR www.example.com.
```

---


# DIGGING DNS 

## DNS TOOLS

dig  
nslookup  
host  
dnsenum  
fierce  
dnsrecon  
theHarvester

## COMMON DIG COMMANDS

```text
dig domain.com
dig domain.com A
dig domain.com AAAA
dig domain.com MX
dig domain.com NS
dig domain.com TXT
dig domain.com CNAME
dig domain.com SOA
dig @1.1.1.1 domain.com
dig +trace domain.com
dig -x 192.168.1.1
dig +short domain.com
dig +noall +answer domain.com
dig domain.com ANY
```

## EXAMPLE COMMANDS

```text
dig google.com
dig +short hackthebox.com
```

## PARAMETERS

```text
@1.1.1.1
+trace
-x
+short
+noall
+answer
ANY
A
AAAA
MX
NS
TXT
CNAME
SOA
```

## TARGETS

```text
domain.com
google.com
hackthebox.com
192.168.1.1
```


# SUBDOMAINS 

**TOOLS**

dnsenum  
ffuf  
gobuster

**SEARCH OPERATORS**

```text
site:example.com
```

**DNS RECORD TYPES USED**

```text
A
AAAA
CNAME
```

---


# SUBDOMAIN BRUTEFORCING 

**TOOLS**

dnsenum  
fierce  
dnsrecon  
amass  
assetfinder  
puredns

**WORDLISTS**

```text
/usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt
```

**COMMANDS**

```bash
dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt -r
```

**FLAGS**

```text
--enum
-f
-r
```

**TARGETS**

```text
inlanefreight.com
```


# DNS ZONE TRANSFERS 

**COMMANDS**

```bash
dig axfr @nsztm1.digi.ninja zonetransfer.me
```

**PARAMETERS**

```text
axfr
@nsztm1.digi.ninja
```

**TARGETS**

```text
zonetransfer.me
```

**DNS RECORD TYPES**

```text
SOA
HINFO
TXT
MX
A
NS
SRV
PTR
AFSDB
```


# VIRTUAL HOSTS 

## CONFIGURATION EXAMPLES

```apache
<VirtualHost *:80>
    ServerName www.example1.com
    DocumentRoot /var/www/example1
</VirtualHost>

<VirtualHost *:80>
    ServerName www.example2.org
    DocumentRoot /var/www/example2
</VirtualHost>

<VirtualHost *:80>
    ServerName www.another-example.net
    DocumentRoot /var/www/another-example
</VirtualHost>
```

## TOOLS

gobuster  
Feroxbuster  
ffuf

## COMMAND PATTERNS

```text
gobuster vhost -u http://<target_IP_address> -w <wordlist_file> --append-domain
```

## COMMANDS

```bash
gobuster vhost -u http://inlanefreight.htb:81 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain
```

## FLAGS

```text
-u
-w
--append-domain
-t
-k
-o
```

## TARGETS

```text
http://<target_IP_address>
http://inlanefreight.htb:81
```

## WORDLISTS

```text
/usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt
```

## VHOSTS NEEDED

```text
inlanefreight.htb
```

## HOST HEADER

```text
Host
```


# CERTIFICATE TRANSPARENCY LOGS

**TOOLS**

crt.sh  
Censys  
curl  
jq

**COMMANDS**

```bash
curl -s "https://crt.sh/?q=facebook.com&output=json" | jq -r '.[] | select(.name_value | contains("dev")) | .name_value' | sort -u
```

**API ENDPOINTS**

```text
https://crt.sh/?q=facebook.com&output=json
```

**JSON FIELDS**

```text
name_value
```

**KEYWORDS**

```text
dev
```

**TARGETS**

```text
facebook.com
```

---


# FINGERPRINTING 

**TOOLS**

Wappalyzer  
BuiltWith  
WhatWeb  
Nmap  
Netcraft  
wafw00f  
curl  
pip3  
Nikto  
git  
perl

**COMMANDS**

```bash
curl -I inlanefreight.com
```

```bash
curl -I https://inlanefreight.com
```

```bash
curl -I https://www.inlanefreight.com
```

```bash
pip3 install git+https://github.com/EnableSecurity/wafw00f
```

```bash
wafw00f inlanefreight.com
```

```bash
sudo apt update && sudo apt install -y perl
```

```bash
git clone https://github.com/sullo/nikto
```

```bash
cd nikto/program
```

```bash
chmod +x ./nikto.pl
```

```bash
nikto -h inlanefreight.com -Tuning b
```

**FLAGS**

```text
-I
--head
-h
-Tuning b
-y
-x
```

**PATHS**

```text
/index.php/wp-json/
/index.php/wp-json/wp/v2/pages/7
/wp-login.php
/license.txt
nikto/program
./nikto.pl
```

**HEADERS**

```text
Server
X-Powered-By
X-Redirect-By
Link
Strict-Transport-Security
X-Content-Type-Options
Content-Encoding
X-Frame-Options
Host
```

**URLS**

```text
https://inlanefreight.com/
https://www.inlanefreight.com/
https://www.inlanefreight.com/index.php/wp-json/
https://www.inlanefreight.com/index.php/wp-json/wp/v2/pages/7
https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Link
https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security
https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/
http://breachattack.com/
https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies
https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options
https://github.com/EnableSecurity/wafw00f
https://github.com/sullo/nikto
```

**TARGETS**

```text
inlanefreight.com
https://inlanefreight.com
https://www.inlanefreight.com
app.inlanefreight.local
dev.inlanefreight.local
```

**TECHNOLOGIES**

```text
Apache/2.4.41 (Ubuntu)
WordPress
Wordfence
Defiant
TLS_AES_256_GCM_SHA384
Let's Encrypt
```


# CRAWLING 

**TOOLS**

Burp Suite Spider  
OWASP ZAP Spider  
Scrapy

**SEED STRUCTURE EXAMPLE**

```text
Homepage
├── link1
├── link2
└── link3
```

```text
link1 Page
├── Homepage
├── link2
├── link4
└── link5
```

**FILES OF INTEREST**

```text
.bak
.old
web.config
settings.php
error_log
access_log
```

**DIRECTORIES**

```text
/files/
```

---


# ROBOTS.TXT 

**PATHS**

```text
/robots.txt
/admin/
/private/
/public/
/sitemap.xml
```

**EXAMPLE FILE**

```txt
User-agent: *
Disallow: /admin/
Disallow: /private/
Allow: /public/

User-agent: Googlebot
Crawl-delay: 10

Sitemap: https://www.example.com/sitemap.xml
```

**DIRECTIVES**

```text
User-agent
Disallow
Allow
Crawl-delay
Sitemap
```

**USER AGENTS**

```text
*
Googlebot
Bingbot
```

**URLS**

```text
www.example.com/robots.txt
https://www.example.com/sitemap.xml
```

---


# WELL-KNOWN URIS 

**PATHS**

```text
/.well-known/
/.well-known/security.txt
/.well-known/change-password
/.well-known/openid-configuration
/.well-known/assetlinks.json
/.well-known/mta-sts.txt
```

**URL EXAMPLES**

```text
https://example.com/.well-known/security.txt
https://example.com/.well-known/openid-configuration
```

**OPENID CONFIGURATION JSON**

```json
{
  "issuer": "https://example.com",
  "authorization_endpoint": "https://example.com/oauth2/authorize",
  "token_endpoint": "https://example.com/oauth2/token",
  "userinfo_endpoint": "https://example.com/oauth2/userinfo",
  "jwks_uri": "https://example.com/oauth2/jwks",
  "response_types_supported": ["code", "token", "id_token"],
  "subject_types_supported": ["public"],
  "id_token_signing_alg_values_supported": ["RS256"],
  "scopes_supported": ["openid", "profile", "email"]
}
```

**ENDPOINTS**

```text
/oauth2/authorize
/oauth2/token
/oauth2/userinfo
/oauth2/jwks
```

**FIELDS**

```text
issuer
authorization_endpoint
token_endpoint
userinfo_endpoint
jwks_uri
response_types_supported
subject_types_supported
id_token_signing_alg_values_supported
scopes_supported
```

**PROTOCOLS**

```text
OpenID Connect
OAuth 2.0
```

---


# CREEPY CRAWLIES 

## TOOLS

Burp Suite Spider  
OWASP ZAP  
Scrapy  
Apache Nutch  
pip3  
wget  
unzip  
python3

## COMMANDS

```bash
pip3 install scrapy
```

```bash
wget -O ReconSpider.zip https://academy.hackthebox.com/storage/modules/144/ReconSpider.v1.2.zip
```

```bash
unzip ReconSpider.zip
```

```bash
python3 ReconSpider.py http://inlanefreight.com
```

## FILES

```text
ReconSpider.zip
ReconSpider.py
results.json
```

## URLS

```text
https://academy.hackthebox.com/storage/modules/144/ReconSpider.v1.2.zip
http://inlanefreight.com
https://www.themeansar.com
https://www.inlanefreight.com/index.php/offices/
https://www.inlanefreight.com/wp-content/uploads/2020/09/goals.pdf
https://www.inlanefreight.com/wp-includes/js/jquery/jquery-migrate.min.js?ver=3.3.2
https://www.inlanefreight.com/wp-content/uploads/2021/03/AboutUs_01-1024x810.png
```

## JSON KEYS

```text
emails
links
external_files
js_files
form_fields
images
videos
audio
comments
```

## JSON STRUCTURE

```json
{
    "emails": [
        "lily.floid@inlanefreight.com",
        "cvs@inlanefreight.com",
        ...
    ],
    "links": [
        "https://www.themeansar.com",
        "https://www.inlanefreight.com/index.php/offices/",
        ...
    ],
    "external_files": [
        "https://www.inlanefreight.com/wp-content/uploads/2020/09/goals.pdf",
        ...
    ],
    "js_files": [
        "https://www.inlanefreight.com/wp-includes/js/jquery/jquery-migrate.min.js?ver=3.3.2",
        ...
    ],
    "form_fields": [],
    "images": [
        "https://www.inlanefreight.com/wp-content/uploads/2021/03/AboutUs_01-1024x810.png",
        ...
    ],
    "videos": [],
    "audio": [],
    "comments": [
        "<!-- #masthead -->",
        ...
    ]
}
```


# SEARCH ENGINE DISCOVERY 

**SEARCH OPERATORS**

```text
site:
inurl:
filetype:
intitle:
intext:
inbody:
cache:
link:
related:
info:
define:
numrange:
allintext:
allinurl:
allintitle:
AND
OR
NOT
*
..
" "
-
```

**SEARCH QUERIES**

```text
site:example.com
```

```text
inurl:login
```

```text
filetype:pdf
```

```text
intitle:"confidential report"
```

```text
intext:"password reset"
```

```text
cache:example.com
```

```text
link:example.com
```

```text
related:example.com
```

```text
info:example.com
```

```text
define:phishing
```

```text
site:example.com numrange:1000-2000
```

```text
allintext:admin password reset
```

```text
allinurl:admin panel
```

```text
allintitle:confidential report 2023
```

```text
site:example.com AND (inurl:admin OR inurl:login)
```

```text
"linux" OR "ubuntu" OR "debian"
```

```text
site:bank.com NOT inurl:login
```

```text
site:socialnetwork.com filetype:pdf user* manual
```

```text
site:ecommerce.com "price" 100..500
```

```text
"information security policy"
```

```text
site:news.com -inurl:sports
```

**GOOGLE DORKS**

```text
site:example.com inurl:login
```

```text
site:example.com (inurl:login OR inurl:admin)
```

```text
site:example.com filetype:pdf
```

```text
site:example.com (filetype:xls OR filetype:docx)
```

```text
site:example.com inurl:config.php
```

```text
site:example.com (ext:conf OR ext:cnf)
```

```text
site:example.com inurl:backup
```

```text
site:example.com filetype:sql
```

**TARGETS**

```text
example.com
bank.com
socialnetwork.com
ecommerce.com
news.com
```


# WEB ARCHIVES 

**TOOLS**

Wayback Machine

**TARGETS**

```text
HackTheBox
```

**DATES**

```text
2017-06-10 @ 04h23:01
```


# AUTOMATING RECON 

## RECON FRAMEWORKS

FinalRecon  
Recon-ng  
theHarvester  
SpiderFoot  
OSINT Framework

---

## INSTALL FINALRECON

```bash
git clone https://github.com/thewhiteh4t/FinalRecon.git
```

```bash
cd FinalRecon
```

```bash
pip3 install -r requirements.txt
```

```bash
chmod +x ./finalrecon.py
```

```bash
./finalrecon.py --help
```

---

## BASIC USAGE

```bash
./finalrecon.py --url http://target.com
```

---

## HEADER + WHOIS RECON

```bash
./finalrecon.py --headers --whois --url http://inlanefreight.com
```

---

## COMMON MODULE FLAGS

```text
--headers
--sslinfo
--whois
--crawl
--dns
--sub
--dir
--wayback
--ps
--full
```

---

## EXTRA OPTIONS

```text
-nb
-dt
-pt
-T
-w
-r
-s
-sp
-d
-e
-o
-cd
-k
```

---


# 