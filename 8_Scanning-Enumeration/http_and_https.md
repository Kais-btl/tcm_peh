*  <http://10.0.2.4> and <https://10.0.2.4> show deffault Apache PHP page
	* apache version on 404 page (no redirect)
	* Apache version 1.3.20
- web Vulnerability scanner
	- nikto
	```bash
	apt install nikto
	nikto -h http://192.168.57.134
	-h = host
	```

- directory busting tools
	- dirbuster:
	- dirb
	- gobuster
	```bash
	gobuster dir -e -u http://192.168.57.134 -w /usr/share/wordlists/dirbuster/wordlist
	-u = url
	```
- burp suite to intercept requests
	- server headers disclose information