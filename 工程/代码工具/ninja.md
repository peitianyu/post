---
title: ninja学习
date: 2024-05-10 12:30:12
categories:
  - 工程
  - 代码工具
tags:
  - 模板
---


## ninja优点
1. ninja语言设计是用于给机器阅读的, 所以人类读取并不友好
2. ninja最大的优点是快速
## 基本使用, 配合cmake
```shell
mkdir build
cd build
cmake -G Ninja ..
# 默认使用最多线程, 可以使用 '-l8 -j8'来指定编译线程数
ninja 
```

