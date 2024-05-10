---
title: 树莓派CAN
date: 2021-12-10 12:30:12
categories:
  - 工程
  - 嵌入式
tags:
  - 嵌入式
  - sch
---

# 参考网址:

[树莓派CAN通讯教程 - MCP2515_j353838430的博客-CSDN博客_树莓派can通信](https://blog.csdn.net/j353838430/article/details/119486631?ops_request_misc=%7B%22request%5Fid%22%3A%22163877530616780271559996%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163877530616780271559996&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-3-119486631.pc_search_mgc_flag&utm_term=树莓派mcp2515&spm=1018.2226.3001.4187)

[(86条消息) SPI协议、MCP2515裸机驱动详解----主流SPI总线接口原理_csshuke的专栏-CSDN博客_mcp2515 spi极性](https://blog.csdn.net/csshuke/article/details/113186248?ops_request_misc=&request_id=&biz_id=102&utm_term=mcp2515原理图&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-4-113186248.pc_search_mgc_flag&spm=1018.2226.3001.4187)

mcp2515模块资料:http://pan.baidu.com/s/1hsi3I4k

# 简介

使用mcp2515实现的spi转can通信

# 原理图

![image-20211210213025816.png](https://s2.loli.net/2021/12/10/UYg8ehqIX3EOAkb.png)

需要注意的是这里的片选,也就是说可以通过片选CS信号实现一个spi转多个can通信,INT引脚用于中断缓存数据,当接收到数据时会将数据先放入缓冲区

# 环境准备

```shell
# 安装cantools
sudo apt install can-utils
pip3 install cantools
# 检测挂载
ifconfig
# 查看是否进入read状态
sudo ip -s -d link show can0
# 使能树莓派SPI并加载MCP2515内核驱动
dtparam=spi=on
dtoverlay=mcp2515-can0,oscillator=16000000,interrupt=25
dtoverlay=spi1-1cs
# 重启
sudo reboot -h now
```

```shell
# 关闭can0
sudo ip link set can0 down
# 设置波特率
sudo ip link set can0 type can bitrate 1000000
# 开启can0
sudo ip link set can0 up
# 查看
sudo ip -s -d link show can0
# 接收报文
candump any,0:0,#FFFFFFFF
# 过滤
candump can0,620:1fffffff,255:7ff
# 发送报文
cansend can0 123#1122334455667788
# 设置回环 波特率 250K ,用于测试can通路,在没有其它硬件连接测试的情况下，可以设定成回环，自发自收
sudo ip link set can0 type can bitrate 500000 loopback on
```

# C语言使用

```c++
// test.cpp

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <net/if.h>
#include <sys/ioctl.h>
#include <sys/socket.h>
#include <linux/can.h>
#include <linux/can/raw.h>
#define command "ip link set can0 type can bitrate 1000000"//将CAN0波特率设置为500K
#define up "ifconfig can0 up"//打开CAN0
#define down "ifconfig can0 down"//关闭CAN0

int can_init()
{
    //关闭CAN设备，设置波特率后，重新打开CAN设备
    system(down);
    system(command);
    system(up);
    return 0;
}

int can_send(can_frame frame)
{
    int s, nbytes;
    struct sockaddr_can addr;
    struct ifreq ifr;
    //创建套接字
    s = socket(PF_CAN, SOCK_RAW, CAN_RAW);
    strcpy(ifr.ifr_name, "can0" );
    //指定 can0 设备
    ioctl(s, SIOCGIFINDEX, &ifr); 
    addr.can_family = AF_CAN;
    addr.can_ifindex = ifr.ifr_ifindex;
    //将套接字与 can0 绑定
    bind(s, (struct sockaddr *)&addr, sizeof(addr));
    //发送 frame[0]
    nbytes = write(s, &frame, sizeof(frame));
    if(nbytes != sizeof(frame))
    {
        printf("Send Error frame[0]\n!");
    }
    close(s);
    return 0;
}

int can_receive(struct can_frame * r_frame,unsigned int filter_id)
{
    int s, nbytes = 0;
    struct sockaddr_can addr;
    struct ifreq ifr;
    struct can_frame frame;
    struct can_filter rfilter;

    // Initial fram
    memset(&frame,0,sizeof(can_frame));
    //创建套接字
    s = socket(PF_CAN, SOCK_RAW, CAN_RAW);
    strcpy(ifr.ifr_name, "can0" );
    //指定 can0 设备
    ioctl(s, SIOCGIFINDEX, &ifr); 
    addr.can_family = AF_CAN;
    addr.can_ifindex = ifr.ifr_ifindex;
    //将套接字与 can0 绑定
    bind(s, (struct sockaddr *)&addr, sizeof(addr));
    //设置过滤规则，取消当前注释为禁用过滤规则，即不接收所有报文，
    // 不设置此项（即如当前代码被注释）为接收所有ID的报文。
    if (filter_id != 0)
    {
        rfilter.can_id   = 0x123;
        // CAN_EFF_MASK | CAN_SFF_MASK
        rfilter.can_mask = CAN_SFF_MASK;
        setsockopt(s, SOL_CAN_RAW, CAN_RAW_FILTER, &rfilter, sizeof(rfilter));
    }
    while (nbytes == 0)
    {
        //接收总线上的报文保存在frame中
        nbytes = read(s, &frame, sizeof(frame));
    }
    *r_frame = frame;
#ifdef MSG_DEBUG
    printf("the nbytes:%d\n", nbytes);
    printf("length:%d", sizeof(frame));
    printf("ID=0x%X DLC=%d\n", frame.can_id, frame.can_dlc);
    printf("data0=0x%02x\n",frame.data[0]);
    printf("data1=0x%02x\n",frame.data[1]);
    printf("data2=0x%02x\n",frame.data[2]);
    printf("data3=0x%02x\n",frame.data[3]);
    printf("data4=0x%02x\n",frame.data[4]);
    printf("data5=0x%02x\n",frame.data[5]);
    printf("data6=0x%02x\n",frame.data[6]);
    printf("data7=0x%02x\n",frame.data[7]);
#endif
    return 0;
}

int led_ctl_on(void)
{
    struct can_frame frame;
    memset(&frame, 0, sizeof(can_frame));
    frame.can_id = 0x101;
    frame.can_dlc = 8;
    frame.data[0] = 1;
    can_send(frame);
    return 0;
}

int led_ctl_off(void)
{
    struct can_frame frame;
    memset(&frame, 0, sizeof(can_frame));
    frame.can_id = 0x101;
    frame.can_dlc = 8;
    frame.data[0] = 2;
    can_send(frame);
    return 0;
}

float can_get_vol(void)
{
    float vol_vle = 0;
    struct can_frame frame;
    memset(&frame, 0, sizeof(can_frame));
    // wait until can frame 100 received
    can_receive(&frame,0);
    printf("###############################\n");
    printf("length:%d", sizeof(frame));
    printf("ID=0x%X DLC=%d\n", frame.can_id, frame.can_dlc);
    printf("data0=0x%02x\n",frame.data[0]);
    printf("data1=0x%02x\n",frame.data[1]);
    printf("data2=0x%02x\n",frame.data[2]);
    printf("data3=0x%02x\n",frame.data[3]);
    printf("data4=0x%02x\n",frame.data[4]);
    printf("data5=0x%02x\n",frame.data[5]);
    printf("data6=0x%02x\n",frame.data[6]);
    printf("data7=0x%02x\n",frame.data[7]);

    vol_vle = (float)frame.data[0]/50;
    return vol_vle;
}

int main(int argc, char* argv[])
{
    char control_str[15]; 
    float vol_val = 0;

    if (argc < 2) {
        printf("can_control service_type\n"
            "    example: ./can_control led_off/led_on/get_vol\n"
            );
        return 0;
    }
    strcpy(control_str,argv[1]);

    // debug
    printf("Argc : %d\n",argc);
    printf("Argv : %s\n , %s\n",argv[0], argv[1]);

    // can_init();
    if (strcmp(control_str,"led_off")==0)
    {
        led_ctl_off();
    }
    else if (strcmp(control_str,"led_on")==0)
    {
        led_ctl_on();
    }
    else if (strcmp(control_str,"get_vol")==0)
    {
        vol_val = can_get_vol();
        printf("Voltage is : %5.2f V\n", vol_val);
    }
    else
    {
        /* Do nothing */
    }
    return 0;
}
```

