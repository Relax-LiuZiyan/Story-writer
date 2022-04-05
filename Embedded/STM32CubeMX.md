---
title: STM32CubeMX
renderNumberedHeading: true
grammar_cjkRuby: true
tags: 'STM32,STM32CubeMX'
---

# SYS Mode and Configuration
![enter description here](./images/1647493843556.png)




# TIMx Mode and configuration
## TIMx PWM Generation CHx

``` c
HAL_TIM_PWM_Start(&htim1,TIM_CHANNEL_1);//开启PWM波形输出
__HAL_TIM_SET_COMPARE(&htim1, TIM_CHANNEL_1, 500); //改变占空比，500/1000=50%
```

# ADC Multi-channel acquisition
## Non-DMA Mode

## DMA Mode