---
title: 罗技遥控
date: 2021-11-3 12:30:12
categories:
  - 工程
  - 代码工具
tags:
  - 嵌入式
---

# 参考网址:

[罗技F310与F710游戏手柄驱动（附C++源码）_冬瓜-CSDN博客](https://blog.csdn.net/weixin_44793491/article/details/120494536?ops_request_misc=%7B%22request%5Fid%22%3A%22163868705416780265496202%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=163868705416780265496202&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-2-120494536.pc_search_mgc_flag&utm_term=罗技f710手柄+ubuntu驱动&spm=1018.2226.3001.4187)

# 代码

```c
// Logitech_controller.h

/* define ----------------------------------------------------------------*/
#ifndef Logitech_DRIVER
#define Logitech_DRIVER

/* Includes ---------------------------------------------------------------*/
#include <stdlib.h>
#include <iostream>
#include <unistd.h>
#include <fcntl.h>
#include <errno.h>
#include <linux/input.h>
#include <string.h>
#include <stdio.h>
#include <map>

#define JSKEY_A     0x001
#define JSKEY_B     0x101
#define JSKEY_X     0x201
#define JSKEY_Y     0x301

#define JSKEY_LB    0x401
#define JSKEY_RB    0x501
#define JSKEY_BACK  0x601
#define JSKEY_START 0x701
#define JSKEY_HOME  0x801

#define JSKEY_LT 0x202
#define JSKEY_RT 0x502

#define JSKEY_CROSS_X 0x602
#define JSKEY_CROSS_Y 0x702
#define JSKEY_LEFTSTICK_X 0x002
#define JSKEY_LEFTSTICK_Y 0x102
#define JSKEY_RIGHTSTICK_X 0x302
#define JSKEY_RIGHTSTICK_Y 0x402

#define JSKEY_PRESS 0x001    
#define JSKEY_RELEASE 0x0    

#define JSKEY_CROSS_LOW_VALUE 0xffff8001 
#define JSKEY_CROSS_HIGH_VALUE 0x7fff   

using namespace std;

class Logitech{
private:
    char *dev;
    ssize_t n;
    int fd;
    int buf[2];
public:
    Logitech(char* device);
    map<int, int> Keystate_map;
    int  init();
    void listen_input();
    void print_key_state();
};
#endif //Logitech_DRIVER

```

```
// Logitech_controller.c 
#include "Logitech_controller.h"

using namespace std;

Logitech::Logitech(char* device)
{
    dev = device;
    memset(buf, 0, sizeof buf);
}

int Logitech::init()
{
    fd = open(dev, O_RDONLY);
    if (fd == -1) {
        fprintf(stderr, "Cannot open %s: %s.\n", dev, strerror(errno));
        return EXIT_FAILURE;
    }
    /*Key Status*/

    /* 0 is released */
    /* 1 is press    */
    Keystate_map[JSKEY_A] =0;
    Keystate_map[JSKEY_B] =0;
    Keystate_map[JSKEY_X] =0;
    Keystate_map[JSKEY_Y] =0;

    /* 0 is released */
    /* 1 is press    */
    Keystate_map[JSKEY_LB] =0;
    Keystate_map[JSKEY_RB] =0;
   
    /* 0 is released */
    /* 1 is press    */
    Keystate_map[JSKEY_BACK] =0;
    Keystate_map[JSKEY_START] =0;
    Keystate_map[JSKEY_HOME] =0;
    
    /*  0 is released */
    /* -1 is the left or up button is pressed */
    /*  1 is the right or down button is pressed*/
    Keystate_map[JSKEY_CROSS_X] =0;
    Keystate_map[JSKEY_CROSS_Y] =0;

    /* the result is the value of the key(0~99)*/
    Keystate_map[JSKEY_LT] =0;
    Keystate_map[JSKEY_RT] =0;

    /* the result is the value of the key(-100~100)*/
    Keystate_map[JSKEY_LEFTSTICK_X] =0;
    Keystate_map[JSKEY_LEFTSTICK_Y] =0;
    Keystate_map[JSKEY_RIGHTSTICK_X] =0;
    Keystate_map[JSKEY_RIGHTSTICK_Y] =0;

    return 0;
}

void Logitech::listen_input()
{
    while (1) {
        memset(buf, 0, sizeof buf);
        n = read(fd, &buf, sizeof buf);
        n = n / sizeof(int);
        if (n == (ssize_t)-1) {
            if (errno == EINTR)
                continue;
            else
                break;
        }

        unsigned short btn = buf[1] >> 16;
        short val = (short)(buf[1] & 0xffff);

         /*Test for button ID*/
         //cout<<"0x"<<hex<<btn<<endl;

        if (btn == JSKEY_LT || btn == JSKEY_RT)
        {
            unsigned short prs_val = val + 32768;
            val = (unsigned short) (((long)prs_val)*100/65536);
            Keystate_map[btn]= val;
        }
        else if (btn == JSKEY_LEFTSTICK_X || btn == JSKEY_LEFTSTICK_Y ||
                 btn == JSKEY_RIGHTSTICK_X || btn == JSKEY_RIGHTSTICK_Y)
        {
            /* y-axis reverse */
            if(btn==JSKEY_LEFTSTICK_Y||btn == JSKEY_RIGHTSTICK_Y)
            {val=(-1)*val;}
            val = val*100/32767;
            Keystate_map[btn]= val;
        }
        else
        {
            switch (val)
            {
            case JSKEY_PRESS:
                Keystate_map[btn]=1;
                break;
            case JSKEY_RELEASE:
                Keystate_map[btn]=0;
                break;
            case JSKEY_CROSS_LOW_VALUE:
                Keystate_map[btn]=-1;
                break;
            case JSKEY_CROSS_HIGH_VALUE:
                Keystate_map[btn]=1;
                break;
            default:
                break;
            }
            /* y-axis reverse */
            if(btn==JSKEY_CROSS_Y)
            {Keystate_map[btn]=(-1)*Keystate_map[btn];}
        }
        print_key_state();
    }
}

void Logitech::print_key_state()
{
   cout<<endl;
   cout<<"JSKEY_A = "<<Keystate_map[JSKEY_A]<<endl;
   cout<<"JSKEY_B = "<<Keystate_map[JSKEY_B]<<endl;
   cout<<"JSKEY_X = "<<Keystate_map[JSKEY_X]<<endl;
   cout<<"JSKEY_Y = "<<Keystate_map[JSKEY_Y]<<endl;

   cout<<"JSKEY_LB = "<<Keystate_map[JSKEY_LB]<<endl;
   cout<<"JSKEY_RB = "<<Keystate_map[JSKEY_RB]<<endl;
   cout<<"JSKEY_BACK = "<<Keystate_map[JSKEY_BACK]<<endl;
   cout<<"JSKEY_START = "<<Keystate_map[JSKEY_START]<<endl;
   cout<<"JSKEY_HOME = "<<Keystate_map[JSKEY_HOME]<<endl;

   cout<<"JSKEY_LT = "<<Keystate_map[JSKEY_LT]<<endl;
   cout<<"JSKEY_RT = "<<Keystate_map[JSKEY_RT]<<endl;

   cout<<"JSKEY_CROSS_X = "<<Keystate_map[JSKEY_CROSS_X]<<endl;
   cout<<"JSKEY_CROSS_Y = "<<Keystate_map[JSKEY_CROSS_Y]<<endl;

   cout<<"JSKEY_LEFTSTICK_X  = "<<Keystate_map[JSKEY_LEFTSTICK_X] <<"     JSKEY_LEFTSTICK_Y   = "<<Keystate_map[JSKEY_LEFTSTICK_Y]<<endl;
   cout<<"JSKEY_RIGHTSTICK_X = "<<Keystate_map[JSKEY_RIGHTSTICK_X]<<"     JSKEY_RIGHTSTICK_Y = "<<Keystate_map[JSKEY_RIGHTSTICK_Y]<<endl;
}
```

```
// main.cpp
#include "Logitech_controller.h"

using namespace std;

int main()
{
    char path[] = "/dev/input/js0";
    Logitech gamepad(path);

    gamepad.init();
    gamepad.listen_input();

    return 0;
}
```

!注意:由于不同遥控对于io定义不同,所以需要改动一下**`Logitech_controller.h`**中的**`#define`**

