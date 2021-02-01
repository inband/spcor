# Example1 - RSVP-TE

AS64499

State prior to TE.

* igp OSPF 
* ldp/mpls setup
* BGP configured on ```csr1``` and ```csr4```
* mtu increase to 1512
* vpnv4 setup and tested (vrf AS64499)

------------------------

In this example:

* create primary TE tunnels -  ```csr1``` <-> ```csr4``` via ```csr2```
* create backup tunnels -  ```csr1``` <-> ```csr4``` via ```csr3```

Globally

```
mpls traffic-eng tunnels
```


Create tunnel Steps:

(tunnel are unidirectional - therefore tunnels have to be configured on each end-point)

```
interface Tunnel 14
 ip unnumbered Loopback0
 tunnel mode mpls traffic-eng
 tunnel destination 192.51.100.4
 tunnel mpls traffic-eng path-option 10 explicit name EP_Tun14
 tunnel mpls traffic-eng path-option 20 dynamic

ip explicit-path name EP_Tun14
 index 1 next-address 192.51.100.201 
 index 2 next-address 192.51.100.205
 
```

And on interfaces in PATH

```
interface Gigabit2.12
 mpls traffic-eng tunnels
 ip rsvp bandwidth
```

