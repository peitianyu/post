---
title: amcl简单使用
date: 2021-11-25 12:30:12
categories:
  - slam
  - 开源框架
tags:
  - slam
---

# 参考网址:

[AMCL介绍及参数说明_走过_冬天的博客-CSDN博客_amcl参数配置](https://blog.csdn.net/zx3517288/article/details/78454196)

[深入AMCL(三):AMCL手动初始化后如何自动定位_yuanguobin01的博客-CSDN博客](https://blog.csdn.net/qq_34672671/article/details/105729458?ops_request_misc=%7B%22request%5Fid%22%3A%22163540823116780274178002%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=163540823116780274178002&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v29_ecpm-29-105729458.pc_search_ecpm_flag&utm_term=amcl&spm=1018.2226.3001.4187)

# 配置

`amcl_demo.launch`

```xml
<launch>

     <!-- r2000驱动 -->
    <include file="$(find pepperl_fuchs_r2000)/launch/gui_example.launch"/>

    <!-- Map server -->
    <!-- 发布map.yaml,其中包含了pgm文件位置-->
    <arg name="map_file" default="$(find run)/map/mymap.yaml"/>
    <node name="map_server" pkg="map_server" type="map_server" args="$(arg map_file)" />


    <!-- Localization -->
    <include file="$(find run)/launch/amcl.launch"/>

    <!-- 获取odom数据 -->
    <!-- 由于采用的是里程计模型,注意里程计的位置输出尤为关键 -->
    <!-- <node name="tcp_server" pkg="tcp_server" type="tcp_server" /> -->
    
    <!-- 注意坐标转换:map->odom->base_link->laser_link -->
    <!-- 如果转换多了,会报错,多转换可能会报laserscan超时,少了,rviz会报没有上级坐标系 -->
    <node pkg="tf" type="static_transform_publisher" name="map_to_odom_broadcaster" args="0 0 0 0 0 0  /map /odom 100"/>
    <node pkg="tf" type="static_transform_publisher" name="base_to_laser_broadcaster" args="0 0 0 0 0 0 /base_link /laser_link 100"/>

    <node pkg="rviz" type="rviz" name="rviz" args="-d $(find run)/rviz/hector_map.rviz"/>

</launch>
```

`amcl.launch`

```xml
<launch>
  <arg name="use_map_topic"   default="false"/>
  <arg name="scan_topic"      default="scan"/> 
  <arg name="initial_pose_x"  default="0.0"/>
  <arg name="initial_pose_y"  default="0.0"/>
  <arg name="initial_pose_a"  default="0.0"/>
  <arg name="odom_frame_id"   default="odom"/>
  <arg name="base_frame_id"   default="base_link"/>
  <arg name="global_frame_id" default="map"/>

  <node pkg="amcl" type="amcl" name="amcl">
    <param name="use_map_topic"             value="$(arg use_map_topic)"/>
    <!-- Publish scans from best pose at a max of 10 Hz -->
      <!-- 差分模型 -->
    <param name="odom_model_type"           value="diff"/>
      <!-- 速度模型参数 -->
    <param name="odom_alpha5"               value="0.1"/>
    <param name="gui_publish_rate"          value="10.0"/>
    <param name="laser_max_beams"           value="810"/>
    <param name="laser_max_range"           value="-1"/>
    <param name="min_particles"             value="500"/>
    <param name="max_particles"             value="5000"/>
    <param name="kld_err"                   value="0.05"/>
    <param name="kld_z"                     value="0.99"/>
      <!-- 里程计模型调节超参 -->
    <param name="odom_alpha1"               value="0.2"/>
    <param name="odom_alpha2"               value="0.2"/>
    <param name="odom_alpha3"               value="0.2"/>
    <param name="odom_alpha4"               value="0.2"/>
      <!-- 一般情况而言,高斯误差会比较大,可以设置大一些 -->
    <param name="laser_z_hit"               value="0.95"/>
    <param name="laser_z_short"             value="0.05"/>
    <param name="laser_z_max"               value="0.05"/>
    <param name="laser_z_rand"              value="0.05"/>
    <param name="laser_sigma_hit"           value="0.2"/>
    <param name="laser_lambda_short"        value="0.1"/>
    <param name="laser_model_type"          value="likelihood_field"/>
    <!-- <param name="laser_model_type" value="beam"/> -->
    <param name="laser_likelihood_max_dist" value="2.0"/>
    <param name="update_min_d"              value="0.1"/>
    <param name="update_min_a"              value="0.2"/>
    <param name="odom_frame_id"             value="$(arg odom_frame_id)"/> 
    <param name="base_frame_id"             value="$(arg base_frame_id)"/> 
    <param name="global_frame_id"           value="$(arg global_frame_id)"/>
    <param name="resample_interval"         value="1"/>
    <!-- Increase tolerance because the computer can get quite busy -->
    <param name="transform_tolerance"       value="1.0"/>
      <!-- 快慢指针,一般slow会小一些,良好的值可能为0.001,fast可能为0.01 -->
    <param name="recovery_alpha_slow"       value="0.0"/>
    <param name="recovery_alpha_fast"       value="0.0"/>
      <!-- 初始位姿 -->
    <param name="initial_pose_x"            value="$(arg initial_pose_x)"/>
    <param name="initial_pose_y"            value="$(arg initial_pose_y)"/>
    <param name="initial_pose_a"            value="$(arg initial_pose_a)"/>
    <remap from="scan"                      to="$(arg scan_topic)"/>
  </node>
</launch>
```

