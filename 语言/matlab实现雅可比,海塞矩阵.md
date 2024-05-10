---
title: matlab实现雅可比,海塞矩阵
date: 2022-3-1 12:30:12
categories:
  - 语言
  - matlab
tags:
  - slam
  - matlab
---

# 参考网址:

 [(110条消息) matlab 构建雅可比矩阵,matlab jacobian函数_weixin_39726697的博客-CSDN博客](https://blog.csdn.net/weixin_39726697/article/details/115901738?ops_request_misc=%7B%22request%5Fid%22%3A%22164613329816780271527587%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fblog.%22%7D&request_id=164613329816780271527587&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-1-115901738.nonecase&utm_term=matlab+jacobian()&spm=1018.2226.3001.4450) 

 [(110条消息) matlab不能使用syms,matlab syms什么意思_不告诉你不告诉你的博客-CSDN博客](https://blog.csdn.net/weixin_42363600/article/details/115823762?ops_request_misc=%7B%22request%5Fid%22%3A%22164613336816780274199935%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=164613336816780274199935&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-4-115823762.pc_search_insert_es_download&utm_term=matlab+syms&spm=1018.2226.3001.4187) 

 [(110条消息) matlab计算hessian矩阵_微电子学与固体电子学-CSDN博客_matlab计算海塞矩阵](https://blog.csdn.net/appleyuchi/article/details/95385461?ops_request_misc=&request_id=&biz_id=102&utm_term=matlab海瑟矩阵&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~sobaiduweb~default-1-95385461.nonecase&spm=1018.2226.3001.4450) 

 [(110条消息) Matlab中gradient函数的使用方法_Simy Hsu's Blog-CSDN博客_gradient matlab](https://blog.csdn.net/u014754127/article/details/53486816?ops_request_misc=%7B%22request%5Fid%22%3A%22164613362016780261992748%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164613362016780261992748&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-53486816.pc_search_insert_es_download&utm_term=matlab+gradient&spm=1018.2226.3001.4187) 

# 实例

```matlab
clc;clear all;close;
% syms x y z
% jacobian([x*y*z, y^2, x + z], [x, y, z])
%
syms a b c x% 设变量为a,b,c
f = exp(a*x^2+b*x+c);
d = jacobian(f,[a,b,c]);% 进行有雅可比变换
disp(d)
% pretty(d)
% hessian矩阵 = 梯度矩阵的雅可比矩阵
% syms x y z;
% f1=(x^2-2*x)*exp(-x^2-y^2-x*y);
% f = x*y + 2*z*x;
% hessian(f,[x,y,z])
% gradient(f1):求f1梯度
S=jacobian(gradient(f,[a,b,c]),[a,b,c]);
disp(S)
% pretty(S)
```

