---
title: Linux NIC Update
tags: Firmware,Driver,
renderNumberedHeading: true
grammar_cjkRuby: true
---

# Firmware update

[适用于 英特尔® 以太网 适配器 700 系列的非易失性内存 （NVM） 更新实用程序 — Linux*](https://www.intel.cn/content/www/cn/zh/download/18635/29927/non-volatile-memory-nvm-update-utility-for-intel-ethernet-adapters-700-series-linux.html)

Select the appropriate version to download.

``` bash
 tar -xf 700series_nvmupdatepackage_v8_00_linux.tar.gz
./700Series/Linux_x64/nvmupdate64e
```


# Driver update
## To manually build the driver
[适用于 Linux* 下 PCIe* 4 万兆位以太网网络连接的英特尔® 网络适配器驱动程序](https://www.intel.cn/content/www/cn/zh/download/18026/30366/intel-network-adapter-driver-for-pcie-40-gigabit-ethernet-network-connections-under-linux.html)

----------------------------
1. Move the base driver tar file to the directory of your choice.For example, use '/home/username/i40e' or '/usr/local/src/i40e'.

2. Untar/unzip the archive, where <x.x.x> is the version number for the driver tar file:
``` bash
 tar zxf i40e-<x.x.x>.tar.gz
```
3. Change to the driver src directory, where <x.x.x> is the version number
   for the driver tar:
``` bash
  cd i40e-<x.x.x>/src/
```
4. Compile the driver module:
``` ruby
# make install
```
The binary will be installed as:`/lib/modules/<KERNELVER>/updates/drivers/net/ethernet/intel/i40e/i40e.ko`. The install location listed above is the default location. This may differ or various Linux distributions.

5. Load the module using the modprobe command.To check the version of the driver and then load it:

``` ruby
# modinfo i40e
# modprobe i40e [parameter=port1_value,port2_value]
```
Alternately, make sure that any older i40e drivers are removed from the kernel before loading the new module:

``` ruby
# rmmod i40e; modprobe i40e
```

Note: For certain distributions like (but not limited to) Red Hat Enterprise Linux 7, Ubuntu, and SUSE Linux Enterprise Server (SLES) 11, once the driver is installed, you may need to update the initrd/initramfs file to prevent the OS loading old versions of the i40e driver.

``` yaml
For Red Hat distributions:
# dracut --force

For Ubuntu:
# update-initramfs -u

For SLES:
# mkinitrd
```

## About boot up the network card driver automatically loaded
If you have already compiled the NIC driver source code with root, the i40e.ko driver will exist in `/lib/modules/<KERNELVER>/updates/drivers/net/ethernet/intel/i40e/`, just regenerate /boot/initrd.img-4.15.0- 169-generic.

``` bash
 update-initramfs -u
```

## Load NIC driver to blacklist


``` bash
vi /etc/modprobe.d/blacklist.conf
# just add it directly to the last line.
blacklist i40e
```

# 

``` bahs
root@f410client-MS-7C37:/home/f410-client/lzy/updata_NIC# ethtool -i enp45s0f0
driver: i40e
version: 2.14.13
firmware-version: 8.00 0x80008b84 1.2766.0
expansion-rom-version:
bus-info: 0000:2d:00.0
supports-statistics: yes
supports-test: yes
supports-eeprom-access: yes
supports-register-dump: yes
supports-priv-flags: yes
```