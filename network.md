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

#### Example
1. Log in to the machine locally using the admin user (cinemataztic). You may need to press ctrl + alt + f2 to open a new terminal session.
2. Open the file /etc/netplan/01-netcfg.yaml using your favourite terminal based editor (e.g. vim or nano).
```
~: nano /etc/netplan/01-netcfg.yaml
```
3. Add your network configuration to the file. If going for static IP the notation must be CIDR. The example below sets the IP address of the machine to the static 192.168.1.2 in the 255.255.255.0 subnet, corresponding to the CIDR 192.168.1.2/24. We assume that the NIC is called `eno1`

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
If your machine has been configured with multiple network interfaces, these can be configured individually. 
In a typical setup this could look like: 
1. NIC1 is configured to access internet (usually DHCP)
2. NIC2 is connected to the cinemas ethernet-based automation network (this interface would likely have a static IP address).


#### Enabling additional network interfaces
Per default the installer only enables the NIC used during the OS installation process. This means that any secondary network interfaces must be enabled manually. Network interfaces are enabled by creating a netplan configuration (yaml) and enabling the configuration.

1. Identify network cards: `~: ip a`
This will produce a list of network interfaces that will look like the list below:
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp5s0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN group default qlen 1000
    link/ether b4:96:91:6c:ae:0c brd ff:ff:ff:ff:ff:ff
3: eno1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether e4:54:e8:d6:e4:f3 brd ff:ff:ff:ff:ff:ff
    altname enp0s31f6
    inet 10.101.54.42/16 brd 10.101.255.255 scope global dynamic eno1
       valid_lft 86267sec preferred_lft 86267sec
    inet6 fe80::e654:e8ff:fed6:e4f3/64 scope link 
       valid_lft forever preferred_lft forever
```
2. Identify all network interfaces that should be used in the setup. In the example above we're interested in `eno1` (currently up) and `enp5s0` (currently down). 
3. In order to enable the network interface we'll need to edit the netplan config file: `sudo vim /etc/netplan/02-cinemataztic-network-manager.yaml` (swap vim with nano if that's your preferred editor).
4. Write the following to the file to enable dhcp on `eno1` and set a static IP address on `enp5s0` by saving the following content to the file. 

```
#/etc/netplan/02-cinemataztic-network-manager.yaml
network:
  ethernets:
    eno1:
      dhcp4: true
    enp5s0:
      dhcp4: no
      dhcp6: no
      addresses:
      - 192.168.1.2/24
  version: 2
```
5. Test the netplan configuration by running `netplan try` in the command line.
6. Follow the instructions on screen to keep the configuration or delete the configuration. 
7. If you keep the configuration and still wish to revert you can run the following command: `sudo bash -c 'echo "# CinemaTaztic Netplan Config goes here" > /etc/netplan/02-cinemataztic-network-manager.yaml' && sudo netplan apply`
