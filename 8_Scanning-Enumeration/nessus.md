- industry tool for scanning
- download nessus
	- https://www.tenable.com/downloads/nessus?loginAttempted=true
	
	```shell
	dpkg -i Nessus-10.1.1-ubuntu910_amd64.deb
	sudo systemctl start nessusd.service 
	# https://kali:8834/ to configure
	```

- options
	- scan known web vulns (faster than complex)
	- all ports
	- advanced options for reporting and scheduling