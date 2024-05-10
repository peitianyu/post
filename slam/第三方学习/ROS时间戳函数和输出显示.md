---
title: ROS时间戳函数和输出显示
date: 2022-3-7 12:30:12
categories:
  - slam
  - ros
tags:
  - ros
---

# 参考网址:

  [(112条消息) ROS时间戳函数和输出显示_Kevin_Xie86的博客-CSDN博客_ros 时间戳](https://blog.csdn.net/Kevin_Xie86/article/details/112765340?ops_request_misc=%7B%22request%5Fid%22%3A%22164662467216780357265424%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164662467216780357265424&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-1-112765340.pc_search_insert_es_download&utm_term=ros时间戳打印&spm=1018.2226.3001.4187) 

# 代码

```c
double t = msg->header.stamp.toSec()//把时间戳转化成浮点型格式
pringf("time:%f\n",t);//输出显示
```

