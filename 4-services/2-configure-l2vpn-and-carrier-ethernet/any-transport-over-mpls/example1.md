# configure VPWS

This example will attempt to create a VPWS:

* Layer2 from CUSTX-CPE1 to CUSTX-CPE2
* vrf name ```CUST1```
* AS64499 will be the Service provider (SP) and supply Layer2 link
* CUST1-CPE1 Lo1 will be ```10.1.1.1/32```
* CUST1-CPE2 Lo1 will be ```10.1.1.2/32```
* use ```10.1.1.10/31``` for L2 link
* setup routing protocol to route between Loopbacks



CUSTX-CPE1
```
hostname CUSTX-CPE1
!
vrf definition CUST1
 rd 1:1
 !
 address-family ipv4
 exit-address-family
!
interface Loopback1
 vrf forwarding CUST1
 ip address 10.1.1.1 255.255.255.255
!
interface GigabitEthernet2.100
 encapsulation dot1Q 100
 vrf forwarding CUST1
 ip address 10.1.1.10 255.255.255.254
!

```

CUSTX-CPE2
```
hostname CUSTX-CPE2
!
!
vrf definition CUST1
 rd 1:1
 !
 address-family ipv4
 exit-address-family
!
interface GigabitEthernet2.100
 encapsulation dot1Q 100
 vrf forwarding CUST1
 ip address 10.1.1.11 255.255.255.254
!

```

CSR1
```
hostname CSR1
!
pseudowire-class CUST1
 encapsulation mpls
!
interface GigabitEthernet5.100
 encapsulation dot1Q 100
 xconnect 192.51.100.4 100 encapsulation mpls pw-class CUST1
!
```


CSR4
```
hostname CSR4
!
pseudowire-class CUST1
 encapsulation mpls
!
interface GigabitEthernet5.100
 encapsulation dot1Q 100
 xconnect 192.51.100.1 100 encapsulation mpls pw-class CUST1
```

Test reachability

```
CUSTX-CPE2#ping vrf CUST1 10.1.1.10 df-bit size 1500
Type escape sequence to abort.
Sending 5, 1500-byte ICMP Echos to 10.1.1.10, timeout is 2 seconds:
Packet sent with the DF bit set
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 2/2/3 ms

```


Well that was pretty easy.  Let's dive a little deeper.

```
CSR1#show mpls l2transport vc 

Local intf     Local circuit              Dest address    VC ID      Status
-------------  -------------------------- --------------- ---------- ----------
Gi5.100        Eth VLAN 100               192.51.100.4    100        UP        
```


```
CSR1#show mpls l2transport pwid 
AToM Pseudowire IDs: In use: 1, In holddown: 0

Label  Peer-Address    VCID       PWID       In-Use FirstUse ResuedAt FreedAt 
------ --------------- ---------- ---------- ------ -------- -------- --------
25     192.51.100.4    100        1          Yes    02:33:55 Never    Never    
```

Show detail

CSR1

```
CSR1#show mpls l2transport vc detail 
Local interface: Gi5.100 up, line protocol up, Eth VLAN 100 up
  Interworking type is Ethernet
  Destination address: 192.51.100.4, VC ID: 100, VC status: up
    Output interface: Gi2.12, imposed label stack {16 22}
    Preferred path: not configured  
    Default path: active
    Next hop: 192.51.100.201
  Create time: 00:12:59, last status change time: 00:12:59
    Last label FSM state change time: 00:12:59
  Signaling protocol: LDP, peer 192.51.100.4:0 up
    Targeted Hello: 192.51.100.1(LDP Id) -> 192.51.100.4, LDP is UP
    Graceful restart: not configured and not enabled
    Non stop routing: not configured and not enabled
    Status TLV support (local/remote)   : enabled/supported
      LDP route watch                   : enabled
      Label/status state machine        : established, LruRru
      Last local dataplane   status rcvd: No fault
      Last BFD dataplane     status rcvd: Not sent
      Last BFD peer monitor  status rcvd: No fault
      Last local AC  circuit status rcvd: No fault
      Last local AC  circuit status sent: No fault
      Last local PW i/f circ status rcvd: No fault
      Last local LDP TLV     status sent: No fault
      Last remote LDP TLV    status rcvd: No fault
      Last remote LDP ADJ    status rcvd: No fault
    MPLS VC labels: local 25, remote 22 
    Group ID: local 0, remote 0
    MTU: local 1500, remote 1500
    Remote interface description: 
  Sequencing: receive disabled, send disabled
  Control Word: On (configured: autosense)
  SSO Descriptor: 192.51.100.4/100, local label: 25
  Dataplane:
    SSM segment/switch IDs: 4100/4099 (used), PWID: 1
  VC statistics:
    transit packet totals: receive 20, send 20
    transit byte totals:   receive 9306, send 9826
    transit packet drops:  receive 0, seq error 0, send 0

```

CSR4

```
CSR4#show mpls l2transport vc 100 detail 
Local interface: Gi5.100 up, line protocol up, Eth VLAN 100 up
  Interworking type is Ethernet
  Destination address: 192.51.100.1, VC ID: 100, VC status: up
    Output interface: Gi2.24, imposed label stack {18 25}
    Preferred path: not configured  
    Default path: active
    Next hop: 192.51.100.204
  Create time: 00:23:56, last status change time: 00:23:37
    Last label FSM state change time: 00:23:37
  Signaling protocol: LDP, peer 192.51.100.1:0 up
    Targeted Hello: 192.51.100.4(LDP Id) -> 192.51.100.1, LDP is UP
    Graceful restart: not configured and not enabled
    Non stop routing: not configured and not enabled
    Status TLV support (local/remote)   : enabled/supported
      LDP route watch                   : enabled
      Label/status state machine        : established, LruRru
      Last local dataplane   status rcvd: No fault
      Last BFD dataplane     status rcvd: Not sent
      Last BFD peer monitor  status rcvd: No fault
      Last local AC  circuit status rcvd: No fault
      Last local AC  circuit status sent: No fault
      Last local PW i/f circ status rcvd: No fault
      Last local LDP TLV     status sent: No fault
      Last remote LDP TLV    status rcvd: No fault
      Last remote LDP ADJ    status rcvd: No fault
    MPLS VC labels: local 22, remote 25 
    Group ID: local 0, remote 0
    MTU: local 1500, remote 1500
    Remote interface description: 
  Sequencing: receive disabled, send disabled
  Control Word: On (configured: autosense)
  SSO Descriptor: 192.51.100.1/100, local label: 22
  Dataplane:
    SSM segment/switch IDs: 4100/4099 (used), PWID: 1
  VC statistics:
    transit packet totals: receive 20, send 20
    transit byte totals:   receive 9306, send 9826
    transit packet drops:  receive 0, seq error 0, send 0


CSR4#
```


