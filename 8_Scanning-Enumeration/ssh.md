- enumerate ssh version
	- NMAP Scan
		- not always possible
	- SSH/ Connecting to the Machine

- nmap
	- OpenSSH 2.9p2
	- ![](assets/Pasted%20image%2020240211144059.png)
- searchs vulns for version
	- [https://www.rapid7.com/db/
	- https://www.exploit-db.com/
	- Offline: `searchsploit`

- SSH
	- banner might expose info
	- (kioptrix needs legacy key algos)
		- `ssh 192.168.57.134 -oKexAlgorithms=+diffie-hellman-group1-sha1 -c aes128-cbc`