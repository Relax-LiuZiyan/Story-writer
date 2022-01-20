---
title: DPDK源码编译与环境搭建
tags: DPDK
renderNumberedHeading: true
grammar_cjkRuby: false
---

# DPDK编译
1. 下载源码，[http://core.dpdk.org/download/](http://core.dpdk.org/download/)，最好下载LTS版本，例如Latest Long Term Stable (LTS) 21.11.0。
2. 下载依赖工具meson和ninja
	- Linux环境是Ubuntu18.04LTS，其自带有ninja。
	- meson不可以直接使用apt-get install meson对应版本的。
3. DPDK构建`meson build`。
4. 配置结束，构建并安装DPDK系统，最后两个命令要以root身份运行。
	``` bash
	cd build
	ninja
	ninja install
	ldconfig
	```
## meson build遇到的两个错误

1. 错误1：`meson_options.txt:7:0: ERROR: Unknown type feature`错误，原因是版本低，需用pip3安装新版本的meson工具。参考链接[How to fix Meson build “ERROR: Unknown Type feature” on Ubuntu 18.04](https://suryanshpradhan.wordpress.com/2020/01/04/how-to-fix-meson-build-error-unknown-type-feature-on-ubuntu-18-04/)。
	+ 卸载使用apt-get下载的工具，`apt-get purge meson`。
	+ `sudo apt-get install python3 python3-pip`
	+ `sudo pip3 install meson`
	+ 查看meson版本，`meson -v`
2. 错误2：`buildtools/meson.build:49:8: ERROR: Problem encountered: missing python module: elftools`，命令`pip3 install pyelftools`即可解决。参考连接：[dpdk: ModuleNotFoundError: No module named 'elftools'](https://github.com/clearlinux/distribution/issues/1464) 。

## 使用pip3安装meson后，普通用户添加环境路径

``` bash
lzy@lzy-Virtual-Machine:~/dpdk/dpdk-21.11/build$ python3
Python 3.6.9 (default, Dec  8 2021, 21:08:43)
[GCC 8.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import sys
>>> sys.path
['', '/usr/lib/python36.zip', '/usr/lib/python3.6', '/usr/lib/python3.6/lib-dynload', '/usr/local/lib/python3.6/dist-packages', '/usr/lib/python3/dist-packages']
>>>exit()
lzy@lzy-Virtual-Machine:~/dpdk/dpdk-21.11/build$ cd /usr/local/lib/python3.6/dist-packages
lzy@lzy-Virtual-Machine:/usr/local/lib/python3.6/dist-packages$ ls
elftools  meson-0.61.1.dist-info  mesonbuild  pyelftools-0.27.dist-info
lzy@lzy-Virtual-Machine:/usr/local/lib/python3.6/dist-packages$ export PATH=/usr/local/lib/python3.6/dist-packages:$PATH
lzy@lzy-Virtual-Machine:/usr/local/lib/python3.6/dist-packages$ echo $PATH
/usr/local/lib/python3.6/dist-packages:/usr/local/lib/python3.6/dist-packages:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```
