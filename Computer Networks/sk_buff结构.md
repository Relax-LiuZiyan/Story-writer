---
title: sk_buff结构
tags: Linux内核,网络通信
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

# 管理函数
## skb_put
![skb_put函数](./images/1654591202583.png)
## skb_push
![skb_push](./images/1654591250135.png)
## skb_pull
![skb_pull](./images/1654591305409.png)

## skb_reserve
![skb_reserve](./images/1654591326009.png)

# SK_Buff data和tail指针问题
网络报文真正发送的数据报文时data指针和tail指针中间的部分，即使head和data中间有空间，发送也不会去考虑

# SK_Buff中的len成员变量

``` c?linenums
skb->len = skb->data - skb->tail;
```

``` c?linenums
// data_len = 2 + 14 +20 + 8 + 72;
data_len =  2 + SIZE_MAC_HDR + SIZE_IP_HDR + SIZE_ICMP_HDR + sizeof(struct chkhdr);
// skb->len = 20+8+72 = 100
// 由于测试代码将data指针移到了网络层，因此不加上2和14字节。
```

![skb->len打印调试信息](./images/1655263579726.png)