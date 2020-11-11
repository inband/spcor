# inet.3

```
root@VMX1:PE1> show route table inet.3  

inet.3: 10 destinations, 10 routes (10 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

10.0.0.4/31        *[LDP/9] 03:32:11, metric 30
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299792
10.0.0.10/31       *[LDP/9] 03:32:11, metric 30
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299792
10.0.0.22/31       *[LDP/9] 03:32:11, metric 30
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299808
172.16.0.1/32      *[LDP/9] 03:32:11, metric 10
                    > to 10.0.0.3 via lt-0/0/0.12
172.16.0.2/32      *[LDP/9] 03:32:11, metric 30
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299792
172.16.0.22/32     *[LDP/9] 03:32:11, metric 40
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299840
172.16.0.33/32     *[LDP/9] 03:32:11, metric 20
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299856
172.16.0.44/32     *[LDP/9] 03:32:11, metric 40
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299824
172.16.0.201/32    *[LDP/9] 03:32:11, metric 20
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299872
172.16.0.202/32    *[LDP/9] 03:32:11, metric 30
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299776
```
