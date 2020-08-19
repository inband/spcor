# Operation of OSPF

1. Router's are configured to use OSFP protocol.  At minimum 2 routers are required and they must share a common link.  
2. Ospf is enabled on each router's interfaces (that share link).  OSPF "hello" messages are sent via multicast IP.
3. Neighbor adjacency will be established if parameters are agreed.
4. Routers will flood Link State Advertisements (LSAs) to established neighbor adjacencies
5. Routers store received LSAs in local link-state database (LSDB) and flood information to adjacencies.
6. Once at steady-state, Shortest Path First (SPF) algorithm is used to build RIB table(routing table).





Link-state Advertisements (LSAs)
