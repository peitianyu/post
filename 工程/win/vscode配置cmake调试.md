---
title: vscode配置cmake调试
date: 2022-06-07 12:30:12
categories:
  - 工程
  - win
tags:
  - 模板
---

# 参考网址:

 [(137条消息) Ubuntu下VsCode和CMake联合调试C++工程_还没想好~的博客-CSDN博客](https://blog.csdn.net/LiuXF93/article/details/123066248?ops_request_misc=%7B%22request%5Fid%22%3A%22165460710316782388054405%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=165460710316782388054405&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-7-123066248-null-null.142^v11^pc_search_result_control_group,157^v13^control&utm_term=vscode配置cmake调试环境&spm=1018.2226.3001.4187) 

 [(137条消息) Ubuntu下VSCode+Cmake配置开发环境_Stone_OverLooking的博客-CSDN博客](https://blog.csdn.net/Stone_OverLooking/article/details/109544940?ops_request_misc=%7B%22request%5Fid%22%3A%22165460710316782388054405%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=165460710316782388054405&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-9-109544940-null-null.142^v11^pc_search_result_control_group,157^v13^control&utm_term=vscode配置cmake调试环境&spm=1018.2226.3001.4187) 

# 步骤

## 安装gdb

```sh
# Unable to start debugging. Launch ...... 
apt-get install gdb
```

## cmake配置

```
# 设置编译模式
set( CMAKE_BUILD_TYPE "Debug" )
set(CMAKE_CXX_FLAGS_DEBUG "$ENV{CXXFLAGS} -O0 -Wall -g2 -ggdb")
set(CMAKE_CXX_FLAGS_RELEASE "$ENV{CXXFLAGS} -O3 -Wall")
add_definitions(-std=c++11)
```

## 编译

```sh
mkdir build
cd build
cmake .. && make -j8
```

## c_cpp_properties.json

```json
{
    "configurations": [
        {
            "name": "Linux",
            //本地标准头文件
            "includePath": [
                "/usr/include",
                "/usr/local/include",
                "${workspaceFolder}/**" //工作目录下的所有子路径
            ],
            "defines": [],
            "compilerPath": "/usr/bin/gcc",
            "cStandard": "c11",
            "cppStandard": "c++17",
            "intelliSenseMode": "clang-x64",
            "compileCommands": "${workspaceFolder}/build/compile_commands.json"
        }
    ],
    "version": 4
}
```

## launch.json

```json
{
    // 使用 IntelliSense 了解相关属性。 
    // 悬停以查看现有属性的描述。
    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) Launch",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/build/Groot",// 表示可执行程序所在的路径，其中，${workspaceRoot}表示VScode加载的文件夹的根目录
            // "program": "${command:cmake.launchTargetPath}",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": true,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "make build" //###最好删了，不然会影响调试，每次调试都直接执行make build
        }
    ]
}
```

## tasks.json

```
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "label": "make build", 
            "type": "shell",
            "command": "cd ./build ;cmake ../ ;make -8",
            "group": {
                "kind": "build",
                "isDefault": true
            }
        },
        {
            "label": "clean",
            "type": "shell",
            "command": "make clean"
        }
    ]
}
```

