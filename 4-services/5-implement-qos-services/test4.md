# Test 4 - MPLS EXP

The behaviour is that Cisco CSR is automatically assigning MPLS EXP the first 3 bits of DSCP.

See verification below.

Test DSCP **EF** ToS 184 | 0xB8

```
root@as64511-host1:~# ping -Q184 203.0.113.202 
PING 203.0.113.202 (203.0.113.202) 56(84) bytes of data.
64 bytes from 203.0.113.202: icmp_seq=1 ttl=62 time=1.35 ms
64 bytes from 203.0.113.202: icmp_seq=2 ttl=62 time=0.990 ms
```
On wire - note EXP 5

```
root@pve6-lab:~# tcpdump -nnti vmbr0v56 mpls -v
tcpdump: listening on vmbr0v56, link-type EN10MB (Ethernet), capture size 262144 bytes
MPLS (label 18, exp 5, ttl 255)
	(label 16, exp 5, [S], ttl 255)
	IP (tos 0xb8, ttl 63, id 45024, offset 0, flags [DF], proto ICMP (1), length 84)
```


Test **CS5** ToS 160 | 0xA0

```
root@as64511-host1:~# ping -Q160 203.0.113.202 
PING 203.0.113.202 (203.0.113.202) 56(84) bytes of data.
64 bytes from 203.0.113.202: icmp_seq=1 ttl=62 time=1.04 ms
64 bytes from 203.0.113.202: icmp_seq=2 ttl=62 time=1.32 ms
```

On wire - note EXP 5

```
root@pve6-lab:~# tcpdump -nnti vmbr0v56 mpls -v
tcpdump: listening on vmbr0v56, link-type EN10MB (Ethernet), capture size 262144 bytes
MPLS (label 18, exp 5, ttl 255)
	(label 16, exp 5, [S], ttl 255)
	IP (tos 0xa0, ttl 63, id 8820, offset 0, flags [DF], proto ICMP (1), length 84)
    203.0.113.101 > 203.0.113.202: ICMP echo request, id 3033, seq 5, length 64

```

Test **CS7** ToS 224 | 0xE0

```
root@as64511-host1:~# ping -Q224 203.0.113.202 
PING 203.0.113.202 (203.0.113.202) 56(84) bytes of data.
64 bytes from 203.0.113.202: icmp_seq=1 ttl=62 time=1.24 ms
64 bytes from 203.0.113.202: icmp_seq=2 ttl=62 time=1.55 ms
```


On wire - note EXP 7

```
root@pve6-lab:~# tcpdump -nnti vmbr0v56 mpls -v
tcpdump: listening on vmbr0v56, link-type EN10MB (Ethernet), capture size 262144 bytes
MPLS (label 18, exp 7, ttl 255)
	(label 16, exp 7, [S], ttl 255)
	IP (tos 0xe0, ttl 63, id 9558, offset 0, flags [DF], proto ICMP (1), length 84)
    203.0.113.101 > 203.0.113.202: ICMP echo request, id 3034, seq 4, length 64

```


And 1 more

Test **CS2** ToS 64 | 0x40

```
root@as64511-host1:~# ping -Q64 203.0.113.202 
PING 203.0.113.202 (203.0.113.202) 56(84) bytes of data.
64 bytes from 203.0.113.202: icmp_seq=1 ttl=62 time=1.37 ms
64 bytes from 203.0.113.202: icmp_seq=2 ttl=62 time=2.84 ms
```

On wire - note EXP 2

```
MPLS (label 18, exp 2, ttl 255)
	(label 16, exp 2, [S], ttl 255)
	IP (tos 0x40, ttl 63, id 23803, offset 0, flags [DF], proto ICMP (1), length 84)
    203.0.113.101 > 203.0.113.202: ICMP echo request, id 3035, seq 4, length 64
```




As a side note(taken directly from **man ping**):

```
  -Q tos
           Set Quality of Service -related bits in ICMP datagrams.  tos can be decimal (ping only) or hex number.

           In RFC2474, these fields are interpreted as 8-bit Differentiated Services (DS), consisting of: bits 0-1 (2 lowest bits)
           of separate data, and bits 2-7 (highest 6 bits) of Differentiated Services Codepoint (DSCP). In RFC2481 and RFC3168,
           bits 0-1 are used for ECN.

           Historically (RFC1349, obsoleted by RFC2474), these were interpreted as: bit 0 (lowest bit) for reserved (currently
           being redefined as congestion control), 1-4 for Type of Service and bits 5-7 (highest bits) for Precedence.

```



```
root@as64511-host1:~# ping -Q 0xB8 203.0.113.202 
PING 203.0.113.202 (203.0.113.202) 56(84) bytes of data.
64 bytes from 203.0.113.202: icmp_seq=1 ttl=62 time=1.11 ms
64 bytes from 203.0.113.202: icmp_seq=2 ttl=62 time=1.08 ms
```

produces the same as

```
root@as64511-host1:~# ping -Q184 203.0.113.202 
```

Which can be verified on the wire.

```
root@pve6-lab:~# tcpdump -nnti vmbr0v56 mpls -v
tcpdump: listening on vmbr0v56, link-type EN10MB (Ethernet), capture size 262144 bytes
MPLS (label 18, exp 5, ttl 255)
	(label 16, exp 5, [S], ttl 255)
	IP (tos 0xb8, ttl 63, id 40749, offset 0, flags [DF], proto ICMP (1), length 84)
    203.0.113.101 > 203.0.113.202: ICMP echo request, id 3053, seq 5, length 64
```
