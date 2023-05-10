---
title: Ubuntu 18.04无法识别网卡
category: /小书匠/日记/2023-05
grammar_cjkRuby: true
tags: '网卡驱动,RTL8111/8168/8411,X570-A PRO'
---

# 问题
主板上的网卡信息无法被识别，通过`sudo lshw -class network`命令后，打印信息为`*-network UNCLAIMED`，这意味着该设备没有被操作系统识别或找不到合适的驱动程序。

由于此主板之前一直使用，重新安装**新的系统**后，才导致网卡口应该没问题的，因此可以认为是驱动的问题。

# 系统环境

 Ubuntu 18.04.6 LTS (GNU/Linux 4.15.0-208-generic x86_64)
 主板：X570-A PRO
 网卡：RTL8111/8168/8411
 
 # 解决办法
 