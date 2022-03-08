---
title: keil and proteus joint debugging
tags: Keil for C51,proteus,debug
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

# Preparation

1. Keil for C51
2. proteus 8.9 SP2
3. VDM51.dll
 
# start woking

## keil for c51
1. Put VDM51.dll into T:\Keil_For_C51\C51\BIN folder
2. Modify the T:\Keil_For_C51\TOOLS.INI configuration file
3. Add the content `TDRV13=BIN\VDM51.DLL ("Proteus VSM Monitor-51 Driver")`.
4. ->![enter description here](./images/1646728700458.png)<-
5. ->![enter description here](./images/1646728722519.png)<-


## proteus
1. ->![enter description here](./images/1646728855416.png)<-
2. ->![enter 撒旦here](./images/1646728999740.png)<- 

# reference 
1. [keil和Proteus联动调试实现Hello world](https://blog.csdn.net/weixin_45616775/article/details/105060552?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164672671416780271926404%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=164672671416780271926404&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-105060552.pc_search_result_control_group&utm_term=keil%E5%92%8CProteus%E8%81%94%E5%8A%A8%E8%B0%83%E8%AF%95&spm=1018.2226.3001.4187)
2. [Keil uvision5 C51和Proteus 8.7professional的联动调试设置](https://blog.csdn.net/qq_43610788/article/details/104279424?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164672671416780271948705%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=164672671416780271948705&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-5-104279424.pc_search_result_control_group&utm_term=keil%E5%92%8CProteus%E8%81%94%E5%8A%A8%E8%B0%83%E8%AF%95&spm=1018.2226.3001.4187)