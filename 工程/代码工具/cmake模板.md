---
title: cmake配置模板
date: 2021-10-19 12:30:12
categories:
  - 工程
  - 代码工具
tags:
  - 模板
---

### Cmake教程

#### 参考网址:

[CMake 入门实战 | HaHack](https://www.hahack.com/codes/cmake/)

### Cmake文件结构

```shell
root@LAPTOP-49LFJ4NT:/root/cmake# tree
.
├── CMakeLists.txt
├── bin
├── build
├── include
│   └── func.h
└── src
    ├── func.cpp
    ├── main.cpp
    └── shell.sh
```

### shell.sh

```shell
# 由于主要在src文件夹下工作，因此在此建一个shell脚本方便编译
cd .. && cd build && cmake .. && make -j && ./main
```

### CmakeLists.txt

```cmake
# 指定cmake版本
cmake_minimum_required(VERSION 3.0)
# 工程名
project(canalyst)

#cmake的c++设置
# 告知當前使用的是交叉編譯方式，必須配置
SET(CMAKE_SYSTEM_NAME Linux)
SET(CMAKE_C_COMPILER "gcc")
SET(CMAKE_CXX_COMPILER "g++")
# 执行路径设置
SET(EXECUTABLE_OUTPUT_PATH ../bin)
# 设置编译选项
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O0 -Wall -pthread -std=c++11 -fPIC ")

# 添加.h文件
include_directories(src)

# 添加.cpp文件
aux_source_directory(src  SRC_LIST)

# 链接库
link_directories(lib)
set(LIB_LIST
    libcontrolcan.so
)

# 输出调试信息
# message("src :${SRC_LIST} ")

# 执行文件
add_executable(${PROJECT_NAME} ${SRC_LIST} )
target_link_libraries(${PROJECT_NAME} ${LIB_LIST})
```

### 运行

```shell
# 在src文件夹下运行shell.sh
./shell
```

