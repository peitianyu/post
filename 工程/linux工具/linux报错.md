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
E: Could not get lock /var/lib/dpkg/lock-frontend - open (11: Resource temporarily unavailable)
E: Unable to acquire the dpkg frontend lock (/var/lib/dpkg/lock-frontend), is another process using it?
```

```shell
sudo rm /var/lib/dpkg/lock-frontend
sudo rm /var/lib/dpkg/lock
sudo rm /var/cache/apt/archives/lock
```

# cannot create temp file for here-document: No space left on device的问题解决

参考网址

 [Linux出现cannot create temp file for here-document: No space left on device的问题解决 - EasonJim - 博客园 (cnblogs.com)](https://www.cnblogs.com/EasonJim/p/6833354.html) 

问题

```shell
bash: cannot create temp file for here-document: No space left on devic
```

```shell
# 查看/var/log路径下文件的大小
du -h –max-depth=1 /var/log/* 
#  查看哪个目录最大，一步一步的查找大文件
du -sh /*
# 查找最大的文件，参考：http://www.cnblogs.com/kerrycode/p/4391859.html
du -h --max-depth=1
```

