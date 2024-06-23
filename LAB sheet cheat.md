
---

## Lab topology

## Branch 1

| VLAN | Network ID      | Subnet Mask      | Usable IP Range         | Broadcast Address |
|------|-----------------|------------------|-------------------------|-------------------|
| 10   | 192.168.10.0    | 255.255.255.0    | 192.168.10.1 - 192.168.10.254 | 192.168.10.255   |
| 20   | 192.168.20.0    | 255.255.255.0    | 192.168.20.1 - 192.168.20.254 | 192.168.20.255   |
| 30   | 192.168.30.0    | 255.255.255.0    | 192.168.30.1 - 192.168.30.254 | 192.168.30.255   |
| 40   | 192.168.40.0    | 255.255.255.0    | 192.168.40.1 - 192.168.40.254 | 192.168.40.255   |
| 50   | 192.168.50.0    | 255.255.255.0    | 192.168.50.1 - 192.168.50.254 | 192.168.50.255   |
## Branch 2

| VLAN | Network ID      | Subnet Mask      | Usable IP Range            | Broadcast Address   |
|------|-----------------|------------------|----------------------------|---------------------|
| 10   | 172.16.10.0     | 255.255.255.0    | 172.16.10.1 - 172.16.10.254 | 172.16.10.255       |
| 20   | 172.16.20.0     | 255.255.255.0    | 172.16.20.1 - 172.16.20.254 | 172.16.20.255       |
| 30   | 172.16.30.0     | 255.255.255.0    | 172.16.30.1 - 172.16.30.254 | 172.16.30.255       |
| 40   | 172.16.40.0     | 255.255.255.0    | 172.16.40.1 - 172.16.40.254 | 172.16.40.255       |
| 50   | 172.16.50.0     | 255.255.255.0    | 172.16.50.1 - 172.16.50.254 | 172.16.50.255       |



![[Pasted image 20240429211238.png]]
## Initial configuration

- **R 8 initial config** 

```
! Enter global configuration mode
en
vtp prim vlan

Configure terminal

no ip domain-lookup

hostname R8

! Configuring VTP version 3 as a server in domain CIFER
Vtp domain CIFER
Vtp version 3
Vtp mode server

vtp pass ciferpass

! Creating VLANs on the switch
Vlan 10
Name VLAN 10
Exit

Vlan 20
Name VLAN 20
Exit

Vlan 30
Name VLAN 30
Exit

Vlan 40
Name VLAN 40
Exit

Vlan 50
Name VLAN 50
Exit

! Creating SVIs for each VLAN
Interface Vlan 10
 Ip address 192.168.10.1 255.255.255.0
 No shutdown
Exit

Interface Vlan 20
 Ip address 192.168.20.1 255.255.255.0
 No shutdown
Exit

Interface Vlan 30
 Ip address 192.168.30.1 255.255.255.0
 No shutdown
Exit

Interface Vlan 40
 Ip address 192.168.40.1 255.255.255.0
 No shutdown
Exit

Interface Vlan 50
 Ip address 192.168.50.1 255.255.255.0
 No shutdown
Exit

! Configuring trunk interfaces with 802.1 Q encapsulation
Interface range e 1/0 - 3 , e 0/0-1

 Switchport trunk encapsulation dot
 Switchport mode trunk
 Switchport trunk allowed vlan 1,10,20,30,40,50
 No shutdown
Exit

! Configure synchronous logging for all vty and console lines
line vty 0 4
 logging synchronous
exit

line console 0
 logging synchronous
exit

! End configuration session
End

! Save the configuration to prevent loss after reboot
Write memory


```

```
! Enter global configuration mode
configure terminal

! Changing the hostname to R7
hostname R7

! Disable IP domain lookup to prevent the router from trying to translate incorrect commands as if they were hostnames
no ip domain-lookup

! Configuring trunk interfaces with 802.1Q encapsulation
interface range e1/0 - 3 ,e0/0-1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 1,10,20,30,40,50
 no shutdown
exit

! Configuring VTP version 3 as the primary server in domain CIFER
vtp domain CIFER
vtp version 3
vtp mode client
vtp pass ciferpass

! Configure synchronous logging for all vty and console lines
line vty 0 4
 logging synchronous
exit

line console 0
 logging synchronous
exit

! End configuration session
end

! Save the configuration to prevent loss after reboot
write memory

```
### Configuration for access

```
  



! Enter global configuration mode
en
configure terminal
no ip domain lookup

! Configure VTP settings
vtp domain CIFER
vtp mode client
vtp version 3
vtp pass ciferpass

! Creating trunk interfaces to connect to the Distribution switch
interface range e0/0 - 1
switchport trunk encapsulation dot1q
switchport mode trunk 

switchport trunk allowed vlan 1,10,20,30,40,50
no shutdown

! Configuring access interfaces for end devices
interface e0/2
switchport mode access 
! Assign to the appropriate VLAN
switchport access vlan 20
spanning-tree portfast 
no shutdown

interface e0/3
switchport mode access 
! Assign to the appropriate VLAN
switchport access vlan 40
spanning-tree portfast 
no shutdown

! configure rapid pvst+
spanning-tree mode rapid-pvst

! Configure synchronous logging for all vty and console lines
line vty 0 4
 logging synchronous
exit

line console 0
 logging synchronous
exit


! End configuration session
end

! Save the configuration to prevent loss after reboot 
write memory

```
---

### [[HSRP]] and [[STP]] 

- **R 7**
```
! Enter global configuration mode
configure terminal

! Configuring HSRP for VLAN 40, R7 as active

! Configuring HSRP for VLAN 10, R8 as active
interface Vlan10
 ip address 192.168.10.2 255.255.255.0
 standby 10 ip 192.168.10.254
 standby 10 priority 90
 standby 10 preempt
 standby 10 authentication md5 key-string Cisco
   standby 10 timers 1 3
 no shutdown
exit

! Configuring HSRP for VLAN 20, R8 as active
interface Vlan20
 ip address 192.168.20.2 255.255.255.0
 standby 20 ip 192.168.20.254
 standby 20 priority 90
 standby 20 preempt
 standby 20 authentication md5 key-string Cisco
 standby 20 timers 1 3
 no shutdown
exit

! Configuring HSRP for VLAN 30, R8 as active
interface Vlan30
 ip address 192.168.30.2 255.255.255.0
 standby 30 ip 192.168.30.254
 standby 30 priority 90
 standby 30 preempt
 standby 30 authentication md5 key-string Cisco
 standby 30 timers 1 3
 no shutdown
exit

interface Vlan40
 ip address 192.168.40.2 255.255.255.0
 standby 40 ip 192.168.40.254
 standby 40 priority 110
 standby 40 preempt
 standby 40 authentication md5 key-string Cisco
   standby 40 timers 1 3
 no shutdown
exit

! Configuring HSRP for VLAN 50, R7 as active
interface Vlan50
 ip address 192.168.50.2 255.255.255.0
 standby 50 ip 192.168.50.254
 standby 50 priority 110
 standby 50 preempt
 standby 50 authentication md5 key-string Cisco
    standby 50 timers 1 3
 no shutdown
exit

! Configuring Spanning Tree for VLAN 40 and 50
spanning-tree mode rapid-pvst

spanning-tree vlan 40 root prim
spanning-tree vlan 50 root prim
spanning-tree vlan 1 root prim
spanning-tree vlan 10 root sec
spanning-tree vlan 20 root sec
spanning-tree vlan 30 root sec

exit

```

- **R 8**
```
! Enter global configuration mode
configure terminal

! Configuring HSRP for VLAN 10, R8 as active
interface Vlan10
 ip address 192.168.10.1 255.255.255.0
 standby 10 ip 192.168.10.254
 standby 10 priority 110
 standby 10 preempt
 standby 10 authentication md5 key-string Cisco
    standby 10 timers 1 3
 no shutdown
exit

! Configuring HSRP for VLAN 20, R8 as active
interface Vlan20
 ip address 192.168.20.1 255.255.255.0
 standby 20 ip 192.168.20.254
 standby 20 priority 110
 standby 20 preempt
 standby 20 authentication md5 key-string Cisco
  standby 20 timers 1 3
 no shutdown
exit

! Configuring HSRP for VLAN 30, R8 as active
interface Vlan30
 ip address 192.168.30.1 255.255.255.0
 standby 30 ip 192.168.30.254
 standby 30 priority 110
 standby 30 preempt
 standby 30 authentication md5 key-string Cisco
  standby 30 timers 1 3
 no shutdown
exit


! Configuring HSRP for VLAN 40, R7 as active
interface Vlan40
 ip address 192.168.40.1 255.255.255.0
 standby 40 ip 192.168.40.254
 standby 40 priority 90
 standby 40 preempt
 standby 40 authentication md5 key-string Cisco
    standby 40 timers 1 3
 no shutdown
exit

! Configuring HSRP for VLAN 50, R7 as active
interface Vlan50
 ip address 192.168.50.1 255.255.255.0
 standby 50 ip 192.168.50.254
 standby 50 priority 90
 standby 50 preempt
 standby 50 authentication md5 key-string Cisco
    standby 50 timers 1 3
 no shutdown
exit

! Configuring Spanning Tree for VLAN 10, 20, and 30
spanning-tree mode rapid-pvst

spanning-tree vlan 1 root sec
spanning-tree vlan 40 root sec
spanning-tree vlan 50  root sec
spanning-tree vlan 10 root prim
spanning-tree vlan 20 root prim
spanning-tree vlan 30 root prim
exit

```
---
### [[SSH]] setup

```
en
configure terminal
!hostname R8
ip domain-name cifer.tech
crypto key generate rsa general-keys modulus 2048
username admin privilege 15 secret ciferpass
ip ssh version 2
line vty 0 4
transport input ssh
login local
exec-timeout 10 0
ip ssh time-out 60
ip ssh authentication-retries 3
end
write memory


```
---
## Setup branch 2 :

- **R 24 **

```

! Enter global configuration mode
en
vtp prim vlan

Configure terminal

no ip domain-lookup

hostname R24

! Configuring VTP version 3 as a server in domain CIFER
Vtp domain CIFER
Vtp version 3
Vtp mode server

vtp pass ciferpass

! Creating VLANs on the switch
Vlan 10
Name VLAN 10
Exit

Vlan 20
Name VLAN 20
Exit

Vlan 30
Name VLAN 30
Exit

Vlan 40
Name VLAN 40
Exit

Vlan 50
Name VLAN 50
Exit

! Creating SVIs for each VLAN with 172.16.x.x IP addresses
Interface Vlan 10
 Ip address 172.16.10.1 255.255.255.0
 No shutdown
Exit

Interface Vlan 20
 Ip address 172.16.20.1 255.255.255.0
 No shutdown
Exit

Interface Vlan 30
 Ip address 172.16.30.1 255.255.255.0
 No shutdown
Exit

Interface Vlan 40
 Ip address 172.16.40.1 255.255.255.0
 No shutdown
Exit

Interface Vlan 50
 Ip address 172.16.50.1 255.255.255.0
 No shutdown
Exit

! Configuring trunk interfaces with 802.1 Q encapsulation
Interface range e 1/0 - 3 , e 0/0-1

 Switchport trunk encapsulation dot
 Switchport mode trunk
 Switchport trunk allowed vlan 1,10,20,30,40,50
 No shutdown
Exit

! Configure synchronous logging for all vty and console lines
line vty 0 4
 logging synchronous
exit

line console 0
 logging synchronous
exit

! End configuration session
End

! Save the configuration to prevent loss after reboot
Write memory

```

- **R 28**

```
! Enter global configuration mode
configure terminal

! Changing the hostname to R23
hostname R23

! Disable IP domain lookup to prevent the router from trying to translate incorrect commands as if they were hostnames
no ip domain-lookup

! Configuring trunk interfaces with 802.1Q encapsulation
interface range e1/0 - 3 ,e0/0-1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 1,10,20,30,40,50
 no shutdown
exit

! Configuring VTP version 3 as the primary server in domain CIFER
vtp domain CIFER
vtp version 3
vtp mode client
vtp pass ciferpass

! Configure synchronous logging for all vty and console lines
line vty 0 4
 logging synchronous
exit

line console 0
 logging synchronous
exit

! End configuration session
end

! Save the configuration to prevent loss after reboot
write memory
```

### VRRP and STP setup :

- **R 23**

```
! Enter global configuration mode
configure terminal

! Configuring VRRP for VLAN 10
interface Vlan10
 ip address 172.16.10.2 255.255.255.0
 vrrp 10 ip 172.16.10.254
 vrrp 10 priority 90
 vrrp 10 preempt
 vrrp 10 timers learn
 vrrp 10 authentication text Cisco
 no shutdown
exit

! Configuring VRRP for VLAN 20
interface Vlan20
 ip address 172.16.20.2 255.255.255.0
 vrrp 20 ip 172.16.20.254
 vrrp 20 priority 90
 vrrp 20 preempt
 vrrp 20 timers learn
 vrrp 20 authentication text Cisco
 no shutdown
exit

! Configuring VRRP for VLAN 30
interface Vlan30
 ip address 172.16.30.2 255.255.255.0
 vrrp 30 ip 172.16.30.254
 vrrp 30 priority 90
 vrrp 30 preempt
 vrrp 30 timers learn
 vrrp 30 authentication text Cisco
 no shutdown
exit

! Configuring VRRP for VLAN 40
interface Vlan40
 ip address 172.16.40.2 255.255.255.0
 vrrp 40 ip 172.16.40.254
 vrrp 40 priority 110
 vrrp 40 preempt
 vrrp 40 timers timers advert 1
 vrrp 40 authentication text Cisco
 no shutdown
exit

! Configuring VRRP for VLAN 50
interface Vlan50
 ip address 172.16.50.2 255.255.255.0
 vrrp 50 ip 172.16.50.254
 vrrp 50 priority 110
 vrrp 50 preempt
 vrrp 50 timers advert 1
 vrrp 40 authentication text Cisco
 no shutdown
exit

! Saving the configuration
end
write memory

```

- **R 24***

```
! Enter global configuration mode
configure terminal

! Configuring VRRP for VLAN 10
interface Vlan10
 ip address 172.16.10.1 255.255.255.0
 vrrp 10 ip 172.16.10.254
 vrrp 10 priority 110
 vrrp 10 preempt
 vrrp 10 timers adver msec 1000 
 vrrp 10 authentication text Cisco
 no shutdown
exit

! Configuring VRRP for VLAN 20
interface Vlan20
 ip address 172.16.20.1 255.255.255.0
 vrrp 20 ip 172.16.20.254
 vrrp 20 priority 110
 vrrp 20 preempt
 vrrp 20 timers adver msec 1000 
 vrrp 20 authentication text Cisco
 no shutdown
exit

! Configuring VRRP for VLAN 30
interface Vlan30
 ip address 172.16.30.1 255.255.255.0
 vrrp 30 ip 172.16.30.254
 vrrp 30 priority 110
 vrrp 30 preempt
 vrrp 30 timers adver msec 1000 
 vrrp 30 authentication text Cisco
 no shutdown
exit

! Configuring VRRP for VLAN 40
interface Vlan40
 ip address 172.16.40.1 255.255.255.0
 vrrp 40 ip 172.16.40.254
 vrrp 40 priority 90
 vrrp 40 preempt
 vrrp 40 timers learn
 vrrp 40 authentication text Cisco
 no shutdown
exit

! Configuring VRRP for VLAN 50
interface Vlan50
 ip address 172.16.50.1 255.255.255.0
 vrrp 50 ip 172.16.50.254
 vrrp 50 priority 90
 vrrp 50 preempt
 vrrp 50 timers  learn
 vrrp 50 authentication text Cisco
exit

! Save the configuration to preserve changes
end
write memory

```


---

## DHCP Configuration

To configure DHCP on a multi-layer switch for the provided VLANs, you'll need to create DHCP pools for each VLAN. Based on the provided configuration and VLAN details, here's how you can configure DHCP on the multi-layer switch:

1. **Enter Global Configuration Mode**
2. **Create DHCP Pools for Each VLAN**

```plaintext
! Enter global configuration mode
configure terminal

! DHCP Pool for VLAN 10
ip dhcp pool VLAN10
   network 192.168.10.0 255.255.255.0
   default-router 192.168.10.1
   dns-server 8.8.8.8
   lease 7

! DHCP Pool for VLAN 20
ip dhcp pool VLAN20
   network 192.168.20.0 255.255.255.0
   default-router 192.168.20.1
   dns-server 8.8.8.8
   lease 7

! DHCP Pool for VLAN 30
ip dhcp pool VLAN30
   network 192.168.30.0 255.255.255.0
   default-router 192.168.30.1
   dns-server 8.8.8.8
   lease 7

! DHCP Pool for VLAN 40
ip dhcp pool VLAN40
   network 192.168.40.0 255.255.255.0
   default-router 192.168.40.1
   dns-server 8.8.8.8
   lease 7

! DHCP Pool for VLAN 50
ip dhcp pool VLAN50
   network 192.168.50.0 255.255.255.0
   default-router 192.168.50.1
   dns-server 8.8.8.8
   lease 7

! Exclude IP addresses that are used for static assignments, such as gateways
ip dhcp excluded-address 192.168.10.1 192.168.10.10
ip dhcp excluded-address 192.168.20.1 192.168.20.10
ip dhcp excluded-address 192.168.30.1 192.168.30.10
ip dhcp excluded-address 192.168.40.1 192.168.40.10
ip dhcp excluded-address 192.168.50.1 192.168.50.10

! End configuration session
end

! Save the configuration to prevent loss after reboot
write memory
```

For Branch 2, we'll set up DHCP on the multilayer switch similarly, but with the IP address ranges provided in the configuration for Branch 2 VLANs (172.16.x.x).

### DHCP Configuration for Branch 2

1. **Enter Global Configuration Mode**
2. **Create DHCP Pools for Each VLAN**

```plaintext
! Enter global configuration mode
configure terminal

! DHCP Pool for VLAN 10
ip dhcp pool VLAN10
   network 172.16.10.0 255.255.255.0
   default-router 172.16.10.1
   dns-server 8.8.8.8
   lease 7

! DHCP Pool for VLAN 20
ip dhcp pool VLAN20
   network 172.16.20.0 255.255.255.0
   default-router 172.16.20.1
   dns-server 8.8.8.8
   lease 7

! DHCP Pool for VLAN 30
ip dhcp pool VLAN30
   network 172.16.30.0 255.255.255.0
   default-router 172.16.30.1
   dns-server 8.8.8.8
   lease 7

! DHCP Pool for VLAN 40
ip dhcp pool VLAN40
   network 172.16.40.0 255.255.255.0
   default-router 172.16.40.1
   dns-server 8.8.8.8
   lease 7

! DHCP Pool for VLAN 50
ip dhcp pool VLAN50
   network 172.16.50.0 255.255.255.0
   default-router 172.16.50.1
   dns-server 8.8.8.8
   lease 7

! Exclude IP addresses that are used for static assignments, such as gateways
ip dhcp excluded-address 172.16.10.1 172.16.10.10
ip dhcp excluded-address 172.16.20.1 172.16.20.10
ip dhcp excluded-address 172.16.30.1 172.16.30.10
ip dhcp excluded-address 172.16.40.1 172.16.40.10
ip dhcp excluded-address 172.16.50.1 172.16.50.10

! End configuration session
end

! Save the configuration to prevent loss after reboot
write memory
```

### Explanation:

- **Global Configuration Mode**: This is where you input most of your configurations.
- **DHCP Pools**: These are created for each VLAN, specifying the network, default gateway, DNS server, and lease time.
- **Excluded Addresses**: These ensure that specific addresses, such as those assigned to gateways or other statically configured devices, are not handed out by DHCP.
- **Default Router**: The default gateway for each VLAN, as specified in the initial configuration.
- **DNS Server**: You can replace `8.8.8.8` with your preferred DNS server.

This configuration allows the multi-layer switch to provide DHCP services for each VLAN, ensuring that devices connected to the VLANs receive appropriate IP addresses, gateways, and DNS settings.