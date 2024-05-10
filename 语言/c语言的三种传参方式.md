---
title: c语言的三种传参方式
date: 2021-12-15 12:30:12
categories:
  - 语言
  - cpp
tags:
  - cpp
---

```c
#include<iostream>
#include <stdint.h>
using namespace std;
 
int  fun1(int a)
{
    return a*a;
}
 
void  man(int &a)
{
    a=a*a;
}
 
void cubeByReference(int *a)
{
    *a=*a * *a;
}

void fun2(uint8_t* *buffer)
{
    uint8_t buf[10]={0,1,2,3,4,5,6,7,8,9};
    *buffer = buf;
}
int main()
{
    int a=5,b=5,number=5;
    cout<<fun1(a)<<endl;
 
    man(b);
    cout<<b<<endl;
 
 
    cubeByReference(&number);
    cout<<number<<endl;

    uint8_t *buf;
    fun2(&buf);
    printf("%d\n", buf[0]);
    // for (int i = 0; i < 10; ++i)
    // {
    //     printf("%d\n", buf[i]);
    // }
 
    return 0;
}
```

