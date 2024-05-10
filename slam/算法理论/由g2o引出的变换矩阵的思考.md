---
title: 由g2o引出的变换矩阵的思考
date: 2022-3-4 12:30:12
categories:
  - slam
  - 理论学习
tags:
  - slam
---

# 参考网址:

[Slam-Project-Of-MyOwn](https://github.com/softdream/Slam-Project-Of-MyOwn/tree/master/test/slam_simulation_with_g2o) 

 [gaoxiang12/slambook2: edition 2 of the slambook (github.com)](https://github.com/gaoxiang12/slambook2) 

# 简介

由于最近在看道锋大佬的slam代码,其中有这样一段关于g2o后端优化的程序,不太理解,于是找道锋大佬咨询

代码:

```c
Eigen::Matrix3d information = 1 * Eigen::Matrix3d::Identity(); //information matrix
Eigen::Matrix<float, 3, 3> T1 = slam.v2t( robotPoseCurr );
Eigen::Matrix<float, 3, 3> T2 = slam.v2t( keyPoses[loopId] );
Eigen::Matrix<float, 3, 3> T = T1.inverse() * T2;//这里T值得是转移矩阵
Eigen::Vector3f V = slam.t2v( T );// 矩阵转向量
optimizer.addEdge( V, keyFrameCount, loopId, information ); // add a loop constraint
```

v2t与t2v代码如下:

```c
// 这里v是当前机器人位姿到回环点的位姿的位姿增量[dx,dy,dtheta],
// T是当前机器人位姿到回环点的位姿坐标变换矩阵（齐次坐标变换，就是由旋转矩阵加平移组成的）
// T = [R,t
// 		0,1] R为旋转矩阵,t为移动矩阵
const Eigen::Matrix<float, 3, 3> SlamProcessor::v2t(const Eigen::Vector3f &v)
{
	float c = ::cos( v(2) );
	float s = ::sin( v(2) );
	Eigen::Matrix<float, 3, 3> A;
    
	A << c, -s, v(0),
		s,  c, v(1),
		0,  0,  1;
    
	return A;
}

const Eigen::Vector3f SlamProcessor::t2v(const Eigen::Matrix<float, 3, 3> &A)
{
	Eigen::Vector3f v;
        
	v(0) = A(0, 2);
	v(1) = A(1, 2);
	v(2) = ::atan2( A( 1, 0 ), A(0, 0) );

	return v;
}
```

于是翻出珍藏多年的slam14讲

首先:变换矩阵T表现为旋转矩阵R与平移矩阵t

```
// T = [R,t
// 		0,1] R为旋转矩阵,t为移动矩阵
```

而这里的T1与T2分别为当前车体坐标与回环点坐标,将他们先转换为其次坐标之后做变换可得变换矩阵

```matlab
T2 = T1*T ==> inv(T1)*T1*T = inv(T1)*T2 ==> T = inv(T1)*T2
```

这也是齐次坐标的好处,便于做坐标转换