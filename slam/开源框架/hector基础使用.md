---
title: hector基础使用
date: 2021-11-5 12:30:12
categories:
  - slam
  - 开源框架
tags:
  - slam
---

# 参考网址:

[思岚A1激光雷达hector_mapping建图与定位_欧俊岑的博客-CSDN博客](https://blog.csdn.net/weixin_41459903/article/details/102795148)

[tx2+r2000激光雷达 hector建图_大家安静啊的博客-CSDN博客](https://blog.csdn.net/weixin_44785456/article/details/116789296?ops_request_misc=%7B%22request%5Fid%22%3A%22163453792216780255277807%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163453792216780255277807&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-116789296.pc_search_ecpm_flag&utm_term=r2000+hector&spm=1018.2226.3001.4187)

[hectorslam之EAI雷达(ydlidar_x2l)配置过程_sunshine-CSDN博客](https://blog.csdn.net/qq_36170626/article/details/98316545?ops_request_misc=%7B%22request%5Fid%22%3A%22163454144916780262550313%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163454144916780262550313&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-98316545.pc_search_ecpm_flag&utm_term=hector_slam配置&spm=1018.2226.3001.4187)

[NickL77/RPLidar_Hector_SLAM: Hector SLAM without odometry data on ROS with the RPLidar A1 (github.com)](https://github.com/NickL77/RPLidar_Hector_SLAM)

# 简介

hector是纯粹基于laserscan数据的算法,只需要提供scan数据即可,这里使用r2000做实验.因此第一步应该发布scan数据.

# Scan激光数据发布

r2000提供了ros发布的驱动文件,因此只需要修改些配置即可.

驱动网址:

[dillenberger/pepperl_fuchs: Pepperl+Fuchs R2000 Driver (github.com)](https://github.com/dillenberger/pepperl_fuchs)

修改一下配置:

```xml
<?xml version="1.0"?>
<launch>
  <node pkg="pepperl_fuchs_r2000" type="r2000_node" name="r2000_driver_node" output="screen">
    <param name="scanner_ip" value="192.168.1.19"/>
    <param name="frame_id" value="laser_link"/>
    <param name="scan_frequency" value="35"/>
    <param name="samples_per_scan" value="3600"/>
    <!-- <remap from="/r2000_driver_node/scan" to="scan"/> -->
  </node>
  <!--<node pkg="dummy_slam_broadcaster" type="dummy_slam_broadcaster_node" name="dummy_slam_broadcaster"/>
  <node name="rvizLocal" pkg="rviz" type="rviz" respawn="false" output="screen" args="-d $(find pepperl_fuchs_r2000)/rviz/test.rviz" required="true"/>-->
</launch>
```

```
scanner_ip       r2000的配置激光ip地址
frame_id         发布出去的框架id(用于tf转换)
scan_frequency   扫描频率
samples_per_scan 每圈的扫描数
# 值得注意的是这里r2000发不出去的scan节点为/r2000_driver_node/scan
# 可以通过remap重定义为/scan
最后通过rviz显示
```

ros执行命令:

```shell
roslaunch pepperl_fuchs_r2000 gui_example.launch
```

# 使用hector_mapping建图并定位

这里可以直接在系统中安装hector,并新建`hector_mapping.launch`文件,指令如下:

```shell
sudo apt-get install ros-melodic-hector-slam
cd catkin_ws/src
catkin_create_pkg run_launch # 因为内部都是launch文件并不需要依赖包
cd run_launch && sudo mkdir launch && cd launch
sudo nano hector_mapping.launch
```

```xml
<launch>

<node pkg="hector_mapping" type="hector_mapping" name="hector_mapping" output="screen">
    <!-- Frame names -->
    <!-- 确定map-> odom转换是否应该由系统发布 -->
    <param name="pub_map_odom_transform" value="true"/>
    <!-- 坐标系id,hector会将map与车体做tf连接到一块,而车体与激光之间的连接需要我们自己做 --> 
    <param name="map_frame" value="map" />
    <param name="base_frame" value="base_link" />
    <!-- 由于没有使用里程计,改为base_link即可 --> 
    <param name="odom_frame" value="base_link" />

    <!-- Tf use -->
    <param name="use_tf_scan_transformation" value="true"/>
    <param name="use_tf_pose_start_estimate" value="false"/>

    <!-- Map size / start point -->
    <param name="map_resolution" value="0.05"/>
    <!-- 地图尺寸需要注意一下,如果地图太小,可能导致激光范围过大,超出地图,这样匹配坐标会很挫 --> 
    <param name="map_size" value="512"/>
    <param name="map_start_x" value="0.5"/>
    <param name="map_start_y" value="0.5" />
    <param name="laser_z_min_value" value = "-1.0" />
    <param name="laser_z_max_value" value = "1.0" />
    <param name="map_multi_res_levels" value="2" />

    <param name="map_pub_period" value="2" />
    <param name="laser_min_dist" value="0.4" />
    <!-- 激光扫描距离要适中,如果太小也会出现建图建的很挫的现象 --> 
    <param name="laser_max_dist" value="5.5" />
    <param name="output_timing" value="false" />
    <!-- 确定scanmatcher到map的转换是否发布到TF -->
    <param name="pub_map_scanmatch_transform" value="true" />
    <!-- 发布的坐标名 -->
    <!--<param name="tf_map_scanmatch_transform_frame_name" value="scanmatcher_frame" />-->

    <!-- Map update parameters -->
    <param name="update_factor_free" value="0.4"/>
    <param name="update_factor_occupied" value="0.7" />    
    <param name="map_update_distance_thresh" value="0.2"/>
    <param name="map_update_angle_thresh" value="0.06" />

    <!-- Advertising config --> 
    <param name="advertise_map_service" value="true"/>
    <!-- 扫描订阅器的队列大小 --> 
    <param name="scan_subscriber_queue_size" value="5"/>
    <!-- 这里更改一下scan_topic 如果我们做了重定向,需要改为/scan--> 
    <param name="scan_topic" value="/r2000_driver_node/scan"/>
</node>

<!-- !注意:这一步需要将车体坐标系与激光坐标系进行tf转换连接起来 --> 
<node pkg="tf" type="static_transform_publisher" name="base_to_laser_broadcaster" args="0 0 0.105 0 0 0 /base_link /laser_link 100"/>_
	<!-- 这里使用的是hector自带的rviz配置,我们也可以在他的基础上增减功能,另存到我们自己的文件src/run_launch/rviz/map/hector_map.rviz --> 
  <node pkg="rviz" type="rviz" name="rviz" args="-d $(find hector_slam_launch)/rviz_cfg/mapping_demo.rviz"/>
    
</launch>
```

执行步骤:

```shell
# 发布scan数据
roslaunch pepperl_fuchs_r2000 gui_example.launch
# 启动hector_mapping
roslaunch run_launch hector_mapping.launch
```

# 一些图片

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210514140914724.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDc4NTQ1Ng==,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210514140924770.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDc4NTQ1Ng==,size_16,color_FFFFFF,t_70#pic_center)

# 总结

1.订阅/scan不要出错

2.tf转换尤为需要注意,map->base_link->laser_link

3.激光距离与地图大小需要注意一下,这里设不好容易崩

