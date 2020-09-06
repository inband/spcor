# Example 2

CSR1 is the PPPoE server, CUSTX-CPE1 is the client and freeradius the RADIUS server


```
hostname CSR1
!
!
vrf definition AS64499
 rd 64499:1
 !
 address-family ipv4
  route-target export 64499:1
  route-target import 64499:1
 exit-address-family
!
vrf definition LAB_MGMT
 rd 2000:1000
 !
 address-family ipv4
  route-target export 2000:1000
  route-target import 2000:1000
 exit-address-family
!         
!
aaa new-model
!
!
aaa group server radius LAB_RADIUS
 server name FREERADIUS
 ip vrf forwarding LAB_MGMT
 ip radius source-interface GigabitEthernet6
!
aaa authentication login default local
aaa authentication ppp default group LAB_RADIUS
aaa authorization exec default local 
aaa authorization network default group LAB_RADIUS 
!
policy-map 100Mb
 class class-default
  shape average 98000000 1400000 0 
!
! 
bba-group pppoe BBA_GROUP_1
 virtual-template 1
!
!
interface Loopback1
 vrf forwarding AS64499
 ip address 192.51.100.1 255.255.255.255
!
!
interface GigabitEthernet5.64499
 encapsulation dot1Q 100 second-dot1q any
 pppoe enable group BBA_GROUP_1
!
!
interface GigabitEthernet6
 vrf forwarding LAB_MGMT
 ip address 192.168.18.11 255.255.255.0
 negotiation auto
 no mop enabled
 no mop sysid
!
!
interface Virtual-Template1
 mtu 1492
 vrf forwarding AS64499
 ip unnumbered Loopback1
 peer default ip address pool LOCAL_POOL_AS64499
 ppp authentication chap
 ppp ipcp dns 8.8.8.8 8.8.4.4
!
!
ip local pool LOCAL_POOL_AS64499 172.20.1.1 172.20.1.255
!
!
radius server FREERADIUS
 address ipv4 192.168.18.12 auth-port 1812 acct-port 1813
 timeout 5
 retransmit 3
 key test
!
```

CUSTX-CPE1

```
hostname CUSTX-CPE1
!
!
!
vrf definition CUST1
 rd 1:1
 !
 address-family ipv4
 exit-address-family
!
!
interface GigabitEthernet2.64499
 encapsulation dot1Q 100 second-dot1q 1
 pppoe enable
 pppoe-client dial-pool-number 1
!
!
interface Dialer1
 vrf forwarding CUST1
 ip address negotiated
 ip mtu 1492
 encapsulation ppp
 dialer pool 1
 ppp authentication chap callin
 ppp chap hostname test2@lab.com.au
 ppp chap password 0 test456
!
```

Verify session on CSR1

```
CSR1#show pppoe session 
     1 session  in LOCALLY_TERMINATED (PTA) State
     1 session  total

Uniq ID  PPPoE  RemMAC          Port                    VT  VA         State
           SID  LocMAC                                      VA-st      Type
      9      9  a2df.d1ed.e9d5  Gi5.64499                1  Vi1.1      PTA  
                4ac7.d41b.83a1  VLAN: 100/1                 UP       
```

```
CSR1#show subscriber session 
Codes: Lterm - Local Term, Fwd - forwarded, unauth - unathenticated, authen -
authenticated, TC Ct. - Number of Traffic Classes on the main session

Current Subscriber Information: Total sessions 1
Uniq ID Interface    State    Service     Up-time  TC Ct. Identifier
9       Vi1.1        authen   Lterm       06:29:28 0      test2@lab.com.au
```

```
CSR1#show ip int Vi1.1                      
Virtual-Access1.1 is up, line protocol is up
  Interface is unnumbered. Using address of Loopback1 (192.51.100.1)
  Broadcast address is 255.255.255.255
  Peer address is 172.20.1.1
  MTU is 1492 bytes
  Helper address is not set
  Directed broadcast forwarding is disabled
  Outgoing Common access list is not set 
  Outgoing access list is not set
  Inbound Common access list is not set 
  Inbound  access list is not set
  Proxy ARP is enabled
  Local Proxy ARP is disabled
  Security level is default
  Split horizon is enabled
  ICMP redirects are always sent
  ICMP unreachables are always sent
  ICMP mask replies are never sent
  IP fast switching is enabled
  IP Flow switching is disabled
  IP CEF switching is enabled
  IP CEF switching turbo vector
  IP Null turbo vector
  VPN Routing/Forwarding "AS64499"
  Associated unicast routing topologies:
        Topology "base", operation state is UP
  IP multicast fast switching is enabled
  IP multicast distributed fast switching is disabled
  IP route-cache flags are Fast, CEF
  Router Discovery is disabled
  IP output packet accounting is disabled
  IP access violation accounting is disabled
  TCP/IP header compression is disabled
  RTP/IP header compression is disabled
  Probe proxy name replies are disabled
  Policy routing is disabled
  Network address translation is disabled
  BGP Policy Mapping is disabled
  Input features: iEdge, MCI Check
  Output features: iEdge
  IPv4 WCCP Redirect outbound is disabled
  IPv4 WCCP Redirect inbound is disabled
  IPv4 WCCP Redirect exclude is disabled
```

Verify From CUSTX-CPE1

```
CUSTX-CPE1#show int Dialer 1
Dialer1 is up, line protocol is up (spoofing)
  Hardware is Unknown
  Internet address is 172.20.1.1/32
  MTU 1500 bytes, BW 56 Kbit/sec, DLY 20000 usec, 
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation PPP, LCP Closed, loopback not set
  Keepalive set (10 sec)
  DTR is pulsed for 1 seconds on reset
  Interface is bound to Vi1
  Last input never, output never, output hang never
  Last clearing of "show interface" counters 1d06h
  Input queue: 0/375/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: weighted fair
  Output queue: 0/1000/64/0 (size/max total/threshold/drops) 
     Conversations  0/0/16 (active/max active/max total)
     Reserved Conversations 0/0 (allocated/max allocated)
     Available Bandwidth 42 kilobits/sec
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     2 packets input, 28 bytes
     4980 packets output, 69720 bytes
Bound to:
Virtual-Access1 is up, line protocol is up 
  Hardware is Virtual Access interface
  Internet address will be negotiated using IPCP
  MTU 1500 bytes, BW 56 Kbit/sec, DLY 20000 usec, 
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation PPP, LCP Open
  Open: IPCP
  PPPoE vaccess, cloned from Dialer1
  Vaccess status 0x44, loopback not set
  Keepalive set (10 sec)
  Interface is bound to Di1 (Encapsulation PPP)
  Last input 00:00:07, output never, output hang never
  Last clearing of "show interface" counters 06:35:07
  Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue: 0/40 (size/max)
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     4736 packets input, 66315 bytes, 0 no buffer
     Received 0 broadcasts (0 IP multicasts)
     0 runts, 0 giants, 0 throttles 
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored, 0 abort
     4735 packets output, 66310 bytes, 0 underruns
     0 output errors, 0 collisions, 0 interface resets
     0 unknown protocol drops
     0 output buffer failures, 0 output buffers swapped out
     0 carrier transitions
```
```
CUSTX-CPE1#show ip route vrf CUST1

Routing Table: CUST1
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is not set

      172.20.0.0/32 is subnetted, 1 subnets
C        172.20.1.1 is directly connected, Dialer1
      192.51.100.0/32 is subnetted, 1 subnets
C        192.51.100.1 is directly connected, Dialer1
```

Check reachability

```
CUSTX-CPE1#ping vrf CUST1 192.51.100.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.51.100.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
```
Traceroute

```
CUSTX-CPE1#traceroute vrf CUST1 192.51.100.1 source Dialer 1 numeric 
Type escape sequence to abort.
Tracing the route to 192.51.100.1
VRF info: (vrf in name/id, vrf out name/id)
  1 192.51.100.1 2 msec *  2 msec
```



Use RADIUS server to specify service-policy.

```
root@freeradius:/etc/freeradius# sqlite3 freeradius.db 
SQLite version 3.27.2 2019-02-25 16:06:06
Enter ".help" for usage hints.
sqlite> INSERT INTO radreply (username,attribute,op,value) VALUES ('test2@lab.com.au','cisco-avpair','=','ip:sub-qos-policy-out=100Mb');
```

Bounce session and debug CSR1 and CUSTX-CPE1, pcap freeradius server

CSR1

```
CSR1#debug pppoe events 
PPPoE protocol events debugging is on
CSR1#terminal monitor 

CSR1#clear subscriber session username  test2@lab.com.au
CSR1#
*Sep  6 08:05:58.460: [9]PPPoE 9: Segment (SSS class): UNBOUND
*Sep  6 08:05:58.484: [9]PPPoE 9: State LOCALLY_TERMINATED    Event SSS DISCONNECT
*Sep  6 08:05:58.488: [9]PPPoE 9: O PADT  R:a2df.d1ed.e9d5 L:4ac7.d41b.83a1 Gi5.64499
*Sep  6 08:05:58.488: [9]PPPoE 9: Destroying  R:a2df.d1ed.e9d5 L:4ac7.d41b.83a1 100 Gi5.64499
*Sep  6 08:05:58.488: PPPoE: Returning Vaccess Virtual-Access1.1
*Sep  6 08:05:58.492: dyn_attrs->xmit_rate: 1000000000 dyn_attrs->rcv_rate: 1000000000
*Sep  6 08:05:58.492: [9]PPPoE 9: AAA get dynamic attrs
*Sep  6 08:05:58.492: [9]PPPoE 9: AAA account stopped
*Sep  6 08:05:58.496: [9]PPPoE 9: Vi1.1 Block vaccess from being freed.
*Sep  6 08:05:58.512: PPPoE 9: I PADT  R:a2df.d1ed.e9d5 L:4ac7.d41b.83a1 100 Gi5.64499
*Sep  6 08:05:58.523: [9]PPPoE 9: Segment (SSS class): UNPROVISION
CSR1#
CSR1#
*Sep  6 08:06:18.687: PPPoE 0: I PADI  R:a2df.d1ed.e9d5 L:ffff.ffff.ffff 100 Gi5.64499
*Sep  6 08:06:18.688:  Service tag: NULL Tag
*Sep  6 08:06:18.688: PPPoE 0: O PADO, R:4ac7.d41b.83a1 L:a2df.d1ed.e9d5 100 Gi5.64499
*Sep  6 08:06:18.690:  Service tag: NULL Tag
*Sep  6 08:06:20.736: PPPoE 0: I PADR  R:a2df.d1ed.e9d5 L:4ac7.d41b.83a1 100 Gi5.64499
*Sep  6 08:06:20.737:  Service tag: NULL Tag
*Sep  6 08:06:20.737: PPPoE : encap string prepared
*Sep  6 08:06:20.737: [10]PPPoE 10: Access IE handle allocated
*Sep  6 08:06:20.737: [10]PPPoE 10: AAA get retrieved attrs
*Sep  6 08:06:20.737: [10]PPPoE 10: AAA get nas port details
*Sep  6 08:06:20.738: dyn_attrs->xmit_rate: 1000000000 dyn_attrs->rcv_rate: 1000000000
*Sep  6 08:06:20.738: [10]PPPoE 10: AAA get dynamic attrs
*Sep  6 08:06:20.738: [10]PPPoE 10: AAA unique ID 17 allocated
*Sep  6 08:06:20.739: [10]PPPoE 10: No AAA accounting method list 
*Sep  6 08:06:20.739: [10]PPPoE 10: Service request sent to SSS
*Sep  6 08:06:20.739: [10]PPPoE 10: Created, Service: None R:4ac7.d41b.83a1 L:a2df.d1ed.e9d5 100 Gi5.64499
*Sep  6 08:06:20.740: [10]PPPoE 10: State NAS_PORT_POLICY_INQUIRY    Event SSS MORE KEYS
*Sep  6 08:06:20.740: [10]PPPoE 10: data path set to PPP
*Sep  6 08:06:20.740: [10]PPPoE 10: Segment (SSS class): PROVISION
*Sep  6 08:06:20.740: [10]PPPoE 10: State PROVISION_PPP    Event SSM PROVISIONED
*Sep  6 08:06:20.740: [10]PPPoE 10: O PADS  R:a2df.d1ed.e9d5 L:4ac7.d41b.83a1 Gi5.64499
*Sep  6 08:06:20.861: [10]PPPoE 10: State LCP_NEGOTIATION    Event SSS CONNECT LOCAL
*Sep  6 08:06:20.866: [10]PPPoE 10: Segment (SSS class): UPDATED
*Sep  6 08:06:20.866: [10]PPPoE 10: Segment (SSS class): BOUND
*Sep  6 08:06:20.866: [10]PPPoE 10: data path set to Virtual Acess
*Sep  6 08:06:20.961: [10]PPPoE 10: State LCP_NEGOTIATION    Event SSM UPDATED
*Sep  6 08:06:20.963: [10]PPPoE 10: State PTA_BINDING    Event STATIC BIND RESPONSE
*Sep  6 08:06:20.963: [10]PPPoE 10: Connected PTA
*Sep  6 08:06:20.997: PPPoE : ipfib_encapstr  prepared
CSR1#
CSR1#show subscriber session username  test2@lab.com.au 
Type: PPPoE, UID: 10, State: authen, Identity: test2@lab.com.au
IPv4 Address: 172.20.1.2 
Session Up-time: 00:01:11, Last Changed: 00:01:11
Interface: Virtual-Access1.1
Switch-ID: 8216

Policy information:
  Authentication status: authen

Classifiers:
Class-id    Dir   Packets    Bytes                  Pri.  Definition
0           In    15         204                    0    Match Any
1           Out   6          84                     0    Match Any

Features:

QoS Policy Map:
Class-id    Dir   Policy Name Source
1           Out   100Mb       Peruser

Configuration Sources:
Type  Active Time  AAA Service ID  Name
USR   00:01:11     -               Peruser
INT   00:01:11     -               Virtual-Template1

```

Verify service-policy

```
CSR1#show policy-map session uid 10
 
 SSS session identifier 10 - 

  Service-policy output: 100Mb

    Class-map: class-default (match-any)  
      38 packets, 1596 bytes
      30 second offered rate 0000 bps, drop rate 0000 bps
      Match: any 
      Queueing
      queue limit 64 packets
      (queue depth/total drops/no-buffer drops) 0/0/0
      (pkts output/bytes output) 0/0
      shape (average) cir 98000000, bc 1400000, be 0
      target shape rate 98000000
```




CUSTX-CPE1

```
CUSTX-CPE1#debug pppoe events 
PPPoE protocol events debugging is on
CUSTX-CPE1#terminal monitor 
CUSTX-CPE1#
*Sep  6 08:05:57.660: PPPoE 9: I PADT  R:4ac7.d41b.83a1 L:a2df.d1ed.e9d5 100 Gi2.64499
*Sep  6 08:05:57.662:  PPPoE : Shutting down client session
*Sep  6 08:05:57.662: [0]PPPoE 9: O PADT  R:4ac7.d41b.83a1 L:a2df.d1ed.e9d5 Gi2.64499
*Sep  6 08:05:57.664: %DIALER-6-UNBIND: Interface Vi1 unbound from profile Di1
*Sep  6 08:05:57.674: %LINEPROTO-5-UPDOWN: Line protocol on Interface Virtual-Access1, changed state to down
*Sep  6 08:05:57.675: %LINK-3-UPDOWN: Interface Virtual-Access1, changed state to down
*Sep  6 08:06:17.848:  Sending PADI: Interface = GigabitEthernet2.64499
*Sep  6 08:06:17.853: PPPoE 0: I PADO  R:4ac7.d41b.83a1 L:a2df.d1ed.e9d5 100 Gi2.64499
*Sep  6 08:06:19.897:  PPPOE: we've got our pado and the pado timer went off
*Sep  6 08:06:19.897: OUT PADR from PPPoE Session
*Sep  6 08:06:19.904: PPPoE 10: I PADS  R:4ac7.d41b.83a1 L:a2df.d1ed.e9d5 100 Gi2.64499
*Sep  6 08:06:19.904: IN PADS from PPPoE Session
*Sep  6 08:06:19.911: %DIALER-6-BIND: Interface Vi1 bound to profile Di1
*Sep  6 08:06:19.911: PPPoE: Virtual Access interface obtained.
*Sep  6 08:06:19.911: PPPoE : encap string prepared
*Sep  6 08:06:19.911: [0]PPPoE 10: data path set to PPPoE Client
*Sep  6 08:06:19.913: %LINK-3-UPDOWN: Interface Virtual-Access1, changed state to up
*Sep  6 08:06:20.127: %LINEPROTO-5-UPDOWN: Line protocol on Interface Virtual-Access1, changed state to up
*Sep  6 08:06:20.149: PPPoE : ipfib_encapstr  prepared
*Sep  6 08:06:20.149: PPPoE : ipfib_encapstr  prepared
CUSTX-CPE1#                   
CUSTX-CPE1#show int Dialer 1                                         
Dialer1 is up, line protocol is up (spoofing)
  Hardware is Unknown
  Internet address is 172.20.1.2/32
  MTU 1500 bytes, BW 56 Kbit/sec, DLY 20000 usec, 
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation PPP, LCP Closed, loopback not set
  Keepalive set (10 sec)
  DTR is pulsed for 1 seconds on reset
  Interface is bound to Vi1
  Last input never, output never, output hang never
  Last clearing of "show interface" counters 1d06h
  Input queue: 0/375/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: weighted fair
  Output queue: 0/1000/64/0 (size/max total/threshold/drops) 
     Conversations  0/0/16 (active/max active/max total)
     Reserved Conversations 0/0 (allocated/max allocated)
     Available Bandwidth 42 kilobits/sec
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     7 packets input, 90 bytes
     5085 packets output, 71182 bytes
Bound to:
Virtual-Access1 is up, line protocol is up 
  Hardware is Virtual Access interface
  Internet address will be negotiated using IPCP
  MTU 1500 bytes, BW 56 Kbit/sec, DLY 20000 usec, 
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation PPP, LCP Open
  Open: IPCP
  PPPoE vaccess, cloned from Dialer1
  Vaccess status 0x44, loopback not set
  Keepalive set (10 sec)
  Interface is bound to Di1 (Encapsulation PPP)
  Last input 00:00:03, output never, output hang never
  Last clearing of "show interface" counters 00:02:46
  Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue: 0/40 (size/max)
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     36 packets input, 515 bytes, 0 no buffer
     Received 0 broadcasts (0 IP multicasts)
     0 runts, 0 giants, 0 throttles 
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored, 0 abort
     35 packets output, 510 bytes, 0 underruns
     0 output errors, 0 collisions, 0 interface resets
     0 unknown protocol drops
     0 output buffer failures, 0 output buffers swapped out
     0 carrier transitions
```


What is happening on wire?

```
root@pve6-lab:~# tcpdump -nnti vmbr0v100

PPPoE  [ses 0x9] LCP, Echo-Request (0x09), id 98, length 14
PPPoE  [ses 0x9] LCP, Echo-Reply (0x0a), id 98, length 14
PPPoE  [ses 0x9] LCP, Term-Request (0x05), id 3, length 6
PPPoE PADT [ses 0x9]
PPPoE  [ses 0x9] LCP, Term-Ack (0x06), id 3, length 6
PPPoE PADT [ses 0x9]
PPPoE PADI [Service-Name] [Host-Uniq 0x92000001000025DC]
PPPoE PADO [Service-Name] [Host-Uniq 0x92000001000025DC] [AC-Name "CSR1"] [AC-Cookie 0xA56CF3AE9AB7314DCE14CEF7D27BF9D8]
PPPoE PADR [Host-Uniq 0x92000001000025DC] [AC-Name "CSR1"] [AC-Cookie 0xA56CF3AE9AB7314DCE14CEF7D27BF9D8] [Service-Name]
PPPoE PADS [ses 0xa] [Host-Uniq 0x92000001000025DC] [AC-Name "CSR1"] [AC-Cookie 0xA56CF3AE9AB7314DCE14CEF7D27BF9D8] [Service-Name]
PPPoE  [ses 0xa] LCP, Conf-Request (0x01), id 1, length 12
PPPoE  [ses 0xa] LCP, Conf-Request (0x01), id 1, length 21
PPPoE  [ses 0xa] LCP, Conf-Ack (0x02), id 1, length 12
PPPoE  [ses 0xa] LCP, Conf-Nack (0x03), id 1, length 10
PPPoE  [ses 0xa] LCP, Conf-Request (0x01), id 2, length 21
PPPoE  [ses 0xa] LCP, Conf-Ack (0x02), id 2, length 21
PPPoE  [ses 0xa] CHAP, Challenge (0x01), id 1, Value 42851caffef0a43ac50655391b36aefa, Name CSR1
PPPoE  [ses 0xa] CHAP, Response (0x02), id 1, Value a95251ca598ebbf52de3ae9b3220e219, Name test2@lab.com.au
PPPoE  [ses 0xa] CHAP, Success (0x03), id 1, Msg 
PPPoE  [ses 0xa] IPCP, Conf-Request (0x01), id 1, length 12
PPPoE  [ses 0xa] IPCP, Conf-Request (0x01), id 1, length 12
PPPoE  [ses 0xa] IPCP, Conf-Nack (0x03), id 1, length 12
PPPoE  [ses 0xa] IPCP, Conf-Ack (0x02), id 1, length 12
PPPoE  [ses 0xa] IPCP, Conf-Request (0x01), id 2, length 12
PPPoE  [ses 0xa] IPCP, Conf-Ack (0x02), id 2, length 12
ARP, Reply 192.51.100.1 is-at a2:df:d1:ed:e9:d5, length 42
ARP, Reply 192.51.100.1 is-at a2:df:d1:ed:e9:d5, length 46
PPPoE  [ses 0xa] LCP, Echo-Request (0x09), id 1, length 14
PPPoE  [ses 0xa] LCP, Echo-Reply (0x0a), id 1, length 14

```

And freeradius

```
root@freeradius:/etc/freeradius# tcpdump -nni any port 1812 -v
tcpdump: listening on any, link-type LINUX_SLL (Linux cooked), capture size 262144 bytes
08:06:20.919373 IP (tos 0x0, ttl 255, id 11218, offset 0, flags [none], proto UDP (17), length 178)
    192.168.18.11.1645 > 192.168.18.12.1812: RADIUS, length: 150
	Access-Request (1), id: 0x03, Authenticator: 42851caffef0a43ac50655391b36aefa
	  Framed-Protocol Attribute (7), length: 6, Value: PPP
	  User-Name Attribute (1), length: 18, Value: test2@lab.com.au
	  CHAP-Password Attribute (3), length: 19, Value: 
	  NAS-Port-Type Attribute (61), length: 6, Value: Virtual
	  NAS-Port Attribute (5), length: 6, Value: 0
	  NAS-Port-Id Attribute (87), length: 13, Value: 0/0/0/100.1
	  Connect-Info Attribute (77), length: 9, Value: AS64499
	  Vendor-Specific Attribute (26), length: 41, Value: Vendor: Cisco (9)
	    Vendor Attribute: 1, Length: 33, Value: client-mac-address=a2df.d1ed.e9d5
	  Service-Type Attribute (6), length: 6, Value: Framed
	  NAS-IP-Address Attribute (4), length: 6, Value: 192.168.18.11
08:06:20.958804 IP (tos 0x0, ttl 64, id 42864, offset 0, flags [none], proto UDP (17), length 83)
    192.168.18.12.1812 > 192.168.18.11.1645: RADIUS, length: 55
	Access-Accept (2), id: 0x03, Authenticator: dba129f3d88c2dfc296ad691741a5f57
	  Vendor-Specific Attribute (26), length: 35, Value: Vendor: Cisco (9)
	    Vendor Attribute: 1, Length: 27, Value: ip:sub-qos-policy-out=100Mb
```
