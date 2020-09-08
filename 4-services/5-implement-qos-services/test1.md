# Test 1

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

```
root@cust1-client1:~# ping 172.18.22.22
PING 172.18.22.22 (172.18.22.22) 56(84) bytes of data.
64 bytes from 172.18.22.22: icmp_seq=1 ttl=61 time=2.10 ms
64 bytes from 172.18.22.22: icmp_seq=2 ttl=61 time=1.57 ms
```
















