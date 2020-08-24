# example 6

Qinq

In this example SP will use Qinq.

The following assuptions will be made.

The SP PE will terminate QinQ.  As there is no switch I'll assume QinQ has been setup in the following way:

* SP has a customer facing switch that uses dot1qtunnel to encapsulate all traffic into a single vlan
* that single vlan is trunked from SP switch to PE
* both PEs and switches have same design in AS64499


However, although PE will have correct config.  CPE will not - I'll just pop traffic in QinQ on CUSTX-CPE


So for this example CUST1

* ```CSR1``` S-tag 100  C-tag 11
* ```CSR4``` S-tag 101  C-tag 11

* ```CUSTX-CPE1``` S-tag 100  C-tag 11
* ```CUSTX-CPE2``` S-tag 101  C-tag 11







