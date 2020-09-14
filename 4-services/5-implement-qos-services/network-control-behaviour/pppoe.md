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





