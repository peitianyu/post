---
title: 3dslam - 简介
date: 2023-1-9 12:30:12
categories:
  - slam
  - 3d_laser_slam
tags:
  - 3d_laser_slam

---

# 代码库

https://github.com/peitianyu/3d_laser_slam.git

# 框架设计

[![pSepyh6.png](https://s1.ax1x.com/2023/01/09/pSepyh6.png)](https://imgse.com/i/pSepyh6)

## 框架说明

```
设计思路主要重心在模块化, 便于优化

param_server: 由于参数配置几乎设计所有环节,所以采用单例模式
data_parse: 采用单例实现,考虑多线程操作,这里用了跨线程类操作
	- in_data: imu, point_cloud, gps
	- out_data: map, out_pose 
front_end: 前端主要为后端提供里程与因子, 并使用跨线程类与单例
	- scan_odom_factor(可能需要多线程操作, 处理有些复杂)
	- gps_factor
	- imu_preintegration_factor
	- loop_close_factor
back_end: 后端使用因子图优化/ekf优化获得最终输出pose
update map, pose: 
	- 根据后端优化获得的优化pose更新map, UpdateByScan(pose, scan)
	- 根据后端优化获得的优化pose与imu实时输出里程坐标
```

## 框架有意思的点希望与大家探讨

```
1. scan_odom_factor
	: 在获得scan_odom时采用的方式方法, 我这里简单的写了个scan2map(主体思路还在于icp), 
		之后可能补上scantomap(使用栅格概率), ndt, csm, 方法大体相通
2. loop_close_factor
	: 回环因子获得方式, 我打算采用scan_context, 不过有考虑将其换成lris,或者icp, 如果有好的思路希望互相探讨
3. update map
	: 我一直拿不定主意使用哪种地图, 常见的地图有直接保存点云pcd, 八叉树, tsdf, 栅格地图, 这里我才用的是稀疏化的栅格图, 可以讨论下
	- 使用栅格地图的原因在于, 1. map内存较少(我这里只保存了占用点), 2. 栅格地图的概率可用于做scantomap优化, 3. 可以较完整的将点云特征保存下来
4. back_end
	: 思考使用因子图优化实现, 当然如果学习的话, 可以使用ekf等进行多传感器融合
5. 在设计的时候有考虑三种情况, 多线程, 参数全局化与模块化, 我的处理方式是跨线程类,单例与基函数, 有推荐的设计模式还是希望与大家探讨
```

# TODO

```
1. data_parse测试
2. scan预处理与特征提取测试
3. scan_match测试
4. scantomap
5. imu
6. factor
7. viz
```

