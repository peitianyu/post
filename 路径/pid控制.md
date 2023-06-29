---
title: 简单pid控制
date: 2021-12-16 12:30:12
categories:
  - 路径
  - path_tracking
tags:
  - path_tracking
---

# 参考网址:

[P、I、D参数分析及倒立摆心得_一念之间、-CSDN博客_pid 倒立摆](https://blog.csdn.net/tqs_1220/article/details/75249145?ops_request_misc=&request_id=&biz_id=102&utm_term=p表示 i表示 d表示&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-8-75249145.pc_search_em_sort&spm=1018.2226.3001.4187)

[PID控制算法的C语言实现_我的博客-CSDN博客_pid算法c程序](https://blog.csdn.net/qq_18454025/article/details/103443771?ops_request_misc=%7B%22request%5Fid%22%3A%22163961834316780274185698%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163961834316780274185698&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-3-103443771.pc_search_em_sort&utm_term=pid&spm=1018.2226.3001.4187)

# PID作用

```
P-------作用是提高系统响应速度,过大会出现震荡---------------------------快速性

I-------作用是消除静差----------------------------------------------准确性

D-------作用是抑制震荡----------------------------------------------稳定性/预测性
```

# 例程

```c++
// pid控制测试
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

// P:响应 I:稳差 D:预判
struct PID_CTRL
{
    float kp;
    float ki;
    float kd;
};


float pos_pid_control(float target, float current, struct PID_CTRL pid)
{
    static float err_last;
    float out,err_sum=0,err_d,err;
    err = target - current;
    err_sum += err;
    err_d = err - err_last;
    out = pid.kp * err + pid.ki * err_sum + pid.kd * err_d;
    err_last = err;
    return out;
}

float gain_pid_control(float target, float current, struct PID_CTRL pid)
{
    static float err_last;
    float out=0;
    float err = target - current;
    out += pid.kp * (err-err_last) + pid.ki * err;
    err_last = err;
    return out;
}

void test_pos_pid_control()
{
    struct PID_CTRL pid{0.5,0.1,0.1};
    pid.kp = 0.5;
    pid.ki = 0.2;
    pid.kd = 0.1;

    float target = 10.0;
    float current = 0.0;
    for(int i = 0; i < 100; i++)
    {
        current += 0.1;
        printf("target: %f, current: %f, out: %f\n", target, current, pos_pid_control(target, current, pid));
    }
}

void test_gain_pid_control()
{
    struct PID_CTRL pid{0.5,0.1,0.1};
    pid.kp = 0.5;
    pid.ki = 0.2;
    pid.kd = 0.1;

    float target = 10.0;
    float current = 0.0;
    for(int i = 0; i < 100; i++)
    {
        current += 0.1;
        printf("target: %f, current: %f, out: %f\n", target, current, gain_pid_control(target, current, pid));
    }
}

int main()
{
    test_pos_pid_control();
    printf("********************************************\n");
    test_gain_pid_control();
    return 0;
}

```

