# reachability

ping from CSR1 to CSR2 (Lo0 192.51.100.2)

```
CSR1#ping 192.51.100.2 repeat 1
Type escape sequence to abort.
Sending 1, 100-byte ICMP Echos to 192.51.100.2, timeout is 2 seconds:
!
Success rate is 100 percent (1/1), round-trip min/avg/max = 1/1/1 ms
``` 
Note that no source address is specified in the **ping**, CSR1 has used the ip address 
on the egress interface. 

```
root@pve6-lab:~# tcpdump -nnti fwln510i1 icmp
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on fwln510i1, link-type EN10MB (Ethernet), capture size 262144 bytes

IP 192.51.100.200 > 192.51.100.2: ICMP echo request, id 14, seq 0, length 80
IP 192.51.100.2 > 192.51.100.200: ICMP echo reply, id 14, seq 0, length 80

```

Test with the Lo0 as the source
```
CSR1#ping 192.51.100.2 repeat 1 source Lo0
Type escape sequence to abort.
Sending 1, 100-byte ICMP Echos to 192.51.100.2, timeout is 2 seconds:
Packet sent with a source address of 192.51.100.1 
!
Success rate is 100 percent (1/1), round-trip min/avg/max = 2/2/2 ms
```
Check with tcpdump

```
IP 192.51.100.1 > 192.51.100.2: ICMP echo request, id 17, seq 0, length 80
IP 192.51.100.2 > 192.51.100.1: ICMP echo reply, id 17, seq 0, length 80
```


