---
title: c++中rand使用
date: 2022-2-24 12:30:12
categories:
  - 语言
  - cpp
tags:
  - cpp
---

# 参考网址

 [(110条消息) rand 函数 与 srand 函数_myKernel-CSDN博客_srand48](https://blog.csdn.net/cnmilan/article/details/7103786) 

 [(110条消息) drand48()函数_dwell548560的博客-CSDN博客_drand48](https://blog.csdn.net/dwell548560/article/details/116794540)

 [(132条消息) srand((unsigned)time(NULL))详解_清风lsq的博客-CSDN博客_srand(time(null))](https://blog.csdn.net/jx232515/article/details/51510336?ops_request_misc=%7B%22request%5Fid%22%3A%22165214478616780366576292%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=165214478616780366576292&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-51510336-null-null.142^v9^pc_search_result_cache,157^v4^control&utm_term=srand((unsigned)time(nullptr))&spm=1018.2226.3001.4187) 

# 简介

rand使用范围是( ***\*0~RAND_MAX\**** ), srand(seed)函数用于给rand()函数设定种子。一般若是设置srand(time(NULL))**,一现在时间为时间种子, 一般目的是由于rand为伪随机,加上以当前时间为种子的话,便于产生真实随机数

# 测试代码

```c++
 void test_rand(void)
     {
           unsigned long n;
          srand((unsigned)time(NULL));
          for(int i = 0; i < 100; i++)
          {
                n = rand();
                printf("d\n", n);
           }
}
```

# drand48()函数

 **drand48 返回服从均匀分布的·[0.0, 1.0) 之间的 double 型随机数。** 

测试代码

```c++
double r = drand48();
```

