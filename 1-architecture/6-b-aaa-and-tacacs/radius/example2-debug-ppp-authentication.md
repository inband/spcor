# Example 2 - debug ppp authentication


Much less verbose than ```debug ppp negotiation```

```
CSR1#un all                                            
All possible debugging has been turned off
CSR1#debug ppp authentication                          
PPP authentication debugging is on
CSR1#clear subscriber session username test2@lab.com.au
CSR1#
CSR1#
CSR1#
*Sep  6 08:38:51.261: ppp13 PPP: Using vpn set call direction
*Sep  6 08:38:51.261: ppp13 PPP: Treating connection as a callin
*Sep  6 08:38:51.261: ppp13 PPP: Session handle[AA00000D] Session id[13]
*Sep  6 08:38:51.300: ppp13 CHAP: O CHALLENGE id 1 len 25 from "CSR1"
*Sep  6 08:38:51.301: ppp13 CHAP: I RESPONSE id 1 len 37 from "test2@lab.com.au"
*Sep  6 08:38:51.302: ppp13 PPP: Sent CHAP LOGIN Request
*Sep  6 08:38:51.339: ppp13 PPP: Received LOGIN Response PASS
*Sep  6 08:38:51.371: Vi1.1 CHAP: O SUCCESS id 1 len 4
CSR1#

```

debug ```aaa```

```
CSR1#debug aaa authentication 
AAA Authentication debugging is on
CSR1#debug aaa authorization 
AAA Authorization debugging is on
CSR1#clear subscriber session username test2@lab.com.au
CSR1#
*Sep  6 08:41:44.939: AAA/BIND(0000001C): Bind i/f Virtual-Template1 
*Sep  6 08:41:44.965: AAA/AUTHEN/PPP (0000001C): Pick method list 'default' 
*Sep  6 08:41:45.066: AAA/BIND(0000001C): Bind i/f Virtual-Access1.1 

```


debug ```radius```

```
CSR1#debug radius verbose 
Radius protocol debugging is on
Radius protocol brief debugging is off
Radius protocol verbose debugging is on
Radius packet hex dump debugging is off
Radius packet protocol debugging is off
Radius elog debugging debugging is off
Radius DTLS debugging debugging is off
Radius packet retransmission debugging is off
Radius table debugging is on
Radius server fail-over debugging is off
CSR1#
CSR1#clear subscriber session username test2@lab.com.au
CSR1#
*Sep  6 08:44:13.121: RADIUS: Removing all radius source-int. pointing to Virtual-Access1.1
CSR1#
CSR1#
CSR1#
CSR1#
*Sep  6 08:44:35.334: RADIUS/ENCODE(0000001E):Orig. component type = PPPoE
*Sep  6 08:44:35.334: RADIUS: DSL line rate attributes successfully added
*Sep  6 08:44:35.334: RADIUS(0000001E): Config NAS IP: 192.168.18.11
*Sep  6 08:44:35.334: RADIUS(0000001E): Config NAS IPv6: ::
*Sep  6 08:44:35.334: RADIUS(0000001E): Sending a IPv4 Radius Packet
*Sep  6 08:44:35.335: RADIUS(0000001E): Started 5 sec timeout
*Sep  6 08:44:35.399: RADIUS: Received from id 1645/9 192.168.18.12:1812, Access-Accept, len 55
CSR1#
```

