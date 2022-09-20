---
title: PCL项目 学习
slug: storywriter/grammar
grammar_abbr: true
grammar_table: true
grammar_defList: true
grammar_emoji: true
grammar_footnote: true
grammar_ins: true
grammar_mark: true
grammar_sub: true
grammar_sup: true
grammar_checkbox: true
grammar_mathjax: true
grammar_flow: true
grammar_sequence: true
grammar_code: true
grammar_highlight: true
grammar_html: true
grammar_linkify: true
grammar_typographer: true
grammar_video: true
grammar_audio: true
grammar_attachment: true
grammar_mermaid: true
grammar_classy: true
grammar_decorate: false
grammar_attrs: false
grammar_cjkEmphasis: true
grammar_cjkRuby: true
grammar_center: true
grammar_align: true
grammar_tableExtra: true
tags: 'Linux内核,网络协议栈,多线程,CPU亲和性(affinity)'
---
[toc]
# 一.CPU性能设置
左上角GAME BOOST调整为OFF，A-XMP调整为1，其他配置可以不用修改。操作目的是将CPU设置为非自动模式，将内存设置为高频模式。CPU主频通过命令进行设置。

![msi主板Bios界面](./images/1663643467056.png)
## 1.1 工具安装
``` bash?linenums
sudo apt-get install cpufrequtils
```
## 1.2 查看CPU信息
执行cpufreq-info命令，显示所有CPU的各种信息，较为重要的有：
>hardware limits： CPU硬件支持的频率最小值-最大值；
available frequency steps： CPU频率可以被设置的值；
available cpufreq governors： CPU频率调节策略。包括powersave, userspace,ondemand,conservative, performance。

![图1 cpufreq-info](./images/1663643705745.png)
### 1.2.1 CPU支持主频
如图1所示，CPU硬件支持的频率最小值为2.20GHz，最大值为3.4GHz。
### 1.2.2 CPU主频可设置的值
如图一，CPU频率可以被设置的值有：3.4GHz，2.8GHz，2.20GHz。
需要注意的是：CPU的频率不能被设置为任意值，必须是available frequency steps中的值，若设置的值不在其中，系统会选择设置为大于且在available frequency steps中的最小值。此外，CPU频率设置不会超过其上下限，即 hardware limits。
### 1.2.3 调节策略
命令：`sudo cpufreq-set -g {模式}`
powersave，是无论如何都只会保持最低频率的所谓”省电”模式；
userspace，是自定义频率时的模式，这个是当你设定特定频率时自动转变的；
ondemand，默认模式。一有cpu计算量的任务，就会立即达到最大频率运行，等执行完 毕就立即回到最低频率；
conservative，翻译成保守（中庸）模 式，会自动在频率上下限调整，
ondemand的区别在于它会按需分配频率，而不是一味追求最高频率；
performance，顾名思义只注重效率，无论如何一直保持以最大频率运行。
### 1.2.4 模式使用
powersave，ondemand，conservative，ondemand，performance这些模式设置后，就如1.2.3中字面意思一样，不用再进一步设置频率。
而userspace模式，假如你选择的是自定义模式可以通过的话，sudo cpufreq-set -f 1700000（你所需要的频率）
注意，此处的频率必须是以KHz为单 位，并且是可以达到的频率（也就是用cpufreq-info查看到的各个频率），cpu频率＝倍频x外频。以下凡是涉及频率的一律如此。
而在自动调节下，也可以设置其上限和下限
``` bash?linenums
sudo cpufreq-set -d {频率下限}
sudo cpufreq-set -u {频率上限}
```
## 1.3 命令总结
``` bash?linenums
Usage: cpufreq-set [options] Options:
-c CPU, –cpu CPU #指定CPU核心号，请注意上图的analyzing CPU数字。
-d FREQ, –min FREQ #手工指定最小主频速度。（在userspace策略）
-u FREQ, –max FREQ #手工指定最大主频速度。（在userspace策略）
-g GOV, –governor GOV #设置工作策略
-f FREQ, –freq FREQ #设定特定的工作频率（CPU默认档次）
-h, –help #输出这个帮助信息
```
## 1.4 编写cpu_set.sh脚本
``` bash?linenums
#!/bin/bash
 
###
 # Copyright 2022 liuziyan, All Rights Reserved. 
 # @Author       : liuziyan
 # @version      : 1.0
 # @Date         : 2022-09-13 19:44:36
 # @Instructions : 
 # @LastEditTime : 2022-09-20 11:28:00
 # @FilePath     : /s_v1.0.8/script/cpu_set.sh
### 

INPUT=$1

if [ "${INPUT}" == "1" ] ; then
    echo "CPU核心10-13修改为性能模式"
    sudo cpufreq-set -g performance -c 10
    sudo cpufreq-set -g performance -c 11
    sudo cpufreq-set -g performance -c 12
    sudo cpufreq-set -g performance -c 13
elif [ "${INPUT}" == "2" ] ; then
    echo "CPU核心10-13修改为默认模式"
    sudo cpufreq-set -g conservative -c 10
    sudo cpufreq-set -g conservative -c 11
    sudo cpufreq-set -g conservative -c 12
    sudo cpufreq-set -g conservative -c 13
elif [ "${INPUT}" == "3" ] ; then
    echo "CPU核心10-13修改为节能模式"
    sudo cpufreq-set -g powersave -c 10
    sudo cpufreq-set -g powersave -c 11
    sudo cpufreq-set -g powersave -c 12
    sudo cpufreq-set -g powersave -c 13
else
	echo "使用方法将CPU设置为对应的模式: 参数1为性能,参数2为默认,参数3为节能!"
    exit
fi

```
脚本调用显示如下所示：
``` bash?linenums
root@:s_v1.0.8# ./script/cpu_set.sh 1
CPU核心10-13修改为性能模式
root@:s_v1.0.8# ./script/cpu_set.sh 2
CPU核心10-13修改为默认模式
root@:s_v1.0.8# ./script/cpu_set.sh 3
CPU核心10-13修改为节能模式
root@:s_v1.0.8# ./script/cpu_set.sh 0
使用方法将CPU设置为对应的模式: 参数1为性能,参数2为默认,参数3为节能!
```
## 1.5 常用关于CPU的命令
``` bash?linenums
cat /proc/cpuinfo
sudo cpufreq_info
watch -n 0 "cat /proc/cpuinfo | grep -i mhz"
```
## 1.6 参考
1. [设置ubuntu中cpu频率](https://blog.csdn.net/xuershuai/article/details/122023817)
2. [linux调频指令-cpufreq介绍](https://blog.csdn.net/qq_40315501/article/details/124466387)

