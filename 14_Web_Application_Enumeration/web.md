- Installing Go
	* Guide on installing Go programming language.
	
- **Finding Subdomains with Assetfinder**
	* Utilize assetfinder for finding subdomains.
```bash
./assetfinder tesla.com >> tesla-subs.txt
./assetfinder --subs-only tesla.com
```

- **Finding Subdomains with Amass**
	* Use Amass for subdomain enumeration.
```bash
amass enum -d tesla.com
```

- **Finding Alive Domains with Httprobe**
	* Use httprobe for finding alive domains.
```bash
cat tesla.com/recon/final.txt | httprobe -s -p https:443 | sed 's/https\?:\/\///' | tr -d ':443'
```

- **Screenshotting Websites with GoWitness**
	- GoWitness - [https://github.com/sensepost/gowitness](https://github.com/sensepost/gowitness)
	* Use gowitness for taking website screenshots.
```bash
gowitness single https://tesla.com
```

- **Automating the Enumeration Process**
	* Customize script for automating subdomain enumeration.
```bash
./subdomain-script.sh tesla.com
```

* **subdomain enum:**
	* https://github.com/Gr1mmie/sumrecon/blob/master/sumrecon.sh
	* TCM's modified script - [https://pastebin.com/MhE6zXVt](https://pastebin.com/MhE6zXVt)

- other
	- The Bug Hunter's Methodology - [https://www.youtube.com/watch?v=uKWu6yhnhbQ](https://www.youtube.com/watch?v=uKWu6yhnhbQ)
	- Nahamsec Recon Playlist - [https://www.youtube.com/watch?v=MIujSpuDtFY&list=PLKAaMVNxvLmAkqBkzFaOxqs3L66z2n8LA](https://www.youtube.com/watch?v=MIujSpuDtFY&list=PLKAaMVNxvLmAkqBkzFaOxqs3L66z2n8LA)