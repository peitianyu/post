---
title: boost_thread
date: 2021-11-25 12:30:12
categories:
  - 语言
  - cpp
tags:
  - cpp
---

# 参考网址:

了解boost

[C++ Boost库分类总结（个人收藏） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/66486828)

boost教程

[Highscore - Boost C++ 库 - 多线程](http://zh.highscore.de/cpp/boost/multithreading.html)

# boost多线程同步简单使用

```c++
#include <boost/thread.hpp> 
#include <iostream> 

void wait(int seconds) 
{ 
  boost::this_thread::sleep(boost::posix_time::seconds(seconds)); 
} 

boost::mutex mutex; 

void thread() 
{ 
  for (int i = 0; i < 5; ++i) 
  { 
    wait(1); 
    mutex.lock(); 
    std::cout << "Thread " << boost::this_thread::get_id() << ": " << i << std::endl; 
    mutex.unlock(); 
  } 
} 

int main() 
{ 
  boost::thread t1(thread); 
  boost::thread t2(thread); 
  t1.join(); 
  t2.join(); 
} 
```

