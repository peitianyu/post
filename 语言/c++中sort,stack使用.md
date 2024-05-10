---
title: c++中sort,stack使用
date: 2022-2-24 12:30:12
categories:
  - 语言
  - cpp
tags:
  - cpp
---

# 参考网址

 [(110条消息) C++ sort()排序详解_ACfun-CSDN博客_c++sort排序](https://blog.csdn.net/qq_41575507/article/details/105936466?ops_request_misc=%7B%22request%5Fid%22%3A%22164559614416780255251330%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164559614416780255251330&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-105936466.pc_search_insert_es_download&utm_term=sort排序&spm=1018.2226.3001.4187) 

 [(110条消息) STL源码剖析 [容器\]（五）[stl_stack.h]_深藏功与名-CSDN博客](https://blog.csdn.net/langb2014/article/details/48103447) 

# sort使用

```c++
bool cmp1(data a, data b){    
    return a.x < b.x;    
}
// 这里使用的是指针
sort(exm_set,exm_set + size, cmp1);   
```

# stack使用

```c++
stack<Tnode*> search_path; 
Tnode* pSearch = Kd; 
// stack容器 - 先进后出
// 支持在这里进行push(), pop(), top()操作  
// 比如:
search_path.push(pSearch);  
search_path.top()
search_path.pop()
```

