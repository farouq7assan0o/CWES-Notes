# Web Fuzzing 

**Introduction**

**Fuzzing vs. Brute-forcing**

**Why Fuzz Web Applications?**

**Essential Concepts**


# Web Fuzzing 

**Installing Go, Python and PIPX**  
sudo apt update  
sudo apt install -y golang  
sudo apt install -y python3 python3-pip  
sudo apt install pipx  
pipx ensurepath  
sudo pipx ensurepath --global  
go version  
python3 --version

**FFUF**  
go install github.com/ffuf/ffuf/v2@latest  
github.com/ffuf/ffuf

**Gobuster**  
go install github.com/OJ/gobuster/v3@latest  
github.com/OJ/gobuster

**FeroxBuster**  
curl -sL [https://raw.githubusercontent.com/epi052/feroxbuster/main/install-nix.sh](https://raw.githubusercontent.com/epi052/feroxbuster/main/install-nix.sh) | sudo bash -s $HOME/.local/bin  
github.com/epi052/feroxbuster

**wfuzz/wenum**  
pipx install git+[https://github.com/WebFuzzForge/wenum](https://github.com/WebFuzzForge/wenum)  
pipx runpip wenum install setuptools  
github.com/WebFuzzForge/wenum


# Directory and File Fuzzing 

**Uncovering Hidden Assets**

**Wordlists**  
/usr/share/seclists/  
/usr/share/seclists/Discovery/Web-Content/common.txt  
/usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt  
/usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt  
/usr/share/seclists/Discovery/Web-Content/big.txt  
[https://github.com/danielmiessler/SecLists](https://github.com/danielmiessler/SecLists)

**ffuf**  
[http://localhost/FUZZ](http://localhost/FUZZ)  
```
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://IP:PORT/FUZZ 
```

```
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://10.129.3.251/FUZZ  
```

```
ffuf -w /usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt  -u http://wingdata/FUZZ
```

```
ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u http://IP:PORT/w2ksvrus/FUZZ -e .php,.html,.txt,.bak,.js -v
```

ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://154.57.164.73:30183/FUZZ  

ffuf -w /usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt  -u http://154.57.164.73:30183/FUZZ

```
ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u http://94.237.123.185:56611/webfuzzing_hidden_path//flag/FUZZ -e .php,.html,.txt,.bak,.js -v
```

94.237.123.185:56611
# Recursive Fuzzing 

**How Recursive Fuzzing Works**

**Recursive Fuzzing with ffuf**  
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -ic -v -u [http://IP:PORT/FUZZ](http://ip:PORT/FUZZ) -e .html -recursion  
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -ic -u [http://IP:PORT/FUZZ](http://ip:PORT/FUZZ) -e .html -recursion -recursion-depth 2 -rate 500  
[http://IP:PORT/FUZZ](http://ip:PORT/FUZZ)  
[http://IP:PORT/level1/FUZZ](http://ip:PORT/level1/FUZZ)  
[http://IP:PORT/level1/level2/FUZZ](http://ip:PORT/level1/level2/FUZZ)  
[http://IP:PORT/level1/level3/FUZZ](http://ip:PORT/level1/level3/FUZZ)  
[http://IP:PORT/recursive_fuzz/](http://ip:PORT/recursive_fuzz/)

**Be Responsible**  
-recursion  
-ic  
-recursion-depth  
-rate  
-timeout

ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -ic -v -u http://83.136.251.11:44651/recursive_fuzz/FUZZ -e .html -recursion





# Parameter and Value Fuzzing 

**GET Parameters**  
[https://example.com/search?query=fuzzing&category=security](https://example.com/search?query=fuzzing&category=security)

**POST Parameters**  
POST /login HTTP/1.1  
Host: example.com  
Content-Type: application/x-www-form-urlencoded  
username=your_username&password=your_password

**wenum**  
pipx install git+[https://github.com/WebFuzzForge/wenum](https://github.com/WebFuzzForge/wenum)  
pipx runpip wenum install setuptools  
wenum -w /usr/share/seclists/Discovery/Web-Content/common.txt --hc 404 -u "[http://IP:PORT/get.php?x=FUZZ](http://ip:PORT/get.php?x=FUZZ)"  
git+[https://github.com/WebFuzzForge/wenum](https://github.com/WebFuzzForge/wenum)

**curl**  
curl [http://IP:PORT/get.php](http://ip:PORT/get.php)  
curl [http://IP:PORT/get.php?x=1](http://ip:PORT/get.php?x=1)  
curl [http://IP:PORT/get.php?x=OA](http://ip:PORT/get.php?x=OA)...  
curl -d "" [http://IP:PORT/post.php](http://ip:PORT/post.php)  
curl -d "y=SU..." [http://IP:PORT/post.php](http://ip:PORT/post.php)

**ffuf**  
ffuf -u [http://IP:PORT/post.php](http://ip:PORT/post.php) -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "y=FUZZ" -w /usr/share/seclists/Discovery/Web-Content/common.txt -mc 200 -v


# Virtual Host and Subdomain Fuzzing

**Virtual Hosts vs Subdomains**

**/etc/hosts**  
echo "IP inlanefreight.htb" | sudo tee -a /etc/hosts

**Gobuster**  
gobuster vhost -u [http://inlanefreight.htb:81](http://inlanefreight.htb:81/) -w /usr/share/seclists/Discovery/Web-Content/common.txt --append-domain  
gobuster dns -d inlanefreight.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt  
gobuster  
[https://github.com/OJ/gobuster](https://github.com/OJ/gobuster)

**Wordlists**  
/usr/share/seclists/Discovery/Web-Content/common.txt  
/usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt


# Filtering Fuzzing Output 

**Gobuster**  
gobuster dir -u [http://example.com/](http://example.com/) -w wordlist.txt -s 200,301 --exclude-length 0

**FFUF**  
ffuf -u [http://example.com/FUZZ](http://example.com/FUZZ) -w wordlist.txt -mc 200 -fw 427 -ms >500  
ffuf -u [http://example.com/FUZZ](http://example.com/FUZZ) -w wordlist.txt -fc 404,401,302  
ffuf -u http://154.57.164.77:32334/admin.php?user_id=180 -w tokens.txt -fc 404,401  

ffuf -u [http://example.com/FUZZ.bak](http://example.com/FUZZ.bak) -w wordlist.txt -fs 0-10239 -ms 10240-102400  
ffuf -u [http://example.com/FUZZ](http://example.com/FUZZ) -w wordlist.txt -mt >500  
ffuf -u [http://IP:PORT/post.php](http://ip:PORT/post.php) -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "y=FUZZ" -w /usr/share/seclists/Discovery/Web-Content/common.txt -v  
ffuf -u [http://IP:PORT/post.php](http://ip:PORT/post.php) -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "y=FUZZ" -w /usr/share/seclists/Discovery/Web-Content/common.txt -v -mc all

**wenum**  
wenum -w wordlist.txt --sc 200,301,302 -u [https://example.com/FUZZ](https://example.com/FUZZ)  
wenum -w wordlist.txt --hc 404,400,500 -u [https://example.com/FUZZ](https://example.com/FUZZ)  
wenum -w wordlist.txt --sw 5-10 -u [https://example.com/FUZZ](https://example.com/FUZZ)  
wenum -w wordlist.txt --hs 10000 -u [https://example.com/FUZZ](https://example.com/FUZZ)  
wenum -w wordlist.txt --sr "admin|password" -u [https://example.com/FUZZ](https://example.com/FUZZ)

**Feroxbuster**  
feroxbuster --url [http://example.com](http://example.com/) -w wordlist.txt -s 200 -S 10240 -X "error"



# Validating Findings 

**Manual Verification**  
curl [http://IP:PORT/backup/](http://ip:PORT/backup/)  
curl -I [http://IP:PORT/backup/password.txt](http://ip:PORT/backup/password.txt)

**Example**  
lighttpd/1.4.76  
/backup/  
backup.sql  
password.txt  
.tar.gz  
Content-Type  
Content-Length  
ETag  
Last-Modified  
Accept-Ranges  
Date  
Server


# Web APIs 

**Representational State Transfer (REST)**

**Simple Object Access Protocol (SOAP)**

**GraphQL**

**Advantages of Web APIs**

**How APIs are different from a web server**


# Identifying Endpoints

**REST**

**SOAP**

**GraphQL**


# API Fuzzing 

**Exploring the API**  
[http://IP:PORT/docs](http://ip:PORT/docs)  
/docs  
GET /  
GET /items/{item_id}  
DELETE /items/{item_id}  
PUT /items/{item_id}  
POST /items/

**Fuzzing the API**  
git clone [https://github.com/PandaSt0rm/webfuzz_api.git](https://github.com/PandaSt0rm/webfuzz_api.git)  
cd webfuzz_api  
pip3 install -r requirements.txt  
python3 api_fuzzer.py [http://IP:PORT](http://ip:PORT/)  
curl [http://localhost:8000/cz](http://localhost:8000/cz)...  
[https://github.com/PandaSt0rm/webfuzz_api.git](https://github.com/PandaSt0rm/webfuzz_api.git)  
requirements.txt  
api_fuzzer.py  
/items  
/docs  
/cz...


# Authentication Bypass via Direct Access 

**Commands**

**Paths / Endpoints / Parameters**  
/admin.php  
/index.php  
index.php  
http://<SERVER_IP>:/admin.php

**Headers / Cookies / Body**  
Location: index.php

**Wordlists / Files**

**Tools / Services / Servers / Listeners**  
Burp Suite

**Code Blocks**

```php
if(!$_SESSION['active']) {
	header("Location: index.php");
}
```

```php
if(!$_SESSION['active']) {
	header("Location: index.php");
	exit;
}
```
# Authentication Bypass via Parameter Modification

**Commands**
```
ffuf -u http://154.57.164.77:32334/admin.php?user_id=FUZZ -w tokens.txt -fc 404,401
```
**Paths / Endpoints / Parameters**  
/index.php  
/admin.php  
/admin.php?user_id=183  
user_id  
PHPSESSID

**Headers / Cookies / Body**  
Cookie: PHPSESSID  
username=htb-stdnt  
password=AcademyStudent%21


**Tools / Services / Servers / Listeners**

**Credentials**  
htb-stdnt  
AcademyStudent!



# Attacking Session Tokens 

**Commands**

```bash
echo -n dXNlcj1odGItc3RkbnQ7cm9sZT11c2Vy | base64 -d
```

```bash
echo -n 'user=htb-stdnt;role=admin' | base64
```

```bash
echo -n 'user=htb-stdnt;role=admin' | xxd -p
```

**Session Tokens**  
a5fd  
2c0c58b27c71a2ec5bf2b4b6e892b9f9  
2c0c58b27c71a2ec5bf2b4546092b9f9  
2c0c58b27c71a2ec5bf2b497f592b9f9  
2c0c58b27c71a2ec5bf2b48bcf92b9f9  
2c0c58b27c71a2ec5bf2b4735e92b9f9  
141233  
141234  
141237  
141238  
141240  
dXNlcj1odGItc3RkbnQ7cm9sZT11c2Vy  
dXNlcj1odGItc3RkbnQ7cm9sZT1hZG1pbg==  
757365723d6874622d73746d6e743b726f6c653d75736572  
757365723d6874622d7374646e743b726f6c653d61646d696e

**Paths / Endpoints / Parameters**  
/index.php  
/admin.php  
session

**Headers / Cookies / Body**  
Set-Cookie: session=a5fd  
Set-Cookie: session=2c0c58b27c71a2ec5bf2b4b6e892b9f9  
Set-Cookie: session=dXNlcj1odGItc3RkbnQ7cm9sZT11c2Vy  
Set-Cookie: session=757365723d6874622d73746d6e743b726f6c653d75736572

**Tools / Services / Servers / Listeners**  
base64  
xxd


# Attacking Session Tokens 

**Commands**

```bash
echo -n dXNlcj1odGItc3RkbnQ7cm9sZT11c2Vy | base64 -d
```

```bash
echo -n 'user=htb-stdnt;role=admin' | base64
```

```bash
echo -n 'user=htb-stdnt;role=admin' | xxd -p
```

**Session Tokens**  
a5fd  
2c0c58b27c71a2ec5bf2b4b6e892b9f9  
2c0c58b27c71a2ec5bf2b4546092b9f9  
2c0c58b27c71a2ec5bf2b497f592b9f9  
2c0c58b27c71a2ec5bf2b48bcf92b9f9  
2c0c58b27c71a2ec5bf2b4735e92b9f9  
141233  
141234  
141237  
141238  
141240  
dXNlcj1odGItc3RkbnQ7cm9sZT11c2Vy  
dXNlcj1odGItc3RkbnQ7cm9sZT1hZG1pbg==  
757365723d6874622d73746d6e743b726f6c653d75736572  
757365723d6874622d7374646e743b726f6c653d61646d696e

**Paths / Endpoints / Parameters**  
/index.php  
/admin.php  
session

**Headers / Cookies / Body**  
Set-Cookie: session=a5fd  
Set-Cookie: session=2c0c58b27c71a2ec5bf2b4b6e892b9f9  
Set-Cookie: session=dXNlcj1odGItc3RkbnQ7cm9sZT11c2Vy  
Set-Cookie: session=757365723d6874622d73746d6e743b726f6c653d75736572

**Tools / Services / Servers / Listeners**  
base64  
xxd


# Further Session Attacks 

**Commands**

**Paths / Endpoints / Parameters**  
[http://vulnerable.htb/?sid=a1b2c3d4e5f6](http://vulnerable.htb/?sid=a1b2c3d4e5f6)  
sid  
session

**Headers / Cookies / Body**  
Set-Cookie: session=a1b2c3d4e5f6

**Session Tokens**  
a1b2c3d4e5f6

**Tools / Services / Servers / Listeners**


#
