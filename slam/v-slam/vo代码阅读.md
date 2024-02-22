# basic_vSLAM

```
https://github.com/arorasarthak/basic_vSLAM
简单介绍:
    该项目为前端单相机, 视觉里程计模块; 整体思路遵循着提取特征点+构造描述子=匹配特征点的逻辑. 
    得到匹配好的特征点对之后做对极约束求解本质矩阵, 而对极约束存在尺度误差, 因此势必需要后期通过一些手段做尺度恢复;
    之后得到本质矩阵后, 通过SVD恢复相机运动, 之后做三角化恢复相机深度.
总结: 
    match_points => 对极约束 => svd恢复相机运动 => 通过相机运动三角化恢复相机点云
特点:
    该算法使用Shitomasi特征点与SIFT描述子来做特征点匹配.
```

# comp_vision

```
https://github.com/mllofriu/comp_vision
简单介绍:
    该项目包含三个测试文件: 坐标变换, 图像金字塔, sfm测试
    相机坐标变换:
        主要关于世界坐标系与相机坐标系之间的坐标变换
    图像金字塔: 
        使用opencv构建图像金字塔的基础使用
    sfm测试:
        1. 仿真匹配点对
        2. 求解本质矩阵
        3. 归一化本质矩阵
        4. 通过SVD恢复相机运动
```

# soft2

```
https://zhuanlan.zhihu.com/p/574423082

简单介绍:
    1. 提取Fl与Fr匹配特征点
    2. 通过RANSAC求解本质矩阵
    3. 通过Fr修正尺度误差与相对于Fl的旋转外参
    4. 通过BA优化(结合之前几帧数据), 优化最终估计
    5. 添加假设: 
        a. 路面上车辆或相似配置，没有纯旋转，一直有平移分量，防止本质矩阵的奇异性
        b. 若是仅前进," 相机主点和极点应大致重合"，使得极线与径向畸变和模糊同向
        c. 用单目即可估计旋转和平移方向，使得里程计对立体校正误差或设备变形更加鲁棒。
        d. 绝对尺度和两个相机的旋转外参可联合估计，因此通过持续在线优化提升精度
        e. 考虑双目和时间导致表象变化来提升特征匹配，因此可"引入地面特征"，提升平移估计精度
        f: SOFT2 提出多重假设视角修正（MHPC）匹配法。
            MHPC 主要驱动是获取均匀分布的特征信息，对于靠近相机的地面特征尤其有效，极大帮助平移估计。
            第一步，通过匀速假设或轻量化里程计（例如 SOFT）计算预先相对运动，
            第二步，在当前帧检测特征，用上一步的里程计来修正。注意只适用于长基线相机，例如 KITTI 数据集。
```

# dc_vmapping

```
https://github.com/syywh/dc_vmapping



FIXME: 信息太少又由于编译不通过, 因此并没有细读这篇代码!!!!!!!!!!!
```

# dso

```
https://github.com/JakobEngel/dso

简单介绍:
    通过稀疏直接法实现, 直接通过最小化光度误差实现. 即直接通过图像间误差决定, 而非重投影之后的几何误差决定;
    直接法将数据关联与位姿估计放在统一的非线性优化问题中;
    光度标定，认为对相机的曝光时间、暗角、伽马响应等参数进行标定后，能够让直接法更加鲁棒.(对于相机本身曝光造成的光影变化会有比较好的效果)

算法内容主要在makeKeyFrame函数中, 前面会一些数据预处理, 如绝对梯度金字塔等


常规思路是: 找到一个坐标变换使得两特征点重投影后的点云误差最小.
其优化思路在于: 找到一个坐标变换使得两个特征点间光度变化最小.
```

# Edrak

```
https://github.com/HemaZ/Edrak/

简单介绍:
    这是一个参考高翔14讲实现的一个算法库. 内部包含了pangolin的基础用法, 以及前端里程计的基本实现
    前端里程计整体思路是: 
        通过orb提取器提取并匹配特征点, 后比较汉明距离筛选特征点, 之后计算本质矩阵, 通过svd计算位姿变化
    体上比较常规, 并无很新的观点
```

# ElasticFusion

```
https://github.com/mp3guy/ElasticFusion

简单介绍: 
    基于稠密图的三位重建: 基础是rgb光流与深度图; 
    回环信息通过Random Ferms: block内rgb_gray四维构造信息表, 对比信息表即可计算相似度
    有趣的一点是, 全局并没有使用pose_graph(理解: 大概的本意是, 我的目的是重建三位环境, 直接对三维模型进行优化即可)
    FIXME: 时间有限, 具体代码并没有深究!!!!!!!!!!!!!!!!
```

# stereo-visual-slam

```
https://github.com/shangzhouye/stereo-visual-slam

简单介绍:
    双目视觉slam定位算法框架
        前端主要包含几个关键模块: 
            feature_detection => feature_matching => motion_estimation
            feature_detection : Oriented FAST + BRIEF describer 
            feature_matching : 通过汉明距离判断相近特征点
            motion_estimation: 上一帧pts点云(point3d)与当前特征点通过ransac求位姿
            insert_key_frame: 插入关键帧, 并在通过三角测量计算深度信息

        后端部分:
            选择做位姿图优化还是加入landmark做BA优化


    opencv中有提供一些常见立体匹配算法示例: 
        https://blog.csdn.net/u011574296/article/details/87546622
```

# Pi-SLAM

```
https://github.com/weixr18/Pi-SLAM

简单介绍: 
    运行在树莓派4b上的ORB-SLAM2的单目视觉主动SLAM项目;
    在文件中有个非常经典的特征点匹配示例: 在test_ORB.cc中, 具体没仔细看
```

# ros_vslam

```
https://github.com/contradict/ros-vslam

简单介绍:
    这是ros下的一个vslam框架, 测试过run_stereo_vo, 其中整体逻辑是:
        detector -> descriptor -> matcher
    内容比较多, 具体没有细看
```

# simple_visual_odometry
```
https://github.com/hsywhu/simple_visual_odometry

简单介绍:
    代码中实验了几种常用算法: gpu版本的特征提取与光流跟踪, 另外测试了通过求解基础矩阵恢复相机运动
    有意思的地方:
        1. 占用地图思路, 这部分比较简单, 知识记录(0, 1)信号
        2. 前端主要做提取特征用(gpu版本的特征提取与光流跟踪), 这里feature中(points, ids, indexs)的indexs是用来做匹配的, 通过indexs来筛选匹配点
        3. 后端为: tracking + pnp + ba优化
```

# slam_VisualOdometry_DirectMethod

```
https://github.com/lebesgue125/slam_VisualOdometry_DirectMethod

简单介绍: 
    项目中包含三个测试模块: 
        1. disparity_calc: 测试视图差与光流跟踪差距
        2. optical_flow: 测试自写光流跟踪算法
        3. direct_method: 直接法实现的视觉里程计:
            1. 随机提取1000个特征点
            2. 并将特征点与视差深度输入到直接法中
            3. 光度误差最小化, 构造优化问题, 求解相机位姿(构建图片金字塔)
```

# stereo_camera_vo

```
https://github.com/lee-shun/stereo_camera_vo

简单介绍:
    - Stereo Camera based visual odometry.
    - LK optical flow is used to track the keypoints.
    - Lost tracking then relocate.(通过tracking到的feature个数判定)
    - Insert keyframes according to the keypoints number.
    - Local bundle adjustment.
    !!!写的很精炼, 值得一看
```

# stereo_vo

```
https://github.com/libing64/stereo_vo

简单介绍:
    非常简单的一个双目视觉里程计, 很适合学习用
流程:
    1. 特征提取并匹配, 构建3d点云
    2. 通过特征跟踪与匹配, 通过ransac求解本质矩阵
    3. 通过本质矩阵恢复相机运动
```

# tiny-vo

```
https://github.com/powei-lin/tiny-vo

简单介绍:
    鱼眼相机实现的一个简单的视觉里程计
    通过fast特征点提取, 并通过光流跟踪特征点(通过tbb加速运算), 这部分属于前端
    通过pnp求解相机位姿, 这部分属于后端
```

# vi-slam

```
https://github.com/eryeden/vi-slam

简单介绍:
    该项目为一个日本人实现的一个项目, 项目根据时间一步步实现. 提供了很多项目经验(实际上由于日语的原因, 注释并不容易看懂)

    !!!之后认真看看
```

# visual_odom

```
https://github.com/ZhenghaoFei/visual_odom

简单介绍:
    FAST特征 + circularMatching(由于是双目相机, 可以操作的比较多) => 三角化 => 通过solvePnPRansac求解本质矩阵 => 恢复相机运动
```

# visual_slam

```
https://github.com/fatihmd/visual_slam

简单介绍:
    基本相同 => FAST+FLK => 求解基本矩阵 => 恢复相机运动
```

# visual_slam1

```
https://github.com/srikanthmalla/visual_slam

简单介绍:
    corner_detector -> featureTracking -> get_RT
```

# visual_slam2

```
https://github.com/mllofriu/visual_slam

简单介绍:
    orb-slam2
```

# visual_slam3

```
https://github.com/muhammadaly/visual_slam

简单介绍:
    
```

# visual-SLAM

```
https://github.com/nyquist-lk/visual-SLAM

简单介绍:
    常见开源视觉slam合集
```

# visual_slam4
```
https://github.com/apresland/visual-slam

简单介绍:
    1. feature detection
    2. feature matching
    3. motion estimation
    4. triangulation
    5. ba优化
    写的很紧致, 可以重点看一下
```

# visual_slam5
```
https://github.com/lacie-life/visual-slam

简单介绍:
    一些常用算法的实现
```

# 思考

```
好像普遍的vo都是将图像转换成灰度图, 这样使图片的色彩信息造成很大缺失, 是否可以通过加入色彩信息做到更加优美的定位

初步思路是将rgb->hsv v信息仅作判断, hs做匹配

两张图片的匹配不是简单的匹配, 而是投影匹配. 也就是说, 怎样找到一个变换使得两张图片的投影误差最小.
传统匹配:
    1. 特征点 -> 描述子 -> 匹配
    2. 特征点 -> 光流 -> 筛选
    3. 直接匹配

出错风险在于:
    1. 运动后的图片, 增加与减少了一些信息
    2. 假设中间部分信息保留, 此时可能由于拍摄的问题导致光影颜色发生偏差
    3. 先验位姿是否足够准确
    4. 前后两帧之间的优化, 这势必会产生一些预料不到的因素
    5. 根本原因在于数据量太过于庞杂
方案:
    1. 通过先验位姿, 剪切出两张图片的共同区域
    2. 通过强度变化与色彩变化联合提供一个残差, 用于最小化匹配
    3. 前后两帧的优化势必缺失很多信息, 构建地图来保存信息
优化点:
    思考一下为何现在更倾向于使用激光雷达而非视觉slam, 用于自动驾驶的定位
    1. 激光雷达直接输出深度信息, 预处理的数据并不是很多; 相反为了获得深度信息, 相机需要做的预处理很多
    2. 分析: 传统直接法与特征点法
        a. 特征点法: 需要先提取特征, 然后提取描述子, 之后匹配特征点, 求解本质矩阵, 恢复相机运动
        b. 光流法: 需要先提取特征, 然后通过光流筛选特征点, 求解本质矩阵, 恢复相机运动
        c. 直接法: 通过光度误差直接求解相机位姿
        我们可以发现, 直接法很明显相对简单, 但直接法也有着很难忽视的缺点:
            i: 对光照强度变化非常敏感
            ii: 迭代时间非常长
        
    3. 如何结合这两种方案并且能够达到一定的精度呢?
        我们可以类比二维定位中:
            scan0 + d_pose = scan1  => img0 + d_pose = img1
        这种思路是否可行呢? 我们需要验证一下

        1. 如何判断hit(即匹配成功)以及概率更新
```