
* **Domain Enumeration with ldapdomaindump**: Extract domain info using ldapdomaindump.
	- Extract domain info using ldapdomaindump.
		```bash
		ldapdomaindump -u user -p pass -d domain.local
	    sudo python3 /usr/bin/ldapdomaindump ldaps://192.168.57.45 -u '' -p '' -o /tmp
		```
* **Domain Enumeration with Bloodhound**: Utilize Bloodhound for visualizing AD trust relationships.
	- Visualize AD trust relationships.
		```bash
		sudo pip3 install bloodhound
		sudo apt install neo4j
		sudo apt install bloodhound
		sudo bloodhound-python -d X.local -u '' -p ''-ns 192.168.57.45 -c all
		```
* **Domain Enumeration with Plumhound**: Extend AD enumeration with Plumhound.
	- Extend AD enumeration with Plumhound.
		```bash
		sudo git clone https://github.com/PlumHound/PlumHound.git
		sudo pip install -r requirements.txt
		sudo python3 PlumHound.py -u user -p pass -d domain.local
		```
* **Domain Enumeration with PingCastle**: Perform domain enumeration with PingCastle.
	- Perform domain enumeration with PingCastle.
		```bash
		pingcastle.exe -o domain -u user -p pass
		```
