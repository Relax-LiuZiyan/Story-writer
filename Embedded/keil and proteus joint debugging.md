---
title: keil and proteus joint debugging
tags: Keil for C51,proteus,debug
renderNumberedHeading: true
grammar_cjkRuby: true
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
2. ![enter description here](./images/1646728999740.png) 

->居中显示的文字<-
