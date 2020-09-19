# Test 1 - abandoned due to VE issues

In this test I've created 2 hosts.

Host1 
* Customer ```CUST1``` in AS64499
* IPv4 Address 172.20.11.11 (Public) allocated by Framed-Route
* Connected to CSR1 via 10Mb link (PPPoE)

Host2
* host in AS64499
* IPv4 Address 172.18.22.22 (Public)


Check in to **Host1**
```
root@pve6-lab:~# pct enter 111
root@cust1-client1:~# 
```

Test reachability to host 2 and traceroute

Ping

```
root@cust1-client1:~# ping 172.18.22.22
PING 172.18.22.22 (172.18.22.22) 56(84) bytes of data.
64 bytes from 172.18.22.22: icmp_seq=1 ttl=61 time=2.10 ms
64 bytes from 172.18.22.22: icmp_seq=2 ttl=61 time=1.57 ms
```

Traceroute

```
root@cust1-client1:~# traceroute -I -n 172.18.22.22
traceroute to 172.18.22.22 (172.18.22.22), 30 hops max, 60 byte packets
 1  172.20.11.1  1.257 ms  1.151 ms  1.098 ms
 2  192.51.100.1  1.514 ms  1.468 ms  1.425 ms
 3  192.51.100.10  2.079 ms  2.034 ms  1.988 ms
 4  172.18.22.22  2.434 ms  2.386 ms  2.333 ms
```



Netcat
```
apt install netcat-openbsd
```












