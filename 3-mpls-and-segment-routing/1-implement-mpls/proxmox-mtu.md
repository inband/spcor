# proxmox mtu

Added mtu 9000 to **interfaces**

```
root@pve6-lab:~# cat /etc/network/interfaces
auto lo
iface lo inet loopback

iface enp3s0f0 inet manual

iface enp3s0f1 inet manual

iface enp4s0f0 inet manual

iface enp4s0f1 inet manual


auto bond0
iface bond0 inet manual
	slaves enp3s0f0 enp3s0f1
	bond_miimon 100
	bond_mode 802.3ad
	bond_xmit_hash_policy layer2+3

auto vmbr0
iface vmbr0 inet static
	address 192.168.250.10
	netmask 255.255.255.0
	gateway 192.168.250.1
	bridge_ports bond0
	bridge_stp off
	bridge_fd 0
	mtu 9000

```
Restarted **networking service**

Worked for:
```
2: enp3s0f0: <BROADCAST,MULTICAST,SLAVE,UP,LOWER_UP> mtu 9000 qdisc mq master bond0 state UP mode DEFAULT group default qlen 1000
3: enp3s0f1: <BROADCAST,MULTICAST,SLAVE,UP,LOWER_UP> mtu 9000 qdisc mq master bond0 state UP mode DEFAULT group default qlen 1000
6: bond0: <BROADCAST,MULTICAST,MASTER,UP,LOWER_UP> mtu 9000 qdisc noqueue master vmbr0 state UP mode DEFAULT group default qlen 1000
511: vmbr0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9000 qdisc noqueue state UP mode DEFAULT group default qlen 1000
```

Untagged to bond0 are only used for MGMT 

```


```


Main concern is with MPLS and associated links and interfaces (vlan12, vlan13, vlan24 and vlan34)

Needed to Mmanually set mtu.  Need to find a better way.

```
ip link set mtu 9000 dev vmbr0v12
ip link set mtu 9000 dev bond0.12
ip link set mtu 9000 dev fwln510i1
ip link set mtu 9000 dev fwpr510p1
ip link set mtu 9000 dev fwln511i1
ip link set mtu 9000 dev fwpr511p1

ip link set mtu 9000 dev vmbr0v13
ip link set mtu 9000 dev bond0.13
ip link set mtu 9000 dev fwln510i2
ip link set mtu 9000 dev fwpr510p2
ip link set mtu 9000 dev fwln512i1
ip link set mtu 9000 dev fwpr512p1

ip link set mtu 9000 dev vmbr0v24
ip link set mtu 9000 dev bond0.24
ip link set mtu 9000 dev fwln511i2
ip link set mtu 9000 dev fwpr511p2
ip link set mtu 9000 dev fwln513i1
ip link set mtu 9000 dev fwpr513p1

ip link set mtu 9000 dev vmbr0v34
ip link set mtu 9000 dev bond0.34
ip link set mtu 9000 dev fwln512i2
ip link set mtu 9000 dev fwpr512p2
ip link set mtu 9000 dev fwln513i2
ip link set mtu 9000 dev fwpr513p2
```

Still missed a few

```
root@pve6-lab:~# ip link | grep '510.*1500'
574: fwbr510i3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
575: fwpr510p3@fwln510i3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master vmbr0v10 state UP mode DEFAULT group default qlen 1000
576: fwln510i3@fwpr510p3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master fwbr510i3 state UP mode DEFAULT group default qlen 1000
root@pve6-lab:~# ip link | grep '511.*1500'
root@pve6-lab:~# ip link | grep '512.*1500'
root@pve6-lab:~# ip link | grep '513.*1500'
```

```
ip link set mtu 9000 dev fwbr510i1
ip link set mtu 9000 dev fwbr510i2

ip link set mtu 9000 dev fwbr511i1
ip link set mtu 9000 dev fwbr511i2

ip link set mtu 9000 dev fwbr512i1
ip link set mtu 9000 dev fwbr512i2

ip link set mtu 9000 dev fwbr513i1
ip link set mtu 9000 dev fwbr513i2

```

And check the ```tap``` interfaces

```
root@pve6-lab:~# ip link | grep 'tap51[0-3]'
561: tap510i0: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 9000 qdisc pfifo_fast master fwbr510i0 state UNKNOWN mode DEFAULT group default qlen 1000
565: tap510i1: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 9000 qdisc pfifo_fast master fwbr510i1 state UNKNOWN mode DEFAULT group default qlen 1000
569: tap510i2: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 9000 qdisc pfifo_fast master fwbr510i2 state UNKNOWN mode DEFAULT group default qlen 1000
573: tap510i3: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 9000 qdisc pfifo_fast master fwbr510i3 state UNKNOWN mode DEFAULT group default qlen 1000
385: tap511i0: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 9000 qdisc pfifo_fast master fwbr511i0 state UNKNOWN mode DEFAULT group default qlen 1000
389: tap511i1: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 9000 qdisc pfifo_fast master fwbr511i1 state UNKNOWN mode DEFAULT group default qlen 1000
393: tap511i2: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 9000 qdisc pfifo_fast master fwbr511i2 state UNKNOWN mode DEFAULT group default qlen 1000
209: tap512i0: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 9000 qdisc pfifo_fast master fwbr512i0 state UNKNOWN mode DEFAULT group default qlen 1000
213: tap512i1: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 9000 qdisc pfifo_fast master fwbr512i1 state UNKNOWN mode DEFAULT group default qlen 1000
217: tap512i2: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 9000 qdisc pfifo_fast master fwbr512i2 state UNKNOWN mode DEFAULT group default qlen 1000
221: tap513i0: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 9000 qdisc pfifo_fast master fwbr513i0 state UNKNOWN mode DEFAULT group default qlen 1000
225: tap513i1: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 9000 qdisc pfifo_fast master fwbr513i1 state UNKNOWN mode DEFAULT group default qlen 1000
229: tap513i2: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 9000 qdisc pfifo_fast master fwbr513i2 state UNKNOWN mode DEFAULT group default qlen 1000

```



Tested OK

```
CSR1#ping 192.51.100.4 source lo0 repeat 1 df-bit size 1500
Type escape sequence to abort.
Sending 1, 1500-byte ICMP Echos to 192.51.100.4, timeout is 2 seconds:
Packet sent with a source address of 192.51.100.1 
Packet sent with the DF bit set
!
Success rate is 100 percent (1/1), round-trip min/avg/max = 1/1/1 ms

```




