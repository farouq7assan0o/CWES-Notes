# Introduction 

**Tools**

Nmap Masscan Unicornscan Nessus OpenVAS Nikto Netcat curl Burp Suite Spider OWASP ZAP Spider Scrapy whois dig nslookup host dnsenum fierce dnsrecon Wayback Machine

---

# WHOIS 
**Commands**

```shell
whois inlanefreight.com
```

**Tools**

whois WhoisFreaks

---
# Utilising WHOIS

**Installation**

```shell
sudo apt update
```

```shell
sudo apt install whois -y
```

**WHOIS Lookup**

```shell
whois facebook.com
```

---
# DNS 

**Hosts File Paths**

```text
C:\Windows\System32\drivers\etc\hosts
```

```text
/etc/hosts
```

**Hosts File Entry Format**

```text
<IP Address>    <Hostname> [<Alias> ...]
```

**Hosts File Example Entries**

```text
127.0.0.1       localhost
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

**Zone File Example**

```text
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

**DNS Record Type Examples**

```text
www.example.com.    IN A     192.0.2.1
www.example.com.    IN AAAA  2001:db8:85a3::8a2e:370:7334
blog.example.com.   IN CNAME webserver.example.net.
example.com.        IN MX 10 mail.example.com.
example.com.        IN NS    ns1.example.com.
example.com.        IN TXT   "v=spf1 mx -all"
example.com.        IN SOA   ns1.example.com. admin.example.com. 2024060301 10800 3600 604800 86400
_sip._udp.example.com. IN SRV 10 5 5060 sipserver.example.com.
1.2.0.192.in-addr.arpa. IN PTR www.example.com.
```

**Public DNS Resolvers**

```text
8.8.8.8
```

---
# Digging DNS 

**Tools**

dig nslookup host dnsenum fierce dnsrecon theHarvester

**dig Commands**

```shell
dig domain.com
```

```shell
dig domain.com A
```

```shell
dig domain.com AAAA
```

```shell
dig domain.com MX
```

```shell
dig domain.com NS
```

```shell
dig domain.com TXT
```

```shell
dig domain.com CNAME
```

```shell
dig domain.com SOA
```

```shell
dig @1.1.1.1 domain.com
```

```shell
dig +trace domain.com
```

```shell
dig -x 192.168.1.1
```

```shell
dig +short domain.com
```

```shell
dig +noall +answer domain.com
```

```shell
dig domain.com ANY
```

```shell
dig +short hackthebox.com
```

---

# Subdomains 

**Tools**

dnsenum ffuf gobuster

**Search Engine Operators**

```text
site:example.com
```

**External Resources**

Certificate Transparency (CT) logs

---


# Subdomain Bruteforcing

**Tools**

dnsenum fierce dnsrecon amass assetfinder puredns

**Wordlist Path**

```text
/usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt
```

**dnsenum Command**

```shell
dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt -r
```

---


# DNS Zone Transfers 

**Zone Transfer Command**

```shell
dig axfr @nsztm1.digi.ninja zonetransfer.me
```

---


# Virtual Hosts 

**Tools**

gobuster Feroxbuster ffuf

**Wordlist Path**

```text
/usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt
```

**gobuster VHost Syntax**

```shell
gobuster vhost -u http://<target_IP_address> -w <wordlist_file> --append-domain
```

**gobuster VHost Example**

```shell
gobuster vhost -u http://inlanefreight.htb:81 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain
```

**gobuster Additional Flags**

```text
-t        increase threads for faster scanning
-k        ignore SSL/TLS certificate errors
-o        save output to file
```

**Apache Name-Based VHost Config Example**

```apacheconf
# Example of name-based virtual host configuration in Apache
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

**Target VHost for Lab Questions**

```text
inlanefreight.htb
```

---


# Certificate Transparency Logs

**Tools / Services**

crt.sh Censys

**crt.sh API Query - Filter by Subdomain String**

```shell
curl -s "https://crt.sh/?q=facebook.com&output=json" | jq -r '.[] | select(.name_value | contains("dev")) | .name_value' | sort -u
```

**crt.sh API URL Pattern**

```text
https://crt.sh/?q=<domain>&output=json
```

---


# Fingerprinting 

**Tools**

Wappalyzer BuiltWith WhatWeb Nmap Netcraft wafw00f Nikto curl

**Banner Grabbing**

```shell
curl -I inlanefreight.com
```

```shell
curl -I https://inlanefreight.com
```

```shell
curl -I https://www.inlanefreight.com
```

**wafw00f Installation**

```shell
pip3 install git+https://github.com/EnableSecurity/wafw00f
```

**wafw00f Usage**

```shell
wafw00f inlanefreight.com
```

**Nikto Installation**

```shell
sudo apt update && sudo apt install -y perl
```

```shell
git clone https://github.com/sullo/nikto
```

```shell
cd nikto/program
```

```shell
chmod +x ./nikto.pl
```

**Nikto Fingerprinting Scan**

```shell
nikto -h inlanefreight.com -Tuning b
```

**Lab VHosts**

```text
app.inlanefreight.local
dev.inlanefreight.local
```

---


# Crawling 

**File Extensions of Interest**

```text
.bak
.old
web.config
settings.php
error_log
access_log
```

**Directory of Interest Pattern**

```text
/files/
```

---



# robots.txt

**File Location**

```text
www.example.com/robots.txt
```

**robots.txt Structure Example**

```text
User-agent: *
Disallow: /admin/
Disallow: /private/
Allow: /public/
User-agent: Googlebot
Crawl-delay: 10
Sitemap: https://www.example.com/sitemap.xml
```

**Directive Syntax Examples**

```text
Disallow: /admin/
Allow: /public/
Crawl-delay: 10
Sitemap: https://www.example.com/sitemap.xml
```

---

# Well-Known URIs 

**Base Path**

```text
/.well-known/
```

**Notable Well-Known URI Endpoints**

```text
https://example.com/.well-known/security.txt
https://example.com/.well-known/change-password
https://example.com/.well-known/openid-configuration
https://example.com/.well-known/assetlinks.json
https://example.com/.well-known/mta-sts.txt
```

**openid-configuration JSON Response Fields**

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

---


# Creepy Crawlies 

**Tools**

Burp Suite Spider OWASP ZAP Scrapy Apache Nutch

**Scrapy Installation**

```shell
pip3 install scrapy
```

**ReconSpider Download and Extract**

```shell
wget -O ReconSpider.zip https://academy.hackthebox.com/storage/modules/144/ReconSpider.v1.2.zip
```

```shell
unzip ReconSpider.zip
```

**ReconSpider Execution**

```shell
python3 ReconSpider.py http://inlanefreight.com
```

**Output File**

```text
results.json
```

**results.json Structure**

```json
{
    "emails": [
        "lily.floid@inlanefreight.com",
        "cvs@inlanefreight.com"
    ],
    "links": [
        "https://www.themeansar.com",
        "https://www.inlanefreight.com/index.php/offices/"
    ],
    "external_files": [
        "https://www.inlanefreight.com/wp-content/uploads/2020/09/goals.pdf"
    ],
    "js_files": [
        "https://www.inlanefreight.com/wp-includes/js/jquery/jquery-migrate.min.js?ver=3.3.2"
    ],
    "form_fields": [],
    "images": [
        "https://www.inlanefreight.com/wp-content/uploads/2021/03/AboutUs_01-1024x810.png"
    ],
    "videos": [],
    "audio": [],
    "comments": [
        "<!-- #masthead -->"
    ]
}
```

---


# Search Engine Discovery 

**Search Operators**

```text
site:example.com
inurl:login
filetype:pdf
intitle:"confidential report"
intext:"password reset"
cache:example.com
link:example.com
related:example.com
info:example.com
define:phishing
numrange:1000-2000
allintext:admin password reset
allinurl:admin panel
allintitle:confidential report 2023
site:example.com AND (inurl:admin OR inurl:login)
"linux" OR "ubuntu" OR "debian"
site:bank.com NOT inurl:login
site:socialnetwork.com filetype:pdf user* manual
site:ecommerce.com "price" 100..500
"information security policy"
site:news.com -inurl:sports
```

**Google Dorks - Login Pages**

```text
site:example.com inurl:login
site:example.com (inurl:login OR inurl:admin)
```

**Google Dorks - Exposed Files**

```text
site:example.com filetype:pdf
site:example.com (filetype:xls OR filetype:docx)
```

**Google Dorks - Configuration Files**

```text
site:example.com inurl:config.php
site:example.com (ext:conf OR ext:cnf)
```

**Google Dorks - Database Backups**

```text
site:example.com inurl:backup
site:example.com filetype:sql
```

**External Resource**

```text
https://www.exploit-db.com/google-hacking-database
```

---
# Web Archives 
**Tools / Services**

Wayback Machine

**Wayback Machine URL**

```text
https://web.archive.org
```

---


# Automating Recon 

**Tools / Frameworks**

FinalRecon Recon-ng theHarvester SpiderFoot OSINT Framework

**FinalRecon Installation**

```shell
git clone https://github.com/thewhiteh4t/FinalRecon.git
```

```shell
cd FinalRecon
```

```shell
pip3 install -r requirements.txt
```

```shell
chmod +x ./finalrecon.py
```

```shell
./finalrecon.py --help
```

**FinalRecon Usage - Flags**

```text
--url URL       Target URL
--headers       Header information
--sslinfo       SSL certificate information
--whois         Whois lookup
--crawl         Crawl target
--dns           DNS enumeration
--sub           Subdomain enumeration
--dir           Directory search
--wayback       Wayback URLs
--ps            Fast port scan
--full          Full recon
-nb             Hide banner
-dt DT          Threads for directory enum (default: 30)
-pt PT          Threads for port scan (default: 50)
-T T            Request timeout (default: 30.0)
-w W            Path to wordlist (default: wordlists/dirb_common.txt)
-r              Allow redirect (default: False)
-s              Toggle SSL verification (default: True)
-sp SP          SSL port (default: 443)
-d D            Custom DNS servers (default: 1.1.1.1)
-e E            File extensions (example: txt,xml,php)
-o O            Export format (default: txt)
-cd CD          Change export directory (default: ~/.local/share/finalrecon)
-k K            Add API key (example: shodan@key)
```

**FinalRecon Example Command**

```shell
./finalrecon.py --headers --whois --url http://inlanefreight.com
```

**FinalRecon Default Export Path**

```text
~/.local/share/finalrecon/dumps/
```

**FinalRecon Default Wordlist**

```text
wordlists/dirb_common.txt
```


