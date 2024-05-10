---
title: 学写2d激光slam-lesson2
date: 2022-2-12 12:30:12
categories:
  - slam
  - 第三方学习
tags:
  - ros
  - slam
---

# 参考网址:

  [xiangli0608/Creating-2D-laser-slam-from-scratch: 从零开始创建二维激光SLAM (github.com)](https://github.com/xiangli0608/Creating-2D-laser-slam-from-scratch) 

 [李想的博客_李太白lx_CSDN博客-从零开始搭二维激光SLAM,激光SLAM,cartographer领域博主](https://blog.csdn.net/tiancailx?spm=1011.2124.3001.5113) 

 [(107条消息) ROS中点云学习（九）：sensor_msgs::LaserScan转sensor_msgs::PointCloud2_00 2的博客-CSDN博客](https://blog.csdn.net/weixin_41170972/article/details/120015410?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_title~default-1.pc_relevant_paycolumn_v3&spm=1001.2101.3001.4242.2&utm_relevant_index=4) 

# lesson2

### 格式转换(sensor_msgs/LaserScan -> sensor_msgs/PointCloud2 )

```shell
# sensor_msgs/LaserScan
std_msgs/Header header
float32 angle_min
float32 angle_max
float32 angle_increment
float32 time_increment
float32 scan_time
float32 range_min
float32 range_max
float32[] ranges
float32[] intensities
```

```shell
# sensor_msgs/PointCloud2
std_msgs/Header header
# width  - 以点数指定点云数据集的宽度。 WIDTH有两个含义：
# *  - 它可以为 无组织数据集 指定云中的总点数;
# *  - 它可以指定 有组织的点云数据集 的宽度（一行中的总点数）.
uint32 height
# height  - 以点数指定点云数据集的高度。HEIGHT有两个含义：
#    * - 对于 无组织数据集，它设置为1（因此用于检查数据集是否ordered）。
#    * - 它可以指定 有组织的点云数据集 的高度（总行数）;
uint32 width
# 描述二进制数据blob中的通道及其布局。
sensor_msgs/PointField[] fields
# 是大端数据格式吗?
bool is_bigendian
#  以字节为单位的点的长度
uint32 point_step
#  行的长度（以字节为单位）
uint32 row_step
#  实际数据（二进制的数据流），大小为（row_step * height）
uint8[] data
#  如果包含Inf / NaN值，则为false，否则为真
bool is_dense
```

```shell
# ros中自动做了 pcl::PointCloud<PointT> 到 sensor_msgs/PointCloud2 的数据类型的转换
std_msgs/Header header
uint32 height
uint32 width
PointT* point_tmp
bool is_dense
```

相对来说比较简单,就是将scan数据转化为PointT数据发布,且ros自带转化成pointcloud2功能,需要注意的是对于无效数据需要特殊处理

```c
// 注意，这里的发布器，发布的数据类型为 pcl::PointCloud<PointT>
// ros中自动做了 pcl::PointCloud<PointT> 到 sensor_msgs/PointCloud2 的数据类型的转换
pointcloud2_publisher_ = node_handle_.advertise<PointCloudT>(
	"pointcloud2_converted", 1, this);
```

无效点处理

```c
// 将 inf 与 nan 点 设置为无效点
if (!std::isfinite(range))
{
    // std::cout << " " << i << " " << scan_msg->ranges[i];
    point_tmp = invalid_point_;
    continue;
}
```

point坐标

```c
// 有些雷达驱动会将无效点设置成 range_max+1
// 所以要根据雷达的range_min与range_max进行有效值的判断
if (range > scan_msg->range_min && range < scan_msg->range_max)
{
    // 获取第i个点对应的角度
    float angle = scan_msg->angle_min + i * scan_msg->angle_increment;
    // 获取第i个点在笛卡尔坐标系下的坐标
    point_tmp.x = range * cos(angle);
    point_tmp.y = range * sin(angle);
    point_tmp.z = 0.0;
}
else
// 无效点
	point_tmp = invalid_point_;
```

发布

```
 cloud_msg->width = scan_msg->ranges.size();
 cloud_msg->height = 1;
 cloud_msg->is_dense = false; // contains nans
 // 将scan_msg的消息头 赋值到 PointCloudT的消息头
 pcl_conversions::toPCL(scan_msg->header, cloud_msg->header);

// 由于ros中自动做了 pcl::PointCloud<PointT> 到 sensor_msgs/PointCloud2 的数据类型的转换
// 所以这里直接发布 pcl::PointCloud<PointT> 即可
pointcloud2_publisher_.publish(cloud_msg);
```

### icp算法

```c
// 1,将scan转成点云数据
// 2,保存当前与上一次点云信息
// 3,pcl自带的icp算法
// ICP 输入数据,输出数据的设置,还可以进行参数配置,这里使用默认参宿
icp_.setInputSource(last_pointcloud_);
icp_.setInputTarget(current_pointcloud_);
// 开始迭代计算
pcl::PointCloud<pcl::PointXYZ> unused_result;
icp_.align(unused_result);
// 如果迭代没有收敛,不进行输出
if (icp_.hasConverged() == false){
    std::cout << "not Converged" << std::endl;
    return;
}
else
{
    // 收敛了之后, 获取坐标变换
    Eigen::Affine3f transfrom;
    transfrom = icp_.getFinalTransformation();

    // 将Eigen::Affine3f转换成x, y, theta, 并打印出来
    float x, y, z, roll, pitch, yaw;
    pcl::getTranslationAndEulerAngles(transfrom, x, y, z, roll, pitch, yaw);
    std::cout << "transfrom: (" << x << ", " << y << ", " << yaw * 180 / M_PI << ")" << std::endl;
}
```

### 总结

```shell
迭代最近点（Iterative Closest Point, 下简称ICP）算法是一种点云匹配算法。

其求解思路为：
首先对于一幅点云中的每个点，在另一幅点云中计算匹配点（最近点）
极小化匹配点间的匹配误差，计算位姿
然后将计算的位姿作用于点云
再重新计算匹配点
如此迭代，直到迭代次数达到阈值，或者极小化函数的变化量小于设定阈值

首先，ICP对初值比较敏感，初值给的不好，就需要花费更多的迭代次数进行匹配．
其次，由于它是迭代很多次的，所以其花费的时间很长，这一点是非常致命的，之后我会通过程序来让大家体验ICP的费时．
再次，精度与速度是矛盾的，ICP算法理论上可以实现很高的精度，但是要很多很多的迭代次数，以及很长的时间．所以，当限制了迭代次数的情况下，精度就不一定能保证了．
```

