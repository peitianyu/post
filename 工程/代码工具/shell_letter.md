---
title: Mk60+shell_letter
date: 2021-11-26 12:30:12
categories:
  - 工程
  - 代码工具
tags:
  - 嵌入式
---

# 参考网址:

[letter-shell | 一个功能强大的嵌入式shell_Mculover666的博客（嵌入式）-CSDN博客_letter shell](https://blog.csdn.net/Mculover666/article/details/105141286?ops_request_misc=%7B%22request%5Fid%22%3A%22163790121416780357282944%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163790121416780357282944&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-105141286.pc_search_mgc_flag&utm_term=嵌入式shell&spm=1018.2226.3001.4187)

# 实例

```c
//shell_port.c
/**
 * @brief	shell移植到STM32L431时的接口实现
 * @author	mculover666
 * @date	2020/03/27 
*/

#include "shell.h"
#include "uart.h"
#include "shell_port.h"

/* 1. 创建shell对象，开辟shell缓冲区 */
Shell shell;
char shell_buffer[512];
/* 2. 自己实现shell写函数 */

//shell写函数原型：typedef void (*shellWrite)(const char);
/**
 * @brief 用户shell写
 * 
 * @param data 数据
 * @param len 数据长度
 * 
 * @return short 实际写入的数据长度
 */
short userShellWrite(char *data, unsigned short len)
{
		for(int i = 0;i<len;i++)
		{
				UART_WriteByte(HW_UART0, data[i]);
		}
    return len;
}

/* 3. 编写初始化函数 */
void User_Shell_Init(void)
{
	//注册自己实现的写函数
    shell.write = userShellWrite;
	
	//调用shell初始化函数
    shellInit(&shell, shell_buffer, 512);
}
```

```c
// shell_port.h
#ifndef _SHELL_PORT_H_
#define	_SHELL_PORT_H_

#include "shell.h"

/* 将shell定义为外部变量，在串口中断回调函数中还要使用 */
extern Shell shell;

/* 声明自己编写的初始化函数 */
void User_Shell_Init(void);

#endif /* _SHELL_PORT_H_ */
```

```c
#include "gpio.h"
#include "common.h"
#include "uart.h"
#include "cpuidy.h"
#include "shell_port.h"
uint16_t recv_buf = 0;

/* USER CODE BEGIN 0 */
int test(int i, char ch, char *str)
{
    printf("input int: %d, char: %c, string: %s\r\n", i, ch, str);
	
		return 0;
}

static void UART_RX_ISR(uint16_t byteReceived)
{
		//调用shell处理数据的接口
		recv_buf = byteReceived;
		shellHandler(&shell, recv_buf);
}
int main(void)
{
    DelayInit();
    uint32_t instance = GPIO_QuickInit(HW_GPIOE, 0, kGPIO_Mode_OPP);
    UART_QuickInit(UART0_RX_PA01_TX_PA02, 115200);
    /*  配置UART 中断配置 打开接收中断 安装中断回调函数 */
    UART_CallbackRxInstall(HW_UART0, UART_RX_ISR);
    /* 打开串口接收中断功能 IT 就是中断的意思*/
    UART_ITDMAConfig(HW_UART0, kUART_IT_Rx, true);
    User_Shell_Init();
    while(1)
    {
        /* 闪烁小灯 */
        GPIO_ToggleBit(HW_GPIOE, 0);
        DelayMs(500);
    }
}

//导出到命令列表里
SHELL_EXPORT_CMD(SHELL_CMD_PERMISSION(0)|SHELL_CMD_TYPE(SHELL_TYPE_CMD_FUNC), test, test, test);
```

