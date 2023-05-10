---
title: Ubuntu 18.04安装驱动
category: /小书匠/日记/2023-05
grammar_cjkRuby: true
tags: '网卡驱动,RTL8111/8168/8411,X570-A PRO,X710-T4'
---
[toc]

# 系统环境
## 安装新内核版本后无法识别主板网卡的问题
主板上的网卡信息无法被识别，通过`sudo lshw -class network`命令后，打印信息为`*-network UNCLAIMED`，通过`ifconfig`命令，没有识别到对应的网卡信息。这意味着该设备没有被操作系统识别或找不到合适的驱动程序。

由于此主板之前一直使用，重新安装**新的系统**后，才导致网卡口应该没问题的，因此可以认为是驱动的问题。

 Ubuntu 18.04.6 LTS (GNU/Linux 4.15.0-208-generic x86_64)
 主板：X570-A PRO
 网卡：RTL8111/8168/8411
  ## 解决办法
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

## 其中遇到的问题

在编译驱动时，通过命令`sudo make all`后，提示没有`/lib/modules/4.15.0-208-generic/build`路径，经过查询学弟在安装系统内核后，忘记安装系统头文件导致此问题，运行命令`sudo apt-get install linux-headers-$(uname -r)`，即可完成内核头文件的安装。

## 参考文献
1. [ubuntu RTL8111/8168/8411网卡驱动怎么正确安装？](https://www.zhihu.com/question/287274115)


# X710-T4网卡驱动安装
## 驱动信息无法识别


![网卡速度和工作方式无法识别](./images/1683724236792.png)

[网卡驱动软件](https://www.intel.cn/content/www/cn/zh/download/18026/intel-network-adapter-driver-for-pcie-40-gigabit-ethernet-network-connections-under-linux.html)，此处选择`i40e-2.22.18.tar.gz`即可。

![X710-T4对应的i40e网卡驱动](./images/1683724641314.png)


``` bash?linenums
To manually build the driver
----------------------------
1. Move the base driver tar file to the directory of your choice.
   For example, use '/home/username/i40e' or '/usr/local/src/i40e'.

2. Untar/unzip the archive, where <x.x.x> is the version number for the
   driver tar file:
   # tar zxf i40e-<x.x.x>.tar.gz
3. Change to the driver src directory, where <x.x.x> is the version number
   for the driver tar:
   # cd i40e-<x.x.x>/src/
4. Compile the driver module:
   # make install
   The binary will be installed as:
   /lib/modules/<KERNEL VER>/updates/drivers/net/ethernet/intel/i40e/i40e.ko
   
   The install location listed above is the default location. This may differ
   for various Linux distributions.

   NOTE: To gather and display additional statistics, use the
   I40E_ADD_PROBES pre-processor macro: 	
   # make CFLAGS_EXTRA=-DI40E_ADD_PROBES
   Please note that this additional statistics gathering can impact
   performance.

   NOTE: You may see warnings from depmod related to unknown RDMA symbols
   during the make of the OOT base driver. These warnings are normal and
   appear because the in-tree RDMA driver will not work with the OOT base
   driver. To address the issue, you need to install the latest OOT versions
   of the base and RDMA drivers.

5. Load the module using the modprobe command.

   To check the version of the driver and then load it:

   # modinfo i40e
   # modprobe i40e [parameter=port1_value,port2_value]

   Alternately, make sure that any older i40e drivers are removed from the
   kernel before loading the new module:

   # rmmod i40e; modprobe i40e

6. Assign an IP address to the interface by entering the following,
   where <ethX> is the interface name that was shown in dmesg after modprobe:

   # ip address add <IP_address>/<netmask bits> dev <ethX>

7. Verify that the interface works. Enter the following, where IP_address
   is the IP address for another machine on the same subnet as the interface
   that is being tested:

   # ping <IP_address>

Note: For certain distributions like (but not limited to) Red Hat Enterprise
Linux 7 and Ubuntu, once the driver is installed, you may need to update the
initrd/initramfs file to prevent the OS loading old versions of the i40e driver.
   For Red Hat distributions:
	# dracut --force

   For Ubuntu:
	# update-initramfs -u
```