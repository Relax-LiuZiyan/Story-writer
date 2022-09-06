---
title: iperf / iperf3 打流问题
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
tags: 'iperf,多链路'
---

# 打流命令
## iperf

``` bash?linenums
iperf -u -c172.16.1.1 -p10000 -l1400 -t100 -b1000M -i1 
iperf -u -s -p10000 -i1
```
## ieprf3

``` bash?linenums
iperf3 -u -c 10.0.0.1 -p10007 -l8756 -t100 -b15G -A10
iperf3 -s -p10000 -A2
```

环境：1Gpbs NIC  网线直连

在测试单链路打流过程中，添加了缓冲发送和接收队列，只在发送端post_routing钩子和接收端pre_routing钩子将报文放到报文链表中，然后利用定时器定时1ms,取500或当前报文队列最小值做为报文发送数量。

即 1400 * 8 * 1000 * 500 / 1024 /1024 / 1024 = 5.2Gpbs, 完全可以达到单链路无线传输的最大速率。

S端打流命令：

``` bash?linenums
iperf3 -u -c 10.0.0.1 -p10000 -l1400 -t100 -b1        G -A0
```

