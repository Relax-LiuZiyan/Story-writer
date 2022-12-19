---
title: PCL项目交换机选择
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
tags: '交换机,高吞吐,异构网络'
---
# 项目需求
预计项目搭建的环境如下图所示，每个客户端四个网口，初步规划分别使用WIFI的频段2.4GHz、5.2GHz、5.8GHz、6GHz(最新WIFI6E协议支持)，进行数据传输。预计要达到汇聚服务器网卡的满吞吐状态，即40Gbps（基本上不可能达到，传统业务传输会导致丢包的情况太多，先讨论最大值）。

![异构汇聚系统框架图](./images/1671432732557.png) 
## 模块化结构
由于系统绝大多数采用WIFI传输，一个报文也就是1500Byte，对应的报文数则是
## 


以RX89X路由器为例，工作在2.4GHz理论传输1.2Gbps，5.2GHz和5.8GHz理论传输为4.8Gbps，GT-AXE11000工作主频6GHz理论传输4.8GHz，则一个客户端总共理论传输为15.6Gbps。

本系统所采用网卡均为X710-T4（10G \* 4）。