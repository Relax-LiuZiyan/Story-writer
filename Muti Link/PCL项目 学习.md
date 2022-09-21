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
# 一、CPU性能设置
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

# 二、常用打流命令
## 2.1 iperf3
``` bash?linenums
iperf3 -u -c 10.0.0.1 -p10000 -l8756 -t100 -b12G -A10
iperf3 -s -p10000 -A10
```
## 2.2 iperf
``` bash?linenums
iperf -u -c172.16.1.1 -p10000 -l1400 -t100 -b1000M -i1 
iperf -u -s -p10000 -i1
```
## 2.3 打流软件对比
1. 进行UDP打流前，iperf3需要发送端与接收端先建立一个TCP的链接，在进行UDP报文的传输，而iperf则不需要进行TCP的建立。
2. iperf使用场景为低流量，高可靠场景，iperf3场景为高流量，快速发包。iperf3会在每个1S周期内的0.1S内前半段时间进行大流量的灌包，后半段时间进行空闲。这样则会导致缓冲区满而导致丢包。
3. iperf3具有-A(affinity)参数，可以将打流程序绑定在一个特定的CPU上，对于大流量灌包有很大的帮助。
## 2.4 额外知识
1. 命令中的-l参数指的是载荷的长度，假如你设置为1400B，发送为UDP报文，其最终发送的报文长度为14(MAC)+20(IP)+8(UDP)+1400(payload) = 1442Byte，在传输过程中是没有MAC头字节的，即MTU1500不包括MAC头。

# 三、LINUX内核线程的使用
有时需要程序来监听和处理某些指定事件，此时经常会做个服务，让其在后台执行，这在应用程序这样的用户态很经常用到，而在Linux Kernel里也会有类似的经历，此时同样可以使用线程来实现，不过它叫内核线程。
LINUX内核线程只能在内核中由其他的线程来创建，而所有的内核线程由kthreadd创建，故而使用ps -ef命令看到所有被[]括起来的内核线程（守护进程）对应的PPID均为2。
内核线程与普通用户态线程除了内核线程没有独立地址空间（其mm成员指向NULL）外，其他的可被调度和被抢占均支持。
``` c?linenums
#include <linux/sched.h>   //wake_up_process()  
#include <linux/kthread.h> //kthread_create()、kthread_run()  
#include <err.h> //IS_ERR()、PTR_ERR()  
```
## 3.1 kthread_create() 创建内核线程
``` c?linenums
#define kthread_create(threadfn, data, namefmt, arg...) \
        kthread_create_on_node(threadfn, data, -1, namefmt, ##arg)
```
## 3.2 kthread_run() 创建并运行内核线程
``` c?linenums
#define kthread_run(threadfn, data, namefmt, ...) \
({ \
        struct task_struct *__k \
                = kthread_create(threadfn, data, namefmt, ## __VA_ARGS__); \
        if (!IS_ERR(__k)) \
                wake_up_process(__k); \
        __k; \
})
```
## 3.3 kthread_stop()，停止指定内核线程
一般是在其他线程中调用停止函数，去停止指定的内核线程。
``` c?linenums
int kthread_stop(struct task_struct *k);
```
## 3.4 kthread_should_stop()，判断线程是否该停止
在指定线程中利用while循环函数调用此函数，判断此线程是否停止。
``` c?linenums
/**
 * kthread_should_stop - should this kthread return now?
 *
 * When someone calls kthread_stop() on your kthread, it will be woken
 * and this will return true.  You should then return, and your return
 * value will be passed through to kthread_stop().
 */
bool kthread_should_stop(void)
{
	return test_bit(KTHREAD_SHOULD_STOP, &to_kthread(current)->flags);
}
```
## 3.5 kthread_bind()，绑定创建好的线程在执行CPU核心上运行
``` c?linenums
void kthread_bind(struct task_struct *k, unsigned int cpu);
```
也可在创建线程的时候调用如下函数在创建的同时一起绑定CPU：
``` c?linenums
struct task_struct *kthread_create_on_cpu(int (*threadfn)(void *data),
                   void *data,
                   unsigned int cpu,
                   const char *namefmt);
```
上述两个代码需要在使用后需要调用kthread_run()里用到的wake_up_process()才能进入运行队列。
### 3.5.1 编译驱动时提示warning : "kthread_create_on_cpu" undefined!
代码中使用了函数`kthread_create_on_cpu`，但是在编译驱动过程中提示，经过查阅结果为：`kthread_create_on_cpu`不是由内核导出的，它是 CPU 热插拔线程使用的内部函数（请参阅参考资料linux/smpboot.h）。如果必须设置线程到特定CPU上，则使用函数kthread_bind进行绑定运行。
### 3.5.2 参考
[linux内核模块：内核方法未定义（kthread_create_on_cpu）](https://qa.1r1g.com/sf/ask/2039076791/#)

## 3.6 实际测试
线程一旦启动起来后，会一直运行，除非该线程主动调用do_exit函数，或者其他的进程调用kthread_stop函数，结束线程的运行。 但如果线程函数正在处理一个非常重要的任务，它不会被中断的。当然如果线程函数永远不返回并且不检查信号，它将永远都不会停止，因此，**==线程函数必须能让出CPU #F44336==**，以便能运行其他线程。同时线程函数也必须能重新被调度运行。在例子程序中，这是**==通过schedule_timeout()函数完成的 #F44336==**。
``` cpp?linenums
// 让CPU调度运行其他线程并等待指定时间后本线程被重新调度，其不改变当前状态
signed long __sched schedule_timeout(signed long timeout) ;

// 其会改变当前状态为可被中断
signed long __sched schedule_timeout_interruptible(signed long timeout) ;
```
在创建的线程中必须调用上述函数，把CPU的工作让出一段时间进行自动类似喂狗的操作，否则会进行提示警告说绑定对应的CPU不能正常使用。
``` c?linenums
#include <linux/module.h>
#include <linux/kthread.h>
#include <linux/slab.h>
static struct task_struct * slam_unbind_thread = NULL;
static int slam_bind_func(void *data)
{
        unsigned int cur_cpu = *((unsigned int *)data);
        printk("[slam_bind_thread/%d] start!\n", cur_cpu);
        while(!kthread_should_stop()){
                printk("I'm in [slam_bind_thread/%d]!\n", cur_cpu);
                schedule_timeout(msecs_to_jiffies(5000));
        }
        printk("[slam_bind_thread/%d] end!\n", cur_cpu);
        return 0;
}
static int slam_unbind_func(void *data)
{
        char *slam_data = kzalloc(strlen(data)+1, GFP_KERNEL);
        strncpy(slam_data, data, strlen(data));
        while(!kthread_should_stop()){
                printk("Unbind Thread:%s(%ld)\n", slam_data, jiffies);
                schedule_timeout(msecs_to_jiffies(5000));
        }
        kfree(slam_data);
        return 0;
}
static __init int kthread_example_init(void)
{
        int cur_cpu;
        unsigned int cpus = num_online_cpus();
        unsigned int bind_thread_params[cpus];
        struct task_struct *slam_bind_threads[cpus];
        slam_unbind_thread = kthread_run(slam_unbind_func, "slam-xinu", "slam_unbind");

        for_each_present_cpu(cur_cpu){
                bind_thread_params[cur_cpu] = cur_cpu;
                slam_bind_threads[cur_cpu] = kthread_create(slam_bind_func, (void *)(bind_thread_params+cur_cpu), "BindThread/%d", cur_cpu);
                kthread_bind(slam_bind_threads[cur_cpu], cur_cpu);
                wake_up_process(slam_bind_threads[cur_cpu]);
        }
        schedule_timeout_interruptible(msecs_to_jiffies(30*1000));
         for(cur_cpu=0;cur_cpu<cpus;cur_cpu++){
              kthread_stop(slam_bind_threads[cur_cpu]);
         }
         return 0;
}

static __exit void kthread_example_exit(void)
{
        if(slam_unbind_thread){
                kthread_stop(slam_unbind_thread);
        }
}
module_init(kthread_example_init);
module_exit(kthread_example_exit);
```
## 3.7 注意
1. 值得一提的是kthread_should_stop函数，我们需要在开启的线程中嵌入该函数并检查此函数的返回值，否则kthread_stop是不起作用的
2. 休眠有两种相关的状态:TASK_INTERRUPTIBLE and TASK_UNINTERRUPTIBLE。它们的惟一却不是处于TASK_UNINTERRUPTIBLE状态的进程会忽略信号，而处于TASK_INTERRUPTIBLE状态的进程如果收到信号会被唤醒并处理信号(然后再次进入等待睡眠状态)。两种状态的进程位于同一个等待队列上，等待某些事件，不能够运行。
3. schedule_time(s*HZ)的参数为节拍数，HZ宏每个系统定义不一样，表示每一秒时钟中断数，如在2.6中为1000，2.4中为100, s为秒单位，例如如果要休眠20ms，则schedule_time(0.02*HZ)就可以了。
4. 在调用kthread_stop函数时，线程函数不能已经运行结束。否则，kthread_stop函数会一直进行等待。在执行kthread_stop的时候，目标线程必须没有退出，否则会Oops。原因很容易理解，当目标线程退出的时候，其对应的task结构也变得无效，kthread_stop引用该无效task结构就会出错。
5。. 线程函数必须能让出CPU，以便能运行其他线程。同时线程函数也必须能重新被调度运行。在例子程序中，这是通过schedule_timeout()函数完成的
## 3.8 查看线程的CPU利用率
可以使用top命令来查看线程（包括内核线程）的CPU利用率。命令如下：` top -p {线程号}`或`ps aux | grep {线程名}`。下面的命令显示所有内核线程：`ps afx`。
## 3.9 参考
1. [内核线程](https://www.jianshu.com/p/b3fed01aa01a)
2. [Linux内核多线程实现方法 —— kthread_create函数【转】](https://www.cnblogs.com/sky-heaven/p/8204631.html)
2. [kthread_create 简单使用](https://blog.csdn.net/iamliuyanlei/article/details/9326119)
# 四、LINUX内核任务延迟队列

# 五、LINUX内核定时器

# 六、LINUX内核延时函数
内核中涉及的延时主要有两种实现方式：**忙等待**或者**睡眠等待**。前者阻塞程序，在延时时间到达前一直占用CPU，而后者是将进程挂起（置进程于睡眠状态并释放CPU资源）。前者一般用在延时时间在毫秒以内的精确延时，后者用于延时时间在毫秒以上的长延时。为了充分利用 CPU 资源，使系统有更好的吞吐性能，在对延迟时间的要求并不是很精确的情况下，睡眠等待通常是值得推荐的。

## 6.1 忙等待短延时 
内核中提供了如下3个函数用于纳秒、微秒和毫秒级的延时：
``` c?linenums
void ndelay(unsigned long nsecs); 
void udelay(unsigned long usecs); 
void mdelay(unsigned long msecs); //一般不建议直接使用mdelay()函数，这将无谓地耗费 CPU 资源
```
上述延迟的实现原理本质上是忙等待，它根据 CPU 频率进行一定次数的循环。其本质同如下代码：
``` c?linenums
void delay(unsigned int time) 
{ 
 while (time--); 
}
```
## 6.2 忙等待长延时函数
内核中进行延迟的一个很直观的方法是比较当前的 jiffies 和目标 jiffies（设置为当前 jiffies 加上时间间隔的 jiffies），直到未来的 jiffies 达到目标 jiffies。

利用jiffies和time_befor实现延时100个jiffies和2秒的代码：
``` c?linenums
 /*延迟 100 个 jiffies*/ 
 unsigned long delay = jiffies + 100; 
 while (time_before(jiffies, delay)); 
  
 /*再延迟 2s*/ 
 unsigned long delay = jiffies + 2*HZ; 
 while (time_before(jiffies, delay)); 
```
其中，time_befor()只是一个函数宏，与其对应的还有一个time_after():
``` c?linenums
#define time_after(a,b) \ 
 (typecheck(unsigned long, a) && \ 
 typecheck(unsigned long, b) && \ 
 ((long)(b) - (long)(a) < 0)) 

#define time_before(a,b) time_after(b,a)
```
## 6.3 睡眠短延时
### 6.3.1 sleep类延时函数
下述函数将使得调用它的进程睡眠参数指定的时间，受系统 HZ 和进程调度的影响，msleep()类似函数的精度是有限的。msleep()、ssleep()不能被打断，而msleep_interruptible()则可以被打断。
``` cpp?linenums
void msleep(unsigned int millisecs); 
unsigned long msleep_interruptible(unsigned int millisecs); 
void ssleep(unsigned int seconds); 
```
### 6.3.2 schedule类睡眠延时函数
``` c?linenums
signed long  schedule_timeout_interruptible(signed long timeout);
signed long  schedule_timeout_uninterruptible(signed long timeout) 
```
`schedule_timeout_uninterruptible()`和`schedule_timeout_interruptible`是将当前任务睡眠指定的jiffies之后重新被调度执行，它的实现原理是向系统添加一个定时器，在定时器处理函数中唤醒参数对应的进程。上一小节的sleep类函数的底层实现也是调用`schedule_timeout_uninterruptible`这个函数进行实现的。
``` c?linenums
void msleep(unsigned int msecs) 
{ 
	unsigned long timeout = msecs_to_jiffies(msecs) + 1; 
	while (timeout) 
		timeout = schedule_timeout_uninterruptible(timeout); 
} 
unsigned long msleep_interruptible(unsigned int msecs) 
{ 
	unsigned long timeout = msecs_to_jiffies(msecs) + 1; 
	while (timeout && !signal_pending(current)) 
		timeout = schedule_timeout_interruptible(timeout); 
	return jiffies_to_msecs(timeout); //返回剩余的延时时间
}
signed long _ _sched schedule_timeout_interruptible(signed long timeout) 
{ 
	_ _set_current_state(TASK_INTERRUPTIBLE); //置进程状态为 TASK_INTERRUPTIBLE
	return schedule_timeout(timeout); 
} 
signed long _ _sched schedule_timeout_uninterruptible(signed long timeout) 
{ 
	_ _set_current_state(TASK_UNINTERRUPTIBLE); //置进程状态为 TASK_UNINTERRUPTIBLE
	return schedule_timeout(timeout); 
} 
```

**注意**：`chedule_timeout` 要求调用者首先设置当前的进程状态。为获得一个不可中断的延迟, 可使用 `TASK_UNINTERRUPTIBLE`代替。如果你忘记改变当前进程的状态,，调用 `schedule_time` 如同调用 `shcedule`，建立一个不用的定时器。

### 6.3.3 sleep_on类，在等待队列上睡眠的延时函数
函数可以将当前进程添加到等待队列中，从而在等待队列上睡眠。当超时发生时，进程将被唤醒（后者可以在超时前被打断）：
``` c?linenums
sleep_on_timeout(wait_queue_head_t *q, unsigned long timeout); 
interruptible_sleep_on_timeout(wait_queue_head_t*q, unsigned long timeout); 
```
# 常用的网站
1. [Linux内核API](https://deepinout.com/linux-kernel-api/linux-kernel-api-process-management/linux-kernel-api-pro)(网站包含有内核API接口的中文注释，可以用于查看源码)
2. 
