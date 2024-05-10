---
title: evo使用
date: 2022-4-26 12:30:12
categories:
  - slam
  - 常用工具
tags:
  - slam
---

# 参考网址:

[(128条消息) 如何获得gazebo仿真中的机器人位姿真值_寒墨阁的博客-CSDN博客_gazebo获取机器人位置](https://blog.csdn.net/hanmoge/article/details/113593380?ops_request_misc=%7B%22request%5Fid%22%3A%22165094059516782425130568%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=165094059516782425130568&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-2-113593380.142^v9^control,157^v4^control&utm_term=gazebo获得真实位姿&spm=1018.2226.3001.4187)

[(128条消息) evo安装、evo使用方法详细介绍使用教程](https://blog.csdn.net/u011341856/article/details/104594392/?ops_request_misc=&request_id=&biz_id=102&utm_term=evo_traj轉換&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-3-104594392.142^v9^control,157^v4^control&spm=1018.2226.3001.4187)

[(128条消息) evo评测VINS-MONO---代码修改、数据格式转换、数据测试_可即的博客-CSDN博客_vins测试](https://blog.csdn.net/xiaojinger_123/article/details/120141017?ops_request_misc=&request_id=&biz_id=102&utm_term=evo_traj轉換&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-4-120141017.142^v9^control,157^v4^control&spm=1018.2226.3001.4187)

[(128条消息) evo测评TUM数据集_dididada~的博客-CSDN博客_evo tum](https://blog.csdn.net/qq_43265072/article/details/104715515?ops_request_misc=%7B%22request%5Fid%22%3A%22165089732116780357243284%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=165089732116780357243284&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-1-104715515.142^v9^control,157^v4^control&utm_term=evo_traj+tum&spm=1018.2226.3001.4187)

# gazebo获得真实位姿

```
<!-- 在gazebo仿真模型文件中添加： -->
<gazebo>
  <plugin name="p3d_base_controller" filename="libgazebo_ros_p3d.so">
    <alwaysOn>true</alwaysOn>
    <updateRate>50.0</updateRate>
    <bodyName>base_footprint</bodyName>
    <topicName>base_pose_ground_truth</topicName>
    <gaussianNoise>0.0</gaussianNoise>
    <frameName>map</frameName>
    <xyzOffsets>0 0 0</xyzOffsets>
    <rpyOffsets>0 0 0</rpyOffsets>
  </plugin>
</gazebo>
```

# ros生成tum文件

```c++
#include <ros/ros.h>
#include "geometry_msgs/PoseWithCovarianceStamped.h"
#include "nav_msgs/Odometry.h"

#include <fstream>
#include <string.h>
#include <sstream>
#include <iomanip>

class EvoCreatTum
{
public:
    EvoCreatTum();
    ~EvoCreatTum();
    void poseCallback(const nav_msgs::Odometry::ConstPtr& msg);
private:
    ros::NodeHandle nh_;
    ros::Subscriber sub_;
    std::string odom_topic_;
    nav_msgs::Odometry pose_;
    std::string outFilename_;
};

EvoCreatTum::EvoCreatTum()
{
    ros::Time::init();
    nh_.param<std::string>("/odom_topic", odom_topic_, "/odom");
    nh_.param<std::string>("outFilename", outFilename_, "/root/catkin_ws/devel/lib/evo_creat_tum/outpose.txt");
    sub_ = nh_.subscribe(odom_topic_, 1, &EvoCreatTum::poseCallback, this);
}


EvoCreatTum::~EvoCreatTum()
{
}

void EvoCreatTum::poseCallback(const nav_msgs::Odometry::ConstPtr &msg)
{
    ros::Time currTime = ros::Time::now();
    double t_s = currTime.toSec();
    // std::cout << std::fixed << std::setprecision(4) << "t_s: " << t_s << std::endl;
    pose_ = *msg;
    double tx = pose_.pose.pose.position.x;
    double ty = pose_.pose.pose.position.y;
    double tz = pose_.pose.pose.position.z;
    double qx = pose_.pose.pose.orientation.x;
    double qy = pose_.pose.pose.orientation.y;
    double qz = pose_.pose.pose.orientation.z;
    double qw = pose_.pose.pose.orientation.w;
    std::fstream outTumpose;
    // 打开并向后添加
    outTumpose.open(outFilename_, std::ios::out | std::ios::app);
    outTumpose << std::fixed << std::setprecision(4) << t_s << " " << std::fixed << std::setprecision(6) << tx << " " << ty << " " << tz << " " << qx << " " << qy << " " << qz << " " << qw << std::endl;
}

int main(int argc, char **argv)
{
    ros::init(argc, argv, "evo_creat_tum_node"); // 节点的名字
    EvoCreatTum evo_creat_tum;
    ros::spin(); // 程序执行到此处时开始进行等待，每次订阅的消息到来都会执行一次ScanCallback()
    return 0;
}
```

```xml
<launch>
    <!-- creat_tum -->
    <param name="/odom_topic" value="/odom"/>
    <param name="outFilename" value="/root/catkin_ws/devel/lib/evo_creat_tum/outpose.txt"/>
    <node name="evo_creat_tum" pkg="evo_creat_tum" type="evo_creat_tum" />
</launch>
```

# evo使用

```sh
# 配准前
evo_traj tum CameraTrajectory.txt groundtruth.txt -p
# 配准后
evo_traj tum CameraTrajectory.txt --ref=groundtruth.txt -p -a
# 输出结果
evo_ape tum groundtruth.txt CameraTrajectory.txt -r full -va -p --save_results results/ORB.zip
```

