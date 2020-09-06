# attributes



```
root@freeradius:/etc/freeradius# sqlite3 freeradius.db 
SQLite version 3.27.2 2019-02-25 16:06:06
Enter ".help" for usage hints.
sqlite> INSERT INTO radreply (username,attribute,op,value) VALUES ('test2@lab.com.au','Framed-Route','=','172.20.11.0/24 172.20.2.11 200');
sqlite> .exit
```

```
sqlite> .headers on
sqlite> select * from radreply;
id|username|attribute|op|value
1|test@lab.com.au|cisco-avpair|=|ip:sub-qos-policy-out=100MB
2|test@lab.com.au|cisco-avpair|=|ip:sub-qos-policy-out=100Mb
3|test2@lab.com.au|cisco-avpair|=|ip:sub-qos-policy-out=100Mb
4|test2@lab.com.au|Framed-IP-Address|=|172.20.2.11
5|test2@lab.com.au|Framed-Route|=|172.20.10.0/24
6|test2@lab.com.au|Framed-Route|=|172.20.11.0/24 172.20.2.12 200
7|test2@lab.com.au|Framed-Route|=|172.20.11.0/24 172.20.2.11 200
sqlite> DELETE FROM radreply WHERE id = 6;
```

###Cisco-AV pair

* Number: 26
* IETF Attribute: Vendor-Specific

In form

```
protocol : attribute sep value
```
Apply [ACLs to Dial Interfaces](https://www.cisco.com/c/en/us/support/docs/security-vpn/remote-authentication-dial-user-service-radius/15251-radius-ACL1.html)
```
cisco-avpair = "ip:route#1=9.9.9.0 255.255.255.0 11.11.11.12"
cisco-avpair = "ip:route#2=15.15.15.0 255.255.255.0 12.12.12.13"
cisco-avpair = "ip:inacl#1=permit icmp 1.1.1.0 0.0.0.255 9.9.9.0 0.0.0.255 log"
cisco-avpair = "ip:inacl#2=permit tcp 1.1.1.0 0.0.0.255 15.15.15 .0 0.0.0.255 log"
```

More examples. [Broadband Scalability and Performance](https://www.cisco.com/c/en/us/td/docs/routers/asr1000/configuration/guide/chassis/asrswcfg/scaling.html)

```
Cisco:Cisco-AVpair = “ip:vrf-id=vrf-name”
Cisco:Cisco-AVpair = “ip:ip-unnumbered=interface-name”
```



Example of syntax

```
root@freeradius:/etc/freeradius# sqlite3 freeradius.db 
SQLite version 3.27.2 2019-02-25 16:06:06
Enter ".help" for usage hints.
sqlite> INSERT INTO radreply (username,attribute,op,value) VALUES ('test2@lab.com.au','cisco-avpair','=','ip:vrf-id=AS64499');
sqlite> INSERT INTO radreply (username,attribute,op,value) VALUES ('test2@lab.com.au','cisco-avpair','=','ip:ip-unnumbered=Loopback1');
sqlite> .exit
```



