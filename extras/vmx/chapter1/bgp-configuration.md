# BGP configuration

```PE1```

```
set logical-systems PE1 protocols bgp group iBGP-RR type internal
set logical-systems PE1 protocols bgp group iBGP-RR local-address 172.16.0.11
set logical-systems PE1 protocols bgp group iBGP-RR family inet unicast add-path receive
set logical-systems PE1 protocols bgp group iBGP-RR family inet unicast add-path send path-count 6
set logical-systems PE1 protocols bgp group iBGP-RR export PL-iBGP-RR-OUT
set logical-systems PE1 protocols bgp group iBGP-RR neighbor 172.16.0.201
set logical-systems PE1 protocols bgp group iBGP-RR neighbor 172.16.0.202
set logical-systems PE1 policy-options policy-statement PL-iBGP-RR-OUT term NHS from family inet
set logical-systems PE1 policy-options policy-statement PL-iBGP-RR-OUT term NHS then next-hop self
set logical-systems PE1 routing-options autonomous-system 65000
```

```
root@VMX1:PE1> show bgp summary         
Groups: 1 Peers: 2 Down peers: 2
Table          Tot Paths  Act Paths Suppressed    History Damp State    Pending
inet.0               
                       0          0          0          0          0          0
Peer                     AS      InPkt     OutPkt    OutQ   Flaps Last Up/Dwn State|#Active/Received/Accepted/Damped...
172.16.0.201          65000          0          0       0       0       56:11 Active
172.16.0.202          65000          0          0       0       0       56:11 Active
```

--------------------------------------

```PE3```

```
set logical-systems PE3 protocols bgp group iBGP-RR type internal
set logical-systems PE3 protocols bgp group iBGP-RR local-address 172.16.0.33
set logical-systems PE3 protocols bgp group iBGP-RR family inet unicast add-path receive
set logical-systems PE3 protocols bgp group iBGP-RR family inet unicast add-path send path-count 6
set logical-systems PE3 protocols bgp group iBGP-RR export PL-iBGP-RR-OUT
set logical-systems PE3 protocols bgp group iBGP-RR neighbor 172.16.0.201
set logical-systems PE3 protocols bgp group iBGP-RR neighbor 172.16.0.202
set logical-systems PE3 policy-options policy-statement PL-iBGP-RR-OUT term NHS from family inet
set logical-systems PE3 policy-options policy-statement PL-iBGP-RR-OUT term NHS then next-hop self
set logical-systems PE3 routing-options autonomous-system 65000
```




