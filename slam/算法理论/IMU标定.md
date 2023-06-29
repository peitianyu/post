---
title: shenlan_vio_course:02IMU标定
date: 2022-06-01 12:30:12
categories:
  - slam
  - 理论学习
tags:
  - slam
---

# 参考网址

 [cggos/shenlan_vio_course: 深蓝学院《视觉SLAM进阶：从零开始手写VIO》第一期 (github.com)](https://github.com/cggos/shenlan_vio_course) 

 [【传感器标定】IMU - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/357859357) 

# 误差来源

![v2-6f6b6c556c0e804276bc1e4a53904523_720w.jpg](https://s2.loli.net/2022/06/01/nTAN4wsMJraEH29.jpg)

# 标定方法

![v2-41a0f156c4c54f8cf6d67d6cf3825f62_720w.jpg](https://s2.loli.net/2022/06/01/y4ijfq1mdJK2htb.jpg)

##  Allan方差标定 

[imu_utils](https://github.com/gaowenliang/imu_utils)

原理: [多传感器融合定位理论基础（三）：惯性器件误差分析 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/125554723) 

##  梯度下降迭代优化标定 

 [Kyle-ak/imu_tk (github.com)](https://github.com/Kyle-ak/imu_tk) 

