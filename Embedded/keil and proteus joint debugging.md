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
4. ![enter description here](./images/1646728700458.png)
5. ![enter description here](./images/1646728722519.png)


## proteus
1. ![enter description here](./images/1646728855416.png)
2. ![enter 撒旦here](./images/1646728999740.png) 
