---
title: shenlan_vio_course:03IMU预积分
date: 2022-06-01 12:30:12
categories:
  - slam
  - 理论学习
tags:
  - slam
---

# 参考网址

 [cggos/shenlan_vio_course: 深蓝学院《视觉SLAM进阶：从零开始手写VIO》第一期 (github.com)](https://github.com/cggos/shenlan_vio_course) 

 [imu预积分解析](https://blog.csdn.net/baidu_37561491/article/details/124051657?spm=1001.2101.3001.6650.4&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-4-124051657-blog-114164685.t5_eslanding_download_v1&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-4-124051657-blog-114164685.t5_eslanding_download_v1&utm_relevant_index=6)

 [多传感器融合定位理论基础（十一）：IMU预积分模型及应用 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/150364458) 

[【Momenta Paper Reading】第七期 预积分（Preintegration）_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1FW411C7R3?spm_id_from=333.337.search-card.all.click&vd_source=745fd1b1f3e42bb544237f6d0bf78bb2)

# 关于imu预积分理解

```
1. 由于imu在关键帧内,无法标定零偏,所以无法更新imu状态量[R, p, v, b]
2. 需要通过其他手段更新imu(视觉,或者激光等), 所以形成了紧耦合
3. 通过更新离散化,即可得到与状态量间残差[dR, dp, dv]
```

# 具体推导



