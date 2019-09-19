### case1
#### 検証内容
vpsサーバー:133.18.200.115(以下、v133-18-200-115)からvpsサーバー:133.18.173.23(以下、v133-18-173-23)へ、
ローカルネットワーク:eth1(以下、eth1)から疎通確認を行う

#### 結果
v133-18-200-115からarpリクエストが送信されるが、v133-18-173-23にそのリクエストが届かなかった。


#### 疎通確認に用いるprocessの説明
process1-v133-18-200-115: v133-18-200-115でv133-18-173-23へeth1を使ってpingするプロセス
process2-v133-18-200-115: process1-v133-18-200-115を行っている間にv133-18-200-115でpingのpacketをtcpdumpするプロセス
process1-v133-18-173-23: v133-18-173-23でprocess1-v133-18-200-115を行っている間にpingのpacketをtcpdumpするプロセス


#### process1-v133-18-200-115
```
[root@v133-18-200-115 ~]# ping 172.16.0.12
PING 172.16.0.12 (172.16.0.12) 56(84) bytes of data.
From 172.16.0.15 icmp_seq=1 Destination Host Unreachable
From 172.16.0.15 icmp_seq=2 Destination Host Unreachable
From 172.16.0.15 icmp_seq=3 Destination Host Unreachable
From 172.16.0.15 icmp_seq=4 Destination Host Unreachable
From 172.16.0.15 icmp_seq=5 Destination Host Unreachable
From 172.16.0.15 icmp_seq=6 Destination Host Unreachable
From 172.16.0.15 icmp_seq=7 Destination Host Unreachable
From 172.16.0.15 icmp_seq=8 Destination Host Unreachable
From 172.16.0.15 icmp_seq=9 Destination Host Unreachable
From 172.16.0.15 icmp_seq=10 Destination Host Unreachable
^C
--- 172.16.0.12 ping statistics ---
10 packets transmitted, 0 received, +10 errors, 100% packet loss, time 9001ms
pipe 4
[root@v133-18-200-115 ~]
```

#### process2-v133-18-200-115
```
[root@v133-18-200-115 ~]# tcpdump -ieth1 icmp or arp
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth1, link-type EN10MB (Ethernet), capture size 262144 bytes
22:23:03.346706 ARP, Request who-has 172.16.0.12 tell v133-18-200-115.vir.kagoya.net, length 28
22:23:04.348707 ARP, Request who-has 172.16.0.12 tell v133-18-200-115.vir.kagoya.net, length 28
22:23:06.345938 ARP, Request who-has 172.16.0.12 tell v133-18-200-115.vir.kagoya.net, length 28
22:23:07.348710 ARP, Request who-has 172.16.0.12 tell v133-18-200-115.vir.kagoya.net, length 28
^C
4 packets captured
4 packets received by filter
0 packets dropped by kernel
```

#### process1-v133-18-173-23
```
[root@v133-18-173-23 ~]# tcpdump -ieth1 icmp or arp
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth1, link-type EN10MB (Ethernet), capture size 262144 bytes
^C
0 packets captured
0 packets received by filter
0 packets dropped by kernel
[root@v133-18-173-23 ~]# 
```
