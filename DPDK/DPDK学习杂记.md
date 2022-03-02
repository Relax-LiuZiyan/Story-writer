---
title: DPDK学习杂记
tags: CPU,ring
renderNumberedHeading: true
grammar_cjkRuby: true
---


# DPDK中CPU相关概念
##  lcore

指eal线程，采用pthread创建，并设置了CPU亲和性。我感觉dpdk把这个概念搞混淆了，有时指逻辑核，有时指线程。不过大部分是指线程。

## lcore_id 

逻辑核标识，可以通过top d1 或cat /proc/cpuinfo 看到的核，这种包括物理核和超线程的核，判断是否为超线程核，可以看/proc/cpuinfo文件中相应的核sibling是不是大于cpu cores，如果是则为超线程核，但超线程对于我们编程是透明的。在/proc/cpuinfo/中Physical id 和core id相同是超线程。

每个eal线程，会存储每线程变量RTE_PER_LCORE(_lcore_id) 存储逻辑核id. 设置亲和力，应该是lcore_id.

## core_id

该逻辑核在物理核中的标识，如果是超线程的话，core_id存在相同的情况。通过/sys/devices/system/cpu/cpuX/topology/core_id 可以看到，也可以通过/proc/cpuinfo中查到，前面一种便于编程。在线程的绑定CPU亲和性方面没有用。

## socket_id

在DPDK中不同于物理核的socket,应该是numa的标识。查看文件/sys/devices/system/nodeX/cpu.


# 网卡初始化失败 eth_i40e_dev_init(): Failed to init adminq: -66 