---
title: Hugepages cannot be modified
tags: Hugepages ,dpdk,vpp
renderNumberedHeading: true
grammar_cjkRuby: true
---

# 问题
在做DPDK测试时，需要用`dpdk-hugepages.py`，进行修改hugepages。当运行`dpdk-hugepages.py -p 1G --setup 2G`脚本后，报错`Unable to set pages (21 instead of 0 in /sys/devices/system/node/node0/hugep`，内容为：不能设置页，hugepages只能设置为21，而不是0。

## 问题分析
stackoverflow上有人遇到同样问题[Can't modify hugepage size once DPDK application is stopped](https://stackoverflow.com/questions/58410451/cant-modify-hugepage-size-once-dpdk-application-is-stopped) & [Hugepages seems to be stuck](https://serverfault.com/questions/912449/hugepages-seems-to-be-stuck)。

``` bash?linenums
echo 0 > sys/devices/system/node/node0/hugepages/hugepages-2048kB/nr_hugepages
echo 0 > /proc/sys/vm/nr_hugepages
cat /sys/devices/system/node/node0/meminfo | fgrep Huge
```
使用前两行命令进行修改nr_hugepages个数，但通过查看发现并没有改变，`HugePages_Total`还是21。


