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

# 一.CPU性能设置

左上角GAME BOOST调整为OFF，A-XMP调整为1，其他配置可以不用修改。

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
## 1.2.1 CPU支持主频
如图1所示，CPU硬件支持的频率最小值为2.20GHz，最大值为3.4GHz。