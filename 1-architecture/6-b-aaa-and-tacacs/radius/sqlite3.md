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


Headers on

```

```


Install REGEXP for sqlite3

```
root@freeradius:/etc/freeradius# apt search sqlite | grep -B1 reg

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

sqlite3-pcre/stable 0~git20070120091816+4229ecc-1 amd64
  Perl-compatible regular expression support for SQLite
root@freeradius:/etc/freeradius# apt install sqlite3-pcre

root@freeradius:/etc/freeradius# sqlite3 freeradius.db 
SQLite version 3.27.2 2019-02-25 16:06:06
Enter ".help" for usage hints.
sqlite> select * from radcheck where username regexp 'ppp';
Error: no such function: regexp

sqlite> .load /usr/lib/sqlite3/pcre.so

sqlite> select * from radcheck where username regexp 'ppp';
3|ppp1|Cleartext-Password|:=|ppp123
4|ppp2|Cleartext-Password|:=|ppp456

```



