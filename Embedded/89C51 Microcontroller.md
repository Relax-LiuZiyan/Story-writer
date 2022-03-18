---
title: 89C51 Microcontroller
renderNumberedHeading: true
grammar_cjkRuby: true
tags: 89c51
---
# 消除keil C51中未调用函数的警告
第一步：使用LX51
![enter description here](./images/1647606282778.png)

第二步：点击LX51 Misc,在下方Misc controls的内容框写上“REMOVEUNUSED”点击OK重新编译即可
![enter description here](./images/1647606289304.png)


# UART
## 串行口接口
![enter description here](./images/1647601210490.png)
SBUF：51单片机中的特殊寄存器，串行数据缓冲器（一个接收一个发送），两个其实是共用的一个地址99H，但是两个在物理上面是分开的。
当发送使用时，就采用SBUF=XXX; 
当接收使用时，采用XXX=SBUF；

T1溢出率：T1计时器的溢出频率（就是计时器每次低位计满向高位进位时间的倒数）
用处：用于计算波特率（每秒传输二进制代码的位数）

## 串行口的控制寄存器（SCON）
![enter description here](./images/1647605627093.png)

![enter description here](./images/1647601373652.png)

## SM0 and SM1 Operating mode selection bits
![Four ways to work with serial ports](./images/1647600561582.png)

## PCON
![PCON Register](./images/1647600787610.png)

SMOD（PCON.7）  波特率倍增位。在串行口方式1、方式2、方式3时，波特率与SMOD有关，当SMOD=1时，波特率提高一倍。复位时，SMOD=0。 

## Baud rate calculations
![enter description here](./images/1647601755031.png)

### 方式0的波特率配置

方式0的波特率计算公式如下：

![enter description here](./images/1647601664492.png)

其中B是波特率，fosc是晶振的频率。
### 方式2的波特率配置
方式2的波特率计算公式如下：

![enter description here](./images/1647601682272.png)
其中B是波特率，fosc是晶振的频率,SMOD是PCON寄存器最高位。

### 方式1和3的波特率配置
![enter description here](./images/1647601714445.png)
