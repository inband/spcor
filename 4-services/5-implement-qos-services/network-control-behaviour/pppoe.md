# PPPoE

1 session terminated locally

```
CSR1#show pppoe session 
     1 session  in LOCALLY_TERMINATED (PTA) State
     1 session  total

Uniq ID  PPPoE  RemMAC          Port                    VT  VA         State
           SID  LocMAC                                      VA-st      Type
     72     72  a2df.d1ed.e9d5  Gi5.64499                1  Vi2.1      PTA  
                4ac7.d41b.83a1  VLAN: 100/1                 UP    

```

Clear session on ```CSR1```

```
CSR1#clear pppoe all
```

CSR1 is ```4a:c7:d4:1b:83:a1```

CUSTX-CPE1 is ```a2:df:d1:ed:e9:d5```


```
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x48] LCP (0xc021), length 6: LCP, Term-Request (0x05), id 3, length 6
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 64: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE D, PPPoE PADT [ses 0x48]
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x48] LCP (0xc021), length 6: LCP, Term-Ack (0x06), id 3, length 6
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 64: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE D, PPPoE PADT [ses 0x48]





a2:df:d1:ed:e9:d5 > ff:ff:ff:ff:ff:ff, ethertype 802.1Q (0x8100), length 64: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE D, PPPoE PADI [Service-Name] [Host-Uniq 0x2E000001000002D9]
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 72: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE D, PPPoE PADO [Service-Name] [Host-Uniq 0x2E000001000002D9] [AC-Name "CSR1"] [AC-Cookie 0x36498E2FB7B49BABDC6CBE93B0A83E7F]
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 72: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE D, PPPoE PADR [Host-Uniq 0x2E000001000002D9] [AC-Name "CSR1"] [AC-Cookie 0x36498E2FB7B49BABDC6CBE93B0A83E7F] [Service-Name]
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 72: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE D, PPPoE PADS [ses 0x49] [Host-Uniq 0x2E000001000002D9] [AC-Name "CSR1"] [AC-Cookie 0x36498E2FB7B49BABDC6CBE93B0A83E7F] [Service-Name]
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x49] LCP (0xc021), length 12: LCP, Conf-Request (0x01), id 1, length 12
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x49] LCP (0xc021), length 21: LCP, Conf-Request (0x01), id 1, length 21
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x49] LCP (0xc021), length 12: LCP, Conf-Ack (0x02), id 1, length 12
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x49] LCP (0xc021), length 10: LCP, Conf-Nack (0x03), id 1, length 10
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x49] LCP (0xc021), length 21: LCP, Conf-Request (0x01), id 2, length 21
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x49] LCP (0xc021), length 21: LCP, Conf-Ack (0x02), id 2, length 21
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x49] CHAP (0xc223), length 27: CHAP, Challenge (0x01), id 1, Value ab1ea204f7705aecc5065539fb2dee87, Name CSR1
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x49] CHAP (0xc223), length 39: CHAP, Response (0x02), id 1, Value 27711da2d70d7ca6f8e2a37c18a96e3c, Name test2@lab.com.au
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x49] CHAP (0xc223), length 6: CHAP, Success (0x03), id 1, Msg 
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x49] IPCP (0x8021), length 12: IPCP, Conf-Request (0x01), id 1, length 12
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x49] IPCP (0x8021), length 12: IPCP, Conf-Request (0x01), id 1, length 12
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x49] IPCP (0x8021), length 12: IPCP, Conf-Ack (0x02), id 1, length 12
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x49] IPCP (0x8021), length 12: IPCP, Conf-Nack (0x03), id 1, length 12
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x49] IPCP (0x8021), length 12: IPCP, Conf-Request (0x01), id 2, length 12
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x49] IPCP (0x8021), length 12: IPCP, Conf-Ack (0x02), id 2, length 12
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x49] LCP (0xc021), length 14: LCP, Echo-Request (0x09), id 1, length 14
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x49] LCP (0xc021), length 14: LCP, Echo-Reply (0x0a), id 1, length 14
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x49] LCP (0xc021), length 14: LCP, Echo-Request (0x09), id 1, length 14
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 42: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x49] LCP (0xc021), length 14: LCP, Echo-Reply (0x0a), id 1, length 14
```

Observations

* no CoS in either direction
* keepalives comes from CSR1 and CUSTX-CPE1 every 10 seconds


I thought it was meant to be every 20 seconds and timeout after 60 seconds - look into further.


Check defaults

```
CSR1#show run all | b bba           
bba-group pppoe BBA_GROUP_1
 virtual-template 1
 sessions max limit 4294967295 threshold 4294967295
 sessions per-vc limit 100 threshold 0
 sessions per-mac limit 100
 sessions per-vlan limit 100 inner 100
 sessions per-vc throttle 100000 3600 0
 sessions per-mac throttle 100000 3600 0
 sessions per-vlan throttle 100000 3600 0
 tag ppp-max-payload minimum 1492 maximum 1500
 pado delay -1
 pado delay circuit-id -1
 pado delay remote-id -1
 control-packets vlan cos 8

```

```
CSR1(config)#bba-group pppoe BBA_GROUP_1
CSR1(config-bba-group)#?
BBA Group configuration commands:
  control-packets   PPPoE control packets related configuration
  default           Set a command to its defaults
  exit              Exit from BBA Group configuration mode
  limit             limit contents of pppoe control messages
  mac-address       Set mac address
  nas-port          Specific format for nas-port
  nas-port-id       Specific format for nas-port-id
  no                Negate a command or set its defaults
  pado              PADO delay options
  pppoe             PPPoE server selection configuration
  service           Services to be associated with this group
  sessions          BBA session commands
  tag               Configure processing options for a tag
  vendor-tag        PPPoE Vendor Specific Tag
  virtual-template  BBA virtual template command

CSR1(config-bba-group)#control-packets vlan cos ?
  <0-7>  Pirority value for PPP/PPPoE control packets in VLAN header

CSR1(config-bba-group)#control-packets vlan cos 6


```

Looks like the session has to be bounced to take effect.  Which vlan tag will change - inner, outer or both (or none)

It was **none**.








