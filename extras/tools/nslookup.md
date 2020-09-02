# nslookup

nslookup is a tool to query name servers.

```
nat@ubuntu18:~$ nslookup
> server 8.8.8.8
Default server: 8.8.8.8
Address: 8.8.8.8#53
> google.com.au
Server:     8.8.8.8
Address:    8.8.8.8#53
 
Non-authoritative answer:
Name:   google.com.au
Address: 142.250.66.195
Name:   google.com.au
Address: 2404:6800:4006:80f::2003
> 8.8.8.8
8.8.8.8.in-addr.arpa    name = dns.google.
 
Authoritative answers can be found from:
> server dns.google.
Default server: dns.google.
Address: 8.8.4.4#53
Default server: dns.google.
Address: 8.8.8.8#53
Default server: dns.google.
Address: 2001:4860:4860::8844#53
Default server: dns.google.
Address: 2001:4860:4860::8888#53
```
