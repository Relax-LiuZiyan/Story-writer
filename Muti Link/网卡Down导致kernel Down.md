---
title: 网卡Down导致kernel Down
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
tags: 'Linux内核,多链路,Kerenl Down'
---


# 前提背景
两个PC之间通过三条链路进行通信，使用的是蔡学长的Linux4.15版本，并且后期加了均匀收发包的算法。

# 问题
在业务流的时候，将其中一条链路Down掉，会导致对应链路的Client端Kernel Down，因此需要找到为何导致此问题。

# 解决

![NIC Down 任务通知链打印信息](./images/1652683702904.png)

当网卡关掉的时候，网卡任务通知链打印信息依次是任务9（NETDEV_GOING_DOWN）和2（NETDEV_DOWN）。 之前经过测试，如果在任务通知链2达到后卸掉任务包会导致空指针问题，因此在任务通知链2到来时就立刻关闭该网卡的链路任务，并且切掉对应链路的报文。