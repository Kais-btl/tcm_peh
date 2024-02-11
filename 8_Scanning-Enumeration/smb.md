  
  * SMB (Samba) filesharing
	  * Server Message Block
	  * originally ran on Port 139
	    after Windows 2000 runs on Port 445
- metasploit framework
	- exploits
	- auxiliaries (scanning & enum)
	- post exploits
	- etc.
- msfvenom is another tool for exploit development
  ```bash
  msfconsole 
  search smb 
  use auxiliary/scanner/smb/smb_version 
  info # info and options about the modules
  options #only options
  set RHOSTS 192.168.57.134 #Remote Host as 192.168.57.134 (only required option field)
  run # run exploit
  ```
- SMBCLIENT
	- connect using anonymous login 
	```bash
	smbclient -L ////<IP_ADDRESS>// 
	smbclient -L //<IP_ADDRESS>
	# -L lists all fileshares
	smbclient ///<IP_ADDRESS>//<FILE_SHARE_NAME$>

	smbclient \\\\192.168.57.134\\ADMIN$ # needs password
	smbclient \\\\192.168.57.134\\IPC$ # works
	help # possible smb commands
	ls # denied => dead end
	exit
	```