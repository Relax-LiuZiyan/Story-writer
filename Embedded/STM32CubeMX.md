---
title: STM32CubeMX
renderNumberedHeading: true
grammar_cjkRuby: true
tags: 'STM32,STM32CubeMX'
---
[toc]
# Delay_us

``` nginx
#define CPU_FREQUENCY_MHZ    72		// STM32时钟主频
void Delay_us(__IO uint32_t delay)
{
    int last, curr, val;
    int temp;

    while (delay != 0)
    {
        temp = delay > 900 ? 900 : delay;
        last = SysTick->VAL;
        curr = last - CPU_FREQUENCY_MHZ * temp;
        if (curr >= 0)
        {
            do
            {
                val = SysTick->VAL;
            }
            while ((val < last) && (val >= curr));
        }
        else
        {
            curr += CPU_FREQUENCY_MHZ * 1000;
            do
            {
                val = SysTick->VAL;
            }
            while ((val <= last) || (val > curr));
        }
        delay -= temp;
    }
}
```

# SYS Mode and Configuration
![enter description here](./images/1647493843556.png)




# TIMx Mode and configuration
## TIMx PWM Generation CHx

``` c
HAL_TIM_PWM_Start(&htim1,TIM_CHANNEL_1);//开启PWM波形输出
__HAL_TIM_SET_COMPARE(&htim1, TIM_CHANNEL_1, 500); //改变占空比，500/1000=50%
```

# ADC Multi-channel acquisition

If you use the ADC of the proteus to collect the voltage, you can only use the non-DMA mode.

## Non-DMA Mode


### reference 

1. [STM32CUBEMX配置多通道ADC](https://blog.csdn.net/weixin_43287964/article/details/116518639?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164914248316782092955785%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=164914248316782092955785&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-1-116518639.142^v5^pc_search_insert_es_download,157^v4^control&utm_term=stm32cubemx+adc%E5%A4%9A%E9%80%9A%E9%81%93&spm=1018.2226.3001.4187)

## DMA Mode

