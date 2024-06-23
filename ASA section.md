
![[Pasted image 20240509034352.png]]
```R9 
en
 conf t
 int e1/0
 no sw
 no sh
 ip addr 10.250.10.9 255.255.255.0
 exit
 
```

```R10
en
 conf t
 int e1/0
 no sw
 no sh
 ip addr 10.250.10.10 255.255.255.0
 exit
 
```

``` R35
en
conf t


in e0/0
ip add 10.250.20.3 255.255.255.0
no shu
end 
wri

```


```sw-edge
in e0/2
sw acc vl 30
in e0/3
sw acc vl 40
in ra e0/0-1
sw trunk enc dot
sw mode trunk

```

```ISP
en
conf t
in e0/0
ip add 10.150.30.10 255.255.255.0
no shu
exit
ip route 0.0.0.0 0.0.0.0 10.150.30.1
end 
wri

```

```ISP-2
en
conf t
in e0/0
ip add 10.150.40.10 255.255.255.0
no shu
exit
ip route 0.0.0.0 0.0.0.0 10.150.40.1
end 
wri

```



```


en
conf t
hostname ASA
mode multi
prompt hostname state


interface  Ethernet2
	no shut
interface  Ethernet3
	no shut
interface  Ethernet0
	no shut
interface  Ethernet1
	no shut


admin-context admin
context admin 
  config-url disk0:/admin.cf

interface Ethernet3.30
	 vlan 30
interface Ethernet3.40
	 vlan 40
 
interface Ethernet2.10
	 vlan 10
interface Ethernet2.20
	 vlan 20


  


failover      
failover lan unit primary
failover lan interface FO Ethernet0
failover link STATE Ethernet1
failover interface ip FO 10.101.101.1 255.255.255.0 standby 10.101.101.2


context ENTR
  config-url disk0:/ENTR.cfg
  
  allocate-interface Ethernet2.10 inside 
  allocate-interface Ethernet3.30 outside 

changeto context ENTR
	interface inside 
		nameif inside
	interface outside 
		nameif outside
	
	interface inside 
	  ip addr 10.250.10.1 255.255.255.0 standby 10.250.10.2
	interface outside 
	 ip addr 10.150.30.1 255.255.255.0 standby 10.150.30.2
	 exit
	 fixup protocol icmp
	 wri


context CLOUD
  config-url disk0:/LOUD.cfg
  
  allocate-interface Ethernet2.20 inside 
  allocate-interface Ethernet3.40 outside 

changeto context CLOUD
	interface inside 
		nameif inside
	interface outside 
		nameif outside
	
	interface inside 
	  ip addr 10.250.20.1 255.255.255.0 standby 10.250.20.2
	interface outside 
	  ip addr 10.150.40.1 255.255.255.0 standby 10.150.40.2
	  	 exit
	 fixup protocol icmp
	 wri

failover group 1
	primary
failover group 2
	secondary

sh run failover

```

- To activate ospf  in multi context you need the exact address 0 255.255... Not gonna work, 
- ASA does not support in interface ospf config unlike routers and multi-layer switch's.
- ASA does not support inter-context dynamic routing because it's relies on multicast with switch's cannot send back to the same interface.
- ASA up intel recently start support protocols like BGP (ASA version 9.2.0).[[Release Notes for the Cisco ASA Series, 9.2(x) - Cisco](https://www.cisco.com/c/en/us/td/docs/security/asa/asa92/release/notes/asarn92.html#pgfId-586890)
```
router ospf 1
network 10.250.10.1 0.0.0.0 area 0
default-information originate always

```