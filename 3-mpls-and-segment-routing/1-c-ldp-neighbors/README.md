 # LDP neighbors

In IOS there is a requirement that the ```mpls ldp router-id``` IP **must** be present in the neighbor table for an LDP transport session to form.

This is normally achieved with an IGP - such as OSPF or IS-IS.


Hellos are UDP/646 datagrams sent via ```224.0.0.2``` on link(segment)

```224.0.0.2``` is All-routers multicast



Hellos are used to from LDP transport (TCP) sessions.  Label Distribution is achieved over LDP transport session - TCP/646.
