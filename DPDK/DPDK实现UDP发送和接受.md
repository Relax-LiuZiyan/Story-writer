---
title: dpdk网络协议栈之arp/icmp的实现
tags: DPDK,UDP,
renderNumberedHeading: true
grammar_cjkRuby: true
---
# DPDK接收与发送过程的实现


## UDP报文封装，先填数据部分再计算校验和

![UDP封装](./images/1645276225307.png)


如果先填写数据data，在计算校验和，对端可以成功接收到该报文。若99行注释，103行打开，则对端不能讲该报文上发到应用层，但是网卡可以抓到该报文。

![先填数据后计算校验和](./images/1645276385993.png)

![先计算校验和后填数据](./images/1645276363423.png)

通过报文抓到的两种不同的报文分析，数据的部分会影响到UDP的checksum的计算，因此需要先填写数据，再计算校验和，才是正确的。

# 接收
