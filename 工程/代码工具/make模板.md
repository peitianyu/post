---
title: make配置模板
date: 2021-10-19 12:30:12
categories:
  - 工程
  - 代码工具
tags:
  - 模板
---

### make文件目录

```shell
root@LAPTOP-49LFJ4NT:/root/make# tree
.
├── Makefile
├── conf.sh
├── main.cpp
└── shell.sh
```

这里使用了shell脚本来实现对于make的编译，由于使用的是`wsl`在windows下操作导致编码格式含有`\r\n`，以此有两个shell脚本。

### conf.sh 

```shell
# 实现对于make的编译与运行
#!/bin/bash
if [[ $1 == clean ]]; then
        make clean
elif [[ $1 == all ]]; then
        make clean && make -j8 && ./main
else
        make -j8 && ./main
```

### shell.sh 

```shell
# 实现将conf.sh的dos编码转化为unix编码，并执行
#!/bin/bash
dos2unix  conf.sh && ./conf.sh
```

### Makefile

```makefile
# 自动捕捉符合c/c++编译
#指定编译工具
CC = gcc
CPP = g++
LINK = g++

LIBS = -lpthread
#编译.so 必须添加 -fPIC 和 -shared 选项
CCFLAGS = -c -g -fPIC
CPPFLAGS = -c -g -fPIC
CCFLAGS = -c -g -fPIC

#期望得到的执行文件或动态库.so
#TARGET=libxx.so
TARGET=main

INCLUDES = -I. #-I../../

CPPFILES = $(wildcard *.cpp )#遍历得到当前目录及上层目录中的所有.cpp文件
CCFILES = $(wildcard *.cc )#遍历得到当前目录及上层目录中的所有.cc文件
CFILES = $(wildcard *.c )#遍历得到当前目录及上层目录中的所有.c文件

OBJFILE = $(CFILES:.c=.o) $(CPPFILES:.cpp=.o) $(CCFILES:.cc=.o)

all:$(TARGET)

$(TARGET):$(OBJFILE)
# 编译得到 .so 文件用下面的代码
#   $(LINK) $^ $(LIBS) -Wall -fPIC -shared -o $@
# 编译得到可执行文件用下面的代码
	$(LINK) $^ $(LIBS) -Wall -O2 -o $@

%.o:%.c
	$(CC) -o $@ $(CCFLAGS) $< $(INCLUDES) 

%.o:%.cpp
	$(CPP) -o $@ $(CPPFLAGS) $< $(INCLUDES)

%.o:%.cc
	$(CPP) -o $@ $(CCFLAGS) $< $(INCLUDES)

build:
	make
	rm -rf $(OBJFILE)

clean:
	rm -rf $(TARGET)
	rm -rf $(OBJFILE)
```

### main.c

```c
#include <stdio.h>
 
int main()
{
    printf("Hello, World! \n");
 
    return 0;
}
```

### 运行

```shell
./shell
```

