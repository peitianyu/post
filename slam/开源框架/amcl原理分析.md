---
title: amcl原理分析
date: 2021-11-25 12:30:12
categories:
  - slam
  - 开源框架
tags:
  - slam
---

# 参考网址:

[slam 学习之 AMCL 概念与原理分析_学之知之的博客-CSDN博客_amcl定位原理](https://blog.csdn.net/p942005405/article/details/108569970?ops_request_misc=%7B%22request%5Fid%22%3A%22163486681116780264091671%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163486681116780264091671&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-2-108569970.pc_search_ecpm_flag&utm_term=amcl&spm=1018.2226.3001.4187)

[机器人定位技术：AMCL的参数你真的会调了吗？(一） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/126199044)

改写amcl

[ROS导航包源码学习2 --- 定位 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/28137335)



# 概念算法

- 运动模型(里程计模型)
- 测量模型(波束模型与似然域模型)
- 采样算法(KLD采样)
- 蒙特卡洛算法
- 自适应蒙特卡洛算法

# 运动模型

关于`速度模型`与`里程计模型` ,主要采取`采样模型`

[概率机器人](https://peitianyu.github.io/blog/2021/10/28/概率机器人/)

这里主要使用里程计模型

# 测量模型

`波束模型`主要调整六个参数:![[公式]](https://www.zhihu.com/equation?tex=%5Calpha_%7Bhit%7D) ， ![[公式]](https://www.zhihu.com/equation?tex=%5Calpha_%7Bshort%7D) ， ![[公式]](https://www.zhihu.com/equation?tex=%5Calpha_%7Brand%7D) ， ![[公式]](https://www.zhihu.com/equation?tex=%5Calpha_%7Bmax%7D) ， ![[公式]](https://www.zhihu.com/equation?tex=b) （ ![[公式]](https://www.zhihu.com/equation?tex=P_%7Bhit%7D) 的方差）和λ（动态障碍物的影响力度)

![img](https://pic4.zhimg.com/80/v2-d036db5bb440afe522b644db5bf3324f_720w.jpg)

似然域模型主要调整两个参数![[公式]](https://www.zhihu.com/equation?tex=%5Calpha_%7Bhit%7D)  ![[公式]](https://www.zhihu.com/equation?tex=%5Calpha_%7Brand%7D) 。

![img](https://pic1.zhimg.com/80/v2-107260979458a0bffb5a1ba9e22d2168_720w.jpg)

# KLD采样

**目的**:**将估计的概率分布转化为真实的概率分布。**

![img](https://img-blog.csdnimg.cn/20190720103418660.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjUzMDIzOQ==,size_16,color_FFFFFF,t_70)

# amcl理解与拓展

![amcl理解.png](https://s2.loli.net/2022/05/23/zpunel8CE9NgUYd.png)

![amcl.png](https://s2.loli.net/2022/05/23/FobivdtWZPBpk5T.png)

