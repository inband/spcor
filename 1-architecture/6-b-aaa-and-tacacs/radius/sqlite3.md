# sqlite3

```
root@freeradius:/etc/freeradius# sqlite3 freeradius.db
```


Create user

```
sqlite> INSERT INTO radcheck (username,attribute,op,value) VALUES ('ppp1','Cleartext-Password',':=','ppp123');
sqlite> INSERT INTO radcheck (username,attribute,op,value) VALUES ('ppp2','Cleartext-Password',':=','ppp456');
```

Static IP

```
sqlite> INSERT INTO radreply (username,attribute,op,value) VALUES ('ppp1','Framed-IP-Address','=','100.66.6.1');
sqlite> INSERT INTO radreply (username,attribute,op,value) VALUES ('ppp2','Framed-IP-Address','=','100.66.6.2');
```



Framed route

```
sqlite> INSERT INTO radreply (username,attribute,op,value) VALUES ('ppp1','Framed-Route','=','192.168.66.0/24 100.66.6.1');
sqlite> INSERT INTO radreply (username,attribute,op,value) VALUES ('ppp2','Framed-Route','=','192.168.66.0/24 100.66.6.2 210');
```

Display entries

```
sqlite> select * from radcheck;
sqlite> select * from radreply;
```


Delete entries

```
sqlite> DELETE FROM radreply WHERE id = 13;
```


Quit
```
sqlite> .quit

```
