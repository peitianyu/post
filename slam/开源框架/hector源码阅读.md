---
title: hector源码理解
date: 2021-11-5 12:30:12
categories:
  - slam
  - 开源框架
tags:
  - slam
---

# 参考网址:

大佬的分析,包含代码注释解析与源码重写,论文翻译

[Hector_slam源码框架分析_翎风的博客-CSDN博客](https://blog.csdn.net/qq_36355662/article/details/90349302)

[Hector SLAM解读（1）原文翻译 - cyberniklee - 博客园 (cnblogs.com)](https://www.cnblogs.com/cyberniklee/p/8484104.html)

[HectorSLAM论文解析・代码重写（2） - scomup - 博客园 (cnblogs.com)](https://www.cnblogs.com/scomup/p/7075296.html)

[zhangwenxiao/HectorSlamWithoutROS: 脱离ROS的hector slam算法 (github.com)](https://github.com/zhangwenxiao/HectorSlamWithoutROS)

# 代码框架

该开源系统的主要代码在hector_mapping文件夹中，文件夹里有src文件夹和include文件夹，包含了算法所有的核心代码。
通过对源码的梳理，整理出了如下流程图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190520100840536.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MzU1NjYy,size_16,color_FFFFFF,t_70)

大佬的图非常清晰的可以看到,整个代码分为`地图匹配`与`地图更新`

# 地图匹配

主要采用**非线性优化**----**高斯牛顿法**,实际上就是通过激光数据与现有地图的差距(双线性插补),通过最小二乘法,计算概率,从而更新位移增量,计算出机器人最可能存在位置

## 双线性插值计算栅格概率

[一篇文章为你讲透双线性插值 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/110754637)

通过双线性插补得到一个位置,然后通过最小二乘法,更新位移增量

## 高斯牛顿法

[高斯牛顿法详解_我只是一只自动小青蛙的博客-CSDN博客](https://blog.csdn.net/qq_42138662/article/details/109289129)

[【泡泡机器人公开课】第三十课：非线性优化与g2o-高翔_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1b441177kr?from=search&seid=5703864891923353812&spm_id_from=333.337.0.0)

# 地图更新

用bresenham`划线`算法计算激光途经栅格

[(60条消息) Bresenham 算法原理_yzh1994414的博客-CSDN博客_bresenham](https://blog.csdn.net/yzh1994414/article/details/82860187)

[画线算法-Bresenham算法_天才樱木-CSDN博客_bresenham画线算法](https://blog.csdn.net/datase/article/details/83620338)

# 优缺点

#### 1.优点

（1）不需要使用里程计，可以用于地面不平坦区域及空中飞行器。
（2）使用多分辨率地图能避免局部最小值。

#### 2.缺点

（1）要求雷达更新频率较高，测量噪声小；或者机器人运动速度低。
（2）无法利用精确的里程计信息。

