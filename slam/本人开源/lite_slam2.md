---
title: lite_slam - ScanMatch
date: 2022-9-5 12:30:12
categories:
  - slam
  - lite_slam
tags:
  - lite_slam
---

# 参考网址:

[peitianyu/lite_slam (github.com)](https://github.com/peitianyu/lite_slam)

[Hector SLAM解读（1）原文翻译 - cyberniklee - 博客园 (cnblogs.com)](https://www.cnblogs.com/cyberniklee/p/8484104.html)

## ScanMatch

```
目的主要是输出激光里程计,这里使用的是scantomap
采用的数据集是r2000数据包,因此对于数据预处理比较简单,且定位精度足够
为加快速度:
	- 多层地图(避免局部最优)
	- 多种分辨率滤波后的激光
	- 迭代残差足够小退出迭代
整体流程:
	输入: pose, scan_points, grid_map(来源: 1, 建图 2, 加载)
	迭代直到更新距离足够小或者超出最大迭代次数
	输出: estimate_pose
```

## 迭代一次流程

```c++
bool ScanToMap::EstimateTransformationOnce(const std::vector<Eigen::Vector2f> &scan_points, std::shared_ptr<GridMapUtils> map_utils, Eigen::Vector3f &estimate_in_world)
{
    Eigen::Matrix3f H = Eigen::Matrix3f::Zero();
    Eigen::Vector3f dTr = Eigen::Vector3f::Zero();

    Eigen::Vector3f last_estimate_pose = estimate_in_world;
    GetHessianDerivative(estimate_in_world, scan_points, map_utils, H, dTr); // 求解海森矩阵, 通过高斯牛顿求解

    if(H(0, 0) != 0 || H(1, 1) != 0)
    {
        Eigen::Vector3f d_pose_in_map = H.inverse() * dTr;
        UpdateEstimatedPose(map_utils, estimate_in_world, d_pose_in_map); // 更新预测坐标
    }

    if(PoseDiffSmallerThan(last_estimate_pose, estimate_in_world)) return true; // 判断更新距离足够小,则退出

    return false;
}
```

## 对于scantomap理解

```
1. 通过scan_point与grid_map匹配构建最小二乘,通过高斯牛顿求解
2. 由于求解时发现grid_map是离散的需要线性化,所以进行双线性插补,得到线性化后map,即▽map
3. 由于匹配时间过长以及担心局部最优,进行多分辨率地图设计
```

## 公式推导

[![v7JwZj.png](https://s1.ax1x.com/2022/09/06/v7JwZj.png)](https://imgse.com/i/v7JwZj)

## 代码体现

```c++
void ScanToMap::GetHessianDerivative(const Eigen::Vector3f &robot_in_world, const std::vector<Eigen::Vector2f> &scan_point, 
                                            std::shared_ptr<GridMapUtils> map_utils, Eigen::Matrix3f &H, Eigen::Vector3f &dTr)
{
    float ss = sin(robot_in_world(2));
    float cs = cos(robot_in_world(2));

    for(uint i = 0; i < scan_point.size(); i++)
    {
        Eigen::Vector2f point_in_scaled_laser = map_utils->LaserInScaledLaser(scan_point[i]); // 获得栅格化的laser_point

        Eigen::Vector2f point_in_world = map_utils->LaserPointToWorld(scan_point[i], robot_in_world); // 求解世界坐标系下的point

        Eigen::Vector2f point_in_map = map_utils->WorldToMapFloat(point_in_world); // 求解地图坐标系下的laser_point

        Eigen::Vector3f interpolated_value = BilinearInterpolationWithDerivative(point_in_map, map_utils); // 双线性插补

        float func_val = 1 - interpolated_value(0); // 具体公式参考hector论文
        dTr(0) += func_val * interpolated_value(1);
        dTr(1) += func_val * interpolated_value(2);
        float rot_deriv = (interpolated_value(1) * (-ss * point_in_scaled_laser(0) - cs * point_in_scaled_laser(1)) +
                           interpolated_value(2) * (cs * point_in_scaled_laser(0) - ss * point_in_scaled_laser(1)));
        dTr(2) += rot_deriv * func_val;

        H(0, 0) += interpolated_value(1) * interpolated_value(1);
        H(1, 1) += interpolated_value(2) * interpolated_value(2);
        H(2, 2) += rot_deriv * rot_deriv;

        H(0, 1) += interpolated_value(1) * interpolated_value(2);
        H(0, 2) += interpolated_value(1) * rot_deriv;
        H(1, 2) += interpolated_value(2) * rot_deriv;
    }

    H(1, 0) = H(0, 1);
    H(2, 0) = H(0, 2);
    H(2, 1) = H(1, 2);
}

Eigen::Vector3f ScanToMap::BilinearInterpolationWithDerivative(const Eigen::Vector2f &point_in_map, std::shared_ptr<GridMapUtils> map_utils) const
{   
    float factor0 = point_in_map(0) - floor(point_in_map(0));
    float factor1 = point_in_map(1) - floor(point_in_map(1));
    float factor0_inv = 1 - factor0;
    float factor1_inv = 1 - factor1;

    float p00 = map_utils->GetCellProb(point_in_map.cast<int>()); // 栅格四角prob
    float p01 = map_utils->GetCellProb(point_in_map.cast<int>() + Eigen::Vector2i(0, 1));
    float p10 = map_utils->GetCellProb(point_in_map.cast<int>() + Eigen::Vector2i(1, 0));
    float p11 = map_utils->GetCellProb(point_in_map.cast<int>() + Eigen::Vector2i(1, 1));

    return Eigen::Vector3f(((factor1*(factor0*p11 + factor0_inv*p01)) + (factor1_inv * (factor0*p10 + factor0_inv*p00))),
                                			( factor1 * ( p11 - p01 ) + factor1_inv * ( p10 - p00 ) ),
                                			( factor0 * ( p11 - p10 ) + factor0_inv * ( p01 - p00 ) ));
}
```

