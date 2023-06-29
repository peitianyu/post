---
title: linux_shell
date: 2021-12-9 12:30:12
categories:
  - 工程
  - linux工具
tags:
  - linux
---

# 删除除某文件以外其他文件

## 参考网址:

[linux删除指定文件夹中某个文件除外的其他文件 - He元素 - 博客园 (cnblogs.com)](https://www.cnblogs.com/blueskycc/p/5139902.html)

```shell
shopt -s extglob
rm -fr !(file1)
# 如果是多个要排除的，可以这样：
rm -rf !(file1|file2)
```

# 挂载

## 参考网址

[ubuntu 挂载windows共享目录的方法 - Supper阿欣 - 博客园 (cnblogs.com)](https://www.cnblogs.com/windchen/p/6236299.html)

[linux挂载文件报错mount error(13): Permission denied - 风中追风h - 博客园 (cnblogs.com)](https://www.cnblogs.com/fzzfh/p/10330014.html)

[win10 家庭版 cmd运行gpedit.msc找不到文件？ - 知乎 (zhihu.com)](https://www.zhihu.com/question/41745930/answer/510335896)

[(2条消息) ubuntu挂载Windows共享文件夹_深藏功与名-CSDN博客_ubuntu挂载windows共享目录](https://langbin.blog.csdn.net/article/details/78929045)

## pi挂载Ubuntu

```shell
sudo mount 192.168.2.111:/home/pty/nfs/ /mnt  
```

## pi挂载win

```shell
sudo mount -t cifs //192.168.10.1/d  /mnt -o username="pty",password="pty123",sec=ntlmssp,rw,dir_mode=0777,file_mode=0777
```

