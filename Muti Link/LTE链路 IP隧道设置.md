---
title: LTE链路 IP隧道设置
tags: IP隧道,跨网段,Linux内核,路由表
slug: storywriter/grammar
grammar_abbr: true
grammar_table: true
grammar_defList: true
grammar_emoji: true
grammar_footnote: true
grammar_ins: true
grammar_mark: true
grammar_sub: true
grammar_sup: true
grammar_checkbox: true
grammar_mathjax: true
grammar_flow: true
grammar_sequence: true
grammar_code: true
grammar_highlight: true
grammar_html: true
grammar_linkify: true
grammar_typographer: true
grammar_video: true
grammar_audio: true
grammar_attachment: true
grammar_mermaid: true
grammar_classy: true
grammar_decorate: false
grammar_attrs: false
grammar_cjkEmphasis: true
grammar_cjkRuby: true
grammar_center: true
grammar_align: true
grammar_tableExtra: true
---
# 路由表信息
C端路由表和IP地址信息如下所示：
``` c?linenums
f410-client@f410client-MS-7C37:~/lzy/408_client_lte$ route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         _gateway        0.0.0.0         UG    20100  0        0 enp45s0f0
default         _gateway        0.0.0.0         UG    20104  0        0 enp39s0
link-local      0.0.0.0         255.255.0.0     U     1000   0        0 enp45s0f0
172.16.1.0      0.0.0.0         255.255.255.0   U     101    0        0 enp45s0f1
172.16.2.0      0.0.0.0         255.255.255.0   U     102    0        0 enp45s0f2
172.16.3.0      0.0.0.0         255.255.255.0   U     103    0        0 enp45s0f3
192.168.0.0     0.0.0.0         255.255.255.0   U     104    0        0 enp39s0
192.168.1.0     0.0.0.0         255.255.255.0   U     100    0        0 enp45s0f0
f410-client@f410client-MS-7C37:~/lzy/408_client_lte$ sudo route del -net default netmask 0.0.0.0 dev enp45s0f0
f410-client@f410client-MS-7C37:~/lzy/408_client_lte$ sudo route add -net 192.168.2.0 netmask 255.255.255.0 dev enp45s0f0 gw 192.168.1.1
f410-client@f410client-MS-7C37:~/lzy/408_client_lte$ ping 192.168.2.2
PING 192.168.2.2 (192.168.2.2) 56(84) bytes of data.
64 bytes from 192.168.2.2: icmp_seq=1 ttl=63 time=2.14 ms
^C
--- 192.168.2.2 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 2.143/2.143/2.143/0.000 ms
f410-client@f410client-MS-7C37:~/lzy/408_client_lte$ route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         _gateway        0.0.0.0         UG    20104  0        0 enp39s0
link-local      0.0.0.0         255.255.0.0     U     1000   0        0 enp45s0f0
172.16.1.0      0.0.0.0         255.255.255.0   U     101    0        0 enp45s0f1
172.16.2.0      0.0.0.0         255.255.255.0   U     102    0        0 enp45s0f2
172.16.3.0      0.0.0.0         255.255.255.0   U     103    0        0 enp45s0f3
192.168.0.0     0.0.0.0         255.255.255.0   U     104    0        0 enp39s0
192.168.1.0     0.0.0.0         255.255.255.0   U     100    0        0 enp45s0f0
192.168.2.0     192.168.1.1     255.255.255.0   UG    0      0        0 enp45s0f0
f410-client@f410client-MS-7C37:~/lzy/408_client_lte$ ifconfig enp45s0f0
enp45s0f0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.2  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 fe80::b083:44b4:4513:9631  prefixlen 64  scopeid 0x20<link>
        ether 9c:69:b4:61:69:08  txqueuelen 1000  (Ethernet)
        RX packets 27  bytes 1798 (1.7 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 409  bytes 39072 (39.0 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

S端路由表和IP地址信息如下所示：

``` c?linenums
f410-server@:408_server_lte$ route
内核 IP 路由表
目标            网关            子网掩码        标志  跃点   引用  使用 接口
default         _gateway        0.0.0.0         UG    100    0        0 enp45s0f0
default         _gateway        0.0.0.0         UG    104    0        0 enp39s0
link-local      0.0.0.0         255.255.0.0     U     1000   0        0 enp45s0f0
172.16.1.0      0.0.0.0         255.255.255.0   U     105    0        0 enp45s0f1
172.16.2.0      0.0.0.0         255.255.255.0   U     106    0        0 enp45s0f2
172.16.3.0      0.0.0.0         255.255.255.0   U     107    0        0 enp45s0f3
192.168.0.0     0.0.0.0         255.255.255.0   U     104    0        0 enp39s0
192.168.1.0     0.0.0.0         255.255.255.0   U     100    0        0 enp45s0f0
192.168.2.0     0.0.0.0         255.255.255.0   U     100    0        0 enp45s0f0
f410-server@:408_server_lte$ ifconfig enp45s0f0
enp45s0f0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.2.2  netmask 255.255.255.0  broadcast 192.168.2.255
        inet6 fe80::8e2f:f9c4:eaf8:244b  prefixlen 64  scopeid 0x20<link>
        ether 9c:69:b4:62:4c:54  txqueuelen 1000  (以太网)
        RX packets 954  bytes 62143 (62.1 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1538  bytes 130549 (130.5 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

路由表修改命令常用：

``` c?linenums
sudo route del -net default netmask 0.0.0.0 dev enp45s0f0
sudo route add -net 192.168.2.0 netmask 255.255.255.0 dev enp45s0f0 gw 192.168.1.1
```

iperf3打流测试命令：

``` c?linenums

iperf3 -u -c 10.0.0.1 -p10000 -l1400 -t100 -b100M -A0

iperf3 -s -p10000 -A0

```

# 网络环境
网络拓扑图如下所示，注意S端的IP地址需要填写一个192.168.1.X的地址，

![Network topology diagram](./images/1657608374281.png)
