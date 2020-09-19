# Test 2 - DSCP

I've been watching Tim Szigeti's QoS series on O'Reilly.  This test will attempt to put the ASR1000 lessons into practice.

For the past couple of week I've been fighting the VE and abandoned test 1.  Hopefully today lab goes OK.

**CML images appear to have 1Mb throughput limitation.**  

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







