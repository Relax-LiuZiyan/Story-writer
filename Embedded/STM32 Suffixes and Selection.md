---
title: STM32 Suffixes and Selection
renderNumberedHeading: true
grammar_cjkRuby: true
tags: 'stm32,Suffixes'
---

![enter description here](./images/1647493362361.png)

从图中可以看出：

"103"那位指的是产品子系列,101= 基本型,102 = USB基本型、USB 2.0全速设备，103 = 增强型，105或107 = 互联型

"C"那位指的是引脚数目，T=36脚，C=48脚，R=64脚，V=100脚，Z = 144脚

"8"那位指的是FLASH的大小，4=16K，6=32K(RAM=10K)，8=64K(RAM=20K)，B=128K(RAM=20K)，C=256K(RAM=48K)，D=384K(RAM=64K)，E=512K(RAM=64K)，F=768K(RAM=96K)，G=1024K(RAM=96K)

"T"那位指的是封装方式，H=BGA,T=LQFP,U=VFQFPN,Y=WLCSP64

"6"那位指的是温度范围，6=-40\~85摄氏度，7=-40\~105摄氏度

除了基本功能外，下面是主要区别：

STM32F103资源：一路CAN2.0B，最大1MBFlash、96KB SRAM，，72MHZ主频，SDIO

STM32F105/107：两路CAN2.0B，最大256KBFlash、64KB SRAM，，72MHZ主频，一路USB2.0 OTG

STM32F207/217与F205/215：两路CAN2.0B，，最大1MB Flash、128KB SRAM，120MHZ主频，两路USB2.0 OTG，SDIO

例如：芯片编号为STM32F103VET6，含义意法半导体公司的STM32系列中的，通用目的的，103子系列芯片，引脚数100，LQFP封装，512KB Flash，温度范围为-40~85℃。