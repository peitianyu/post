---
title: ubuntu18查看磁盘占用
date: 2022-3-30 12:30:12
categories:
  - 工程
  - linux工具
tags:
  - 嵌入式
---

# 参考网址:

 [(121条消息) ubuntu查看磁盘占用和分配情况_forAllforMe的博客-CSDN博客_ubuntu 查看磁盘占用](https://blog.csdn.net/cym_anhui/article/details/82736198?ops_request_misc=%7B%22request%5Fid%22%3A%22164861859916782089375824%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164861859916782089375824&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-82736198.142^v5^article_score_rank,143^v6^control&utm_term=ubuntu查看磁盘占用&spm=1018.2226.3001.4187) 

#  df  -hl 

```shell
Filesystem      Size  Used Avail Use% Mounted on
udev            978M     0  978M   0% /dev
tmpfs           200M  3.6M  197M   2% /run
/dev/sda5        28G   12G   15G  45% /
tmpfs          1000M  216K  999M   1% /dev/shm
tmpfs           5.0M  4.0K  5.0M   1% /run/lock
tmpfs          1000M     0 1000M   0% /sys/fs/cgroup
tmpfs           200M   52K  200M   1% /run/user/1000
```

#  sudo fdisk -l 

```shell
Disk /dev/sda: 60 GiB, 64424509440 bytes, 125829120 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x0b032873

Device     Boot    Start      End  Sectors  Size Id Type
/dev/sda1  *        2046 60547071 60545026 28.9G  5 Extended
/dev/sda5           2048 58593279 58591232   28G 83 Linux
/dev/sda6       58595328 60547071  1951744  953M 82 Linux swap / Solaris
```

#  free -m  查看内存

```shell
lostman@lostman-MS-6702E:~/桌面$ free -m
                      total       used       free     shared    buffers     cached
Mem:               496        457         38          0          8            104
-/+ buffers/cache:           344        151
Swap:              510        156        354
```

# top 查看内存与cpu



