---
title: linux定时器
date: 2021-12-10 12:30:12
categories:
  - 工程
  - linux工具
tags:
  - linux
---

# 参考网址:

[LInux下几种定时器的比较和使用_weixin_34358365的博客-CSDN博客](https://blog.csdn.net/weixin_34358365/article/details/94217782?ops_request_misc=%7B%22request%5Fid%22%3A%22163914549216780255279840%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163914549216780255279840&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-94217782.nonecase&utm_term=linux定时器&spm=1018.2226.3001.4187)

# 1、[sleep，usleep和nanosleep](http://www.jb51.net/LINUXjishu/109403.html)

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/time.h>
#include <sched.h>
#define COUNT 1000
#define MILLION 1000000L
int main(void)
{
    int i;
    struct timespec slptm;
    long   tdif;
    struct timeval tend, tstart; 
    slptm.tv_sec = 0;
    slptm.tv_nsec = 1000;      //1000 ns = 1 us
    //struct sched_param param;    
    //param.sched_priority = 0;
    //sched_setscheduler(getpid(), SCHED_FIFO, &param);
    if (gettimeofday(&tstart, NULL) == -1) {
        fprintf(stderr, "Failed to get start time\n");
        return 1;
    }
    for (i = 0; i < COUNT; i++) {
        if (nanosleep(&slptm, NULL) == -1) {
            perror("Failed to nanosleep");
            return 1;
        }
    }
    if (gettimeofday(&tend, NULL) == -1) {
        fprintf(stderr, "Failed to get end time\n");
        return 1;
    }
    tdif = MILLION * (tend.tv_sec - tstart.tv_sec) + (tend.tv_usec - tstart.tv_usec);
    printf("nanosleep() time is %ld us\n", tdif/COUNT);
    return 0;
}
```

# 2、使用信号量SIGALRM + alarm(),只能秒级

```c
#include <stdio.h>
#include <signal.h>
void timer(int sig)
{
        if(SIGALRM == sig)
        {
                printf("timer\n");
                alarm(1);       //重新继续定时1s
        }
        return ;
}
int main()
{
        signal(SIGALRM, timer); //注册安装信号
        alarm(1);       //触发定时器
        getchar();
        return 0;
}
```

# 3、使用RTC(系统硬件提供的RTC，精度可调)

```c
#include <stdio.h>
#include <linux/rtc.h>
#include <sys/ioctl.h>
#include <sys/time.h>
#include <sys/types.h>
#include <fcntl.h>
#include <unistd.h>
#include <errno.h>
#include <stdlib.h>

int main(int argc, char* argv[])
{
        unsigned long i = 0;
        unsigned long data = 0;
        int retval = 0;
        int fd = open ("/dev/rtc", O_RDONLY);
        if(fd < 0)
        {
                perror("open");
                exit(errno);
        }
        /*Set the freq as 4Hz*/
        if(ioctl(fd, RTC_IRQP_SET, 1) < 0)
        {
                perror("ioctl(RTC_IRQP_SET)");
                close(fd);
                exit(errno);
        }
        /* Enable periodic interrupts */
        if(ioctl(fd, RTC_PIE_ON, 0) < 0)
        {
                perror("ioctl(RTC_PIE_ON)");
                close(fd);
                exit(errno);
        }
        for(i = 0; i < 100; i++)
        {
                if(read(fd, &data, sizeof(unsigned long)) < 0)
                {
                        perror("read");
                        close(fd);
                        exit(errno);
                }
                printf("timer\n");
        }
        /* Disable periodic interrupts */
        ioctl(fd, RTC_PIE_OFF, 0);
        close(fd);
        return 0;
}
```

# 4、使用select()

```c
#include<stdio.h>
#include <sys/time.h>
#include <sys/select.h>
#include <time.h>

int Callback_1(int x) // Callback Function 1
{
    printf("Hello, this is Callback_1: x = %d \n", x);
    return 0;
}

//设置模板
template<typename T>
void setTimer(int seconds, int mseconds,T arg,int (*callback)(T)) // Set Timer
{
        struct timeval temp;
        temp.tv_sec = seconds;
        temp.tv_usec = mseconds;
        select(0, NULL, NULL, NULL, &temp);
        callback(arg);
        return ;
}

float last_time;
float get_diff_time(void)
{
        struct timeval tv;
        gettimeofday(&tv, NULL);
        float time = tv.tv_sec%10 + tv.tv_usec / 1000000.0;
        if (time < last_time)
                time += 10;
        float time_diff = time - last_time;
        last_time = time;
        return time_diff;
}


int main()
{
    int a = 2;
    for (size_t i = 0; i < 5; i++)
    {
        float time_diff = get_diff_time();
        printf("time_diff = %f \n", time_diff);
        setTimer(1, 0, a, Callback_1);
    }
    return 0;
}
```
