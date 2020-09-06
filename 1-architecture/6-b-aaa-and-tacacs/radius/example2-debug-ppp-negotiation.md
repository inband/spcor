# Example2 - debug ppp negotiation


CSR1

```
CSR1#debug ppp negotiation 
PPP protocol negotiation debugging is on
CSR1#
CSR1#
CSR1#
CSR1#clear subscriber session username  test2@lab.com.au
CSR1#
*Sep  6 08:23:42.955: Vi1.1 PPP: Block vaccess from being freed [0x11]
*Sep  6 08:23:42.956: Vi1.1 PPP DISC: Lower Layer disconnected
*Sep  6 08:23:42.958: Vi1.1 PPP: Sending Acct Event[Down] id[17]
*Sep  6 08:23:42.958: PPP: NET STOP send to AAA.
*Sep  6 08:23:42.958: ppp_session_ntfy delete, topswidb Vi1.1, va Vi1.1, platform notify 0
*Sep  6 08:23:42.958: Vi1.1 PPP: Unlocked by [0x1] Still Locked by [0x10]
*Sep  6 08:23:42.961: Vi1.1 IPCP: Event[DOWN] State[Open to Starting]
*Sep  6 08:23:42.961: Vi1.1 IPCP: Event[CLOSE] State[Starting to Initial]
*Sep  6 08:23:42.961: Vi1.1 LCP: O TERMREQ [Open] id 3 len 4
*Sep  6 08:23:42.961: Vi1.1 LCP: Event[CLOSE] State[Open to Closing]
*Sep  6 08:23:42.962: Vi1.1 PPP: Phase is TERMINATING
*Sep  6 08:23:42.964: Vi1.1 PPP: Block vaccess from being freed [0x10]
*Sep  6 08:23:42.965: Vi1.1 Deleted neighbor route from AVL tree: topoid 796304859762330381, address 172.20.1.2
*Sep  6 08:23:42.965: Vi1.1 IPCP: Remove route to 172.20.1.2
*Sep  6 08:23:42.966: Vi1.1 LCP: Event[DOWN] State[Closing to Initial]
*Sep  6 08:23:42.966: ppp_session_ntfy delete, topswidb Vi1.1, va Vi1.1, platform notify 0
*Sep  6 08:23:42.966: Vi1.1 PPP: Unlocked by [0x10] Still Locked by [0x0]
*Sep  6 08:23:42.966: Vi1.1 PPP: Free previously blocked vaccess
CSR1#
CSR1#
CSR1#
*Sep  6 08:23:42.966: Vi1.1 PPP: Phase is DOWN
CSR1#
CSR1#
CSR1#
CSR1#
*Sep  6 08:24:05.159: PPP: Alloc Context [7FC096F537A0]
*Sep  6 08:24:05.159: ppp11 PPP: Phase is ESTABLISHING
*Sep  6 08:24:05.159: ppp11 PPP: Using vpn set call direction
*Sep  6 08:24:05.160: ppp11 PPP: Treating connection as a callin
*Sep  6 08:24:05.160: ppp11 PPP: Session handle[4500000B] Session id[11]
*Sep  6 08:24:05.160: ppp11 LCP: Event[OPEN] State[Initial to Starting]
*Sep  6 08:24:05.160: ppp11 PPP LCP: Enter passive mode, state[Stopped]
*Sep  6 08:24:05.172: ppp11 LCP: I CONFREQ [Stopped] id 1 len 10
*Sep  6 08:24:05.172: ppp11 LCP:    MagicNumber 0x62807D41 (0x050662807D41)
*Sep  6 08:24:05.172: ppp11 LCP: O CONFREQ [Stopped] id 1 len 19
*Sep  6 08:24:05.172: ppp11 LCP:    MRU 1492 (0x010405D4)
*Sep  6 08:24:05.172: ppp11 LCP:    AuthProto CHAP (0x0305C22305)
*Sep  6 08:24:05.172: ppp11 LCP:    MagicNumber 0x2047CF94 (0x05062047CF94)
*Sep  6 08:24:05.172: ppp11 LCP: O CONFACK [Stopped] id 1 len 10
*Sep  6 08:24:05.173: ppp11 LCP:    MagicNumber 0x62807D41 (0x050662807D41)
*Sep  6 08:24:05.173: ppp11 LCP: Event[Receive ConfReq+] State[Stopped to ACKsent]
*Sep  6 08:24:05.174: ppp11 LCP: I CONFNAK [ACKsent] id 1 len 8
*Sep  6 08:24:05.174: ppp11 LCP:    MRU 1500 (0x010405DC)
*Sep  6 08:24:05.174: ppp11 LCP: O CONFREQ [ACKsent] id 2 len 19
*Sep  6 08:24:05.174: ppp11 LCP:    MRU 1500 (0x010405DC)
*Sep  6 08:24:05.174: ppp11 LCP:    AuthProto CHAP (0x0305C22305)
*Sep  6 08:24:05.174: ppp11 LCP:    MagicNumber 0x2047CF94 (0x05062047CF94)
*Sep  6 08:24:05.174: ppp11 LCP: Event[Receive ConfNak/Rej] State[ACKsent to ACKsent]
*Sep  6 08:24:05.175: ppp11 LCP: I CONFACK [ACKsent] id 2 len 19
*Sep  6 08:24:05.175: ppp11 LCP:    MRU 1500 (0x010405DC)
*Sep  6 08:24:05.175: ppp11 LCP:    AuthProto CHAP (0x0305C22305)
*Sep  6 08:24:05.176: ppp11 LCP:    MagicNumber 0x2047CF94 (0x05062047CF94)
*Sep  6 08:24:05.176: ppp11 LCP: Event[Receive ConfAck] State[ACKsent to Open]
*Sep  6 08:24:05.188: ppp11 PPP: Phase is AUTHENTICATING, by this end
*Sep  6 08:24:05.188: ppp11 CHAP: O CHALLENGE id 1 len 25 from "CSR1"
*Sep  6 08:24:05.188: ppp11 LCP: State is Open
*Sep  6 08:24:05.190: ppp11 CHAP: I RESPONSE id 1 len 37 from "test2@lab.com.au"
*Sep  6 08:24:05.190: ppp11 PPP: Phase is FORWARDING, Attempting Forward
*Sep  6 08:24:05.191: ppp11 PPP: Phase is AUTHENTICATING, Unauthenticated User
*Sep  6 08:24:05.238: ppp11 PPP: Phase is FORWARDING, Attempting Forward
*Sep  6 08:24:05.274: Vi1.1 PPP: Phase is AUTHENTICATING, Authenticated User
*Sep  6 08:24:05.274: Vi1.1 CHAP: O SUCCESS id 1 len 4
*Sep  6 08:24:05.275: Vi1.1 PPP: No AAA accounting method list 
*Sep  6 08:24:05.275: Vi1.1 PPP: Phase is UP
*Sep  6 08:24:05.275: Vi1.1 IPCP: Protocol configured, start CP. state[Initial]
*Sep  6 08:24:05.275: Vi1.1 IPCP: Event[OPEN] State[Initial to Starting]
*Sep  6 08:24:05.275: Vi1.1 IPCP: O CONFREQ [Starting] id 1 len 10
*Sep  6 08:24:05.275: Vi1.1 IPCP:    Address 192.51.100.1 (0x0306C0336401)
*Sep  6 08:24:05.275: Vi1.1 IPCP: Event[UP] State[Starting to REQsent]
*Sep  6 08:24:05.278: Vi1.1 IPCP: I CONFREQ [REQsent] id 1 len 10
*Sep  6 08:24:05.279: Vi1.1 IPCP:    Address 0.0.0.0 (0x030600000000)
*Sep  6 08:24:05.279: Vi1.1 IPCP AUTHOR: Start.  Her address 0.0.0.0, we want 0.0.0.0
*Sep  6 08:24:05.279: Vi1.1 IPCP AUTHOR: Done.  Her address 0.0.0.0, we want 0.0.0.0
*Sep  6 08:24:05.279: Vi1.1 IPCP: Pool returned 172.20.1.3
*Sep  6 08:24:05.279: Vi1.1 IPCP: O CONFNAK [REQsent] id 1 len 10
*Sep  6 08:24:05.279: Vi1.1 IPCP:    Address 172.20.1.3 (0x0306AC140103)
*Sep  6 08:24:05.280: Vi1.1 IPCP: Event[Receive ConfReq-] State[REQsent to REQsent]
*Sep  6 08:24:05.280: Vi1.1 IPCP: I CONFACK [REQsent] id 1 len 10
*Sep  6 08:24:05.280: Vi1.1 IPCP:    Address 192.51.100.1 (0x0306C0336401)
*Sep  6 08:24:05.280: Vi1.1 IPCP: Event[Receive ConfAck] State[REQsent to ACKrcvd]
*Sep  6 08:24:05.280: Vi1.1 IPCP: I CONFREQ [ACKrcvd] id 2 len 10
*Sep  6 08:24:05.281: Vi1.1 IPCP:    Address 172.20.1.3 (0x0306AC140103)
*Sep  6 08:24:05.281: Vi1.1 IPCP: O CONFACK [ACKrcvd] id 2 len 10
*Sep  6 08:24:05.281: Vi1.1 IPCP:    Address 172.20.1.3 (0x0306AC140103)
*Sep  6 08:24:05.281: Vi1.1 IPCP: Event[Receive ConfReq+] State[ACKrcvd to Open]
*Sep  6 08:24:05.284: Vi1.1 IPCP: State is Open
*Sep  6 08:24:05.285: ppp_session_ntfy, topswidb Vi1.1, va Vi1.1, platform notify 0
*Sep  6 08:24:05.287: Vi1.1 Added to neighbor route AVL tree: topoid 796304859762330381, address 172.20.1.3
*Sep  6 08:24:05.287: Vi1.1 IPCP: Install route to 172.20.1.3
CSR1#

```


What happened on the wire?


CSR1(PPPoE server) is ```4a:c7:d4:1b:83:a1```

CUSTX-CPE1(client) is ```a2:df:d1:ed:e9:d5```


```
root@pve6-lab:~# tcpdump -nntei vmbr0v100 -v

4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0xa] LCP (0xc021), length 6: LCP, Term-Request (0x05), id 3, length 6
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 64: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE D, PPPoE PADT [ses 0xa]
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0xa] LCP (0xc021), length 6: LCP, Term-Ack (0x06), id 3, length 6
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 64: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE D, PPPoE PADT [ses 0xa]
a2:df:d1:ed:e9:d5 > ff:ff:ff:ff:ff:ff, ethertype 802.1Q (0x8100), length 64: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE D, PPPoE PADI [Service-Name] [Host-Uniq 0x92000001000025DC]
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 72: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE D, PPPoE PADO [Service-Name] [Host-Uniq 0x92000001000025DC] [AC-Name "CSR1"] [AC-Cookie 0xA56CF3AE9AB7314DCE14CEF7D27BF9D8]
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 72: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE D, PPPoE PADR [Host-Uniq 0x92000001000025DC] [AC-Name "CSR1"] [AC-Cookie 0xA56CF3AE9AB7314DCE14CEF7D27BF9D8] [Service-Name]
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 72: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE D, PPPoE PADS [ses 0xb] [Host-Uniq 0x92000001000025DC] [AC-Name "CSR1"] [AC-Cookie 0xA56CF3AE9AB7314DCE14CEF7D27BF9D8] [Service-Name]
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0xb] LCP (0xc021), length 12: LCP, Conf-Request (0x01), id 1, length 12
	encoded length 10 (=Option(s) length 6)
	  Magic-Num Option (0x05), length 6: 0x62807d41
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0xb] LCP (0xc021), length 21: LCP, Conf-Request (0x01), id 1, length 21
	encoded length 19 (=Option(s) length 15)
	  MRU Option (0x01), length 4: 1492
	  Auth-Prot Option (0x03), length 5: CHAP, MD5
	  Magic-Num Option (0x05), length 6: 0x2047cf94
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0xb] LCP (0xc021), length 12: LCP, Conf-Ack (0x02), id 1, length 12
	encoded length 10 (=Option(s) length 6)
	  Magic-Num Option (0x05), length 6: 0x62807d41
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0xb] LCP (0xc021), length 10: LCP, Conf-Nack (0x03), id 1, length 10
	encoded length 8 (=Option(s) length 4)
	  MRU Option (0x01), length 4: 1500
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0xb] LCP (0xc021), length 21: LCP, Conf-Request (0x01), id 2, length 21
	encoded length 19 (=Option(s) length 15)
	  MRU Option (0x01), length 4: 1500
	  Auth-Prot Option (0x03), length 5: CHAP, MD5
	  Magic-Num Option (0x05), length 6: 0x2047cf94
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0xb] LCP (0xc021), length 21: LCP, Conf-Ack (0x02), id 2, length 21
	encoded length 19 (=Option(s) length 15)
	  MRU Option (0x01), length 4: 1500
	  Auth-Prot Option (0x03), length 5: CHAP, MD5
	  Magic-Num Option (0x05), length 6: 0x2047cf94
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0xb] CHAP (0xc223), length 27: CHAP, Challenge (0x01), id 1, Value fdb6b04af98f4c63c5065539a37aea46, Name CSR1
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0xb] CHAP (0xc223), length 39: CHAP, Response (0x02), id 1, Value 50e6bef18d263c4bd89fa9ae3ffe008c, Name test2@lab.com.au
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0xb] CHAP (0xc223), length 6: CHAP, Success (0x03), id 1, Msg 
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0xb] IPCP (0x8021), length 12: IPCP, Conf-Request (0x01), id 1, length 12
	encoded length 10 (=Option(s) length 6)
	  IP-Addr Option (0x03), length 6: 192.51.100.1
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0xb] IPCP (0x8021), length 12: IPCP, Conf-Request (0x01), id 1, length 12
	encoded length 10 (=Option(s) length 6)
	  IP-Addr Option (0x03), length 6: 0.0.0.0
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0xb] IPCP (0x8021), length 12: IPCP, Conf-Ack (0x02), id 1, length 12
	encoded length 10 (=Option(s) length 6)
	  IP-Addr Option (0x03), length 6: 192.51.100.1
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0xb] IPCP (0x8021), length 12: IPCP, Conf-Nack (0x03), id 1, length 12
	encoded length 10 (=Option(s) length 6)
	  IP-Addr Option (0x03), length 6: 172.20.1.3
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0xb] IPCP (0x8021), length 12: IPCP, Conf-Request (0x01), id 2, length 12
	encoded length 10 (=Option(s) length 6)
	  IP-Addr Option (0x03), length 6: 172.20.1.3
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 68: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0xb] IPCP (0x8021), length 12: IPCP, Conf-Ack (0x02), id 2, length 12
	encoded length 10 (=Option(s) length 6)
	  IP-Addr Option (0x03), length 6: 172.20.1.3
a2:df:d1:ed:e9:d5 > ff:ff:ff:ff:ff:ff, ethertype 802.1Q (0x8100), length 64: vlan 100, p 0, ethertype 802.1Q, vlan 11, p 0, ethertype ARP, Ethernet (len 6), IPv4 (len 4), Reply 192.51.100.1 is-at a2:df:d1:ed:e9:d5, length 42
a2:df:d1:ed:e9:d5 > ff:ff:ff:ff:ff:ff, ethertype 802.1Q (0x8100), length 64: vlan 101, p 0, ethertype ARP, Ethernet (len 6), IPv4 (len 4), Reply 192.51.100.1 is-at a2:df:d1:ed:e9:d5, length 46
```
