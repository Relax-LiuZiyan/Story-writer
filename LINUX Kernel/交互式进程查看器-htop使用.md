---
title: 交互式进程查看器-htop使用
tags: htop,cpu,线程
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
---
# 主界面介绍
上面界面是可以通过设置进行修改，1-32对应CPU核心0-31，条状为CPU占用率，下侧界面为任务线程(进程、用户态线程与内核线程等)。

![htop主界面](./images/1667221831345.png)
## 主界面右上侧细节讲述
### CPU
使用率的 bar 包含了红色跟绿色，有时甚至还会有蓝色，那其实是有意义：

- 红色 代表 kernel thread 佔用的 CPU，像是系统需要自动做 process scheduling、memory management 等等，是整个系统中最重要、优先权也最高的任务
  
- 绿色 代表 normal priority thread，进程的优先权比 kernel thread 低一些，一般来说使用者执行的程序如果没有特别调优先权的话，都会归在这一类
  
- 蓝色 代表 low priority thread，因为优先权比较低，分配到的 CPU 自然也比较少，适合“我 ok，你先跑”那类比较无关紧要的 process，如果 CPU 已经被压榨到快不行了，或是 memory 真的不够用了，第一个杀掉的也是这类 process

![CPU工作](./images/1667223293892.png)
### Load Average & Tasks & Uptime
首先 Tasks 栏位的 125, 355 thr, 304 kthr, 2 running 代表的是目前总共有 125个process、355个thread，304个kernel thread(内核线程需要打开才可看到，在主界面按下K即可), 其中 2个 thread 正在执行（这里指正在物理CPU上操作的进程 or 正在等待轮到它在CPU上运行的进程）。

![](./images/1667269845325.png)
