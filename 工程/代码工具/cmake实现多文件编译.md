---
title: cmake实现多文件编译
date: 2021-12-3 12:30:12
categories:
  - 工程
  - 代码工具
tags:
  - linux
  - cpp
---

### Cmake文件结构

```shell
root@LAPTOP-49LFJ4NT:/root/cmake# tree
.
├── build
└── src
    ├── CMakeLists.txt
    ├── Queue
    │   ├── CMakeLists.txt
    │   └── src
    │       ├── QUE.cpp
    │       ├── QUE.h
    │       └── main.cpp
    └── canalyst_test
        ├── CMakeLists.txt
        ├── lib
        │   └── libcontrolcan.so
        └── src
            ├── Tongyi_Driver.cpp
            ├── Tongyi_Driver.h
            ├── controlcan.cpp
            ├── controlcan.h
            └── main.cpp
```

### shell.sh

```shell
# 由于主要在src文件夹下工作，因此在此建一个shell脚本方便编译
cd .. && cd build && cmake ../src && make -j 
```

### CmakeLists.txt

```cmake
# 指定cmake版本
cmake_minimum_required(VERSION 3.0)
# 工程名
project(Cmake)

# 添加下层cmake
add_subdirectory(canalyst_test)
add_subdirectory(Queue)
```

### 执行文件

```shell
cd build && bin/./queue
```

