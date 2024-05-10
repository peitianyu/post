---
title: RTOS多任务访问同一个UART的操作方法
date: 2021-11-1 12:30:12
categories:
  - 工程
  - 嵌入式
tags:
  - cpp
  - 嵌入式
---

### 参考网址:

[RTOS多任务访问同一个UART的操作方法](https://mp.weixin.qq.com/s/4wWhF-Pm0vC8O0Qt5eiN-Q)

在RTOS多任务编程的时候，同一个硬件（比如UART、I2C等）被多个任务访问的情况比较多，如果不合理处理，就会导致“混乱”的局面。

处理“混乱”局面的方法比较多，下面基于**FreeRTOS**，以UART为例讲讲常见的***互斥***、***队列***这两种方法。

### **互斥访问方法**

```c
//创建互斥量资源
SemaphoreHandle_t xSemaphore = NULL;
xSemaphore = xSemaphoreCreateMutex();

void TaskA(void *pvParameters)
{
  for(;;)
  {
    //占用资源
    if(xSemaphoreTake(xSemaphore, 10 ) == pdTRUE)
    {
      //使用资源(发送数据)
      USART_SendNByte();
      //释放资源
      xSemaphoreGive(xSemaphore);
    }
  }
}
```

**信号量与互斥量区别**：

**信号量**：多个任务同步使用某个资源；

一个任务完成某个动作后通过信号告诉别的任务，别的任务才可以执行某些动作；

**互斥量**：多任务互斥使用某个资源；

一个任务占用某个资源，那么别的任务就无法访问，直到该任务离开，其他任务才可以访问该资源；

### **队列操作方法**

队列操作方法就是FIFO，先入先出的原理。比如：**任务A要使用UART发送一串数据，将其加入队列； 接着任务B也要使用UART发送一串数据**。

```c
QueueHandle_t xQueue;
xQueue = xQueueCreate(QUEUE_LENGTH, QUEUE_ITEM_SIZE);

xTaskCreate(UART_Send_Task, "UART_Send", STACK_SIZE, NULL, TASK_PRIORITY, NULL);

void TaskA(void *pvParameters)
{
  for(;;)
  {
    //任务相关操作

    //加入队列
    xQueueSend(xQueue, &TaskA_Buf, 10)
  }
}

void TaskB(void *pvParameters)
{
  for(;;)
  {
    //任务相关操作

    //加入队列
    xQueueSend(xQueue, &TaskB_Buf, 10)
  }
}

void UART_Send_Task(void *pvParameters)
{
  for(;;)
  {
    //循环读取队列BUF
    if(xQueueReceive(xQueue, &Buf, 10) == pdTRUE)
    {
      USART_SendNByte(&Buf);
    }
  }
}
```

