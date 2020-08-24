# example 2

For this example test the following:

* use different VCs on PE



What happen when a different VC is used on either end?

Both PEs have VC of 1001

```
CSR1#show mpls l2transport vc 

Local intf     Local circuit              Dest address    VC ID      Status
-------------  -------------------------- --------------- ---------- ----------
Gi5.100        Eth VLAN 100               192.51.100.4    1001       UP        

```

On CSR4 change VC to 1002

```
CSR4(config)#interface GigabitEthernet5.100
CSR4(config-subif)# xconnect 192.51.100.1 1002 encapsulation mpls pw-class CUST1
```


Check on CSR1

```
CSR1#show mpls l2transport vc 1001

Local intf     Local circuit              Dest address    VC ID      Status
-------------  -------------------------- --------------- ---------- ----------
Gi5.100        Eth VLAN 100               192.51.100.4    1001       DOWN      

```

Attachment Circuit is UP but VC status is DOWN. VC has to be consistent on either end.



```
CSR1#show mpls l2transport vc 1001 detail 
Local interface: Gi5.100 up, line protocol up, Eth VLAN 100 up
  Interworking type is Ethernet
  Destination address: 192.51.100.4, VC ID: 1001, VC status: down
    Last error: Local access circuit is not ready for label advertise
    Output interface: none, imposed label stack {}
    Preferred path: not configured  
    Default path: no route
    No adjacency
  Create time: 23:19:12, last status change time: 00:02:28
    Last label FSM state change time: 00:02:28
  Signaling protocol: LDP, peer unknown 
    Targeted Hello: 192.51.100.1(LDP Id) -> 192.51.100.4, LDP is DOWN, no binding
    Graceful restart: not configured and not enabled
    Non stop routing: not configured and not enabled
    Status TLV support (local/remote)   : enabled/None (no remote binding)
      LDP route watch                   : enabled
      Label/status state machine        : local standby, AC-ready, LnuRnd
      Last local dataplane   status rcvd: No fault
      Last BFD dataplane     status rcvd: Not sent
      Last BFD peer monitor  status rcvd: No fault
      Last local AC  circuit status rcvd: No fault
      Last local AC  circuit status sent: DOWN(hard-down)
      Last local PW i/f circ status rcvd: No fault
      Last local LDP TLV     status sent: No fault (withdrawn)
      Last remote LDP TLV    status rcvd: None (no remote binding)
      Last remote LDP ADJ    status rcvd: None (no remote binding)
    MPLS VC labels: local 27, remote unassigned 
    Group ID: local 0, remote unknown
    MTU: local 1500, remote unknown
    Remote interface description: 
  Sequencing: receive disabled, send disabled
  Control Word: On (configured: autosense)
  SSO Descriptor: 192.51.100.4/1001, local label: 27
  Dataplane:
    SSM segment/switch IDs: 0/4102 (used), PWID: 2
  VC statistics:
    transit packet totals: receive 20, send 11
    transit byte totals:   receive 9090, send 1206
    transit packet drops:  receive 0, seq error 0, send 0
```


There is no remote label binding.

This is also seen of CSR4

```
CSR4#show mpls l2transport binding 
  Destination Address: 192.51.100.1,VC ID: 1002
    Local Label:  22
        Cbit: 1,    VC Type: Ethernet,    GroupID: 0
        MTU: 1500,   Interface Desc: n/a
        VCCV: CC Type: CW [1], RA [2], TTL [3]
              CV Type: LSPV [2]
    Remote Label: unassigned
```


Change CSR4 back

```
CSR4(config)#interface GigabitEthernet5.100                               
CSR4(config-subif)# xconnect 192.51.100.1 1001 encapsulation mpls pw-class CUST1
```

```
CSR4#show mpls l2transport binding 
  Destination Address: 192.51.100.1,VC ID: 1001
    Local Label:  23
        Cbit: 1,    VC Type: Ethernet,    GroupID: 0
        MTU: 1500,   Interface Desc: n/a
        VCCV: CC Type: CW [1], RA [2], TTL [3]
              CV Type: LSPV [2]
    Remote Label: 25
        Cbit: 1,    VC Type: Ethernet,    GroupID: 0
        MTU: 1500,   Interface Desc: n/a
        VCCV: CC Type: CW [1], RA [2], TTL [3]
              CV Type: LSPV [2]
```


Local binding 23 will be advertised to CSR1 from CSR4.  We see the CSR1 has advertised label 25.

Check CSR1 has the inverse information.

```
CSR1#show mpls l2transport binding 1001
  Destination Address: 192.51.100.4,VC ID: 1001
    Local Label:  25
        Cbit: 1,    VC Type: Ethernet,    GroupID: 0
        MTU: 1500,   Interface Desc: n/a
        VCCV: CC Type: CW [1], RA [2], TTL [3]
              CV Type: LSPV [2]
    Remote Label: 23
        Cbit: 1,    VC Type: Ethernet,    GroupID: 0
        MTU: 1500,   Interface Desc: n/a
        VCCV: CC Type: CW [1], RA [2], TTL [3]
              CV Type: LSPV [2]

```

