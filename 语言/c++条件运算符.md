---
title: c++条件运算符(?:)
date: 2022-5-3 12:30:12
categories:
  - 语言
  - cpp
tags:
  - cpp
hidden: true
---

# 参考网址:

[(130条消息) C++ 条件运算符 (?:)_TechArtisan6的博客-CSDN博客_c++条件运算符](https://blog.csdn.net/zaishuiyifangxym/article/details/101107702?ops_request_misc=%7B%22request%5Fid%22%3A%22165155021216780357213584%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=165155021216780357213584&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-1-101107702.142^v9^control,157^v4^control&utm_term=c%2B%2B++%3A&spm=1018.2226.3001.4187)

# 简单实例

```cpp
// 表达式1 ? 表达式2 ：表达式3
int a = 1;
int b = 2;
int c = a > b ? a + b : a - b;
cout << c << endl;
```

