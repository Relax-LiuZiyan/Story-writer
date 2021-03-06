---
title: Hugepages cannot be modified
tags: Hugepages ,dpdk,vpp
renderNumberedHeading: true
grammar_cjkRuby: true
---

[toc]

# 硬件环境
- Hyper-V虚拟机
- 系统：Ubuntu 18.04
- 处理器配置：
    - 处理器：16
    - NUMA节点：1
    - 插槽：1
    - 每个核心的硬件线程数：2
- 内存：16G & 未开启动态内存

# 问题
在做DPDK测试时，需要用`dpdk-hugepages.py`，进行修改hugepages。当运行`dpdk-hugepages.py -p 1G --setup 2G`脚本后，报错`Unable to set pages (21 instead of 0 in /sys/devices/system/node/node0/hugep`，内容为：不能设置页，hugepages只能设置为21，而不是0。

## 问题分析
stackoverflow上有人遇到同样问题[Can't modify hugepage size once DPDK application is stopped](https://stackoverflow.com/questions/58410451/cant-modify-hugepage-size-once-dpdk-application-is-stopped) & [Hugepages seems to be stuck](https://serverfault.com/questions/912449/hugepages-seems-to-be-stuck)。

``` bash?linenums
echo 0 > /sys/devices/system/node/node0/hugepages/hugepages-2048kB/nr_hugepages
echo 0 > /proc/sys/vm/nr_hugepages
cat /sys/devices/system/node/node*/meminfo | fgrep Huge
```
使用前两行命令进行修改nr_hugepages个数，但通过查看发现并没有改变，`HugePages_Total`还是21。

## 发现问题
经过简单学习hugepages，发现需要进行挂载才能使用。

``` bash
lzy@lzy-Virtual-Machine:~$ mount | grep huge
cgroup on /sys/fs/cgroup/hugetlb type cgroup (rw,nosuid,nodev,noexec,relatime,hugetlb)
hugetlbfs on /dev/hugepages type hugetlbfs (rw,relatime,pagesize=2M)
```

卸载`/sys/fs/cgroup/hugetl`并且结束对应的程序。

``` bash
lzy@lzy-Virtual-Machine:~$ sudo grep huge /proc/*/numa_maps
/proc/859/numa_maps:ac0200000 default file=/memfd:seg_2-0\040(deleted) huge dirty=1 N0=1 kernelpagesize_kB=2048
/proc/859/numa_maps:1000000000 default file=/memfd:buffers-numa-0\040(deleted) huge dirty=20 N0=20 kernelpagesize_kB=2048

lzy@lzy-Virtual-Machine:~$ ps 859
  PID TTY      STAT   TIME COMMAND
  859 ?        Ssl    0:11 /usr/bin/vpp -c /etc/vpp/startup.conf
```
最后定位到，是`vpp`会使用`hugepages`。

## 解决问题
使用kill发送结束信号，软件应该会自动重启。
``` bash
lzy@lzy-Virtual-Machine:~$ sudo grep huge /proc/*/numa_maps
/proc/854/numa_maps:ac0200000 default file=/memfd:seg_2-0\040(deleted) huge dirty=1 N0=1 kernelpagesize_kB=2048
/proc/854/numa_maps:1000000000 default file=/memfd:buffers-numa-0\040(deleted) huge dirty=20 N0=20 kernelpagesize_kB=2048
lzy@lzy-Virtual-Machine:~$ ps 854
  PID TTY      STAT   TIME COMMAND
  854 ?        Ssl    0:01 /usr/bin/vpp -c /etc/vpp/startup.conf
lzy@lzy-Virtual-Machine:~$ sudo kill -9 854
lzy@lzy-Virtual-Machine:~$ sudo grep huge /proc/*/numa_maps
/proc/2280/numa_maps:ac0200000 default file=/memfd:seg_2-0\040(deleted) huge dirty=1 N0=1 kernelpagesize_kB=2048
/proc/2280/numa_maps:1000000000 default file=/memfd:buffers-numa-0\040(deleted) huge dirty=20 N0=20 kernelpagesize_kB=2048
```


正确方法如下：
``` bash
lzy@lzy-Virtual-Machine:~$ service vpp stop
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
停止“vpp.service”需要认证。
Authenticating as: lzy,,, (lzy)
Password:
==== AUTHENTICATION COMPLETE ===
lzy@lzy-Virtual-Machine:~$ sudo grep huge /proc/*/numa_maps
lzy@lzy-Virtual-Machine:~$ cat /sys/devices/system/node/node*/meminfo | fgrep Huge
Node 0 AnonHugePages:         0 kB
Node 0 ShmemHugePages:        0 kB
Node 0 FileHugePages:        0 kB
Node 0 HugePages_Total:  1024
Node 0 HugePages_Free:   1024
Node 0 HugePages_Surp:      0
root@lzy-Virtual-Machine:/home/lzy# echo 0 > /proc/sys/vm/nr_hugepages
root@lzy-Virtual-Machine:/home/lzy#  cat /sys/devices/system/node/node*/meminfo | fgrep Huge
Node 0 AnonHugePages:         0 kB
Node 0 ShmemHugePages:        0 kB
Node 0 FileHugePages:        0 kB
Node 0 HugePages_Total:     0
Node 0 HugePages_Free:      0
Node 0 HugePages_Surp:      0
```

# 问题总结
hugepages之所以不能修改变为0，但是可以比21大，因此怀疑是有程序在占用，因此不可以直接通过命令直接修改将其变为0。

## 不结束占用hugepages的进程，直接取消挂载

``` bash
root@lzy-Virtual-Machine:/home/lzy# umount /sys/fs/cgroup/hugetl
root@lzy-Virtual-Machine:/home/lzy# cat /sys/devices/system/node/node*/meminfo | fgrep Huge
Node 0 AnonHugePages:         0 kB
Node 0 ShmemHugePages:        0 kB
Node 0 FileHugePages:        0 kB
Node 0 HugePages_Total:    21
Node 0 HugePages_Free:      0
Node 0 HugePages_Surp:     21
```


# hugepages相关学习

- [Linux下hugetlbpage使用详解](http://www.lenky.info/archives/2012/03/1219)：详细的介绍了HugePages_Total、HugePages_Free、HugePages_Rsvd、HugePages_Surp具体意义，以及如何在NUMA系统上的合理分配Hugepages。