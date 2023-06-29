---
title: cc++中malloc和calloc以及relloc使用
date: 2022-2-24 12:30:12
categories:
  - 语言
  - cpp
tags:
  - cpp
---

# 参考网址

 [(110条消息) c语言中calloc是什么意思,C语言中malloc和calloc以及relloc函数的区别_棒棒鸡不棒的博客-CSDN博客](https://blog.csdn.net/weixin_35890173/article/details/117107121?ops_request_misc=%7B%22request%5Fid%22%3A%22164568581316780255219883%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=164568581316780255219883&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-117107121.pc_search_insert_es_download&utm_term=calloc&spm=1018.2226.3001.4187) 

# 区别

##  malloc 

 char *p = (char*)maolloc(100); 

##  calloc 

 calloc申请的动态内存空间是干净的内存，当你输出内存中的数据时它是NULL  ,而malloc()申请的动态内存空间不一定是干净的，当你输出时有可能会打印出一些未知的数据 

##  relloc 

 rellc()函数是用于你的动态内存空间不足以存储你所要储存的信息时，需要再追加一段内存，函数的格式是：(类型说明符*)relloc(p,size);p是你原来申请的动态空间的首地址，是你要申请的动态内存的大小，而不是你要追加的动态内存空间大小，这一点要切记。 

