---
title: c++查看内存使用
date: 2022-5-9 12:30:12
categories:
  - 语言
  - cpp
tags:
  - cpp
---

# 参考网址:

 [(131条消息) PROC系列之---/proc/pid/statm_沙漠里的海豚的博客-CSDN博客_proc statm](https://blog.csdn.net/zjl_1026_2001/article/details/2294072?ops_request_misc=%7B%22request%5Fid%22%3A%22165208801016781667894376%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=165208801016781667894376&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-4-2294072-null-null.142^v9^pc_search_result_cache,157^v4^control&utm_term=%2Fproc%2Fself%2Fstatm&spm=1018.2226.3001.4187) 

# 简介

```sh
cat  /proc/self/statm
654 57 44 0 0 334 0
```

## 解释

```txt
CPU 以及CPU0。。。的每行的每个参数意思（以第一行为例）为：
参数 解释 /proc/ /status
Size (pages) 任务虚拟地址空间的大小 VmSize/4
Resident(pages) 应用程序正在使用的物理内存的大小 VmRSS/4
Shared(pages) 共享页数 0
Trs(pages) 程序所拥有的可执行虚拟内存的大小 VmExe/4
Lrs(pages) 被映像到任务的虚拟内存空间的库的大小 VmLib/4
Drs(pages) 程序数据段和用户态的栈的大小 （VmData+ VmStk ）4
dt(pages) 0 
```

# 参考程序

```cc
void dump_mem_usage()
{
    FILE* f = fopen("/proc/self/statm", "rt");
    if (!f) return;
    char   str[300];
    size_t n = fread(str, 1, 200, f);
    str[n]   = 0;
    printf("MEM: %s\n", str);
    fclose(f);
}
```

