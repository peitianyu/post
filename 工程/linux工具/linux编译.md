---
title: linux报错
date: 2022-1-15 12:30:12
categories:
  - 工程
  - linux工具
tags:
  - linux
---

# 参考网址:

 [(99条消息) 编译警告：warning: Clock skew detected. Your build may be incomplete._竭尽全力的专栏-CSDN博客](https://blog.csdn.net/JIEJINQUANIL/article/details/106414938) 

# warning: Clock skew detected

```shell
find . -type f | xargs -n 5 touch
```

# apt upgdate 失败

```shell
sudo rm /var/lib/apt/lists/lock
sudo rm /var/cache/apt/archives/lock
sudo rm /var/lib/dpkg/lock
sudo dpkg --configure -a 
```

