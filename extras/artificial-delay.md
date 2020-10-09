# cisco delay


Create a delay - in lab everything is sub 1 millisecond - so behaviour and testing differs from PRODUCTION testing.

```
lab-mel-r1(config-if)#delay ?
  <1-16777215>  Throughput delay (tens of microseconds)
```

This didn't work as expected - I tried on interface and sub-interface.

I'll have to research further and come back.

Looks like it is used cost best route metrics for routing protocols (ie EIGRP)


### proxmox lab

Before

```
lab-mel-r1#ping 103.194.0.252
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 103.194.0.252, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
```


Find the interface - need to  know the **vm id** and **vlan**

```
root@pve6-lab:/var/lib/vz/images# ip link | grep 253.*912
1603: fwpr253p1@fwln253i1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9000 qdisc noqueue master vmbr0v912 state UP mode DEFAULT group default qlen 1000
```
Add delay

```
tc qdisc add dev fwpr253p1 root netem delay 100ms
```

```
root@pve6-lab:/var/lib/vz/images# ip link | grep 253.*912
1603: fwpr253p1@fwln253i1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9000 qdisc netem master vmbr0v912 state UP mode DEFAULT group default qlen 1000
```
```
root@pve6-lab:/var/lib/vz/images# tc -s qdisc | grep fwpr253p1
qdisc netem 8002: dev fwpr253p1 root refcnt 2 limit 1000 delay 100.0ms
```
Verify

```
lab-mel-r1#ping 103.194.0.252
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 103.194.0.252, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 101/101/102 ms
```



Delete delay

```
tc qdisc del dev fwpr253p1 root netem 
```
