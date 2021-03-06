---
title: DPDK Learning Record
tags: DPDK,NIC
renderNumberedHeading: true
grammar_cjkRuby: true
---
[toc]

# DPDK Common Commands
## route

``` bash?linenums
sudo ifconfig enp45s0f1 172.16.0.1/24
sudo route add -net 172.16.0.0 netmask 255.255.255.0 dev enp45s0f1
```

## Environment  Variables
``` bash

export RTE_SDK=/home/f410-client/lzy/dpdk/dpdk-stable-19.11.11
export RTE_TARGET=x86_64-native-linux-gcc

export RTE_SDK=/home/f410-server/lzy/dpdk/dpdk-stable-19.11.11
export RTE_TARGET=x86_64-native-linux-gcc

```

## Set terminal to automatically load DPDK environment variables

``` bash

# Add to the end of /etc/profile

if [ -f ~/.bashrc ]; then
. ~/.bashrc
fi

# Add to the end of ~/.bashrc

export RTE_SDK=/home/f410-client/lzy/dpdk/dpdk-stable-19.08.2/
export RTE_TARGET=x86_64-native-linux-gcc

```

## netassist

``` javascript
192.168.0.150 :8889
```

## wireshark

``` javascript
arp.src.hw_mac eq 9c:69:b4:61:69:08

```

## VScode Settings : c_cpp_properties.json
``` json
{
    "configurations": [
        {
            "name": "Linux",
            "includePath": [
                "${workspaceFolder}/**",
                "/home/f410-server/lzy/dpdk/dpdk-stable-19.11.11/lib/**",
                "/home/f410-server/lzy/dpdk/dpdk-stable-19.11.11/build/lib/**",
                "/home/f410-server/lzy/dpdk/dpdk-stable-19.11.11/x86_64-native-linux-gcc/include/**",
                "/usr/include/**",
                "/usr/local/include/**",
                "/usr/lib/**",
                "/lib/**"
            ],
            "defines": [],
            "compilerPath": "/usr/bin/gcc",
            "cStandard": "c17",
            "cppStandard": "c++20",
            "intelliSenseMode": "linux-gcc-x64"
        }
    ],
    "version": 4
}

```
# CPU-related concepts in DPDK
##  lcore

指eal线程，采用pthread创建，并设置了CPU亲和性。我感觉dpdk把这个概念搞混淆了，有时指逻辑核，有时指线程。不过大部分是指线程。

## lcore_id 

逻辑核标识，可以通过top d1 或cat /proc/cpuinfo 看到的核，这种包括物理核和超线程的核，判断是否为超线程核，可以看/proc/cpuinfo文件中相应的核sibling是不是大于cpu cores，如果是则为超线程核，但超线程对于我们编程是透明的。在/proc/cpuinfo/中Physical id 和core id相同是超线程。

每个eal线程，会存储每线程变量RTE_PER_LCORE(_lcore_id) 存储逻辑核id. 设置亲和力，应该是lcore_id.

## core_id

该逻辑核在物理核中的标识，如果是超线程的话，core_id存在相同的情况。通过/sys/devices/system/cpu/cpuX/topology/core_id 可以看到，也可以通过/proc/cpuinfo中查到，前面一种便于编程。在线程的绑定CPU亲和性方面没有用。

## socket_id

在DPDK中不同于物理核的socket,应该是numa的标识。查看文件/sys/devices/system/nodeX/cpu.


# NIC initialization faild: eth_i40e_dev_init(): Failed to init adminq: -66 

## Check if the DPDK is compatible with the NIC

 1. [DPDK SUPPORTED HARDWARE](https://core.dpdk.org/supported/)
 2. [DPDK INTEL NIC](https://core.dpdk.org/supported/nics/intel/)
 3. [DPDK X710](https://doc.dpdk.org/guides/nics/i40e.html)

![DPDK Recommended Matching List](./images/1646211984713.png)
![X710 Information](./images/1646636385663.png)
## select the appropriate DPDK,Driver and Firmware version

Since DPDK version 20.11(LTS) is selected,the driver version is 2.14.13 and firmware version is 8.00.

## download source code and insmod igb_uio
kernel: The module igb_uio has been moved to the git repository [dpdk-kmods](https://git.dpdk.org/dpdk-kmods/) in a new directory linux/igb_uio.

``` bash
sudo modprobe uio
sudo insmoigb_uio.ko
```
##  Intel® X710/XL710 Gigabit Ethernet Controller VF Infrastructure
In a virtualized environment, the programmer can enable a maximum of 128 Virtual Functions (VF) globally per Intel® X710/XL710 Gigabit Ethernet Controller NIC device. The Physical Function in host could be either configured by the Linux* i40e driver (in the case of the Linux Kernel-based Virtual Machine [KVM]) or by DPDK PMD PF driver. When using both DPDK PMD PF/VF drivers, the whole NIC will be taken over by DPDK based application.


``` bash
#  Using Linux* i40e driver
rmmod i40e (To remove the i40e module)
insmod i40e.ko max_vfs=2,2 (To enable two Virtual Functions per port)
```

``` bash
# Using the DPDK PMD PF i40e driver:
# Kernel Params: iommu=pt, intel_iommu=on

modprobe uio
insmod igb_uio
./dpdk-devbind.py -b igb_uio bb:ss.f
echo 2 > /sys/bus/pci/devices/0000\:bb\:ss.f/max_vfs (To enable two VFs on a specific PCI device)
```

Launch the DPDK testpmd/example or your own host daemon application using the DPDK PMD library.

Virtual Function enumeration is performed in the following sequence by the Linux* pci driver for a dual-port NIC. When you enable the four Virtual Functions with the above command, the four enabled functions have a Function# represented by (Bus#, Device#, Function#) in sequence starting from 0 to 3. However:

- Virtual Functions 0 and 2 belong to Physical Function 0
- Virtual Functions 1 and 3 belong to Physical Function 1

> Note:
> 
> The above is an important consideration to take into account when targeting specific packets to a selected port.
> 
> For Intel® X710/XL710 Gigabit Ethernet Controller, queues are in pairs. One queue pair means one receive queue and one transmit queue. The default number of queue pairs per VF is 4, and can be 16 in maximum.

https://doc.dpdk.org/guides-20.11/nics/intel_vf.html?highlight=x710

##  Modify IOMMU
Since the VFIO driver depends on the IOMMU,the boot parameters in grub need to be modified.

Check if iommu is running:
``` bash
dmesg | grep -e DMAR -e IOMMU
```

check if start-up parameters are added:
``` bash
cat /proc/cmdline | grep iommu=pt
cat /proc/cmdline | grep intel_iommu=on
```

add configuartion information to the /etc/default/grub file

``` bash
# //原文件的配置信息
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
//添加配置信息
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash iommu=pt intel_iommu=on"
```

update configuration information and reboot

``` javascript
sudo grub-mkconfig -o /boot/grub/grub.cfg 
sudo reboot 
```

[DPDK中使用VFIO的配置](https://www.cnblogs.com/vancasola/p/9378970.html)

# Running dpdk-pmdinfo.py shows No module named 'elftools'e

``` bash
**root@f410client-MS-7C37:/home/f410-client/lzy/dpdk/dpdk-stable-20.11.4/usertools# ls
cpu_layout.py    dpdk-hugepages.py  dpdk-telemetry-client.py  meson.build
dpdk-devbind.py  dpdk-pmdinfo.py    dpdk-telemetry.py
root@f410client-MS-7C37:/home/f410-client/lzy/dpdk/dpdk-stable-20.11.4/usertools#
root@f410client-MS-7C37:/home/f410-client/lzy/dpdk/dpdk-stable-20.11.4/usertools# ./dpdk-pmdinfo.py -h
Traceback (most recent call last):
  File "./dpdk-pmdinfo.py", line 15, in <module>
    from elftools.common.exceptions import ELFError
ModuleNotFoundError: No module named 'elftools'**
```

The query shows that elftools is a module for python,so you have to download it with pip or pip3. But pip3 cannot locate the elftools module.

``` python

pip3 install pyelftools

```

# ERROR: Target does not have the DPDK UIO Kernel Module.  To fix, please try to rebuild target.

This is because the version of DPDK no longer provides igb_uio driver directly, you need to download source code and compile it before you can use it.

# DPDK TIMER
# Examples provided by DPDK
  
  ## main function

``` c

hz = rte_get_timer_hz();
lcore_id = rte_lcore_id();

/* load timer0, every second, on master lcore, reloaded automatically */
rte_timer_reset(&timer0, hz, PERIODICAL, lcore_id, timer0_cb, NULL);

/* load timer1, every second/3, on next lcore, reloaded manually */
lcore_id = rte_get_next_lcore(lcore_id, 0, 1);
rte_timer_reset(&timer1, hz/3, SINGLE, lcore_id, timer1_cb, NULL);

	/* Call lcore_mainloop() on each remaining slave lcore. */
	/* Bind and run the lcore_mainloop function on all slave lcore except the master lcore  */
	RTE_LCORE_FOREACH_SLAVE(lcore_id) {
		/* This function must be called by the timer bound core to update the timer. */ 
		rte_eal_remote_launch(lcore_mainloop, NULL, lcore_id);
	}
	
	/* call it on master lcore too */
	(void) lcore_mainloop(NULL);
	
```

## timer0 callback function

``` c
static void timer0_cb(__attribute__((unused)) struct rte_timer *tim,  __attribute__((unused)) void *arg)
{
	static unsigned counter = 0;
	unsigned lcore_id = rte_lcore_id();

	printf("%s() on lcore %u\n", __func__, lcore_id);

	/* this timer is automatically reloaded until we decide to stop it, when counter reaches 20. */
	
	if ((counter ++) == 20)
		rte_timer_stop(tim);
}
```

## timer1 callback function

First get the lcore_id that called the function, then rebind the function to the next lcores. 

``` c
static void timer1_cb(__attribute__((unused)) struct rte_timer *tim,  __attribute__((unused)) void *arg)
{
	unsigned lcore_id = rte_lcore_id();
	uint64_t hz;

	printf("%s() on lcore %u\n", __func__, lcore_id);

	/* reload it on another lcore */
	hz = rte_get_timer_hz();
	lcore_id = rte_get_next_lcore(lcore_id, 0, 1);
	rte_timer_reset(tim, hz/3, SINGLE, lcore_id, timer1_cb, NULL);
}
```

## lcore_mainloop function

``` c
static __attribute__((noreturn)) int lcore_mainloop(__attribute__((unused)) void *arg)
{
	uint64_t prev_tsc = 0, cur_tsc, diff_tsc;
	unsigned lcore_id;

	lcore_id = rte_lcore_id();
	printf("Starting mainloop on core %u\n", lcore_id);

	while (1) {
		/*
		 * Call the timer handler on each core: as we don't
		 * need a very precise timer, so only call
		 * rte_timer_manage() every ~10ms (at 2Ghz). In a real
		 * application, this will enhance performances as
		 * reading the HPET timer is not efficient.
		 */
		cur_tsc = rte_rdtsc();
		diff_tsc = cur_tsc - prev_tsc;
		
		/* The rte_timer_manage function is called approximately once every 10ms. */
		if (diff_tsc > TIMER_RESOLUTION_CYCLES) {
		
			/* Manage the timer list and execute callback functions.   */
			/* This function must be called periodically from EAL lcores main_loop().  */
			/* The above two lines are the official comments, which means that if a timer is run on a core,
			the function must be run on that core to manage the timing list and the wake-up callback function. */
			rte_timer_manage();
			prev_tsc = cur_tsc;
		}

	}
	}
```

## Reference
1. [DPDK Timer Library原理（学习笔记）](https://www.cnblogs.com/realjimmy/p/12912751.html)
2. [Docs » Programmer’s Guide » 21. Timer Library](http://doc.dpdk.org/guides-20.02/prog_guide/timer_lib.html)

# Sample basicfwd

# dpdk Pktgen
The software will use the DPDK library, so the DPDK version requirements are very strict, so be sure to choose the most relevant one.

Here I am using DPDK 19.11.11 (LTS), pktgen-20.02.0.。

[pktgen-dpdk Download Website](http://git.dpdk.org/apps/pktgen-dpdk/refs/)

Just put the downloaded source code directly under dpdk/examples/.

## Problems encountered during installation
### Error:Did not find CMake 'cmake'

``` routeros
sudo apt-get install cmake -y
```

### fatal error: lua.h: No such file or directory

下载：https://www.lua.org/ftp/

``` bash

tar -zxvf lua-5.3.6.tar.gz

# Put it under pktgen-3.4.5/lib/lua/

cd   lua-5.3.6

make linux
sudo make install
make local

lua -v

```

### fatal error: pcap.h

Download Website : https://www.tcpdump.org/

``` bash
sudo apt-get install flex  bison  -y
sudo apt-get install bison -y

```

[Ubuntu下libpcap安装步骤](https://www.cnblogs.com/flyinggod/p/9322267.html)

![enter description here](./images/1648608010396.png)

``` bash

# Since the program will depend on the libpcap library to run, it needs to be copied to the user library

sudo  find / -name libpcap.so  
sudo  find / -name libpcap.so.1

sudo cp /usr/local/lib/libpcap.so /usr/lib/
sudo cp /usr/local/lib/libpcap.so.1 /usr/lib/

```

### Run error : PANIC in tailqinitfn_mbuf_dynfield_tailq():


``` bash
f410-client@f410client-MS-7C37:~/lzy/dpdk/dpdk-stable-19.11.11/examples/pktgen-dpdk-pktgen-20.02.0$ ./app/x86_64-native-linux-gcc/pktgen
EAL: RTE_MBUF_DYNFIELD tailq is already registered
PANIC in tailqinitfn_mbuf_dynfield_tailq():
Cannot initialize tailq: RTE_MBUF_DYNFIELD
6: [./app/x86_64-native-linux-gcc/pktgen(_start+0x2a) [0x560c0791662a]]
5: [/lib/x86_64-linux-gnu/libc.so.6(__libc_start_main+0x78) [0x7f7e85ccbc18]]
4: [./app/x86_64-native-linux-gcc/pktgen(__libc_csu_init+0x4d) [0x560c0806186d]]
3: [./app/x86_64-native-linux-gcc/pktgen(+0xd826c) [0x560c0785926c]]
2: [./app/x86_64-native-linux-gcc/pktgen(__rte_panic+0xc5) [0x560c07848a3e]]
1: [./app/x86_64-native-linux-gcc/pktgen(rte_dump_stack+0x2e) [0x560c07a73a1e]]
Aborted (core dumped)

```

View the link libraries used by pktgen-dpdk

``` bash
f410-client@f410client-MS-7C37:~/lzy/dpdk/dpdk-stable-19.11.11/examples/pktgen-dpdk-pktgen-20.02.0$ ldd ./app/x86_64-native-linux-gcc/pktgen
        linux-vdso.so.1 (0x00007fff8bbc5000)
        librt.so.1 => /lib/x86_64-linux-gnu/librt.so.1 (0x00007f35b0d0b000)
        libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007f35b096d000)
        libnuma.so.1 => /usr/lib/x86_64-linux-gnu/libnuma.so.1 (0x00007f35b0762000)
        libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f35b055e000)
        libpcap.so.1 => /usr/local/lib/libpcap.so.1 (0x00007f35b0313000)
        libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f35b00f4000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f35afd03000)
        /lib64/ld-linux-x86-64.so.2 (0x00007f35b1f7c000)
        librte_ethdev.so.21 => /usr/local/lib/x86_64-linux-gnu/librte_ethdev.so.21 (0x00007f35afa5d000)
        librte_mbuf.so.21 => /usr/local/lib/x86_64-linux-gnu/librte_mbuf.so.21 (0x00007f35af851000)
        librte_mempool.so.21 => /usr/local/lib/x86_64-linux-gnu/librte_mempool.so.21 (0x00007f35af647000)
        librte_eal.so.21 => /usr/local/lib/x86_64-linux-gnu/librte_eal.so.21 (0x00007f35af356000)
        librte_kvargs.so.21 => /usr/local/lib/x86_64-linux-gnu/librte_kvargs.so.21 (0x00007f35af153000)
        librte_telemetry.so.21 => /usr/local/lib/x86_64-linux-gnu/librte_telemetry.so.21 (0x00007f35aef4a000)
        librte_net.so.21 => /usr/local/lib/x86_64-linux-gnu/librte_net.so.21 (0x00007f35aed43000)
        librte_ring.so.21 => /usr/local/lib/x86_64-linux-gnu/librte_ring.so.21 (0x00007f35aeb3f000)
        librte_meter.so.21 => /usr/local/lib/x86_64-linux-gnu/librte_meter.so.21 (0x00007f35ae93c000)
	
f410-client@f410client-MS-7C37:/usr/local/lib$ ll
total 3536
drwxr-xr-x  7 root root     4096 4月   7 15:32 ./
drwxr-xr-x 11 root root     4096 12月 17 19:11 ../
-rw-r--r--  1 root root   453644 4月   7 15:32 liblua.a
-rw-r--r--  1 root root  1952722 4月   7 11:16 libpcap.a
lrwxrwxrwx  1 root root       12 4月   7 11:16 libpcap.so -> libpcap.so.1*
lrwxrwxrwx  1 root root       17 4月   7 11:16 libpcap.so.1 -> libpcap.so.1.10.1*
-rwxr-xr-x  1 root root  1144728 4月   7 11:16 libpcap.so.1.10.1*
drwxr-xr-x  4 root root     4096 4月   7 15:11 lua/
drwxr-xr-x  2 root root     4096 4月   7 11:16 pkgconfig/
drwxrwsr-x  4 root staff    4096 3月  31 06:24 python2.7/
drwxrwsr-x  3 root staff    4096 2月  28 16:47 python3.6/
drwxr-xr-x  4 root root    36864 3月   3 10:12 x86_64-linux-gnu/	

```


By looking at the link library, I found that the link library provided by x86_64-linux-gnu is used, but since the compiler used in this project is x86_64-native-linux-gcc, I need to remove the link library. The reason why pktgen-dpdk uses this one is that there is another version of DPDK installed in the previous installation, and the link library was put directly under /usr/lib when compiling.

Just recompile pktgen-dpdk and run it.

### Backspace key becomes ^H

Just run the command: `stty erase ^H` in the terminal.

### reference 
[DPDK PKTGEN使用](https://www.jianshu.com/p/fa7d9f2c0f55)
[DPDK以及Pktgen的编译安装](https://blog.csdn.net/Sword1996/article/details/88718131)
[pktgen+dpdk安装（20201222）](https://blog.csdn.net/shaoyunzhe/article/details/111560782)
[dpdk相关pktgen 的安装以及使用](https://blog.csdn.net/linggang_123/article/details/114521238)
## Usage
