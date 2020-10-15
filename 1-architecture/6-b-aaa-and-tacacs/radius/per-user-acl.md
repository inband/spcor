# Per-user ACL

* Ingress to PE ```ip:inacl=```
* Egress to PE ```ip:outacl=```
* named ACLs failed, numbered ACLs work

Add to sqlite3 database

```
sqlite> INSERT INTO radreply (username,attribute,op,value) VALUES ('ppp2','cisco-avpair','=','ip:inacl=2666');
sqlite> .load /usr/lib/sqlite3/pcre.so
sqlite> select * from radreply where value regexp '2666';
id|username|attribute|op|value
30|ppp2|cisco-avpair|=|ip:inacl=2666
```

Configure on PE router

```
lab-mel-r2#show ip access-lists 2666
Extended IP access list 2666
    10 deny ip any host 100.66.6.248 (5 matches)
    20 permit ip any any (10 matches)
```


Clear session and debug

```
lab-mel-r2#debug radius authentication

lab-mel-r2#clear subscriber session uid 13         
lab-mel-r2#
lab-mel-r2#
*Oct 15 03:07:14.252: RADIUS: Removing all radius source-int. pointing to Virtual-Access1.1
lab-mel-r2#
lab-mel-r2#
lab-mel-r2#
*Oct 15 03:07:36.338: RADIUS/ENCODE(00000027):Orig. component type = PPPoE
*Oct 15 03:07:36.339: RADIUS: DSL line rate attributes successfully added
*Oct 15 03:07:36.339: RADIUS(00000027): Config NAS IP: 192.168.18.252
*Oct 15 03:07:36.339: RADIUS(00000027): Config NAS IPv6: ::
*Oct 15 03:07:36.339: RADIUS/ENCODE(00000027): acct_session_id: 4029
*Oct 15 03:07:36.339: RADIUS(00000027): sending
*Oct 15 03:07:36.340: RADIUS(00000027): Send Access-Request to 192.168.18.12:1812 id 1645/18, len 131
RADIUS:  authenticator D7 D2 1C 69 90 F2 06 7D - 01 7F C7 81 24 1A 9E C3
*Oct 15 03:07:36.340: RADIUS:  Framed-Protocol     [7]   6   PPP                       [1]
*Oct 15 03:07:36.341: RADIUS:  User-Name           [1]   6   "ppp2"
*Oct 15 03:07:36.341: RADIUS:  CHAP-Password       [3]   19  *
*Oct 15 03:07:36.341: RADIUS:  NAS-Port-Type       [61]  6   Virtual                   [5]
*Oct 15 03:07:36.342: RADIUS:  NAS-Port            [5]   6   0                         
*Oct 15 03:07:36.342: RADIUS:  NAS-Port-Id         [87]  9   "0/0/0/0"
*Oct 15 03:07:36.342: RADIUS:  Connect-Info        [77]  6   "TEST"
*Oct 15 03:07:36.342: RADIUS:  Vendor, Cisco       [26]  41  
*Oct 15 03:07:36.342: RADIUS:   Cisco AVpair       [1]   35  "client-mac-address=e237.b7e0.50af"
*Oct 15 03:07:36.343: RADIUS:  Service-Type        [6]   6   Framed                    [2]
*Oct 15 03:07:36.343: RADIUS:  NAS-IP-Address      [4]   6   192.168.18.252            
*Oct 15 03:07:36.343: RADIUS(00000027): Sending a IPv4 Radius Packet
*Oct 15 03:07:36.344: RADIUS(00000027): Started 5 sec timeout
*Oct 15 03:07:36.385: RADIUS: Received from id 1645/18 192.168.18.12:1812, Access-Accept, len 85
RADIUS:  authenticator 04 BB AC 52 E9 78 BF 17 - 46 C7 DC 58 19 FA B7 98
*Oct 15 03:07:36.385: RADIUS:  Framed-Protocol     [7]   6   PPP                       [1]
*Oct 15 03:07:36.385: RADIUS:  Framed-IP-Address   [8]   6   100.66.6.2                
*Oct 15 03:07:36.385: RADIUS:  Framed-Route        [22]  32  "192.168.66.0/24 100.66.6.2 210"
*Oct 15 03:07:36.386: RADIUS:  Vendor, Cisco       [26]  21  
*Oct 15 03:07:36.386: RADIUS:   Cisco AVpair       [1]   15  "ip:inacl=2666"
*Oct 15 03:07:36.386: RADIUS(00000027): Received from id 1645/18

```

pcap on freeradius

```
root@freeradius:~# tcpdump -nni any port 1812 -v
tcpdump: listening on any, link-type LINUX_SLL (Linux cooked), capture size 262144 bytes
03:07:35.706089 IP (tos 0x0, ttl 255, id 8917, offset 0, flags [none], proto UDP (17), length 159)
    192.168.18.252.1645 > 192.168.18.12.1812: RADIUS, length: 131
	Access-Request (1), id: 0x12, Authenticator: d7d21c6990f2067d017fc781241a9ec3
	  Framed-Protocol Attribute (7), length: 6, Value: PPP
	  User-Name Attribute (1), length: 6, Value: ppp2
	  CHAP-Password Attribute (3), length: 19, Value: 
	  NAS-Port-Type Attribute (61), length: 6, Value: Virtual
	  NAS-Port Attribute (5), length: 6, Value: 0
	  NAS-Port-Id Attribute (87), length: 9, Value: 0/0/0/0
	  Connect-Info Attribute (77), length: 6, Value: TEST
	  Vendor-Specific Attribute (26), length: 41, Value: Vendor: Cisco (9)
	    Vendor Attribute: 1, Length: 33, Value: client-mac-address=e237.b7e0.50af
	  Service-Type Attribute (6), length: 6, Value: Framed
	  NAS-IP-Address Attribute (4), length: 6, Value: 192.168.18.252
03:07:35.745905 IP (tos 0x0, ttl 64, id 58562, offset 0, flags [none], proto UDP (17), length 113)
    192.168.18.12.1812 > 192.168.18.252.1645: RADIUS, length: 85
	Access-Accept (2), id: 0x12, Authenticator: 04bbac52e978bf1746c7dc5819fab798
	  Framed-Protocol Attribute (7), length: 6, Value: PPP
	  Framed-IP-Address Attribute (8), length: 6, Value: 100.66.6.2
	  Framed-Route Attribute (22), length: 32, Value: 192.168.66.0/24 100.66.6.2 210
	  Vendor-Specific Attribute (26), length: 21, Value: Vendor: Cisco (9)
	    Vendor Attribute: 1, Length: 13, Value: ip:inacl=2666
```




Verify

```
lab-mel-r2#show subscriber session uid 14 detailed 
Type: PPPoE, UID: 14, State: authen, Identity: ppp2
IPv4 Address: 100.66.6.2 
Session Up-time: 06:59:49, Last Changed: 06:59:49
Interface: Virtual-Access1.1
Switch-ID: 4160

Policy information:
  Context 7F94A3482E48: Handle 2100000E
  AAA_id 00000027: Flow_handle 0
  Authentication status: authen

Classifiers:
Class-id    Dir   Packets    Bytes                  Pri.  Definition
0           In    5031       70428                  0    Match Any
1           Out   2514       35196                  0    Match Any

Features:

IP Config:
M=Mandatory, T=Tag, Mp=Mandatory pool
Flags  Peer IP Address                  Pool Name             Interface      
       100.66.6.2                       [None]                [None]         
       ::                               [None]                [None]         
          
Static Routes:
Class-id  Configuration Status           Source
0          This feature is enabled       Peruser

Per-User ACL:
Class-id   Dir  Protocol  ACL Name                            Source
0          In   IP        2666                                Peruser

Configuration Sources:
Type  Active Time  AAA Service ID  Name
USR   06:59:49     -               Peruser
INT   06:59:49     -               Virtual-Template1

```


The above is OK for users that share common list.  A specific user ACL can also be created:

```
sqlite> select * from radreply where value regexp 'inacl#';
id|username|attribute|op|value
26|ppp1|cisco-avpair|=|ip:inacl#1=deny ip any host 100.66.6.248
27|ppp1|cisco-avpair|=|ip:inacl#100=permit ip any any
```




