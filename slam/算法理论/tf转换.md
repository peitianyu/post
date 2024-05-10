---
title: tf转换
date: 2022-2-17 12:30:12
categories:
  - slam
  - 理论学习
tags:
  - slam
  - ros
---

# 参考网址

 [(109条消息) 从零开始搭二维激光SLAM --- 基于PL-ICP的激光雷达里程计_李想的博客-CSDN博客_base_to_laser](https://blog.csdn.net/tiancailx/article/details/111593526?spm=1001.2014.3001.5501) 

 [(109条消息) ROS小白学习历程-map/odom/base_link坐标系的关系及其转换_kuai-的博客-CSDN博客](https://blog.csdn.net/qq_43481884/article/details/105429655) 

# 常见坐标系

```txt
map: 地图坐标系，也被称为世界坐标系，是静止不动的
odom: 里程计坐标系，相对于map来说一般情况下是静止的，有些情况下会变动（定位节点为了修正机器人的位姿从而改变了map->odom间的坐标变换）
base_link: 代表机器人的旋转中心的坐标系，相对于odom来说base_link是运动的
laser_link: 激光雷达的坐标系，相对于base_link来说是静止的，因为雷达装在机器人上，雷达不会自己飞起来
一般依赖关系为:map -> odom -> base_link -> laser_link
```

# 注意

```
1, odom一般相对于map是静止的但由于odom有累计误差,所以还是会有偏移,并且map与odom之间存在父子关系
2, 参照系紧紧粘在移动机器人基座上的任何一个位置和角度,一般我取控制中心
3, odom到base_link的坐标转换是从运动源计算出来广播的。
4, map到base_link的坐标转换是被定位模块计算出来的. 但定位模块并不发布map到base_link的转换. 相反它先接受		从odom到base_link的转换, 再计算并广播map到odom的位置转换关系。

```





