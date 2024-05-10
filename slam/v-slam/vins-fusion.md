# vins-fusion

## ubuntu20.04编译并测试MH_01_easy.bag

编译时报错[解决方案](https://blog.csdn.net/xiaojinger_123/article/details/121517771?utm_source=app&app_version=4.20.0)

```
# 安装依赖
sudo apt-get install liblapack-dev libsuitesparse-dev libcxsparse3 libgflags-dev libgoogle-glog-dev libeigen3-dev libgtest-dev
# 安装ceres-1.14.0
wget ceres-solver.org/ceres-solver-1.14.0.tar.gz
tar xvf ceres-solver-1.14.0.tar.gz
cd ceres-solver-1.14.0
mkdir build
cd build
cmake ..
make -j4
make test
sudo make install
# 创建catkin_ws
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/
catkin_make
# VINS-Fusion编译
cd ~/catkin_ws/src
git clone https://github.com/peitianyu/vin-fusion-ubuntu20.git
catkin_make
# 测试数据集 MH_01_easy.bag
roslaunch vins vins_rviz.launch
rosrun vins vins_node ~/catkin_ws/src/VINS-Fusion/config/euroc/euroc_stereo_imu_config.yaml 
(optional) rosrun loop_fusion loop_fusion_node ~/catkin_ws/src/VINS-Fusion/config/euroc/euroc_stereo_imu_config.yaml 
rosbag play YOUR_DATASET_FOLDER/MH_01_easy.bag
```

## 标定流程

```
1. 先使用camera_model实现内参标定
2. 代码运行时, 根据选择使用粗略外参, 或者使用精确外参, 若使用粗略外参, 则会在estimator.cpp中进行外参精标定
```

## 代码分析

```
从运行我们可以看到rosrun vins vins_node ~/catkin_ws/src/VINS-Fusion/config/euroc/euroc_stereo_imu_config.yaml 
最基本的流程是通过imu+camera实现的紧耦合前后端, 并没有加入回环检测与gps融合, 这些东西通过插件的方式来实现, 
例如使用回环, 会加入回环因子, 使用gps会加入gps因子, 最后融合输出

这边主要关注主模块也就是vins_node这部分

1. main函数
    a. 参数配置与初始化estimator
    b. 订阅数据源: imu, camera, 并将imu数据传给estimator
    c. 数据源时间同步并将camera数据传给estimator
2. 主要程序运行在estimator中
    a. 数据源订阅输入
        1. imu输入: a. 输入imu_buf b. 输出高频odom
        2. img输入: 光流追踪与特征提取(若非多线程会直接运行后端优化)
    b. 数据处理(processMeasurements)
        1. processIMU: 预积分与state更新, 加入预积分因子
        2. processImage: 1. camera外参标定 2. 加入点云因子, 优化位姿(这里优化与传统点云优化并无区别) 3. 滑窗 4. 优化特征点云
```

## [SFM算法流程](https://blog.csdn.net/lpj822/article/details/82716971?spm=1001.2101.3001.6650.7&utm_medium=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~Rate-7-82716971-blog-79834735.pc_relevant_multi_platform_whitelistv4&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~Rate-7-82716971-blog-79834735.pc_relevant_multi_platform_whitelistv4&utm_relevant_index=9)

```
1. 特征检测
2. 特征匹配(排除误匹配点)
3. 相机去畸变(相机内参求解归一化平面的相机点)
4. 计算对极几何计算F矩阵
5. 通过F矩阵SVD分解求解相对位姿
6. 三角法计算3D点云
7. pnp求解(直接线性变换(DLT), 光束平差法(BA))
在vins中sfm初始化的目的:
    1. 提供一组稀疏化特征land_marks
    2. 估计相机初始位姿
```

## 对极约束(2D->2D坐标变换求解)

```
意义:
    从优化角度来看, 我们希望找到一个坐标变换使得所有特征点与相机中心三点共面.
    即: Σ(p2T*F*p1) -> 0
一般做法是:
    通过八点法构建线性方程, 求解本质矩阵(E)或者基础矩阵(F), 然后通过奇异值分解(SVD)恢复坐标变换
```

## PNP(2D->3D坐标变换求解)

```
从优化角度来看: 我们需要找到一个坐标变换, 使得2d相机点与3d点云之间一一对应
即: Σ(u - KTP/s)^2 -> 0 , 也就是通过重投影误差实现光束平差法
```

## 总结

```
vins-fusion总体逻辑算是相当清晰了
0. 通过sfm构建初始化特征点云, 并提供初始位姿
1. 通过imu预积分提供imu预积分因子
2. 通过提取特征, 光流跟踪更新特征点云(这里使用光流似乎确实适合于室内使用)
3. 根据预积分提供先验位姿与点云构建位姿因子, 进行因子图优化
4. 只使用可观测点云, 其他点云会被处理掉
5. 加速处理: 滑窗处理

有趣的点:
1. vins-fusion通过因子图的方式实现的后端可拓展很有意思, 可以通过插件的方式实现loop_fusion, global_fusion
2. 在优化过程中vins-fusion在不断进行imu_camera外参优化
3. 从代码分析来看思路上似乎并不比lio_sam更优秀
4. 思考单独观测下特征点精度, 这是与点云的最大区别
5. 思考是否可以通过联合点优化点云精度
```

## 关于vins_fusion重构思考

```
模块:
0. system
1. image处理
2. imu预积分
3. 外参标定
4. 后端优化(用于管理factors)
5. math常用数学运算
6. 可视化
```