---
title: C程序注意点
date: 2021-11-1 12:30:12
categories:
  - 语言
  - cpp
tags:
  - cpp
---

# 参考网址:

https://mp.weixin.qq.com/s/sgWvpKMqetU6l6th4SRJ9A

### 意想不到的八进制

```c++
// b为八进制
int a=34, b=034; 
```

### 指针加减运算

```c++
int a=1;  
int *p=(int *)0x00001000;  
a=a+1;  
p=p+1; 
// 这里的p是指针,
// p+1实际上是按照公式p+1*sizeof(int)来计算的。
```

比如RAM初始化零操作,如下,只有4字节空间被初始化为零

```c
unsigned int *pRAMaddr;         //定义地址指针变量  
// pRAMaddr+=4代码其实使pRAMaddr偏移了4*sizeof(int)=16个字节
for(pRAMaddr=StartAddr;pRAMaddr<EndAddr;pRAMaddr+=4)  
{  
     *pRAMaddr=0x00000000;   //指定RAM地址清零  
    // 这里清除了四个字节
} 
```

### 关键字sizeof

**sizeof**是一个关键字,需要注意的是,**使用sizeof获取数组长度时，不要对指针应用sizeof操作符**.

```c
void ClearRAM(char array[])  
{  
    int i ;  
    for(i=0;i<sizeof(array)/sizeof(array[0]);i++) //这里用法错误，array实际上是指针  
    {  
        array[i]=0x00;  
    }  
}  
  
int main(void)  
{  
    char Fle[20];  
      
    ClearRAM(Fle);          //只能清除数组Fle中的前四个元素  
}  
```

### 结构体填充

结构体可能产生填充，因为对大多数处理器而言，访问按字或者半字对齐的数据速度更快，当定义结构体时，编译器为了性能优化，可能会将它们按照半字或字对齐，这样会带来填充问题。比如以下两个结构体：

第一个结构体：

```c
struct {  
    char  c；  
    short s；  
    int   x；  
}str_test1; 
```

第二个结构体：

```c
struct {  
    char  c；  
    int   x；  
    short s；      
}str_test2;
```

这两个结构体元素都是相同的变量，只是元素换了下位置，那么这两个结构体变量占用的内存大小相同吗？

其实这两个结构体变量占用的内存是不同的，对于Keil MDK编译器，默认情况下**第一个结构体变量占用8个字节**，**第二个结构体占用12个字节**，差别很大。第一个结构体变量在内存中的存储格式如图所示：

```
1~4字节  cc填充SSSS
5~8字节  XXXXXXXX
```

第二个结构体变量在内存中的存储格式如图所示。对比两个图可以看出MDK编译器是是怎么将数据对齐的，这其中的填充内容是之前内存中的数据，是随机的，所以不能再结构之间逐字节比较；另外，合理的排布结构体内的元素位置，可以最大限度减少填充，节省RAM。

```
1~4字节   cc填充
5~8字节   XXXXXXXX
9~12字节  ssss填充
```

