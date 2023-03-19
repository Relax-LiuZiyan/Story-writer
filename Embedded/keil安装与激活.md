---
title: keil安装与激活
tags: 新建,模板,小书匠
slug: storywriter/grammar
renderNumberedHeading: true
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

[toc]
# MDK for Arm
mdk软件下载地址为：[MDK-ARM Version 5.37](https://www.keil.com/demo/eval/arm.htm)，但是需要注意一点MDK在V537以后采用的编译器为V6版本，不支持向前兼容编译器V5的工程，因此不推荐使用MDK_V537，这里我使用的版本为MDK_V536，下载地址为：https://armkeil.blob.core.windows.net/eval/MDK536.EXE ,推荐使用迅雷下载。

器件包下载网址为[MDK5 Software Packs](https://www.keil.com/dd2/pack/)，下载STM32F1即可。

![器件包F1和G4](./images/1667057183444.png)
先安装MDK536，然后再安装前面两个器件包，keygan为注册机用于pojie MDK使用，==**由于keygan为破解软件在使用前关闭所有的杀毒软件** #F44336==！

![安装需要的四个软件](./images/1667057600377.png)
注意安装MDK时，文件夹地址可以任意选择，但是注意一定不能有中文，最好使用`_`（下划线）替代空格。

![enter description here](./images/1667051360220.png)
用户信息内容随便填写即可。

![用户信息](./images/1667057285215.png)
安装MDK期间，可能会提示安装驱动，默认安装即可。软件安装后，就是要进行器件安装包，点击器件包后会自动寻找MDK的pack文件夹，一直点下一步即可，F1的器件包安装即可。

![器件包安装](./images/1667057529235.png)
破解MDK，==**MDK和keygan都一定要管理员身份运行** #F44336==。

![一定要管理员身份运行](./images/1667057718282.png)

![获取license信息](./images/1667057878568.png)
在序号1处复制CID信息到序号2处，在序号3处选择Target为ARM，然后点击序号4处获取信息，把序号5处的信息复制到6处，点击序号7，最终可以看到序号8处提示注册时间到2032年，代表注册成功。

![注册信息](./images/1667058049743.png)

若描述不够详细，可以参考注册视频：https://github.com/Relax-LiuZiyan/Story-writer/tree/master/Embedded/videos/MDK注册过程.mp4

# 所有资料下载网址
