---
title: linux_gpio
date: 2021-11-23 12:30:12
categories:
  - 工程
  - linux工具
tags:
  - linux
---

# 参考网址:

[Linux操作GPIO（文件IO方式）_Leon-CSDN博客](https://blog.csdn.net/lell3538/article/details/51277870?ops_request_misc=%7B%22request%5Fid%22%3A%22163767480816780261986595%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163767480816780261986595&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-51277870.pc_search_mgc_flag&utm_term=linux操作gpio&spm=1018.2226.3001.4187)

[在Linux 中的应用层操作gpio的方法_仗劍走天涯-CSDN博客](https://blog.csdn.net/u010299133/article/details/82286700?ops_request_misc=%7B%22request%5Fid%22%3A%22163767480816780261986595%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163767480816780261986595&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-82286700.pc_search_mgc_flag&utm_term=linux操作gpio&spm=1018.2226.3001.4187)

# 位置

`控制GPIO`的目录位于`/sys/class/gpio`

# 例子

```
1. 导出
/sys/class/gpio# echo 44 > export
2. 设置方向
/sys/class/gpio/gpio44# echo out > direction
3. 查看方向
/sys/class/gpio/gpio44# cat direction
4. 设置输出
/sys/class/gpio/gpio44# echo 1 > value
5. 查看输出值
/sys/class/gpio/gpio44# cat value
6. 取消导出
/sys/class/gpio# echo 44 > unexport
```

 以**echo**的形式调用**system**函数进行操作，这种形式编程比较简单，结构比较清晰，如下

```c++
void set_gpio64_low(void)  
{     
    system("echo 64 > /sys/class/gpio/export");  
    system("echo out > /sys/class/gpio/gpio64/direction");  
    system("echo 0 > /sys/class/gpio/gpio64/value");  
}  
```

 通过**文件**的形式来调用

```c++
#include <stdio.h>
 
#include <stdlib.h>
 
#include <string.h>
#include <unistd.h>
#include <fcntl.h>   //define O_WRONLY and O_RDONLY
 
void initGpio(int n)
{
    FILE * fp =fopen("/sys/class/gpio/export","w");
    if (fp == NULL)
        perror("export open filed");
    else
        fprintf(fp,"%d",n);
    fclose(fp);
}   //create gpio file
 
void setGpioDirection(int n,char *direction)
{
    char path[100] = {0};
    sprintf(path,"/sys/class/gpio/gpio%d/direction",n);
    FILE * fp =fopen(path,"w");
    if (fp == NULL)
        perror("direction open filed");
    else
        fprintf(fp,"%s",direction);
    fclose(fp);
}   //set gpio "in" or "out"
 
int getGpioValue(int n)
{
    char path[64];
    char value_str[3];
    int fd;
 
    snprintf(path, sizeof(path), "/sys/class/gpio/gpio%d/value", n);
    fd = open(path, O_RDONLY);
    if (fd < 0) {
        perror("Failed to open gpio value for reading!");
        return -1;
    }
 
    if (read(fd, value_str, 3) < 0) {
        perror("Failed to read value!");
        return -1;
    }
 
    close(fd);
    return (atoi(value_str));
}   //get gpio(n)'s value

void setGpioValue(int n,int value)
{
    char path[64];
    char value_str[3];
    int fd;
 
    snprintf(path, sizeof(path), "/sys/class/gpio/gpio%d/value", n);
    fd = open(path, O_WRONLY);
    if (fd < 0) {
        perror("Failed to open gpio value for writing!");
        return;
    }
 
    if (value)
        strcpy(value_str,"1");
    else
        strcpy(value_str,"0");
 
    if (write(fd, value_str, 1) != 1) {
        perror("Failed to write value!");
        return;
    }
 
    close(fd);
}   //set gpio(n)'s value
 
int main()
 
{
    initGpio(18);
    setGpioDirection(18,(char*)"out");
    while(1)
    {
        setGpioValue(18,0);
        printf("%d\n",getGpioValue(18));//每隔1s输出一次gpio18的值
        sleep(1);
        setGpioValue(18,1);
        printf("%d\n",getGpioValue(18));//每隔1s输出一次gpio18的值
        sleep(1);
    }
 
    return 0;
 
}
```



