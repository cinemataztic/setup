# Network setup
Machines can be reached by IP address or hostname.local. This is a prerequisite for cueing games in network-based automation setups.
Depending on your setup, you may need to configure your machines with fixed IP addresses via DHCP (mac address based IP reservation) or completely static IP addresses.

Depending on your setup, you may need to configure your machines with fixed IP addresses via DHCP (mac address based IP reservation) or completely static IP addresses.

## DHCP (default)
The boxes are configured for DHCP based network per default.

### DHCP + mac based address reservation
You may configure your DHCP server to provide a stable IP address for each machine based on its mac address. The mac address can be found on cloud.cinemataztic.com once the machine has been set up.

### Static IP
If you need a static IP address (e.g. because DHCP-based address reservation is unavailable) the machines can be configured with a static IP address.
You'll need the following information:
- IP address (CIDR notation)
- Gateway (optional)
- DNS servers (optional)

The machines run Ubuntu 20, and static IP addresses can be set via the netplan utility: https://netplan.io/

### Examples
1. Log in to the machine locally using the admin user (cinemataztic). You may need to press ctrl + alt + f2 to open a new terminal session.
2. Open the file /etc/netplan/01-netcfg.yaml using your favourite terminal based editor (e.g. vim or nano).
```
~: nano /etc/netplan/01-netcfg.yaml
```
3. Add your network configuration to the file. If going for static IP the notation must be CIDR. The example below sets the IP address of the machine to the static 192.168.1.2 in the 255.255.255.0 subnet, corresponding to the CIDR 192.168.1.2/24.

```
# /etc/netplan/01-netcfg.yaml 
# This file describes the network interfaces available on your system 
# For more information, see netplan(5). 

network:
  version: 2
  renderer: networkd
  ethernets:
    eno1:
      dhcp4: no
      dhcp6: no
      addresses:
      - 192.168.1.2/24
```
Save and close the file and try the netplan configuration with the command:

```
~: netplan try
```
**netplan try**: Apply configuration and wait for user confirmation; will roll back if network is broken or no confirmation is given.

#### Reverting
If you saved the configuration in the previous step and wish to roll back to a DHCP configuration, here's an example configuration:
```
# This file describes the network interfaces available on your system 
# For more information, see netplan(5). 
network:   
  version: 2   
  renderer: networkd   
  ethernets:     
    eno1:       
    dhcp4: yes
```

### Multiple Network Interfaces
If your machine has been configured with multiple network interfaces, these can be configured individually. An example setup would be that the first network physical network interface is configured to access internet for downloading games and assets and the game play itself, while the second physical network interface is configured to connect to the cinemas ethernet-based automation network (this interface would have a static IP address).
