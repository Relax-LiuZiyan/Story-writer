---
title: PCL项目C端代码修改
tags: 多链路,Linux内核,网络通信
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


[toc]
# alloc_icmp_package函数修改

1. 删除没用的注释；
2. alloc_skb分配SKB数据大小改为只有data_len，不应该包含本身结构体的大小；
3. data_len大小改为2 + SIZE_MAC_HDR + SIZE_IP_HDR + SIZE_ICMP_HDR + sizeof(struct chkhdr);，去掉SIZE_FCS_HDR，在构造报文时，不需要包含此大小；
4. SKB中的data指针地址，要保证最后指在网络层头地址。

## FCS Status : Bad

![构造的ICMP报文提示错误](./images/1654604153953.png)
这是由于之前构造代码时，添加了SIZE_FCS_HDR，导致wireshark误认为最后四个字节为FCS校验码，因此报错。去除生成的报文中的SIZE_FCS_HDR，即可生成正常的ICMP报文。

![无FCS校验错误信息](./images/1654604301038.png)
## bogus IPv4 version
在修改此函数时，误删除了一行代码，`skb_push(skb, SIZE_IP_HDR);`，在得到传输层的指针后，需要再添加上述代码，使`skb->data`指针再次移到网络层头所在的地址，具体原因未知。

![bogus IPv4 version](./images/1654604415789.png)
修改了函数参数调用，之前函数参数调用为第一行，修改了link_info结构体，添加了icmp_seq变量做为ICMP中Seq参数传入。

``` c?linenums
skb = alloc_icmp_package(ch.code, ch.type, nic_list[k].ip, link_list[k].ip, &ch);
skb = alloc_icmp_package(ch.type, link_list[k].icmp_seq++, nic_list[k].ip, link_list[k].ip, &ch);
```