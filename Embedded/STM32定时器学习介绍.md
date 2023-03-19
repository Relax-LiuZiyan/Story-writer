---
title: STM32定时器学习介绍
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
tags: 'PWM,输出比较,输入捕获,PWM捕获'
---

[toc]
# 输出比较模式
此模式可以说是PWM输出模式的增强型，PWM输出模式可以实现的此模式都可以实现。由于PWM输出模式是通过CNT寄存器与CRR寄存器的比较完成高低电平（占空比）的实现，而具体频率的实现是由ARR寄存器和定时器分频系数所决定的，每一个定时器中的由多个通道，每个通道的CNT和CRR是独立的，但是ARR是公用的，因此它们输出的频率都是相同的。
而输出比较模式，虽然也是CNT和CCR的比较进行输出电平，但是区别在于频率的实现是由于CCR的值不是固定死的，而是一直动态改变的。

