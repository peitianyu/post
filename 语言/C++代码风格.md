---
title: c++风格
date: 2022-5-3 12:30:12
categories:
  - 语言
  - cpp
tags:
  - cpp
hidden: true
---

# 参考网址:

[C++ 风格指南 - 内容目录 — Google 开源项目风格指南 (zh-google-styleguide.readthedocs.io)](https://zh-google-styleguide.readthedocs.io/en/latest/google-cpp-styleguide/contents/)

# 简介

```txt
// 文件:
a_b_c.cc
<PROJECT>_<PATH>_<FILE>_H_

#ifndef FOO_BAR_BAZ_H_
#define FOO_BAR_BAZ_H_
...
#endif // FOO_BAR_BAZ_H_
// 类型:
struct Abc{};
class Abc{};
enum Abc{};
namespace abc{}
// 变量:
string table_name;  // 好 - 用下划线.

// 类数据成员
class TableInfo {
  ...
 private:
  string table_name_;  // 好 - 后加下划线.
  string tablename_;   // 好.
  static Pool<TableInfo>* pool_;  // 好.
  static Pool<TableInfo>* m_pool;
};

// 结构体变量
struct UrlTableProperties {
  string name;
  int num_entries;
  static Pool<UrlTableProperties>* pool;
};

// 常量命名,声明为 constexpr 或 const 的变量
const int kDaysInAWeek = 7;
constexpr int kDaysWeek = 7;

// 枚举
ENUM_NAME

// 宏命名
#define ROUND(x) ...
#define PI_ROUNDED 3.0
```



# 头文件

```cpp
.cc文件主要用于linux
.hpp template
```

## inline

```txt
只有当函数只有 10 行甚至更少时才将其定义为内联函数.(小且频繁)
```

## static

```c++
//  一般用于func内,不做全局static
void func(){
	static int a = 0;
	a++;
}
// static class Create();
class A{
    static create(); // 主要用于创建
}
```

## #include

```cpp
#include "foo/public/fooserver.h" // 优先位置

#include <sys/types.h>
#include <unistd.h>

#include <hash_map>
#include <vector>

#include "base/basictypes.h"
#include "base/commandlineflags.h"
#include "foo/public/bar.h"
```

# 作用域

```cpp
// 在 .cc 中使用别名缩短常用的命名空间
namespace baz = ::foo::bar::baz;
```

## const与constexpr

```cpp
尽量多的使用const与constexpr保证安全需要
```

## 声明初始化

```cpp
// 使用RAII,尽量使用POD
int a = 0;
A a = A();
class A{
  	A(){
       	b = new B(); 
    }  
private:
    B* b;
};
```

# 命名规则

## 文件

```cpp
my_useful_class.cc
```

# 注释(Doxygen)

# 类

## Lambda

```cpp
double rate = 0.49;
// lamber
auto r2 = [=](double monty, int year)->double{return monty*rate*year; };
```



## std::optional

## explicit(显示表达)(尽量多使用)

```txt
一般用于只一个量,且容易出现隐式转换的时候
```

## 可拷贝类型和可移动类型

```cc
// MyClass is neither copyable nor movable.
MyClass(const MyClass&) = delete;
MyClass& operator=(const MyClass&) = delete;
```

## 结构体 VS. 类

```
结构体一般只有成员变量
```

## 接口类

```cc
class BaseInterface{
	virtual const int func() = 0;
};
class Base{ public: BaseInterface
	const int func() override;
}
```

## sizeof()

```cc
sizeof(A); // 不建议
sizeof(a); // 建议
```

## static_cast

```cc
static_cast<int>(f = 0.22);
```

## 初始化(0,nullptr,NULL)

## i++与++i

```cc
for(intertor i = s.begin();++i;s.end()) // 避免重复
```

## struct初始化

```cc
struct A = {0,1.2,"hello"}; // POD
```

