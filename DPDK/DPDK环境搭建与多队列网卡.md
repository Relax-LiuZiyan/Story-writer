---
title: DPDK环境搭建与多队列网卡
tags: 新建,模板,小书匠
renderNumberedHeading: true
grammar_cjkRuby: true
---

# vmware配置问题

## E1000 与 VMXNET3
VMXNET3网卡
``` bash

root@ubuntu:/home/king/share/dpdk/dpdk-stable-19.08.2/usertools# cat /proc/interrupts | grep eth0
  56:          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0   PCI-MSI 1572864-edge      eth0-rxtx-0
  57:          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0   PCI-MSI 1572865-edge      eth0-rxtx-1
  58:          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0   PCI-MSI 1572866-edge      eth0-rxtx-2
  59:          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0   PCI-MSI 1572867-edge      eth0-rxtx-3
  60:          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0   PCI-MSI 1572868-edge      eth0-rxtx-4
  61:          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0   PCI-MSI 1572869-edge      eth0-rxtx-5
  62:          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0   PCI-MSI 1572870-edge      eth0-rxtx-6
  63:          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0   PCI-MSI 1572871-edge      eth0-rxtx-7
  64:          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0   PCI-MSI 1572872-edge      eth0-event-8
```

E1000网卡
``` bahs

root@ubuntu:/home/king/share/dpdk/dpdk-stable-19.08.2/usertools# cat /proc/interrupts | grep eth2
  19:          7       3617          0          0          0          0          0          0          0         55          0          0          0          0          0          0   IO-APIC   19-fasteoi   eth2
```

# CPU亲和性


``` bash
root@ubuntu:/home/king/share/dpdk/dpdk-stable-19.08.2/usertools# cat /proc/irq/56/
affinity_hint      eth0-rxtx-0/       node               smp_affinity       smp_affinity_list  spurious
root@ubuntu:/home/king/share/dpdk/dpdk-stable-19.08.2/usertools# cat /proc/irq/56/smp_affinity
00000000,00000000,00000000,00000400
```

