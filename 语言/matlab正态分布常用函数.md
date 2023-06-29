---
title: matlab正态分布常用函数
date: 2022-3-1 12:30:12
categories:
  - 语言
  - matlab
tags:
  - matlab
---

# 参考网址:

  [(110条消息) 【Matlab】正态分布常用函数normpdf_normcdf_norminv_normrnd_normfit_Find your love-CSDN博客_matlab正态分布函数](https://blog.csdn.net/shanchuan2012/article/details/52901758?ops_request_misc=%7B%22request%5Fid%22%3A%22164603310116780265432441%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=164603310116780265432441&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-52901758.pc_search_insert_es_download&utm_term=normrnd&spm=1018.2226.3001.4187) 

# normrnd

用法

```matlab
R = normrnd(mu,sigma)         % 生成一个数
R = normrnd(mu,sigma,m,n,...) % 生成m*n列向量
```

例子

```matlab
>> normrnd(0,1)
ans =
    1.4122
>> normrnd(0,1,5,3)
ans =
    0.0226    0.9199   -0.7777
   -0.0479    0.1498    0.5667
    1.7013    1.4049   -1.3826
   -0.5097    1.0341    0.2445
   -0.0029    0.2916    0.8084
```

