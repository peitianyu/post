---
title: c语言实现类
date: 2022-12-16 12:30:12
categories:
  - 语言
  - cpp
tags:
  - cpp
hidden: true
---

# 参考网址:

[(62条消息) C语言结构体中的函数指针与函数_成长的企鹅的博客-CSDN博客](https://blog.csdn.net/xcxinghai/article/details/84009214?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2~default~BlogCommendFromBaidu~Rate-1-84009214-blog-97621457.pc_relevant_3mothn_strategy_and_data_recovery&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2~default~BlogCommendFromBaidu~Rate-1-84009214-blog-97621457.pc_relevant_3mothn_strategy_and_data_recovery&utm_relevant_index=1)

# 示例

```c
#include <stdio.h>

struct Demo
{
    int x,y;
    void (*Construct)(struct Demo*); // 构造
    void (*Destruct)(struct Demo*);  // 析构
    int (*Add)(struct Demo*, int, int); // 函数指针
};

void DemoConstruct(struct Demo* self)
{
    printf("Constructing Demo object...\n");
    self->x = 0;
    self->y = 0;
}

void DemoDestruct(struct Demo* self)
{
    printf("Destructing Demo object...\n");
}

int DemoAdd(struct Demo* self, int x, int y)
{
    return x + y;
}

int main()
{
    struct Demo demo;

    // Initialize function pointers
    demo.Construct = &DemoConstruct;
    demo.Destruct = &DemoDestruct;
    demo.Add = &DemoAdd;

    // Call constructor
    demo.Construct(&demo);

    // Call function pointer
    int result = demo.Add(&demo, 10, 20);
    printf("Result: %d\n", result);

    // Call destructor
    demo.Destruct(&demo);

    return 0;
}
```



