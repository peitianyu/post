---
title: hex解析
date: 2022-1-3 12:30:12
categories:
  - 工程
  - 嵌入式
tags:
  - 嵌入式
---

# 参考网址:

 [(94条消息) HEX文件格式解析_a1037488611的专栏-CSDN博客_hex文件格式解析](https://blog.csdn.net/a1037488611/article/details/43340055?ops_request_misc=%7B%22request%5Fid%22%3A%22164121480016780269824327%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164121480016780269824327&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~sobaiduend~default-1-43340055.nonecase&utm_term=hex解析&spm=1018.2226.3001.4450) 

# 格式如下

![1641215282358.png](https://s2.loli.net/2022/01/03/UjoEasnlNmLwYtZ.png)

```
：(冒号)每个Intel HEX 记录 都由冒 号开头 ；
LL 是 数 据 长 度域, 它 代表 记录当 中 数 据字 节 (dd) 的 数量 ；
aaaa 是地址域, 它代表 记录当 中 数据的起始地址；
TT是代表HEX 记录类 型的域 , 它 可能是以下 数 据 当 中的一 个：
    00 – 数 据 记录（Data Record）
    01 – 文件结 束 记录（End of FileRecord）
    02 – 扩展段地址 记录（ExtendedSegment Address Record）
	03 – 开始段地址 记录（Start Segment Address Record）
    04 – 扩展 线 性地址 记录（Extended Linear Address Record）
	05 – 开始线性地址 记录（Extended Segment Address Record）
dd 是数 据域 , 它 代表一 个 字 节 的 数 据. 一 个记录 可以有 许 多 数 据字 节 . 记录当 中 数 据字 节 的 数 量必 须 和数 据 长 度域(ll) 中指定的 数字相符.
cc 是校验 和域 , 它 表示 这个记录 的校 验 和. 校 验 和的 计 算是通 过将记录当 中所有十六 进 制 编码数 字 对 的 值相加, 以256 为 模 进 行以下 补 足.

表示为：“：[1字节长度][2字节地址][1字节记录类型][n字节段][1字节校验和] ”
```

# 例如

```
:10 2462 00 464C5549442050524F46494C4500464C33
：[10个数据长度][字节地址为:0x2462][记录类型为:00 数据记录][n字节段数据][crc校验:0x33]
```

