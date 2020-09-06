# freeradius & sqlite

radius (remote authentication dial in user service) is defined in [rfc2865](https://tools.ietf.org/html/rfc2865). It describes an application that is used to carry authentication, authorisation and configuration information to and from a NAS (Network Access Server). In this lab I’m using Cisco CSR as the NAS, freeradius as the radius server and sqlite as the database.

Cisco implements aaa (authentication, authorisation and accounting) which looks after the following.

* who are you (authentication)
* what are you allow to do (authorisation)
* what are you doing (accounting)

aaa can be configured to use radius, tacacs or a local implementation. The advantage of radius and tacacs is that the aaa information can be centralised. Furthermore, applications like freeradius can centralise information and scale out leveraging an advanced database. However, in this lab I’ll use the light-weight standalone database sqlite.


```
# Debian 10 Buster
root@freeradius:~# apt update
root@freeradius:~# apt install freeradius -y
 
root@freeradius:/etc/freeradius# systemctl status freeradius
● freeradius.service - FreeRADIUS multi-protocol policy server
   Loaded: loaded (/lib/systemd/system/freeradius.service; disabled; vendor preset: enabled)
   Active: active (running) since Fri 2020-07-24 05:55:07 UTC; 49min ago
     Docs: man:radiusd(8)
           man:radiusd.conf(5)
           http://wiki.freeradius.org/
           http://networkradius.com/doc/
  Process: 25691 ExecStartPre=/usr/sbin/freeradius $FREERADIUS_OPTIONS -Cxm -lstdout (code=exited, status=0/SUCCESS)
  Process: 25693 ExecStart=/usr/sbin/freeradius $FREERADIUS_OPTIONS (code=exited, status=0/SUCCESS)
 Main PID: 25695 (freeradius)
    Tasks: 6 (limit: 4915)
   Memory: 11.0M
   CGroup: /system.slice/freeradius.service
           └─25695 /usr/sbin/freeradius

```



```
apt update
apt install freeradius -y
```

```
vim /etc/freeradius/3.0/mods-available/sql
```

Change ```driver```

```
sql {
        # The sub-module to use to execute queries. This should match
        # the database you're attempting to connect to.
        #
        #    * rlm_sql_mysql
        #    * rlm_sql_mssql
        #    * rlm_sql_oracle
        #    * rlm_sql_postgresql
        #    * rlm_sql_sqlite
        #    * rlm_sql_null (log queries to disk)
        #
        #driver = "rlm_sql_null"
        driver =  "rlm_sql_sqlite"

#
#       Several drivers accept specific options, to set them, a
#       config section with the the name as the driver should be added
#       to the sql instance.
#
#       Driver specific options are:
#
        sqlite {
                # Path to the sqlite database
                filename = "/etc/freeradius/freeradius.db"

                # How long to wait for write locks on the database to be
                # released (in ms) before giving up.
                busy_timeout = 200

                # If the file above does not exist and bootstrap is set
                # a new database file will be created, and the SQL statements
                # contained within the bootstrap file will be executed.
                bootstrap = "${modconfdir}/${..:name}/main/sqlite/schema.sql"
        }
```

Add to ```mods-enabled``` and import ```schema```

```
cd /etc/freeradius/3.0/
cd mods-enabled/
ln -s ../mods-available/sql

cd /etcfreeradius/
sqlite3 freeradius.db < 3.0/mods-config/sql/main/sqlite/schema.sql 
chown freeradius:freeradius freeradius.db
chmod 664 freeradius.db 
sqlite3 freeradius.db 

systemctl restart freeradius
systemctl status freeradius
man 8 radiusd

```


Add a user and an attribute

```
root@freeradius:/etc/freeradius# sqlite3 freeradius.db 
SQLite version 3.27.2 2019-02-25 16:06:06
Enter ".help" for usage hints.
sqlite> INSERT INTO radcheck (username,attribute,op,value) VALUES ('test2@lab.com.au','Cleartext-Password',':=','test456');
sqlite> INSERT INTO radreply (username,attribute,op,value) VALUES ('test2@lab.com.au','cisco-avpair','=','ip:sub-qos-policy-out=100Mb');

```
