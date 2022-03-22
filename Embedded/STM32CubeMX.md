---
title: STM32 Learning
renderNumberedHeading: true
grammar_cjkRuby: true
tags: 'STM32,STM32CubeMX,keil for arm'
---

# SYS Mode and Configuration
![enter description here](./images/1647493843556.png)




# TIMx Mode and configuration
## TIMx PWM Generation CHx

``` c
HAL_TIM_PWM_Start(&htim1,TIM_CHANNEL_1);//开启PWM波形输出
__HAL_TIM_SET_COMPARE(&htim1, TIM_CHANNEL_1, 500); //改变占空比，500/1000=50%
```

# JDY-31

![enter description here](./images/1647953581837.png)

# USART
## Serial redirection
### Plan A
``` c
#if 1
#pragma import(__use_no_semihosting)             
                
struct __FILE 
{ 
	int handle; 

}; 

FILE __stdout;       
//定义_sys_exit()以避免使用半主机模式    
void _sys_exit(int x) 
{ 
	x = x; 
} 
//重定义fputc函数 
int fputc(int ch, FILE *f)
{      
	while((USART1->SR&0X40)==0);//循环发送,直到发送完毕   
    USART1->DR = (uint8_t) ch;      
	return ch;
}
#endif     


```

### Plan B

``` c
#ifdef __GNUC__
  #define PUTCHAR_PROTOTYPE int __io_putchar(int ch)
#else
 #define PUTCHAR_PROTOTYPE int fputc(int ch, FILE *f)
#endif

PUTCHAR_PROTOTYPE
{
 HAL_UART_Transmit(&huart1,(uint8_t *)&ch,1,0xFFFF);//阻塞方式打印
  return ch;
}
```

### Program Comparison
printf重定向到串口就必须关闭半主机模式。半主机模式简而言之就是将电脑的键盘和显示屏作为(scanf,printf)输入输出外设。第一种方法使用微库将默认关闭半主机模式，但微库会使代码量优化减少，可能造成代码的稳定性降低，微库是面向深层嵌入式开发的（这句话可能是说微库是面向最终产品的，做实验与练习没必要用）。而第二种方法直接手动取消半主机模式，更加方便。