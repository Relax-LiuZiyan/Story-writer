---
title: Hyper-V & Ubuntu18.04
tags: 资源分配
renderNumberedHeading: true
grammar_cjkRuby: true
---

[toc]


# 前言
Win10家庭版中没有，需要单独安装。
Win10专业版：控制面板->程序->启用或关闭Windows功能->Hyper-V，选中后打勾重启即可。

# Hyper-V安装Ubuntu18.04
1. 下载镜像[http://releases.ubuntu.com/](http://releases.ubuntu.com/)
2. 在Hyper-V中新建虚拟机，虚拟机的代数选择第1代；连接虚拟硬盘中选择使用现有虚拟硬盘，位置选择为下载的镜像；其余选项默认选择即可。
## 处理器资源控制
假设：一个CPU，16核心，32线程，则总效率可以认为3200%。
1. 虚拟处理器的数量：使用的物理核个数。
2. 虚拟机保留（百分比）：使用虚拟处理器的资源率。例如：虚拟处理器的数量为16个，并且虚拟机保留（百分比）为100%，则相当于效率使用1600%，占总系统资源的百分比为1600% / 3200 % = 50%。
3. 虚拟机限制（百分比）：则该虚拟机最多可以使用CPU的处理器数据处理能力。例如：虚拟机限制为50%，则该虚拟机可以使用的硬件CPU最高资源是50%，虽然虚拟机提示CPU利用率接近100%。
4. 详细内容：[Hyper-V 3 限定虚拟机可用的CPU利用率](https://blog.51cto.com/wangshujiang/936269)
5. 实现所有CPU利用率达100%：for i in \`seq 1 $(cat /proc/cpuinfo |grep "physical id" |wc -l)\`; do dd if=/dev/zero of=/dev/null & done；批量删除任务：for i in \`seq 29814 29829\`;dokill -9 $i;done。
   - cat /proc/cpuinfo |grep "physical id" | wc -l 可以获得CPU的个数,　我们将其表示为N。
   - seq 1 N 用来生成１到Ｎ之间的数字
   - for i in \`seq 1 N\`; 就是循环执行命令,从１到Ｎ
   - dd if=/dev/zero of=/dev/null 执行dd命令,　输出到/dev/null, 实际上只占用CPU,　没有IO操作.
   - 由于连续执行Ｎ个(Ｎ是CPU个数)的dd 命令, 且使用率为100%,这时调度器会调度每个dd命令在不同的CPU上处理。