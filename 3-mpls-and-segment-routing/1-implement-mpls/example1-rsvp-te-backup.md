# Example1 rsvp te backup tunnel


csr1

```
interface Tunnel2014
 ip unnumbered Loopback0
 tunnel mode mpls traffic-eng
 tunnel destination 192.51.100.4
 tunnel mpls traffic-eng path-option 10 explicit name EP_Tun2014

ip explicit-path name EP_Tun2014 enable
 index 1 next-address 192.51.100.203
 index 2 next-address 192.51.100.207
```

csr4


```
interface Tunnel2014
 ip unnumbered Loopback0
 tunnel mode mpls traffic-eng
 tunnel destination 192.51.100.1
 tunnel mpls traffic-eng path-option 10 explicit name EP_Tun2014

ip explicit-path name EP_Tun2014 enable
 index 1 next-address 192.51.100.206
 index 2 next-address 192.51.100.202
```

csr3 will require

```

```
