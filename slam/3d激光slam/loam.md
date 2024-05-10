---
title: loam代码理解
date: 2023-6-15 12:30:12
categories:
  - 3d激光slam
tags:
  - 3d激光slam
---

# 代码论文

https://github.com/cuitaixiang/LOAM_NOTED

[LOAM: Lidar Odometry and Mapping in Real-time](https://link.zhihu.com/?target=https%3A//www.ri.cmu.edu/pub_files/2014/7/Ji_LidarMapping_RSS2014_v8.pdf)

# 算法简介

LOAM整体思想是将复杂问题分为: **1.高频运动估计(10HZ, 粗估计), 2. 低频的环境建图估计(1HZ, 精估计).**

scan2scan实现高频位姿估计, 之后采用scan2map实现低频位姿估计.

## 高频运动估计(10HZ, 粗估计)

scan2scan实现高频匹配, 实现点线匹配与点面匹配, 由于运动中激光会有畸变, 所以还需要做去畸变操作.

1. 激光去畸变, 基于匀速假设, 通过imu修正角度, 通过然后通过匀速修正偏移
2. 边缘检测与平面检测(两个点边缘线, 三个点面片) 构建残差 (kd_tree), 这里实际上隐含了多线信息条件
3. 点线匹配与点面匹配

## 低频的环境建图估计(1HZ, 精估计)

map2map实现低频匹配, 采用了PCA主成分思想提取线面特征，达到降维的目的,保证匹配精度的同时提升匹配效率。

1. 周围最近5个点, 构建PCA特征
2. 算出直线和或平面的法向量(这一步实现了一定程度的降采样)，按照点到线和点到面，计算损失函数, 构建残差

## 融合输出

通过时间做一个坐标变换, 之后以此为基准, 将lidar odom转换输出