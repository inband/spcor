# Example 2 - debug radius authentication


```
CSR1#debug radius authentication 
Radius protocol debugging is on
Radius protocol brief debugging is off
Radius protocol verbose debugging is off
Radius packet hex dump debugging is off
Radius packet protocol (authentication) debugging is on
Radius packet protocol (accounting) debugging is off
Radius elog debugging debugging is off
Radius DTLS debugging debugging is off
Radius packet retransmission debugging is off
Radius table debugging is on
Radius server fail-over debugging is off
CSR1#
CSR1#clear subscriber session username test2@lab.com.au
CSR1#
CSR1#
CSR1#
*Sep  6 08:46:01.316: RADIUS: Removing all radius source-int. pointing to Virtual-Access1.1
CSR1#
CSR1#
CSR1#
*Sep  6 08:46:23.462: RADIUS/ENCODE(0000001F):Orig. component type = PPPoE
*Sep  6 08:46:23.462: RADIUS: DSL line rate attributes successfully added
*Sep  6 08:46:23.462: RADIUS(0000001F): Config NAS IP: 192.168.18.11
*Sep  6 08:46:23.463: RADIUS(0000001F): Config NAS IPv6: ::
*Sep  6 08:46:23.463: RADIUS/ENCODE(0000001F): acct_session_id: 4021
*Sep  6 08:46:23.463: RADIUS(0000001F): sending
*Sep  6 08:46:23.463: RADIUS(0000001F): Send Access-Request to 192.168.18.12:1812 id 1645/10, len 150
RADIUS:  authenticator 1B A3 BD 59 E1 78 43 6F - C5 06 55 39 5D 98 E8 59
*Sep  6 08:46:23.464: RADIUS:  Framed-Protocol     [7]   6   PPP                       [1]
*Sep  6 08:46:23.464: RADIUS:  User-Name           [1]   18  "test2@lab.com.au"
*Sep  6 08:46:23.465: RADIUS:  CHAP-Password       [3]   19  *
*Sep  6 08:46:23.465: RADIUS:  NAS-Port-Type       [61]  6   Virtual                   [5]
*Sep  6 08:46:23.465: RADIUS:  NAS-Port            [5]   6   0                         
*Sep  6 08:46:23.465: RADIUS:  NAS-Port-Id         [87]  13  "0/0/0/100.1"
*Sep  6 08:46:23.466: RADIUS:  Connect-Info        [77]  9   "AS64499"
*Sep  6 08:46:23.466: RADIUS:  Vendor, Cisco       [26]  41  
*Sep  6 08:46:23.466: RADIUS:   Cisco AVpair       [1]   35  "client-mac-address=a2df.d1ed.e9d5"
*Sep  6 08:46:23.466: RADIUS:  Service-Type        [6]   6   Framed                    [2]
*Sep  6 08:46:23.467: RADIUS:  NAS-IP-Address      [4]   6   192.168.18.11             
*Sep  6 08:46:23.467: RADIUS(0000001F): Sending a IPv4 Radius Packet
*Sep  6 08:46:23.467: RADIUS(0000001F): Started 5 sec timeout
*Sep  6 08:46:23.504: RADIUS: Received from id 1645/10 192.168.18.12:1812, Access-Accept, len 55
RADIUS:  authenticator 39 A4 A7 CE FC 8A 69 3A - 12 8D C0 EB 83 91 62 3A
*Sep  6 08:46:23.504: RADIUS:  Vendor, Cisco       [26]  35  
*Sep  6 08:46:23.504: RADIUS:   Cisco AVpair       [1]   29  "ip:sub-qos-policy-out=100Mb"
*Sep  6 08:46:23.504: RADIUS(0000001F): Received from id 1645/10
CSR1#
CSR1#

```
