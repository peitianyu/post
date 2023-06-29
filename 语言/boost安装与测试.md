---
title: boost安装与测试
date: 2021-11-25 12:30:12
categories:
  - 语言
  - cpp
tags:
  - cpp
---

# 参考网址:

[Ubuntu下安装boost库_上善若水-CSDN博客](https://blog.csdn.net/zhangxiao93/article/details/51077933?ops_request_misc=%7B%22request%5Fid%22%3A%22163782562316780255241172%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163782562316780255241172&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-51077933.pc_search_mgc_flag&utm_term=ubuntu+boost库安装&spm=1018.2226.3001.4187)

[在Ubuntu上安装boost库_一点一滴-CSDN博客](https://blog.csdn.net/yhrun/article/details/8099630)

[Ubuntu20.04安装boost库_vanyongqi-CSDN博客_ubuntu 安装boost](https://blog.csdn.net/qq_41722524/article/details/114322697?ops_request_misc=%7B%22request%5Fid%22%3A%22163782562316780255241172%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163782562316780255241172&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-4-114322697.pc_search_mgc_flag&utm_term=ubuntu+boost库安装&spm=1018.2226.3001.4187)

# 安装

## apt安装

```shell
sudo apt-get install libboost-dev
```

## build安装

- 下载boost库:[Boost C++ Libraries](https://www.boost.org/)
- 解压: `tar -xzvf boost_1_xx_xx.tar.gz`
- 进入解压目录,执行脚本,`sudo ./bootstrap.sh`
- 安装, `sudo ./b2 install`
- 测试环境变量

```shell
sudo gedit /etc/profile
 
#在文件末尾添加
export CPLUS_INCLUDE_PATH=/usr/local/include/boost:$CPLUS_INCLUDE_PATH
export LIBRARY_PATH=/usr/local/lib:$LIBRARY_PATH
export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
```

# 测试

```shell
sudo nano test.cpp
```

```c++
#include<iostream>
#include<boost/bind.hpp>
using namespace std;
using namespace boost;
int fun(int x,int y){return x+y;}
int main(){
	int m=1;int n=2;
	cout<<boost::bind(fun,_1,_2)(m,n)<<endl;
	return 0;
}
```

```shell
g++ test.cpp -o test
```

# cmake使用boost库

```cmake
// CMakeLists.txt
cmake_minimum_required(VERSION 2.8.3)
project(boost_test)
find_package(Boost REQUIRED COMPONENTS system thread)
include_directories(${Boost_INCLUDE_DIRS})
add_executable(boost_test boost_test.cpp)
target_link_libraries(boost_test ${Boost_LIBRARIES})
```

```cpp
// boost_test.cpp
#include<iostream>
#include<boost/bind.hpp>
using namespace std;
using namespace boost;
int fun(int x,int y){return x+y;}
int main(){
	int m=1;int n=2;
	cout<<boost::bind(fun,_1,_2)(m,n)<<endl;
	return 0;
}
```
