# freeradius & sqlite

radius (remote authentication dial in user service) is defined in [rfc2865](https://tools.ietf.org/html/rfc2865). It describes an application that is used to carry authentication, authorisation and configuration information to and from a NAS (Network Access Server). In this lab I’m using Cisco CSR as the NAS, freeradius as the radius server and sqlite as the database.

Cisco implements aaa (authentication, authorisation and accounting) which looks after the following.

* who are you (authentication)
* what are you allow to do (authorisation)
* what are you doing (accounting)

aaa can be configured to use radius, tacacs or a local implementation. The advantage of radius and tacacs is that the aaa information can be centralised. Furthermore, applications like freeradius can centralise information and scale out leveraging an advanced database. However, in this lab I’ll use the light-weight standalone database sqlite.


```
# Debian 10 Buster
root@freeradius:~# apt update
root@freeradius:~# apt install freeradius -y
 
root@freeradius:/etc/freeradius# systemctl status freeradius
● freeradius.service - FreeRADIUS multi-protocol policy server
   Loaded: loaded (/lib/systemd/system/freeradius.service; disabled; vendor preset: enabled)
   Active: active (running) since Fri 2020-07-24 05:55:07 UTC; 49min ago
     Docs: man:radiusd(8)
           man:radiusd.conf(5)
           http://wiki.freeradius.org/
           http://networkradius.com/doc/
  Process: 25691 ExecStartPre=/usr/sbin/freeradius $FREERADIUS_OPTIONS -Cxm -lstdout (code=exited, status=0/SUCCESS)
  Process: 25693 ExecStart=/usr/sbin/freeradius $FREERADIUS_OPTIONS (code=exited, status=0/SUCCESS)
 Main PID: 25695 (freeradius)
    Tasks: 6 (limit: 4915)
   Memory: 11.0M
   CGroup: /system.slice/freeradius.service
           └─25695 /usr/sbin/freeradius

```



CSR1

```
interface GigabitEthernet6
 vrf forwarding LAB_MGMT
 ip address 192.168.18.1 255.255.255.0
 negotiation auto
!
```

Test reachability to **freeradius**

```
CSR1(config)#do ping vrf LAB_MGMT 192.168.18.12
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.18.12, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms

```

Configure CSR1 as PPPoE server

```
hostname CSR1
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
aaa new-model
!
!
aaa group server radius LAB_RADIUS
 server name FREERADIUS
 ip vrf forwarding LAB_MGMT
 ip radius source-interface GigabitEthernet6
!
aaa authentication ppp default group LAB_RADIUS
!
interface Loopback1
 vrf forwarding AS64499
 ip address 192.51.100.1 255.255.255.255
!
interface GigabitEthernet5.64499
 encapsulation dot1Q 100 second-dot1q any
 pppoe enable group BBA_GROUP_1
!
interface GigabitEthernet6
 vrf forwarding LAB_MGMT
 ip address 192.168.18.1 255.255.255.0
 negotiation auto
!
interface Virtual-Template1
 vrf forwarding AS64499
 no ip address
 peer default ip address pool LOCAL_POOL_AS64499
 ppp authentication chap
 ppp ipcp dns 8.8.8.8 8.8.4.4
!
```



Now setup PPPoE client on CUSTX-CPE1

```
hostname CUSTX-CPE1
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
interface Dialer1
 vrf forwarding CUST1
 no ip address
 encapsulation ppp
 dialer pool 1
 ppp authentication chap callin
 ppp chap hostname test@lab.com.au
 ppp chap password 0 test123
!
!
```

Debug CUSTX-CPE1

```
*Sep  5 01:54:31.524:  Sending PADI: Interface = GigabitEthernet2.64499
*Sep  5 01:54:31.526: PPPoE 0: I PADO  R:ba3f.e231.cd9d L:a2df.d1ed.e9d5 100 Gi2.64499
*Sep  5 01:54:33.573:  PPPOE: we've got our pado and the pado timer went off
*Sep  5 01:54:33.573: OUT PADR from PPPoE Session
*Sep  5 01:54:33.577: PPPoE 13: I PADS  R:ba3f.e231.cd9d L:a2df.d1ed.e9d5 100 Gi2.64499
*Sep  5 01:54:33.577: IN PADS from PPPoE Session
*Sep  5 01:54:33.583: %DIALER-6-BIND: Interface Vi1 bound to profile Di1
*Sep  5 01:54:33.583: PPPoE: Virtual Access interface obtained.
*Sep  5 01:54:33.583: PPPoE : encap string prepared
*Sep  5 01:54:33.583: [0]PPPoE 13: data path set to PPPoE Client
*Sep  5 01:54:33.585: %LINK-3-UPDOWN: Interface Virtual-Access1, changed state to up
*Sep  5 01:54:33.621: PPPoE 13: I PADT  R:ba3f.e231.cd9d L:a2df.d1ed.e9d5 100 Gi2.64499
*Sep  5 01:54:33.621:  PPPoE : Shutting down client session
*Sep  5 01:54:33.621: [0]PPPoE 13: O PADT  R:ba3f.e231.cd9d L:a2df.d1ed.e9d5 Gi2.64499
*Sep  5 01:54:33.622: %DIALER-6-UNBIND: Interface Vi1 unbound from profile Di1
*Sep  5 01:54:33.625: %LINK-3-UPDOWN: Interface Virtual-Access1, changed state to down

```

So PPPoE Discovery starting.  Let's look at CSR1

```
CSR1#debug pppoe events 

CSR1#
*Sep  5 01:55:38.781: PPPoE 0: I PADI  R:a2df.d1ed.e9d5 L:ffff.ffff.ffff 100 Gi5.64499
*Sep  5 01:55:38.781:  Service tag: NULL Tag
*Sep  5 01:55:38.781: PPPoE 0: O PADO, R:ba3f.e231.cd9d L:a2df.d1ed.e9d5 100 Gi5.64499
*Sep  5 01:55:38.781:  Service tag: NULL Tag
CSR1#
CSR1#
*Sep  5 01:55:40.831: PPPoE 0: I PADR  R:a2df.d1ed.e9d5 L:ba3f.e231.cd9d 100 Gi5.64499
*Sep  5 01:55:40.831:  Service tag: NULL Tag
*Sep  5 01:55:40.831: PPPoE : encap string prepared
*Sep  5 01:55:40.831: [16]PPPoE 16: Access IE handle allocated
*Sep  5 01:55:40.831: [16]PPPoE 16: AAA get retrieved attrs
*Sep  5 01:55:40.832: [16]PPPoE 16: AAA get nas port details
*Sep  5 01:55:40.832: [16]PPPoE 16: AAA get dynamic attrs
*Sep  5 01:55:40.832: [16]PPPoE 16: AAA unique ID 20 allocated
*Sep  5 01:55:40.832: [16]PPPoE 16: No AAA accounting method list 
*Sep  5 01:55:40.832: [16]PPPoE 16: Service request sent to SSS
*Sep  5 01:55:40.832: [16]PPPoE 16: Created, Service: None R:ba3f.e231.cd9d L:a2df.d1ed.e9d5 100 Gi5.64499
*Sep  5 01:55:40.833: [16]PPPoE 16: State NAS_PORT_POLICY_INQUIRY    Event SSS MORE KEYS
*Sep  5 01:55:40.833: [16]PPPoE 16: data path set to PPP
*Sep  5 01:55:40.833: [16]PPPoE 16: Segment (SSS class): PROVISION
*Sep  5 01:55:40.833: [16]PPPoE 16: State PROVISION_PPP    Event SSM PROVISIONED
*Sep  5 01:55:40.833: [16]PPPoE 16: O PADS  R:a2df.d1ed.e9d5 L:ba3f.e231.cd9d Gi5.64499
*Sep  5 01:55:40.875: [16]PPPoE 16: State LCP_NEGOTIATION    Event PPP DISCONNECT
*Sep  5 01:55:40.875: [16]PPPoE 16: O PADT  R:a2df.d1ed.e9d5 L:ba3f.e231.cd9d Gi5.64499
*Sep  5 01:55:40.875: [16]PPPoE 16: Destroying  R:a2df.d1ed.e9d5 L:ba3f.e231.cd9d 100 Gi5.64499
*Sep  5 01:55:40.875: [16]PPPoE 16: AAA get dynamic attrs
*Sep  5 01:55:40.875: [16]PPPoE 16: AAA account stopped
*Sep  5 01:55:40.876: [16]PPPoE 16: Segment (SSS class): UNPROVISION
*Sep  5 01:55:40.877: PPPoE 16: I PADT  R:a2df.d1ed.e9d5 L:ba3f.e231.cd9d 100 Gi5.64499

```

Negotiating failing - let's focus on ppp negotiation

```
CSR1#debug ppp negotiation 
PPP protocol negotiation debugging is on
CSR1#
CSR1#
*Sep  5 01:57:54.567: PPP: Alloc Context [7FC2121718B8]
*Sep  5 01:57:54.567: ppp22 PPP: Phase is ESTABLISHING
*Sep  5 01:57:54.568: ppp22 PPP: Using vpn set call direction
*Sep  5 01:57:54.568: ppp22 PPP: Treating connection as a callin
*Sep  5 01:57:54.568: ppp22 PPP: Session handle[EC000016] Session id[22]
*Sep  5 01:57:54.568: ppp22 LCP: Event[OPEN] State[Initial to Starting]
*Sep  5 01:57:54.568: ppp22 PPP LCP: Enter passive mode, state[Stopped]
*Sep  5 01:57:54.581: ppp22 LCP: I CONFREQ [Stopped] id 1 len 10
*Sep  5 01:57:54.581: ppp22 LCP:    MagicNumber 0x5BF781DC (0x05065BF781DC)
*Sep  5 01:57:54.581: ppp22 LCP: O CONFREQ [Stopped] id 1 len 19
*Sep  5 01:57:54.581: ppp22 LCP:    MRU 1492 (0x010405D4)
*Sep  5 01:57:54.581: ppp22 LCP:    AuthProto CHAP (0x0305C22305)
*Sep  5 01:57:54.581: ppp22 LCP:    MagicNumber 0x57037439 (0x050657037439)
*Sep  5 01:57:54.581: ppp22 LCP: O CONFACK [Stopped] id 1 len 10
*Sep  5 01:57:54.581: ppp22 LCP:    MagicNumber 0x5BF781DC (0x05065BF781DC)
*Sep  5 01:57:54.581: ppp22 LCP: Event[Receive ConfReq+] State[Stopped to ACKsent]
*Sep  5 01:57:54.583: ppp22 LCP: I CONFNAK [ACKsent] id 1 len 8
*Sep  5 01:57:54.583: ppp22 LCP:    MRU 1500 (0x010405DC)
*Sep  5 01:57:54.583: ppp22 LCP: O CONFREQ [ACKsent] id 2 len 19
*Sep  5 01:57:54.583: ppp22 LCP:    MRU 1500 (0x010405DC)
*Sep  5 01:57:54.583: ppp22 LCP:    AuthProto CHAP (0x0305C22305)
*Sep  5 01:57:54.583: ppp22 LCP:    MagicNumber 0x57037439 (0x050657037439)
*Sep  5 01:57:54.583: ppp22 LCP: Event[Receive ConfNak/Rej] State[ACKsent to ACKsent]
*Sep  5 01:57:54.584: ppp22 LCP: I CONFACK [ACKsent] id 2 len 19
*Sep  5 01:57:54.584: ppp22 LCP:    MRU 1500 (0x010405DC)
*Sep  5 01:57:54.584: ppp22 LCP:    AuthProto CHAP (0x0305C22305)
*Sep  5 01:57:54.584: ppp22 LCP:    MagicNumber 0x57037439 (0x050657037439)
*Sep  5 01:57:54.584: ppp22 LCP: Event[Receive ConfAck] State[ACKsent to Open]
*Sep  5 01:57:54.600: ppp22 PPP: Phase is AUTHENTICATING, by this end
*Sep  5 01:57:54.601: ppp22 CHAP: O CHALLENGE id 1 len 25 from "CSR1"
*Sep  5 01:57:54.601: ppp22 LCP: State is Open
*Sep  5 01:57:54.611: ppp22 CHAP: I RESPONSE id 1 len 36 from "test@lab.com.au"
*Sep  5 01:57:54.611: ppp22 PPP: Phase is FORWARDING, Attempting Forward
*Sep  5 01:57:54.611: ppp22 PPP: Phase is AUTHENTICATING, Unauthenticated User
*Sep  5 01:57:54.612: ppp22 CHAP: O FAILURE id 1 len 25 msg is "Authentication failed"
*Sep  5 01:57:54.612: ppp22 PPP DISC: AAA Server did not respond
*Sep  5 01:57:54.612: ppp22 PPP: Sending Acct Event[Down] id[26]
*Sep  5 01:57:54.613: PPP: NET STOP send to AAA.
*Sep  5 01:57:54.613: ppp22 LCP: O TERMREQ [Open] id 3 len 4
*Sep  5 01:57:54.613: ppp22 LCP: Event[CLOSE] State[Open to Closing]
*Sep  5 01:57:54.613: ppp22 PPP: Phase is TERMINATING
*Sep  5 01:57:54.614: ppp22 LCP: I TERMACK [Closing] id 3 len 4
*Sep  5 01:57:54.614: ppp22 LCP: Event[Receive TermAck] State[Closing to Closed]
*Sep  5 01:57:54.614: ppp22 LCP: Event[DOWN] State[Closed to Initial]
*Sep  5 01:57:54.614: ppp22 PPP: Phase is DOWN

```

Ok authenication is failing.  ```AAA Server did not respond```



No traffic on 1812
```
root@freeradius:/etc/freeradius# tcpdump -nni any port 1812 -v
tcpdump: listening on any, link-type LINUX_SLL (Linux cooked), capture size 262144 bytes

```

Back on CSR1.  Forgot to tell CSR1 where ```FREERADIUS``` is

```
!
radius server FREERADIUS
 address ipv4 192.168.18.12 auth-port 1812 acct-port 1813
 timeout 5
 retransmit 3
 key 0 test 
```

```
CSR1#show aaa servers 

RADIUS: id 1, priority 1, host 192.168.18.12, auth-port 1812, acct-port 1813
     State: current UP, duration 8s, previous duration 0s
     Dead: total time 0s, count 3
     Quarantined: No
     Authen: request 3, timeouts 3, failover 0, retransmission 3
             Response: accept 0, reject 0, challenge 0
             Response: unexpected 0, server error 0, incorrect 0, time 0ms
             Transaction: success 0, failure 0
             Throttled: transaction 0, timeout 0, failure 0
     Author: request 0, timeouts 0, failover 0, retransmission 0
             Response: accept 0, reject 0, challenge 0
             Response: unexpected 0, server error 0, incorrect 0, time 0ms
             Transaction: success 0, failure 0
             Throttled: transaction 0, timeout 0, failure 0
     Account: request 0, timeouts 0, failover 0, retransmission 0
             Request: start 0, interim 0, stop 0
             Response: start 0, interim 0, stop 0
             Response: unexpected 0, server error 0, incorrect 0, time 0ms
             Transaction: success 0, failure 0
             Throttled: transaction 0, timeout 0, failure 0
     Elapsed time since counters last cleared: 0m
     Estimated Outstanding Access Transactions: 1
     Estimated Outstanding Accounting Transactions: 0
     Estimated Throttled Access Transactions: 0
     Estimated Throttled Accounting Transactions: 0
     Maximum Throttled Transactions: access 0, accounting 0
     Requests per minute past 24 hours:
             high - 0 hours, 1 minutes ago: 1
             low  - 0 hours, 1 minutes ago: 1
             average: 1

```

Ok now traffic on **freeradius**

```
tcpdump: listening on any, link-type LINUX_SLL (Linux cooked), capture size 262144 bytes
02:10:26.089909 IP (tos 0x0, ttl 255, id 7230, offset 0, flags [none], proto UDP (17), length 177)
    192.168.18.1.1645 > 192.168.18.12.1812: RADIUS, length: 149
	Access-Request (1), id: 0x30, Authenticator: 9bf9143344a7b316c5065539781db14a
	  Framed-Protocol Attribute (7), length: 6, Value: PPP
	  User-Name Attribute (1), length: 17, Value: test@lab.com.au
	  CHAP-Password Attribute (3), length: 19, Value: 
	  NAS-Port-Type Attribute (61), length: 6, Value: Virtual
	  NAS-Port Attribute (5), length: 6, Value: 0
	  NAS-Port-Id Attribute (87), length: 13, Value: 0/0/0/100.1
	  Connect-Info Attribute (77), length: 9, Value: AS64499
	  Vendor-Specific Attribute (26), length: 41, Value: Vendor: Cisco (9)
	    Vendor Attribute: 1, Length: 33, Value: client-mac-address=a2df.d1ed.e9d5
	  Service-Type Attribute (6), length: 6, Value: Framed
	  NAS-IP-Address Attribute (4), length: 6, Value: 192.168.18.1
```

But **freeradius** not responding.

Restart service

```
root@freeradius:/etc/freeradius# systemctl restart freeradius
root@freeradius:/etc/freeradius# tcpdump -nni any port 1812 -v
tcpdump: listening on any, link-type LINUX_SLL (Linux cooked), capture size 262144 bytes
02:13:15.145959 IP (tos 0x0, ttl 255, id 7421, offset 0, flags [none], proto UDP (17), length 177)
    192.168.18.1.1645 > 192.168.18.12.1812: RADIUS, length: 149
	Access-Request (1), id: 0x34, Authenticator: 9aca123b2471c480c506553919f8c0d4
	  Framed-Protocol Attribute (7), length: 6, Value: PPP
	  User-Name Attribute (1), length: 17, Value: test@lab.com.au
	  CHAP-Password Attribute (3), length: 19, Value: 
	  NAS-Port-Type Attribute (61), length: 6, Value: Virtual
	  NAS-Port Attribute (5), length: 6, Value: 0
	  NAS-Port-Id Attribute (87), length: 13, Value: 0/0/0/100.1
	  Connect-Info Attribute (77), length: 9, Value: AS64499
	  Vendor-Specific Attribute (26), length: 41, Value: Vendor: Cisco (9)
	    Vendor Attribute: 1, Length: 33, Value: client-mac-address=a2df.d1ed.e9d5
	  Service-Type Attribute (6), length: 6, Value: Framed
	  NAS-IP-Address Attribute (4), length: 6, Value: 192.168.18.1
02:13:15.250278 IP (tos 0x0, ttl 64, id 47759, offset 0, flags [none], proto UDP (17), length 89)
    192.168.18.12.1812 > 192.168.18.1.1645: RADIUS, length: 61
	Access-Accept (2), id: 0x34, Authenticator: 8d997390466f813548326030dd78f6e6
	  Framed-Protocol Attribute (7), length: 6, Value: PPP
	  Vendor-Specific Attribute (26), length: 35, Value: Vendor: Cisco (9)
	    Vendor Attribute: 1, Length: 27, Value: ip:sub-qos-policy-out=100MB

```

Still not authenticating

```
*Sep  5 02:16:03.543: ppp61 PPP: Using vpn set call direction
*Sep  5 02:16:03.543: ppp61 PPP: Treating connection as a callin
*Sep  5 02:16:03.543: ppp61 PPP: Session handle[9F00003D] Session id[61]
*Sep  5 02:16:03.560: ppp61 CHAP: O CHALLENGE id 1 len 25 from "CSR1"
*Sep  5 02:16:03.571: ppp61 CHAP: I RESPONSE id 1 len 36 from "test@lab.com.au"
*Sep  5 02:16:03.571: ppp61 PPP: Sent CHAP LOGIN Request
*Sep  5 02:16:13.578: ppp61 AUTH: Timeout 1
*Sep  5 02:16:13.587: ppp61 CHAP: I RESPONSE id 1 len 36 from "test@lab.com.au"
*Sep  5 02:16:13.587: ppp61 CHAP: Ignoring Additional Response
*Sep  5 02:16:13.619: %RADIUS-4-RADIUS_DEAD: RADIUS server 192.168.18.12:1812,1813 is not responding.
*Sep  5 02:16:13.619: %RADIUS-4-RADIUS_ALIVE: RADIUS server 192.168.18.12:1812,1813 is being marked alive.
*Sep  5 02:16:23.592: ppp61 AUTH: Timeout 2
*Sep  5 02:16:23.603: ppp61 CHAP: I RESPONSE id 1 len 36 from "test@lab.com.au"
*Sep  5 02:16:23.603: ppp61 CHAP: Ignoring Additional Response
*Sep  5 02:16:23.687: ppp61 PPP: Received LOGIN Response FAIL
*Sep  5 02:16:23.687: ppp61 CHAP: O FAILURE id 1 len 25 msg is "Authentication failed"

```

```
policy-map 100MB
 class class-default
  shape average 98000000 1400000 0
```

```ip unnumbered``` in Virtual-template

```
interface Virtual-Template1
 vrf forwarding AS64499
 ip unnumbered Loopback1

```


Still failing

```
*Sep  5 02:37:11.635: ppp91 PPP: Phase is FORWARDING, Attempting Forward
*Sep  5 02:37:11.635: ppp91 PPP: Phase is AUTHENTICATING, Unauthenticated User
```



