# freeradius & sqlite

radius (remote authentication dial in user service) is defined in [rfc2865](https://tools.ietf.org/html/rfc2865). It describes an application that is used to carry authentication, authorisation and configuration information to and from a NAS (Network Access Server). In this lab I’m using Cisco ASR as the NAS, freeradius as the radius server and sqlite as the database.

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
