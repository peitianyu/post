---
title: gtest安装与使用
date: 2022-4-15 12:30:12
categories:
  - 工程
  - 代码工具
tags:
  - tool
---

# 参考网址:

[(128条消息) cmake + googletest 之一 入门_Joel的小喵咪的博客-CSDN博客](https://blog.csdn.net/joelcat/article/details/90766192?ops_request_misc=%7B%22request%5Fid%22%3A%22165087082116780357286038%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=165087082116780357286038&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-4-90766192.142^v9^control,157^v4^control&utm_term=gtest+cmake&spm=1018.2226.3001.4187)

[(128条消息) GTest的安装与使用_蕾蕾的好包包的博客-CSDN博客_gtest安装](https://blog.csdn.net/qq_39036287/article/details/106772896?ops_request_misc=%7B%22request%5Fid%22%3A%22165087349316782246480465%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=165087349316782246480465&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-3-106772896.142^v9^control,157^v4^control&utm_term=gtest+安装&spm=1018.2226.3001.4187)

# 安装

```sh
git clone https://ghproxy.com/https://github.com/google/googletest.git
cd googletest && mkdir build
cd build && cmake ..
sudo make -j4 
cd lib && 
sudo cp *.a /usr/lib
```

# 简单使用

## 目录

```sh
├── CMakeLists.txt
├── main.cpp
├── src
│   └── myproject
│       └── myclass.hpp
└── test
    ├── CMakeLists.txt
    └── test_my_class.cpp
```

## 文件(从上到下)

```cmake
# CMakeLists.txt
cmake_minimum_required(VERSION 3.10)
project(MyProject)

set(CMAKE_CXX_STANDARD 14)

include_directories(${PROJECT_SOURCE_DIR}/src)

# 开启测试
enable_testing()

set(MY_PROJECT_SRC
        src/myproject/myclass.hpp)

add_executable(MyProject
        ${MY_PROJECT_SRC}
        main.cpp )

# 添加测试目录
add_subdirectory(test)
```

```cpp
// main.cpp
#include<iostream>

int main(int argc, char **argv)
{
    std::cout << "Hello World!" << std::endl;
    return 0;
}
```

```cpp
// myclass.hpp
#pragma once

#include <string>

class my_class
{
public:
    my_class(const std::string &name, int age)
    {
        m_age = age;
        m_name = name;
    }

public:
    int get_age() { return m_age; }
    std::string get_name() { return m_name; }

private:
    int m_age;
    std::string m_name;
};
```

```cmake
# CMakeLists.txt
# 查找 GTest 库
find_package(GTest REQUIRED)
# GTest 的头文件
include_directories(${GTEST_INCLUDE_DIRS})

add_executable(test_my_class test_my_class.cpp)

# 链接测试库
target_link_libraries( test_my_class
        ${GTEST_BOTH_LIBRARIES}
        pthread )

# 添加到测试
gtest_discover_tests(test_my_class)
```

```cpp
// test_my_class.cpp
#include <gtest/gtest.h>

#include <myproject/myclass.hpp>

TEST(test_my_class, get_age)
{
    my_class myClass("Joel", 21);
    ASSERT_TRUE(myClass.get_age() == 16) << "age is not 16";
}

TEST(test_my_class, get_name)
{
    my_class myClass("Joel", 21);
    ASSERT_EQ(myClass.get_name(), "Joel") << "name is not Joel";
}
```

# 具体工具类

## 布尔值检查

| Fatal assertion            | Nonfatal assertion         | Verifies             |
| -------------------------- | -------------------------- | -------------------- |
| ASSERT_TRUE(*condition*);  | EXPECT_TRUE(*condition*);  | *condition* is true  |
| ASSERT_FALSE(*condition*); | EXPECT_FALSE(*condition*); | *condition* is false |

## 数值型数据检查

| Fatal assertion                  | Nonfatal assertion               | Verifies               |
| -------------------------------- | -------------------------------- | ---------------------- |
| ASSERT_EQ(*expected*, *actual*); | EXPECT_EQ(*expected*, *actual*); | *expected* == *actual* |
| ASSERT_NE(*val1*, *val2*);       | EXPECT_NE(*val1*, *val2*);       | *val1* != *val2*       |
| ASSERT_LT(*val1*, *val2*);       | EXPECT_LT(*val1*, *val2*);       | *val1* < *val2*        |
| ASSERT_LE(*val1*, *val2*);       | EXPECT_LE(*val1*, *val2*);       | *val1* <= *val2*       |
| ASSERT_GT(*val1*, *val2*);       | EXPECT_GT(*val1*, *val2*);       | *val1* > *val2*        |
| ASSERT_GE(*val1*, *val2*);       | EXPECT_GE(*val1*, *val2*);       | *val1* >= *val2*       |

## 字符串比较

| Fatal assertion                                 | Nonfatal assertion                              | Verifies                            |
| ----------------------------------------------- | ----------------------------------------------- | ----------------------------------- |
| ASSERT_STREQ(*expected_str*, *actual_str*);     | EXPECT_STREQ(*expected_str*, *actual_str*);     | 两个C字符串有相同的内容             |
| ASSERT_STRNE(*str1*, *str2*);                   | EXPECT_STRNE(*str1*, *str2*);                   | 两个C字符串有不同的内容             |
| ASSERT_STRCASEEQ(*expected_str*, *actual_str*); | EXPECT_STRCASEEQ(*expected_str*, *actual_str*); | 两个C字符串有相同的内容，忽略大小写 |
| ASSERT_STRCASENE(*str1*, *str2*);               | EXPECT_STRCASENE(*str1*, *str2*);               | 两个C字符串有不同的内容，忽略大小写 |

## 异常检查

| Fatal assertion                              | Nonfatal assertion                           | Verifies                                          |
| -------------------------------------------- | -------------------------------------------- | ------------------------------------------------- |
| ASSERT_THROW(*statement*, *exception_type*); | EXPECT_THROW(*statement*, *exception_type*); | *statement* throws an exception of the given type |
| ASSERT_ANY_THROW(*statement*);               | EXPECT_ANY_THROW(*statement*);               | *statement* throws an exception of any type       |
| ASSERT_NO_THROW(*statement*);                | EXPECT_NO_THROW(*statement*);                | *statement* doesn’t throw any exception           |

## 浮点型检查

| Fatal assertion                       | Nonfatal assertion                    | Verifies                               |
| ------------------------------------- | ------------------------------------- | -------------------------------------- |
| ASSERT_FLOAT_EQ(*expected, actual*);  | EXPECT_FLOAT_EQ(*expected, actual*);  | the two float values are almost equal  |
| ASSERT_DOUBLE_EQ(*expected, actual*); | EXPECT_DOUBLE_EQ(*expected, actual*); | the two double values are almost equal |

## 邻近值比较

| Fatal assertion                       | Nonfatal assertion                    | Verifies                                                     |
| ------------------------------------- | ------------------------------------- | ------------------------------------------------------------ |
| ASSERT_NEAR(*val1, val2, abs_error*); | EXPECT_NEAR*(val1, val2, abs_error*); | the difference between *val1* and *val2* doesn’t exceed the given absolute error |

## 事件机制(全局->TestSuite->TestCase)

```cpp
#include<gtest/gtest.h>
#include<map>
#include<iostream>
using namespace std;
class Student{
public:
    Student(){
        age=0;
    }
    Student(int a){
        age=a;
    }
    void print(){
    cout<<"*********** "<<age<<" **********"<<endl;;
        }  
private:
    int age;
};
class FooEnvironment : public testing::Environment{
public:
    virtual void SetUp()
    {
        std::cout << "Foo FooEnvironment SetUP" << std::endl;
    }
    virtual void TearDown()
    {
        std::cout << "Foo FooEnvironment TearDown" << std::endl;
    }
};
static Student *s;
//在第一个test之前，最后一个test之后调用SetUpTestCase()和TearDownTestCase()
class TestMap:public testing::Test
{
public:
    static void SetUpTestCase()
    {
        cout<<"SetUpTestCase()"<<endl;
    s=new Student(23);
    }
 
    static void TearDownTestCase()
    {
    delete s;
        cout<<"TearDownTestCase()"<<endl;
    }
    void SetUp()
    {
        cout<<"SetUp() is running"<<endl;
         
    }
    void TearDown()
    {
        cout<<"TearDown()"<<endl;
    } 
};
 
TEST_F(TestMap, Test1)
 {
 
    // you can refer to s here
    s->print();
}
int main(int argc, char** argv)
{
    testing::AddGlobalTestEnvironment(new FooEnvironment);
    testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```

