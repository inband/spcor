 # LDP neighbors

In IOS there is a requirement that the ```mpls ldp router-id``` IP **must** be present in the neighbor table for an LDP transport session to form.

This is normally achieved with an IGP - such as OSPF or IS-IS.
