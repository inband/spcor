# bridged tagged

Moving on from bridged-untagged IFD ```ge-0/0/2``` is configured to tag 1000 on IFL ```ge-0/0/2.1000```

```
set interfaces ge-0/0/1 flexible-vlan-tagging
set interfaces ge-0/0/1 native-vlan-id 1000
set interfaces ge-0/0/1 encapsulation flexible-ethernet-services
set interfaces ge-0/0/1 unit 0 encapsulation vlan-bridge
set interfaces ge-0/0/1 unit 0 vlan-id 1000

set interfaces ge-0/0/2 flexible-vlan-tagging
set interfaces ge-0/0/2 encapsulation flexible-ethernet-services
set interfaces ge-0/0/2 unit 1000 encapsulation vlan-bridge
set interfaces ge-0/0/2 unit 1000 vlan-id 1000
```

Verify

```
root@VMX2# run show interfaces ge-0/0/2.1000 
  Logical interface ge-0/0/2.1000 (Index 342) (SNMP ifIndex 553)
    Flags: Up SNMP-Traps 0x20004000 VLAN-Tag [ 0x8100.1000 ]  Encapsulation: VLAN-Bridge
    Input packets : 0
    Output packets: 319
    Protocol bridge, MTU: 1522

```

Verify at ```vmx2-host2```

```
root@ubuntu-vmx2:/root/vmx# tcpdump -nntei ens21 -v
tcpdump: listening on ens21, link-type EN10MB (Ethernet), capture size 262144 bytes
f2:f0:7b:40:e9:31 > ff:ff:ff:ff:ff:ff, ethertype 802.1Q (0x8100), length 60: vlan 1000, p 0, ethertype ARP, Ethernet (len 6), IPv4 (len 4), Request who-has 10.0.0.2 tell 10.0.0.1, length 42
f2:f0:7b:40:e9:31 > ff:ff:ff:ff:ff:ff, ethertype 802.1Q (0x8100), length 60: vlan 1000, p 0, ethertype ARP, Ethernet (len 6), IPv4 (len 4), Request who-has 10.0.0.2 tell 10.0.0.1, length 42
```
------------------------------------

On the ```vmx-host2``` it needs to tune in.  Note - the following is not persistent.

```
root@vmx2-host2:~# ip link add link eth1 name eth1.1000 type vlan id 1000
root@vmx2-host2:~# ip link set dev eth1.1000 up
root@vmx2-host2:~# ip addr add 10.0.0.2/24 brd 10.0.0.255 dev eth1.1000
```


```
root@vmx2-host2:~# ip link show type vlan
4: eth1.1000@eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether da:76:06:9a:e9:17 brd ff:ff:ff:ff:ff:ff
```

For persistent

```
root@vmx2-host2:~# cat /etc/network/interfaces
auto lo
iface lo inet loopback


auto eth1
iface eth1 inet static
        address 10.1.0.2
        netmask 255.255.255.0

auto eth1.1000
iface eth1.1000 inet static
        address 10.0.0.2
        netmask 255.255.255.0
        vlan-raw-device eth1
```
