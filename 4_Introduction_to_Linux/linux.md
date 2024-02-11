# sudo

`/etc/sudoers`:

-   -   Explanation: Displays the content of the "/etc/sudoers" file, which contains configuration information for the  `sudo`  command.
    -   Example: Running  `/etc/sudoers`  would display the configuration directives for  `sudo`  access and permissions.

`sudo -l`:

-   -   Explanation: Lists the commands a user is allowed to run with  `sudo`  privileges.
    -   Example: Running  `sudo -l`  would display the commands and permissions available to the current user with  `sudo`  access.
# networking
`ip a`:

-   -   Explanation: Displays the network interfaces and their associated IP addresses.
    -   Example: Running  `ip a`  would show information about network interfaces, including their IP addresses, MAC addresses, and other details.

`ifconfig`:

-   -   Explanation: Displays the configuration and status of network interfaces.
    -   Example: Running  `ifconfig`  would show the configuration details, including IP addresses, MAC addresses, and other information for active network interfaces.

`iwconfig`:

-   -   Explanation: Displays the configuration and status of wireless network interfaces.
    -   Example: Running  `iwconfig`  would show the configuration details, such as wireless signal strength, frequency, and encryption information, for active wireless interfaces.

`ip n`:

-   -   Explanation: Displays the Neighbor Table, which contains the IP-to-MAC address mappings for devices in the local network.
    -   Example: Running  `ip n`  would show the IP and MAC addresses of devices that have recently communicated with the current device.

`arp -a`:

-   -   Explanation: Displays the ARP (Address Resolution Protocol) cache, which maps IP addresses to MAC addresses.
    -   Example: Running  `arp -a`  would show the IP and MAC addresses of devices that have been resolved recently by the ARP protocol.

`ip r`:

-   -   Explanation: Displays the routing table, which contains information about network routes.
    -   Example: Running  `ip r`  would show the routing table, including destination networks, gateway IP addresses, and network interfaces.

`route`:

-   -   Explanation: Displays or manipulates the IP routing table.
    -   Example: Running  `route`  would display the routing table, similar to the  `ip r`  command.

# services
`sudo service apache2 start`:

-   -   Explanation: Starts the Apache web server service.
    -   Example: Running  `sudo service apache2 start`  would initiate the Apache web server and make it available for serving web pages.

`sudo service apache2 stop`:

-   -   Explanation: Stops the Apache web server service.
    -   Example: Running  `sudo service apache2 stop`  would halt the running Apache web server, shutting down any active web page serving.

`python3 -m http.server 80`:

-   -   Explanation: Starts a simple HTTP server using Python on port 80.
    -   Example: Running  `python3 -m http.server 80`  would start a basic HTTP server on port 80, allowing you to serve files from the current directory.

`sudo systemctl enable ssh`:

-   -   Explanation: Enables the SSH (Secure Shell) service to start automatically on system boot.
    -   Example: Running  `sudo systemctl enable ssh`  would configure the system to start the SSH service during system startup.

`sudo systemctl disable ssh`:

-   -   Explanation: Disables the SSH service from starting automatically on system boot.
    -   Example: Running  `sudo systemctl disable ssh`  would prevent the SSH service from starting automatically during system startup.
