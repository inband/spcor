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

CSR1(config-bba-group)#control-packets vlan cos 2


```

Looks like the session has to be bounced to take effect.  Which vlan tag will change - inner, outer or both (or none)

It was **none**.



```
CSR1#debug pppoe packets 
PPPoE control packets debugging is on
CSR1#clea
CSR1#clear pp
CSR1#clear pppoe all
CSR1#
CSR1#
CSR1#
CSR1#
CSR1#
CSR1#
*Sep 14 11:29:57.647: PPPoE 0: I PADI  R:a2df.d1ed.e9d5 L:ffff.ffff.ffff 100 Gi5.64499
contiguous pak, size 44
	 FF FF FF FF FF FF A2 DF D1 ED E9 D5 81 00 00 64
	 81 00 00 01 88 63 11 09 00 00 00 10 01 01 00 00
	 01 03 00 08 2E 00 00 01 00 00 02 D9
*Sep 14 11:29:57.648: PPPoE 0: O PADO, R:4ac7.d41b.83a1 L:a2df.d1ed.e9d5 100 Gi5.64499
*Sep 14 11:29:57.648:  Service tag: NULL Tag
contiguous pak, size 72
	 A2 DF D1 ED E9 D5 4A C7 D4 1B 83 A1 81 00 40 64
	 81 00 40 01 88 63 11 07 00 00 00 2C 01 01 00 00
	 01 03 00 08 2E 00 00 01 00 00 02 D9 01 02 00 04
	 43 53 52 31 01 04 00 10 36 49 8E 2F B7 B4 9B AB
	 DC 6C BE 93 B0 A8 3E 7F
*Sep 14 11:29:59.696: PPPoE 0: I PADR  R:a2df.d1ed.e9d5 L:4ac7.d41b.83a1 100 Gi5.64499
contiguous pak, size 72
	 4A C7 D4 1B 83 A1 A2 DF D1 ED E9 D5 81 00 00 64
	 81 00 00 01 88 63 11 19 00 00 00 2C 01 03 00 08
	 2E 00 00 01 00 00 02 D9 01 02 00 04 43 53 52 31
	 01 04 00 10 36 49 8E 2F B7 B4 9B AB DC 6C BE 93
	 B0 A8 3E 7F 01 01 00 00
*Sep 14 11:29:59.697: [76]PPPoE 76: O PADS  R:a2df.d1ed.e9d5 L:4ac7.d41b.83a1 Gi5.64499
contiguous pak, size 72
	 A2 DF D1 ED E9 D5 4A C7 D4 1B 83 A1 81 00 40 64
	 81 00 40 01 88 63 11 65 00 4C 00 2C 01 03 00 08
	 2E 00 00 01 00 00 02 D9 01 02 00 04 43 53 52 31
	 01 04 00 10 36 49 8E 2F B7 B4 9B AB DC 6C BE 93
	 B0 A8 3E 7F 01 01 00 00

```


However, debug indicates in should be **both**.


```
81 00 40 64
81 00 40 01

```


Maybe it is the existing policy map.

```
class-map match-all CLASS_RTP
 match dscp ef 
!
policy-map POLICY_RTP
 class CLASS_RTP
  set cos-inner 5
!
!
interface Virtual-Template1
 mtu 1492
 vrf forwarding AS64499
 ip unnumbered Loopback1
 peer default ip address pool LOCAL_POOL_AS64499
 ppp authentication chap
 ppp ipcp dns 8.8.8.8 8.8.4.4
 service-policy output POLICY_RTP

```


Remove policy map - ok just noticed the **tcpdump** only seeing CUSTX-CPE1 after CoS change.

```
root@pve6-lab:~# tcpdump -nnei vmbr0v100 vlan 100

Changed to:

root@pve6-lab:~# tcpdump -nnei vmbr0v100

```

Ok so it was working all along.

```
root@pve6-lab:~# tcpdump -nnei vmbr0v100
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on vmbr0v100, link-type EN10MB (Ethernet), capture size 262144 bytes

21:44:44.624604 a2:df:d1:ed:e9:d5 > ff:ff:ff:ff:ff:ff, ethertype 802.1Q (0x8100), length 64: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE D, PPPoE PADI [Service-Name] [Host-Uniq 0x2E000001000002D9]
21:44:44.626629 4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 72: vlan 100, p 2, ethertype 802.1Q, vlan 1, p 2, ethertype PPPoE D, PPPoE PADO [Service-Name] [Host-Uniq 0x2E000001000002D9] [AC-Name "CSR1"] [AC-Cookie 0x36498E2FB7B49BABDC6CBE93B0A83E7F]
21:44:46.675064 a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 72: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE D, PPPoE PADR [Host-Uniq 0x2E000001000002D9] [AC-Name "CSR1"] [AC-Cookie 0x36498E2FB7B49BABDC6CBE93B0A83E7F] [Service-Name]
21:44:46.678330 4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 72: vlan 100, p 2, ethertype 802.1Q, vlan 1, p 2, ethertype PPPoE D, PPPoE PADS [ses 0x4d] [Host-Uniq 0x2E000001000002D9] [AC-Name "CSR1"] [AC-Cookie 0x36498E2FB7B49BABDC6CBE93B0A83E7F] [Service-Name]
21:44:46.685237 a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x4d] LCP (0xc021), length 12: LCP, Conf-Request (0x01), id 1, length 12
21:44:46.686241 4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 2, ethertype 802.1Q, vlan 1, p 2, ethertype PPPoE S, PPPoE  [ses 0x4d] LCP (0xc021), length 21: LCP, Conf-Request (0x01), id 1, length 21
21:44:46.686253 4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 2, ethertype 802.1Q, vlan 1, p 2, ethertype PPPoE S, PPPoE  [ses 0x4d] LCP (0xc021), length 12: LCP, Conf-Ack (0x02), id 1, length 12
21:44:46.686778 a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x4d] LCP (0xc021), length 10: LCP, Conf-Nack (0x03), id 1, length 10
21:44:46.687441 4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 2, ethertype 802.1Q, vlan 1, p 2, ethertype PPPoE S, PPPoE  [ses 0x4d] LCP (0xc021), length 21: LCP, Conf-Request (0x01), id 2, length 21
21:44:46.688126 a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x4d] LCP (0xc021), length 21: LCP, Conf-Ack (0x02), id 2, length 21
21:44:46.711769 4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 2, ethertype 802.1Q, vlan 1, p 2, ethertype PPPoE S, PPPoE  [ses 0x4d] CHAP (0xc223), length 27: CHAP, Challenge (0x01), id 1, Value f8c3a17248cf3d31c5065539174f8a2c, Name CSR1
21:44:46.712330 a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x4d] CHAP (0xc223), length 39: CHAP, Response (0x02), id 1, Value 23e0e227b8e014c799fec48858616b04, Name test2@lab.com.au
21:44:46.774219 4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 2, ethertype 802.1Q, vlan 1, p 2, ethertype PPPoE S, PPPoE  [ses 0x4d] CHAP (0xc223), length 6: CHAP, Success (0x03), id 1, Msg 
21:44:46.774623 4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 2, ethertype 802.1Q, vlan 1, p 2, ethertype PPPoE S, PPPoE  [ses 0x4d] IPCP (0x8021), length 12: IPCP, Conf-Request (0x01), id 1, length 12
21:44:46.776069 a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x4d] IPCP (0x8021), length 12: IPCP, Conf-Request (0x01), id 1, length 12
21:44:46.776936 a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x4d] IPCP (0x8021), length 12: IPCP, Conf-Ack (0x02), id 1, length 12
21:44:46.776997 4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 2, ethertype 802.1Q, vlan 1, p 2, ethertype PPPoE S, PPPoE  [ses 0x4d] IPCP (0x8021), length 12: IPCP, Conf-Nack (0x03), id 1, length 12
21:44:46.777495 a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x4d] IPCP (0x8021), length 12: IPCP, Conf-Request (0x01), id 2, length 12
21:44:46.778284 4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 2, ethertype 802.1Q, vlan 1, p 2, ethertype PPPoE S, PPPoE  [ses 0x4d] IPCP (0x8021), length 12: IPCP, Conf-Ack (0x02), id 2, length 12
21:44:56.759677 4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 2, ethertype 802.1Q, vlan 1, p 2, ethertype PPPoE S, PPPoE  [ses 0x4d] LCP (0xc021), length 14: LCP, Echo-Request (0x09), id 1, length 14
21:44:56.760239 a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x4d] LCP (0xc021), length 14: LCP, Echo-Reply (0x0a), id 1, length 14
21:44:56.774230 a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x4d] LCP (0xc021), length 14: LCP, Echo-Request (0x09), id 1, length 14
21:44:56.774721 4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 42: vlan 100, p 2, ethertype 802.1Q, vlan 1, p 2, ethertype PPPoE S, PPPoE  [ses 0x4d] LCP (0xc021), length 14: LCP, Echo-Reply (0x0a), id 1, length 14

```


CoS 6 is going to be the desired value - now we can allocate bandwidth egress for CoS 6.


