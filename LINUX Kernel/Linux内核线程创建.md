---
title: Linux内核线程创建
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
tags: 'Linux内核,线程,CPU亲和性(affinity)'
---

# 测试环境背景
Linux Kernel version: v4.15.18

# 常用线程函数介绍

经过源码查看无论是`kthread_create`还是`kthread_create_on_cpu`，都是调用`kthread_create_on_node`进行创建线程。

``` c?linenums

#define kthread_create(threadfn, data, namefmt, arg...) \
	kthread_create_on_node(threadfn, data, NUMA_NO_NODE, namefmt, ##arg)

struct task_struct *kthread_create_on_cpu(int (*threadfn)(void *data), void *data, unsigned int cpu, const char *namefmt);

/* 
参数 int (*threadfn) (void *data) 是一个函数指针，即它是一个函数，此函数是此进程执行时执行的函数，此函数返回值为int型，参数是一个void型指针。
参数void * data是一个void型指针，是传递给第一个参数所代表函数的参数，即进程执行时函数的参数。
参数node为存储节点编号，内核将内存区域进程编号，如果指定编号，则创建的新进程在指定的内存区域，如果不指定，设置为-1，则内核随机选择内存区域。
namefmt为进程的输出类型名。
 */			  

struct task_struct *kthread_create_on_node(int (*threadfn)(void *data), void *data, int node,  const char namefmt[],   ...)	

```


# 出现问题

代码中使用了函数kthread_create_on_cpu，但是在编译驱动过程中提示`warning : "kthread_create_on_cpu"[/tmp/VisualKernel/mpsrvlkm.ko] undefined!`，经过查阅结果为：kthread_create_on_cpu不是由内核导出的，它是 CPU 热插拔线程使用的内部函数（请参阅 参考资料linux/smpboot.h）。如果必须设置线程到特定CPU上，则使用函数kthread_bind进行绑定运行。


reference : 
[linux内核模块：内核方法未定义（kthread_create_on_cpu）](https://qa.1r1g.com/sf/ask/2039076791/#)

# reference :
1. [kthread_create_on_node和kthread_stop](https://zhuanlan.zhihu.com/p/56642985)
2. [内核线程创建](https://blog.csdn.net/zhaojie8324616/article/details/101223668)
3. [如何将进程线程与CPU核进行绑定](https://blog.csdn.net/weixin_44613415/article/details/124646042)(用户态)
4. [linux 内核线程 绑定cpu](https://www.csdn.net/tags/NtTaggzsMzY2MzUtYmxvZwO0O0OO0O0O.html)
5. [Linux性能优化（十五）——CPU绑定](https://blog.csdn.net/A642960662/article/details/123031554)