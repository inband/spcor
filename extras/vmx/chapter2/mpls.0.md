# mpls.0


```
root@VMX1:PE1> show route table mpls.0              

mpls.0: 13 destinations, 13 routes (13 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

0                  *[MPLS/0] 03:36:11, metric 1
                      Receive
1                  *[MPLS/0] 03:36:11, metric 1
                      Receive
2                  *[MPLS/0] 03:36:11, metric 1
                      Receive
13                 *[MPLS/0] 03:36:11, metric 1
                      Receive
299776             *[LDP/9] 03:36:07, metric 1
                    > to 10.0.0.3 via lt-0/0/0.12, Pop      
299776(S=0)        *[LDP/9] 03:36:07, metric 1
                    > to 10.0.0.3 via lt-0/0/0.12, Pop      
299792             *[LDP/9] 03:36:07, metric 1
                    > to 10.0.0.3 via lt-0/0/0.12, Swap 299776
299808             *[LDP/9] 03:36:07, metric 1
                    > to 10.0.0.3 via lt-0/0/0.12, Swap 299792
299824             *[LDP/9] 03:36:07, metric 1
                    > to 10.0.0.3 via lt-0/0/0.12, Swap 299808
299840             *[LDP/9] 03:36:07, metric 1
                    > to 10.0.0.3 via lt-0/0/0.12, Swap 299824
299856             *[LDP/9] 03:36:07, metric 1
                    > to 10.0.0.3 via lt-0/0/0.12, Swap 299840
299872             *[LDP/9] 03:36:07, metric 1
                    > to 10.0.0.3 via lt-0/0/0.12, Swap 299856
299888             *[LDP/9] 03:36:07, metric 1
                    > to 10.0.0.3 via lt-0/0/0.12, Swap 299872

```
