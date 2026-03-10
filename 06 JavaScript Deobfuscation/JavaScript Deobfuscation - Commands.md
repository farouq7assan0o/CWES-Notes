# Introduction 

**Commands**

**Payloads**

**Paths / Endpoints / Parameters**

**Tools / Services / Servers / Listeners**

**DOM IDs / Element Names / Attributes**

**Reusable Snippets**

**Request Patterns**

---


# Source Code 

**Commands**

[CTRL + U]

view-source:http://SERVER_IP:PORT

**Payloads**

```html
<style>
    *,
    html {
        margin: 0;
        padding: 0;
        border: 0;
    }
    ...SNIP...
    h1 {
        font-size: 144px;
    }
    p {
        font-size: 64px;
    }
</style>
```

```html
<head>
    <link rel="stylesheet" href="style.css">
</head>
```

```html
<script src="secret.js"></script>
```

```javascript
eval(function (p, a, c, k, e, d) { e = function (c) { '...SNIP... |true|function'.split('|'), 0, {}))
```

**Paths / Endpoints / Parameters**

http://SERVER_IP:PORT

view-source:http://SERVER_IP:PORT

style.css

secret.js

src="secret.js"

href="style.css"

rel="stylesheet"

**Tools / Services / Servers / Listeners**

Firefox

PwnBox

HTML

CSS

JavaScript

CTRL + U

````

```javascript
eval(function (p, a, c, k, e, d) { e = function (c) { '...SNIP... |true|function'.split('|'), 0, {}))
````

**Request Patterns**


# Code Obfuscation 

**Commands**

**Payloads**

[http://beautifytools.com/javascript-obfuscator.php](http://beautifytools.com/javascript-obfuscator.php)

**Paths / Endpoints / Parameters**

**Tools / Services / Servers / Listeners**

beautifytools.com

JavaScript Obfuscator

Python

PHP

JavaScript

**DOM IDs / Element Names / Attributes**

**Reusable Snippets**

**Request Patterns**


# Basic Obfuscation 

**Commands**

[https://jsconsole.com](https://jsconsole.com/)

[https://javascript-minifier.com/](https://javascript-minifier.com/)

[http://beautifytools.com/javascript-obfuscator.php](http://beautifytools.com/javascript-obfuscator.php)

**Payloads**

```javascript
console.log('HTB JavaScript Deobfuscation Module');
```

```javascript
eval(function(p,a,c,k,e,d){e=function(c){return c};if(!''.replace(/^/,String)){while(c--){d[c]=k[c]||c}k=[function(e){return d[e]}];e=function(){return'\\w+'};c=1};while(c--){if(k[c]){p=p.replace(new RegExp('\\b'+e(c)+'\\b','g'),k[c])}}return p}('5.4(\'3 2 1 0\');',6,6,'Module|Deobfuscation|JavaScript|HTB|log|console'.split('|'),0,{}))
```

**Paths / Endpoints / Parameters**

[https://jsconsole.com](https://jsconsole.com/)

[https://javascript-minifier.com/](https://javascript-minifier.com/)

[http://beautifytools.com/javascript-obfuscator.php](http://beautifytools.com/javascript-obfuscator.php)

**Tools / Services / Servers / Listeners**

JSConsole

javascript-minifier

BeautifyTools

JavaScript

**DOM IDs / Element Names / Attributes**

**Reusable Snippets**

```javascript
console.log('HTB JavaScript Deobfuscation Module');
```

```javascript
eval(function(p,a,c,k,e,d){e=function(c){return c};if(!''.replace(/^/,String)){while(c--){d[c]=k[c]||c}k=[function(e){return d[e]}];e=function(){return'\\w+'};c=1};while(c--){if(k[c]){p=p.replace(new RegExp('\\b'+e(c)+'\\b','g'),k[c])}}return p}('5.4(\'3 2 1 0\');',6,6,'Module|Deobfuscation|JavaScript|HTB|log|console'.split('|'),0,{}))
```

**Request Patterns**


# Advanced Obfuscation 

**Commands**

[https://obfuscator.io](https://obfuscator.io/)

[https://jsconsole.com](https://jsconsole.com/)

**Payloads**

```javascript
var _0x1ec6=['Bg9N','sfrciePHDMfty3jPChqGrgvVyMz1C2nHDgLVBIbnB2r1Bgu='];(function(_0x13249d,_0x1ec6e5){var _0x14f83b=function(_0x3f720f){while(--_0x3f720f){_0x13249d['push'](_0x13249d['shift']());}};_0x14f83b(++_0x1ec6e5);}(_0x1ec6,0xb4));var _0x14f8=function(_0x13249d,_0x1ec6e5){_0x13249d=_0x13249d-0x0;var _0x14f83b=_0x1ec6[_0x13249d];if(_0x14f8['eOTqeL']===undefined){var _0x3f720f=function(_0x32fbfd){var _0x523045='abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789+/=',_0x4f8a49=String(_0x32fbfd)['replace'](/=+$/,'');var _0x1171d4='';for(var _0x44920a=0x0,_0x2a30c5,_0x443b2f,_0xcdf142=0x0;_0x443b2f=_0x4f8a49['charAt'](_0xcdf142++);~_0x443b2f&&(_0x2a30c5=_0x44920a%0x4?_0x2a30c5*0x40+_0x443b2f:_0x443b2f,_0x44920a++%0x4)?_0x1171d4+=String['fromCharCode'](0xff&_0x2a30c5>>(-0x2*_0x44920a&0x6)):0x0){_0x443b2f=_0x523045['indexOf'](_0x443b2f);}return _0x1171d4;};_0x14f8['oZlYBE']=function(_0x8f2071){var _0x49af5e=_0x3f720f(_0x8f2071);var _0x52e65f=[];for(var _0x1ed1cf=0x0,_0x79942e=_0x49af5e['length'];_0x1ed1cf<_0x79942e;_0x1ed1cf++){_0x52e65f+='%'+('00'+_0x49af5e['charCodeAt'](_0x1ed1cf)['toString'](0x10))['slice'](-0x2);}return decodeURIComponent(_0x52e65f);},_0x14f8['qHtbNC']={},_0x14f8['eOTqeL']=!![];}var _0x20247c=_0x14f8['qHtbNC'][_0x13249d];return _0x20247c===undefined?(_0x14f83b=_0x14f8['oZlYBE'](_0x14f83b),_0x14f8['qHtbNC'][_0x13249d]=_0x14f83b):_0x14f83b=_0x20247c,_0x14f83b;};console[_0x14f8('0x0')](_0x14f8('0x1'));
```

```javascript
[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]][([][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]]+[])[!+[]+!+[]+!+[]]+(!![]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]])[+!+[]+[+[]]]+([][[]]+[])[+!+[]]+(![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[+!+[]]+([][[]]+[])[+[]]+([][(!
...SNIP...
[]]+(!![]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]])[+!+[]+[+[]]]+([][[]]+[])[+!+[]]+(![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[+!+[]]+([][[]]+[])[+[]]+([][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]])[+!+[]+[+[]]]+(!![]+[])[+!+[]]])[!+[]+!+[]+[+[]]]](!+[]+!+[]+[+[]])))()
```

**Paths / Endpoints / Parameters**

[https://obfuscator.io](https://obfuscator.io/)

[https://jsconsole.com](https://jsconsole.com/)

**Tools / Services / Servers / Listeners**

obfuscator.io

JSConsole

JavaScript Obfuscator

JJ Encode

AA Encode

JSF

**DOM IDs / Element Names / Attributes**

**Reusable Snippets**

```javascript
console[_0x14f8('0x0')](_0x14f8('0x1'));
```

**Request Patterns**


# Deobfuscation

**Commands**

CTRL+SHIFT+Z

[https://prettier.io/playground/](https://prettier.io/playground/)

[https://beautifier.io/](https://beautifier.io/)

[https://matthewfl.com/unPacker.html](https://matthewfl.com/unPacker.html)

console.log

**Payloads**

```javascript
eval(function (p, a, c, k, e, d) { e = function (c) { return c.toString(36) }; if (!''.replace(/^/, String)) { while (c--) { d[c.toString(a)] = k[c] || c.toString(a) } k = [function (e) { return d[e] }]; e = function () { return '\\w+' }; c = 1 }; while (c--) { if (k[c]) { p = p.replace(new RegExp('\\b' + e(c) + '\\b', 'g'), k[c]) } } return p }('g 4(){0 5="6{7!}";0 1=8 a();0 2="/9.c";1.d("e",2,f);1.b(3)}', 17, 17, 'var|xhr|url|null|generateSerial|flag|HTB|flag|new|serial|XMLHttpRequest|send|php|open|POST|true|function'.split('|'), 0, {}))
```

```javascript
function generateSerial() {
  ...SNIP...
  var xhr = new XMLHttpRequest;
  var url = "/serial.php";
  xhr.open("POST", url, true);
  xhr.send(null);
};
```

**Paths / Endpoints / Parameters**

secret.js

/serial.php

POST

null

true

**Tools / Services / Servers / Listeners**

Firefox

Browser Dev Tools

Debugger

Prettier

Beautifier

UnPacker

XMLHttpRequest

**DOM IDs / Element Names / Attributes**

secret.js

{ }

**Reusable Snippets**

```javascript
var xhr = new XMLHttpRequest;
var url = "/serial.php";
xhr.open("POST", url, true);
xhr.send(null);
```

```javascript
console.log
```

**Request Patterns**

```javascript
xhr.open("POST", url, true);
xhr.send(null);
```


# Code Analysis 

**Commands**

XMLHttpRequest

xhr.open

xhr.send

POST

**Payloads**

```javascript
'use strict';
function generateSerial() {
  ...SNIP...
  var xhr = new XMLHttpRequest;
  var url = "/serial.php";
  xhr.open("POST", url, true);
  xhr.send(null);
};
```

**Paths / Endpoints / Parameters**

/serial.php

POST

null

true

**Tools / Services / Servers / Listeners**

JavaScript

XMLHttpRequest

HTTP

POST

**DOM IDs / Element Names / Attributes**

**Reusable Snippets**

```javascript
var xhr = new XMLHttpRequest;
var url = "/serial.php";
xhr.open("POST", url, true);
xhr.send(null);
```

**Request Patterns**

```javascript
xhr.open("POST", url, true);
xhr.send(null);
```


# HTTP Requests

**Commands**

```bash
curl http://SERVER_IP:PORT/
```

```bash
curl -s http://SERVER_IP:PORT/ -X POST
```

```bash
curl -s http://SERVER_IP:PORT/ -X POST -d "param1=sample"
```

**Payloads**

```bash
-d "param1=sample"
```

**Paths / Endpoints / Parameters**

http://SERVER_IP:PORT/

-X POST

-s

-d "param1=sample"

**Tools / Services / Servers / Listeners**

cURL

Linux

macOS

Windows PowerShell

**DOM IDs / Element Names / Attributes**

**Reusable Snippets**

```bash
curl -s http://SERVER_IP:PORT/ -X POST
```

```bash
curl -s http://SERVER_IP:PORT/ -X POST -d "param1=sample"
```

**Request Patterns**

```bash
curl -s http://SERVER_IP:PORT/ -X POST
```

```bash
curl -s http://SERVER_IP:PORT/ -X POST -d "param1=sample"
```


# Decoding 

**Commands**

```bash
curl http://SERVER_IP:PORT/serial.php -X POST -d "param1=sample"
```

```bash
echo https://www.hackthebox.eu/ | base64
```

```bash
echo aHR0cHM6Ly93d3cuaGFja3RoZWJveC5ldS8K | base64 -d
```

```bash
man ascii
```

```bash
echo https://www.hackthebox.eu/ | xxd -p
```

```bash
echo 68747470733a2f2f7777772e6861636b746865626f782e65752f0a | xxd -p -r
```

```bash
echo https://www.hackthebox.eu/ | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

```bash
echo uggcf://jjj.unpxgurobk.rh/ | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

**Payloads**

ZG8gdGhlIGV4ZXJjaXNlLCBkb24ndCBjb3B5IGFuZCBwYXN0ZSA7KQo=

aHR0cHM6Ly93d3cuaGFja3RoZWJveC5ldS8K

68747470733a2f2f7777772e6861636b746865626f782e65752f0a

uggcf://jjj.unpxgurobk.rh/

**Paths / Endpoints / Parameters**

http://SERVER_IP:PORT/serial.php

-X POST

-d "param1=sample"

**Tools / Services / Servers / Listeners**

curl

base64

xxd

tr

Cipher Identifier

rot13

**DOM IDs / Element Names / Attributes**

**Reusable Snippets**

```bash
echo <STRING> | base64
```

```bash
echo <STRING> | base64 -d
```

```bash
echo <STRING> | xxd -p
```

```bash
echo <HEX_STRING> | xxd -p -r
```

```bash
echo <STRING> | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

**Request Patterns**

```bash
curl http://SERVER_IP:PORT/serial.php -X POST -d "param1=sample"
```

---


#