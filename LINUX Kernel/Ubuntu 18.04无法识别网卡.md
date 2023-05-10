---
title: Ubuntu 18.04无法识别网卡
category: /小书匠/日记/2023-05
grammar_cjkRuby: true
tags: '网卡驱动,RTL8111/8168/8411,X570-A PRO'
---

# 问题
主板上的网卡信息无法被识别，通过`sudo lshw -class network`命令后，打印信息为`*-network UNCLAIMED`，通过`ifconfig`命令，没有识别到对应的网卡信息。这意味着该设备没有被操作系统识别或找不到合适的驱动程序。

由于此主板之前一直使用，重新安装**新的系统**后，才导致网卡口应该没问题的，因此可以认为是驱动的问题。
# 系统环境
 Ubuntu 18.04.6 LTS (GNU/Linux 4.15.0-208-generic x86_64)
 主板：X570-A PRO
 网卡：RTL8111/8168/8411
  # 解决办法
 首先下载网卡驱动信息
 https://www.realtek.com/en/component/zoo/category/network-interface-controllers-10-100-1000m-gigabit-ethernet-pci-express-software
 ![选择网卡驱动](./images/1683722783662.png)
 
卸载之前的驱动信息，避免相同驱动冲突问题，通过lsmod查看后，进行卸载`sudo rmmod r8169`。

解压下载的文件`tar -jxf r8168-8.051.02.tar.bz2`，解压后，进入到`src`文件夹后，运行`sudo make all`命令。由于本系统是新系统，因此提前需要安装`sudo apt-get install make`和`sudo apt-get install gcc`。

安装驱动：可能没有执行脚本权限，先赋权限`chmod +x autorun.sh`，执行`sudo ./autorun.sh`完毕后重启。

此时再通过`sudo lshw -class network`命令后，显示安装成功。

![识别网卡成功](./images/1683723171875.png)
由于已经通过make编译生成了驱动文件，因此可以在驱动文件下生产了网卡驱动，可以删除解压的文件夹即可。


![生产的网卡驱动信息](./images/1683723427543.png)