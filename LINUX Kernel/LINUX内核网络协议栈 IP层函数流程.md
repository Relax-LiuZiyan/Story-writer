---
title: LINUX内核网络协议栈_IP层的函数流程
category: /LINUX内核/IP层函数/2023-12
grammar_cjkRuby: true
tags: 'LINUX内核协议栈,IP层,IP报文发送流程,IP报文接收流程'
---
# IP层报文发送流程
## 上层协议发送接口
### ip_append_data
1. ip_append_data() 的任务就是将多个片段合并成一个大的IP报文，该函数仅仅做合并工作，并不实际发送报文，如果要发送，调用者后面应该主动调用 `ip_push_pending_frames()`；
2. 这里要注意区分IP报文和 IP片段的不同，发送端可以组织一个很大的IP报文，但是在实际发送时，为了适配设备的MTU，需要将这一个IP报文拆封成多个IP片段，这些拆分的IP片段的ipid相同，片内偏移不同；
3. 该函数将参数指定的数据(from,length)按照MTU大小组织成一个个的方便后面IP协议处理的skb，并且将这些skb放入传输控制块的发送缓冲区中，这些skb在代码中被称之为 pending数据；
4. ip_append_data()在分配了skb后，会拷贝L4 payload部分，还会初始化skb中的某些字段，如上图缓冲区左侧的图示；其余的如L4 header由调用ip_push_pending_frames()的函数负责填充、IP header由ip_push_pending_frames()填充

具体关于此函数的功能可以查看：[linux内核协议栈 IPv4之发送接口: ip_append_data() Ⅰ 【UDP使用】](https://blog.csdn.net/wangquan1992/article/details/109203513?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522170221500616800213025890%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=170221500616800213025890&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-2-109203513-null-null.nonecase&utm_term=ip_append_data&spm=1018.2226.3001.4450)，这篇文章很好的解释了实现。注意一点，本系统的网卡是具备了`S/G IO`功能，因此主要采用文章中的前两种情况。


### ip_queue_xmit

### IP报头中的TOS字段

IP协议之TOS字段说明
https://www.cnblogs.com/lsgxeva/p/12384641.html

最小延迟，对应于对延迟敏感的应用，如telnet和人login等。
最大吞吐量，对应于对吞吐量要求比较高的应用，如FTP文件应用，对文件传输吞吐量有比较高的要求。
最高可靠性，对网络传输可靠性要求高的应用，如使用SNMP的应用、路由协议等等。
最小费用，如NNTP这种用户网络新闻等。


# 报文路由和邻居子系统发送报文

查找一个网络报文的路由信息的结构体可以使用函数如下：

``` c?linenums

//  其中sk->sk_bound_dev_if是一个整数值，它代表了内核中某个特定网络接口的索引，每个网络接口（如以太网卡、Wi-Fi 适配器等）都有一个唯一的索引号，用于在内核中唯一标识该接口。如果 sk_bound_dev_if 为 0，则表示套接字没有绑定到任何特定的网络接口，它会根据路由表和其他网络设置来选择合适的接口进行通信。可以通过`ip link`命令查看当前各个网络设备的索引号。


rt = z(net, fl4, sk, 
						daddr, inet->inet_saddr, 
						inet->inet_dport,
					   	inet->inet_sport,
					   	sk->sk_protocol,
					   	RT_CONN_FLAGS_TOS(sk, tos),
					   	sk->sk_bound_dev_if);

rt = ip_route_output_key(&init_net, &fl.u.ip4);

```

### `skb`结构体获取路由信息`rtable`

在`skb`结构体中有一个成员变量`_skb_refdst`，有函数为`static inline struct dst_entry *skb_dst(const struct sk_buff *skb)`，返回一个结构体`dst_entry `，此处可以直接强制转换为`(struct rtable *)skb_dst(skb)`，进行使用。

### 路由决策结构体`dst_entry`和`rtable`

由于本系统截获网络报文的HOOK点在`NF_IP_POST_ROUTING`，位于IP层路由决策之后，因此此处截获的报文应该经过了路由决策，本系统对截获的报文重新修改了网络报文的目的地址，与之前的目的地址不在一个子网，因此需要修改`skb`结构体中的`dst_entry`结构。因为此结构中，包含了此网络报文的路由信息、缓存路由决策等信息，如果不重新修改，会导致在后续的邻居子系统输出中出现异常问题。

与上面`dst_entry`结构体类似的有一个结构体为`rtable`，两个结构体都是用于处理网络报文中的路由决策的结构体，但是在不同层次上有一定的不同。

`dst_entry`是一个通用的目的路由结构，用于表示网络数据包的目的地路径信息。它是一个比较通用的结构，不仅用于 IP 路由，也用于其他类型的目的地路径决策，例如非 IP 协议的路径决策。包含了与路由决策相关的各种信息，如对下一跳的引用、路径度量、TTL（生存时间）处理等。在数据包传输过程中，`dst_entry` 用于存储和缓存特定数据包的路由信息，以提高路由查找的效率。

`rtable` 是`dst_entry`结构的一个特定实例，专门用于 IPv4 路由。它扩展了`dst_entry`结构，添加了特定于IPv4路由的字段和信息，如目的地 IP 地址、源 IP 地址、网关 IP 地址等。在处理 IPv4 数据包的路由决策时，`rtable`被用来存储具体的路由信息。

简而言之，`dst_entry`提供了一个通用的框架，用于存储和管理路由信息，而 `rtable` 是这个框架在 IPv4 上的一个具体实现。当处理 IPv4 网络数据包时，`rtable `用于存储该数据包的路由决策，而 `dst_entry` 则作为这些信息的容器，被包含在数据包的` struct sk_buff `中，以支持网络数据包的传输和处理。