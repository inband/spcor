# external BGP (eBGP)

Border Gateway Protocol (BGP) is the routing protocol of the Internet.

[rfc4271](https://tools.ietf.org/html/rfc4271)

eBGP is used to exchange routing information between different Autonomous Systems (ASs) whereas iBGP can be used as a routing protocol within an AS.

BGP leverages TCP for exchanging Network Layer Reachability Information(NLRI) between peers. 

A BGP peer can apply policy to NLRI to build RIB. 

The peer may then share NLRI within its own AS (iBGP) and/or exchange NLRI with other BGP's peers (eBGP) performing a tranist AS role.

An AS may chose not to exchange NLRI in a transit role and is described as a stub AS.


