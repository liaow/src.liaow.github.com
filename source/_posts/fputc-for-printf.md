title: STM32串口输出printf调试信息
date: 2016-6-3 10:18:23
comments: true
categories: ARM
tags:
- STM32
- MDK
---

IDE为MKD5 ARM版本。
MCU为ST公司STM32F4系列。
本程序适用于大部分ARM串口调试输出printf，请读者自行测试。

### 一、Initialization
系统初始化，略。
串口初始化，略。
 <!--more-->
 
### 二、fputc for printf
1. 在合适位置定义实现fputc函数，如main.c
``` c
int fputc(int ch, FILE *f) {
    /* Send over usart */
    USART_Putc(USART6, ch);

    /* Return character back */
    return ch;
}
```

2. 串口函数USART_Putc示例：
``` c
static __INLINE void TM_USART_Putc(USART_TypeDef* USARTx, volatile char c) {
    /* Check USART if enabled */
    if ((USARTx->CR1 & USART_CR1_UE)) { 
        /* Wait to be ready, buffer empty */
        USART_WAIT(USARTx);
        /* Send data */
        USARTx->DR = (uint16_t)(c & 0x01FF);
        /* Wait to be ready, buffer empty */
        USART_WAIT(USARTx);
    }
}
```
到此，即可使用串口工具查看程序printf输出信息了。



   
