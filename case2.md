### case2
#### 検証内容
- vpsサーバー:133.18.200.115(以下、v133-18-200-115)からvpsサーバー:133.18.173.23(以下、v133-18-173-23)へ、ローカルネットワーク:eth1(以下、eth1)から疎通確認を行う。
- 事前にv133-18-200-115のarpテーブルへ静的にv133-18-173-23のeth1のmacアドレスを追加しておく

#### 結果
v133-18-200-115からpingリクエストが送信されるが、v133-18-173-23にそのリクエストが届かなかった。


#### 疎通確認に用いるprocessの説明
- process0-v133-18-200-115: 静的にarpテーブルへmacアドレスを追加するプロセス
- process1-v133-18-200-115: v133-18-200-115でv133-18-173-23へeth1を使ってpingするプロセス
- process2-v133-18-200-115: process1-v133-18-200-115を行っている間にv133-18-200-115でpingのpacketをtcpdumpするプロセス
- process1-v133-18-173-23: process1-v133-18-200-115を行っている間にv133-18-173-23でpingのpacketをtcpdumpするプロセス

#### process0-v133-18-200-115
```
[root@v133-18-200-115 ~]# ip neigh del 172.16.0.12 dev eth1
[root@v133-18-200-115 ~]# ip neigh add 172.16.0.12 lladdr fa:16:3e:a2:1c:70 dev eth1
[root@v133-18-200-115 ~]
```
#### process1-v133-18-200-115
```
[root@v133-18-200-115 ~]# ping 172.16.0.12
PING 172.16.0.12 (172.16.0.12) 56(84) bytes of data.
^C
--- 172.16.0.12 ping statistics ---
120 packets transmitted, 0 received, 100% packet loss, time 119007ms

[root@v133-18-200-115 ~]# 
```

#### process2-v133-18-200-115
```
[root@v133-18-200-115 ~]# tcpdump -ieth1 icmp or arp -n
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth1, link-type EN10MB (Ethernet), capture size 262144 bytes
22:38:36.161810 IP 172.16.0.15 > 172.16.0.12: ICMP echo request, id 14133, seq 50, length 64
22:38:37.161773 IP 172.16.0.15 > 172.16.0.12: ICMP echo request, id 14133, seq 51, length 64
22:38:38.161817 IP 172.16.0.15 > 172.16.0.12: ICMP echo request, id 14133, seq 52, length 64
22:38:39.161764 IP 172.16.0.15 > 172.16.0.12: ICMP echo request, id 14133, seq 53, length 64
^C
4 packets captured
4 packets received by filter
0 packets dropped by kernel
```

#### process1-v133-18-173-23
```
[root@v133-18-173-23 ~]# tcpdump -ieth1 icmp or arp -n
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth1, link-type EN10MB (Ethernet), capture size 262144 bytes
^C
0 packets captured
0 packets received by filter
0 packets dropped by kernel
[root@v133-18-173-23 ~]# 
```
