# Test 2 - DSCP

I've been watching Tim Szigeti's QoS series on O'Reilly.  This test will attempt to put the ASR1000 lessons into practice.

For the past couple of week I've been fighting the VE and abandoned test 1.  Hopefully today lab goes OK.

**CML images appear to have ~1Mbps throughput limitation.**  

CML limitation is fine.  I understand why.  But this means using fixed bandwidth values instead of percent values.  This is ok too as Im working to a value of 1000kbps.

So this test lab.

* using AS64511
* AS64511 is configured with ospf(IGP), mpls and bgp (vpnv4) 
* vrf AS64511
* 2 Debian hosts in AS64511

as64511-host1 ```203.0.113.101```


as64511-host2 ```203.0.113.202```



So let's look at ```as64511-host1```.  as64511-host2 is very similar configuration.

```
root@as64511-host1:~# ip route
default via 192.168.250.1 dev eth0 
192.168.250.0/24 dev eth0 proto kernel scope link src 192.168.250.151 
203.0.113.0/24 via 203.0.113.97 dev eth1 
203.0.113.96/29 dev eth1 proto kernel scope link src 203.0.113.101 
```

```as64511-host1``` has 2 interfaces.  

```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 203.0.113.101/29

up ip route add 203.0.113.0/24 dev eth1 via 203.0.113.97
down ip route del 203.0.113.0/24 dev eth1 via 203.0.113.97

```


Test reachability ```as64511-host1``` to ```as64511-host2```

```
root@as64511-host1:~# ping 203.0.113.202
PING 203.0.113.202 (203.0.113.202) 56(84) bytes of data.
64 bytes from 203.0.113.202: icmp_seq=1 ttl=62 time=1.20 ms
64 bytes from 203.0.113.202: icmp_seq=2 ttl=62 time=1.11 ms
```

Verify from ```as64511-host2```

```
root@as64511-host2:~# tcpdump -nntei eth1 icmp -v
tcpdump: listening on eth1, link-type EN10MB (Ethernet), capture size 262144 bytes
56:13:23:ca:63:41 > 0a:0a:69:b2:46:8b, ethertype IPv4 (0x0800), length 98: (tos 0x0, ttl 62, id 42455, offset 0, flags [DF], proto ICMP (1), length 84)
    203.0.113.101 > 203.0.113.202: ICMP echo request, id 2192, seq 1, length 64
0a:0a:69:b2:46:8b > 56:13:23:ca:63:41, ethertype IPv4 (0x0800), length 98: (tos 0x0, ttl 64, id 43864, offset 0, flags [none], proto ICMP (1), length 84)
    203.0.113.202 > 203.0.113.101: ICMP echo reply, id 2192, seq 1, length 64

```

From above we see DSCP tos 0x0 


Traceroute

```
root@as64511-host1:~# traceroute 203.0.113.202
traceroute to 203.0.113.202 (203.0.113.202), 30 hops max, 60 byte packets
 1  203.0.113.97 (203.0.113.97)  0.635 ms  0.584 ms  0.548 ms
 2  203.0.113.201 (203.0.113.201)  2.448 ms  2.426 ms  2.406 ms
 3  203.0.113.202 (203.0.113.202)  2.589 ms  2.567 ms  2.535 ms
```

So ```as64511-host1``` to ```csr5``` to ```csr8``` to ```as64511-host2```

```csr6``` not shown in traceroute due to ```no mpls ip propagate-ttl``` on ```csr5```


Using iperf3 let's get a baseline on throughput.


```as64511-host1``` is the server listening on 5201
```as64511-host2``` is the client


```
CSR5#clear counters GigabitEthernet 6
Clear "show interface" counters on this interface [confirm]

CSR8#clear counters GigabitEthernet 5
Clear "show interface" counters on this interface [confirm]
```

Test uploads

```
root@as64511-host1:~# iperf3 -c 203.0.113.202
Connecting to host 203.0.113.202, port 5201
[  6] local 203.0.113.101 port 46568 connected to 203.0.113.202 port 5201
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  6]   0.00-1.00   sec   231 KBytes  1.89 Mbits/sec   12   19.7 KBytes       
[  6]   1.00-2.00   sec   129 KBytes  1.06 Mbits/sec    0   25.3 KBytes       
[  6]   2.00-3.00   sec  81.6 KBytes   668 Kbits/sec    0   30.9 KBytes       
[  6]   3.00-4.00   sec   172 KBytes  1.41 Mbits/sec    0   43.6 KBytes       
[  6]   4.00-5.00   sec   262 KBytes  2.14 Mbits/sec    0   67.5 KBytes       
[  6]   5.00-6.00   sec   190 KBytes  1.56 Mbits/sec    0    104 KBytes       
[  6]   6.00-7.00   sec   253 KBytes  2.07 Mbits/sec    0    152 KBytes       
[  6]   7.00-8.00   sec   316 KBytes  2.59 Mbits/sec    0    207 KBytes       
[  6]   8.00-9.00   sec   443 KBytes  3.63 Mbits/sec    0    262 KBytes       
[  6]   9.00-10.00  sec  0.00 Bytes  0.00 bits/sec    0    315 KBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  6]   0.00-10.00  sec  2.03 MBytes  1.70 Mbits/sec   12             sender
[  6]   0.00-12.88  sec  1.38 MBytes   897 Kbits/sec                  receiver

iperf Done.

```



```
CSR5#show interfaces GigabitEthernet 6
GigabitEthernet6 is up, line protocol is up 
  Hardware is CSR vNIC, address is 1e99.3123.1c91 (bia 1e99.3123.1c91)
  Internet address is 203.0.113.97/29
  MTU 1500 bytes, BW 1000000 Kbit/sec, DLY 10 usec, 
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  Keepalive set (10 sec)
  Full Duplex, 1000Mbps, link type is auto, media type is RJ45
  output flow-control is unsupported, input flow-control is unsupported
  ARP type: ARPA, ARP Timeout 04:00:00
  Last input 00:02:20, output 00:02:20, output hang never
  Last clearing of "show interface" counters 00:02:04
  Input queue: 0/375/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue: 0/40 (size/max)
  5 minute input rate 49000 bits/sec, 1 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     1384 packets input, 2035157 bytes, 0 no buffer
     Received 0 broadcasts (0 IP multicasts)
     0 runts, 0 giants, 0 throttles 
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
     0 watchdog, 0 multicast, 0 pause input
     1043 packets output, 65195 bytes, 0 underruns
     0 output errors, 0 collisions, 0 interface resets
     0 unknown protocol drops
     0 babbles, 0 late collision, 0 deferred
     0 lost carrier, 0 no carrier, 0 pause output
     0 output buffer failures, 0 output buffers swapped out

CSR5#clear counters GigabitEthernet 6 
Clear "show interface" counters on this interface [confirm]
```


Test downloads

```
root@as64511-host1:~# iperf3 -R -c 203.0.113.202
Connecting to host 203.0.113.202, port 5201
Reverse mode, remote host 203.0.113.202 is sending
[  6] local 203.0.113.101 port 46678 connected to 203.0.113.202 port 5201
[ ID] Interval           Transfer     Bitrate
[  6]   0.00-1.00   sec   114 KBytes   933 Kbits/sec                  
[  6]   1.00-2.00   sec   111 KBytes   910 Kbits/sec                  
[  6]   2.00-3.00   sec   110 KBytes   899 Kbits/sec                  
[  6]   3.00-4.00   sec   110 KBytes   899 Kbits/sec                  
[  6]   4.00-5.00   sec   111 KBytes   910 Kbits/sec                  
[  6]   5.00-6.00   sec   110 KBytes   899 Kbits/sec                  
[  6]   6.00-7.00   sec   110 KBytes   899 Kbits/sec                  
[  6]   7.00-8.00   sec   108 KBytes   887 Kbits/sec                  
[  6]   8.00-9.00   sec   108 KBytes   887 Kbits/sec                  
[  6]   9.00-10.00  sec   110 KBytes   899 Kbits/sec                  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  6]   0.00-10.01  sec  2.85 MBytes  2.39 Mbits/sec    0             sender
[  6]   0.00-10.00  sec  1.08 MBytes   902 Kbits/sec                  receiver

iperf Done.
```


```
CSR5#show interfaces GigabitEthernet 6
GigabitEthernet6 is up, line protocol is up 
  Hardware is CSR vNIC, address is 1e99.3123.1c91 (bia 1e99.3123.1c91)
  Internet address is 203.0.113.97/29
  MTU 1500 bytes, BW 1000000 Kbit/sec, DLY 10 usec, 
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  Keepalive set (10 sec)
  Full Duplex, 1000Mbps, link type is auto, media type is RJ45
  output flow-control is unsupported, input flow-control is unsupported
  ARP type: ARPA, ARP Timeout 04:00:00
  Last input 00:00:47, output 00:00:47, output hang never
  Last clearing of "show interface" counters 00:01:19
  Input queue: 0/375/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue: 0/40 (size/max)
  5 minute input rate 10000 bits/sec, 3 packets/sec
  5 minute output rate 74000 bits/sec, 7 packets/sec
     1578 packets input, 112881 bytes, 0 no buffer
     Received 0 broadcasts (0 IP multicasts)
     0 runts, 0 giants, 0 throttles 
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
     0 watchdog, 0 multicast, 0 pause input
     2088 packets output, 3103168 bytes, 0 underruns
     0 output errors, 0 collisions, 0 interface resets
     0 unknown protocol drops
     0 babbles, 0 late collision, 0 deferred
     0 lost carrier, 0 no carrier, 0 pause output
     0 output buffer failures, 0 output buffers swapped out
```


Observations

To me it is not clear where packets are limited - are they being dropped.  The hosts and routers do not indicate drops.

```
root@as64511-host1:~# ip -s link show eth1
3: eth1@if1536: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether 92:e0:66:f3:03:c9 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    RX: bytes  packets  errors  dropped overrun mcast   
    15941670   17483    0       0       0       0       
    TX: bytes  packets  errors  dropped carrier collsns 
    14052057   14193    0       0       0       0   

root@as64511-host2:~# ip -s link show eth1
3: eth1@if1546: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether 0a:0a:69:b2:46:8b brd ff:ff:ff:ff:ff:ff link-netnsid 0
    RX: bytes  packets  errors  dropped overrun mcast   
    13407174   17202    0       0       0       0       
    TX: bytes  packets  errors  dropped carrier collsns 
    16016478   13324    0       0       0       0   
```


Now im going to push as much as I can.

```
root@as64511-host1:~# ping -s1464 -M do -i0.001 203.0.113.202 
...

1472 bytes from 203.0.113.202: icmp_seq=7551 ttl=62 time=6936 ms
1472 bytes from 203.0.113.202: icmp_seq=7554 ttl=62 time=6936 ms
1472 bytes from 203.0.113.202: icmp_seq=7556 ttl=62 time=6943 ms
^C
--- 203.0.113.202 ping statistics ---
8395 packets transmitted, 2795 received, 66.7064% packet loss, time 689ms
rtt min/avg/max/mdev = 1.098/6368.915/8831.645/1503.062 ms, pipe 1070
```


Now Im confused - no indication of drops.  

I'm going to let following command run for ~1 hour and do some checks on VE to see where traffic is dropped.


```
root@as64511-host1:~# ping -s1464 -M do -i0.001 203.0.113.202 
```


ICMP has no transmission control.

Look at link between ```as64511-host1``` to ```csr5``` whick is **vlan 51**

This is just a portion and it is clear ```as64511-host1``` is transmitting all given the sequence numbers.

```
root@pve6-lab:~# tcpdump -nnei vmbr0v51
...
13:06:42.038266 92:e0:66:f3:03:c9 > 1e:99:31:23:1c:91, ethertype IPv4 (0x0800), length 1506: 203.0.113.101 > 203.0.113.202: ICMP echo request, id 2210, seq 18681, length 1472
13:06:42.048390 92:e0:66:f3:03:c9 > 1e:99:31:23:1c:91, ethertype IPv4 (0x0800), length 1506: 203.0.113.101 > 203.0.113.202: ICMP echo request, id 2210, seq 18682, length 1472
13:06:42.058527 92:e0:66:f3:03:c9 > 1e:99:31:23:1c:91, ethertype IPv4 (0x0800), length 1506: 203.0.113.101 > 203.0.113.202: ICMP echo request, id 2210, seq 18683, length 1472
13:06:42.062272 1e:99:31:23:1c:91 > 92:e0:66:f3:03:c9, ethertype IPv4 (0x0800), length 1506: 203.0.113.202 > 203.0.113.101: ICMP echo reply, id 2210, seq 17447, length 1472
13:06:42.062385 92:e0:66:f3:03:c9 > 1e:99:31:23:1c:91, ethertype IPv4 (0x0800), length 1506: 203.0.113.101 > 203.0.113.202: ICMP echo request, id 2210, seq 18684, length 1472
13:06:42.072511 92:e0:66:f3:03:c9 > 1e:99:31:23:1c:91, ethertype IPv4 (0x0800), length 1506: 203.0.113.101 > 203.0.113.202: ICMP echo request, id 2210, seq 18685, length 1472
13:06:42.082644 92:e0:66:f3:03:c9 > 1e:99:31:23:1c:91, ethertype IPv4 (0x0800), length 1506: 203.0.113.101 > 203.0.113.202: ICMP echo request, id 2210, seq 18686, length 1472
13:06:42.087140 1e:99:31:23:1c:91 > 92:e0:66:f3:03:c9, ethertype IPv4 (0x0800), length 1506: 203.0.113.202 > 203.0.113.101: ICMP echo reply, id 2210, seq 17453, length 1472
13:06:42.087260 92:e0:66:f3:03:c9 > 1e:99:31:23:1c:91, ethertype IPv4 (0x0800), length 1506: 203.0.113.101 > 203.0.113.202: ICMP echo request, id 2210, seq 18687, length 1472
13:06:42.097394 92:e0:66:f3:03:c9 > 1e:99:31:23:1c:91, ethertype IPv4 (0x0800), length 1506: 203.0.113.101 > 203.0.113.202: ICMP echo request, id 2210, seq 18688, length 1472

```

Next ```csr5``` to ```csr6``` which is **vlan56**

And we have our answer - ```csr5``` is dropping given the sequence numbers.


```
root@pve6-lab:~# tcpdump -nnti vmbr0v56
...
IP 172.31.0.200.49152 > 172.31.0.201.3784: BFDv1, Control, State Up, Flags: [Control Plane Independent], length: 24
MPLS (label 18, exp 0, ttl 255) (label 16, exp 0, [S], ttl 255) IP 203.0.113.101 > 203.0.113.202: ICMP echo request, id 2210, seq 38864, length 1472
MPLS (label 23, exp 0, [S], ttl 62) IP 203.0.113.202 > 203.0.113.101: ICMP echo reply, id 2210, seq 37679, length 1472
MPLS (label 18, exp 0, ttl 255) (label 16, exp 0, [S], ttl 255) IP 203.0.113.101 > 203.0.113.202: ICMP echo request, id 2210, seq 38867, length 1472
IP 172.31.0.201.49152 > 172.31.0.200.3784: BFDv1, Control, State Up, Flags: [Control Plane Independent], length: 24
MPLS (label 23, exp 0, [S], ttl 62) IP 203.0.113.202 > 203.0.113.101: ICMP echo reply, id 2210, seq 37685, length 1472
IP 172.31.0.200.49152 > 172.31.0.201.3784: BFDv1, Control, State Up, Flags: [Control Plane Independent], length: 24
MPLS (label 18, exp 0, ttl 255) (label 16, exp 0, [S], ttl 255) IP 203.0.113.101 > 203.0.113.202: ICMP echo request, id 2210, seq 38873, length 1472
MPLS (label 23, exp 0, [S], ttl 62) IP 203.0.113.202 > 203.0.113.101: ICMP echo reply, id 2210, seq 37691, length 1472
MPLS (label 18, exp 0, ttl 255) (label 16, exp 0, [S], ttl 255) IP 203.0.113.101 > 203.0.113.202: ICMP echo request, id 2210, seq 38874, length 1472
```


So where on ```csr5``` is it being dropped.


There is no drops ```csr5``` on:


interface Gi6 ```as64511-host1``` to ```csr5```

nor

interface Gi2 ```csr5``` to ```csr6```



Perhaps it is:

```
platform punt-policer 
```





