---
title: Singleton
date: 2022-5-5 12:30:12
categories:
  - 工程
  - 代码工具
tags:
  - cpp
hidden: true
---

# 参考网址:

 [DesignPatterns (github.com)](https://github.com/pezy/DesignPatterns/blob/master/Singleton/main.cpp) 

# 注意

主要用于全局变量(单例模式)

# 代码

```c++
#include<iostream>

template<class T>
class Singleton
{
public:
  Singleton(const Singleton&) = delete;
  Singleton& operator=(const Singleton&) = delete;
  Singleton():m_pInstance(nullptr){}

  virtual ~Singleton(){ delete m_pInstance; }
  T* GetInstance() {
    if(m_pInstance == nullptr) m_pInstance = new T();
    return m_pInstance;
  }
private:
  T* m_pInstance;
};

class A
{
   public:
     static A* GetInstance()
     {
       static Singleton<A> singleton;
       return singleton.GetInstance();
     }
     void test()
     {
       std::cout<<"A"<<std::endl;
     }
};

int main()
{
  A* a = A::GetInstance();
  a->test();
  return 0;
}
```

