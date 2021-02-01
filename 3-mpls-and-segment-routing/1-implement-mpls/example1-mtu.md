# Example1 - mtu

Issues with reachability over mpls network due to mtu 1500.

```
csr1#ping 192.51.100.4 source Lo0 df-bit size 1500
Type escape sequence to abort.
Sending 5, 1500-byte ICMP Echos to 192.51.100.4, timeout is 2 seconds:
Packet sent with a source address of 192.51.100.1 
Packet sent with the DF bit set
.....
Success rate is 0 percent (0/5)

csr1#ping 192.51.100.4 source Lo0 df-bit size 1496
Type escape sequence to abort.
Sending 5, 1496-byte ICMP Echos to 192.51.100.4, timeout is 2 seconds:
Packet sent with a source address of 192.51.100.1 
Packet sent with the DF bit set
!!!!!
```

Increased mtu to 1512 in AS64499

```
csr1#show run interface GigabitEthernet 2
Building configuration...

Current configuration : 107 bytes
!
interface GigabitEthernet2
 mtu 1512
 no ip address
 negotiation auto
 no mop enabled
 no mop sysid
end

```


Verify

```
csr1#ping 192.51.100.4 source Lo0 df-bit size 1508
Type escape sequence to abort.
Sending 5, 1508-byte ICMP Echos to 192.51.100.4, timeout is 2 seconds:
Packet sent with a source address of 192.51.100.1 
Packet sent with the DF bit set
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/9/24 ms

csr1#ping 192.51.100.4 source Lo0 df-bit size 1509
Type escape sequence to abort.
Sending 5, 1509-byte ICMP Echos to 192.51.100.4, timeout is 2 seconds:
Packet sent with a source address of 192.51.100.1 
Packet sent with the DF bit set
.....
```
