---
title: C语言学习
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
tags: 'C语言,函数,指针'
---
# 结构体函数成员于回调函数
1. 在C语言结构体内部定义一个指向函数的指针；
2. 在具体“对象”初始化的时候，将结构体中的指向函数的指针指向具体实现功能的函数;
3. 在这个函数需要调用时进行调用

![结构体回调函数实现](./images/1668496375664.png)
通过指向函数的指针指向具体的实现函数，后面再来统一调用，这种方式就是我们在C语言里面常说的回调函数。回调函数的应用有很多，比如当我们在做单片机中断的时候，就可以将单片机中断处理函数写成一个指向函数的指针，在每次单片机程序初始化的时候，将中断里面的函数指针指向具体的中断处理函数，等到中断产生的时候，单片机就可以去调用这个指向函数的指针了。而在程序运行的时候，我们也可以随时去修改这个指向函数的指针指向的对象，使程序更加灵活。

![代码实现](./images/1668497103870.png)
