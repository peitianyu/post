---
title: carto建图与定位
date: 2022-4-18 12:30:12
categories:
  - slam
  - 开源框架
tags:
  - slam
---

# 参考网址:

 [(124条消息) Cartographer用于机器人纯定位_梦凝小筑的博客-CSDN博客_cartographer纯定位](https://blog.csdn.net/weixin_36976685/article/details/103834608?ops_request_misc=%7B%22request%5Fid%22%3A%22165025689116780271994944%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=165025689116780271994944&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-103834608.142^v9^control,157^v4^control&utm_term=cartographer纯定位&spm=1018.2226.3001.4187)

[(124条消息) 使用Cartographer2D建图_熊铁树的博客-CSDN博客_cartographer建图流程](https://blog.csdn.net/qq_41807801/article/details/112058770?ops_request_misc=%7B%22request%5Fid%22%3A%22165025403116780269840689%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=165025403116780269840689&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-112058770.142^v9^control,157^v4^control&utm_term=cartographer建图&spm=1018.2226.3001.4187)

[(124条消息) 用自己的机器人实现cartographer建图测试与地图保存应该这样做_白茶-清欢的博客-CSDN博客_cartographer建图](https://blog.csdn.net/zhao_ke_xue/article/details/87875984?ops_request_misc=&request_id=&biz_id=102&utm_term=cartographer建图&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-4-87875984.142^v9^control,157^v4^control&spm=1018.2226.3001.4187)

[(125条消息) 【移动机器人技术】Cartographer使用流程-建图-纯定位-导航_A MAN NAMED MAGIC的博客-CSDN博客_cartographer 定位](https://blog.csdn.net/weixin_43259286/article/details/105143605?ops_request_misc=&request_id=&biz_id=102&utm_term=cartographer纯定位导航mapserver&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-105143605.142^v9^control,157^v4^control&spm=1018.2226.3001.4187)

[(127条消息) ROS SLAM功能包应用方法--cartographer_鸢雨如歌的博客-CSDN博客_cartographer 导航](https://blog.csdn.net/yuexiaduzhui/article/details/108124038)

[(127条消息) cartographer建图参数配置详细说明_非晚非晚的博客-CSDN博客_cartographer参数配置](https://blog.csdn.net/QLeelq/article/details/112237663?ops_request_misc=%7B%22request%5Fid%22%3A%22165028205416780255271354%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=165028205416780255271354&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-112237663.142^v9^control,157^v4^control&utm_term=cartographer+参数&spm=1018.2226.3001.4187)

# 建图

demo_revo_lds.launch

```xml
<launch>
  <!-- 启动仿真程序 -->
  <!-- <include file="$(find robot_sim_demo)/launch/robot_spawn.launch"/> -->
  <!-- 启动关键的建图节点，这是我们主要要更改的地方，更改下面的configuration_directory和configuration_basename的路径 -->
  <node name="cartographer_node" pkg="cartographer_ros"
      type="cartographer_node" args="
          -configuration_directory $(find carto)/configuration_files
          -configuration_basename revo_lds.lua"
      output="screen">
		<!-- 更改horizontal_laser_2d为我们的雷达话题 -->
    <!-- <remap from="scan" to="scan" /> -->
		<!-- 更改odom为里程计话题 -->
    <!-- <remap from="odom" to="odom" /> -->
  </node>

  <!-- 一般不更改，有个建图分辨率参数 -->
  <node name="cartographer_occupancy_grid_node" pkg="cartographer_ros"
      type="cartographer_occupancy_grid_node" args="-resolution 0.05" />
  <!-- 下面这两个一般直接注释掉，我们自己需要再启动就可以了 -->
  <node name="rviz" pkg="rviz" type="rviz" required="true"
      args="-d $(find carto)/rviz/cartographer.rviz" />

</launch>
```

配置文件 revo_lds.lua

```lua
include "map_builder.lua"
include "trajectory_builder.lua"

options = {
  map_builder = MAP_BUILDER,
  trajectory_builder = TRAJECTORY_BUILDER,
  map_frame = "map",
	-- tracking_frame更改为我们机器人的基坐标tf，一般是base_link，
  tracking_frame = "base_link",

	-- 以下四个参数与里程计有关，在下面进行单独说明
  odom_frame = "odom",
  published_frame = "base_link",
  provide_odom_frame = false,
	use_odometry = true,

  publish_frame_projected_to_2d = false,
  use_pose_extrapolator = true,
  use_nav_sat = false,
  use_landmarks = false,
  num_laser_scans = 1,
  num_multi_echo_laser_scans = 0,
  num_subdivisions_per_laser_scan = 1,
  num_point_clouds = 0,
  lookup_transform_timeout_sec = 0.2,
  submap_publish_period_sec = 0.3,
  pose_publish_period_sec = 5e-3,
  trajectory_publish_period_sec = 30e-3,
  rangefinder_sampling_ratio = 1.,
  odometry_sampling_ratio = 0.3,
  fixed_frame_pose_sampling_ratio = 1.,
  imu_sampling_ratio = 1.,
  landmarks_sampling_ratio = 1.,
}

MAP_BUILDER.use_trajectory_builder_2d = true

TRAJECTORY_BUILDER_2D.submaps.num_range_data = 35
TRAJECTORY_BUILDER_2D.min_range = 0.3
TRAJECTORY_BUILDER_2D.max_range = 8.
TRAJECTORY_BUILDER_2D.missing_data_ray_length = 1.
-- 决定是否使用imu数据
TRAJECTORY_BUILDER_2D.use_imu_data = false

TRAJECTORY_BUILDER_2D.use_online_correlative_scan_matching = true
TRAJECTORY_BUILDER_2D.real_time_correlative_scan_matcher.linear_search_window = 0.1
TRAJECTORY_BUILDER_2D.real_time_correlative_scan_matcher.translation_delta_cost_weight = 10.
TRAJECTORY_BUILDER_2D.real_time_correlative_scan_matcher.rotation_delta_cost_weight = 1e-1

POSE_GRAPH.optimization_problem.huber_scale = 1e2
POSE_GRAPH.optimize_every_n_nodes = 35
POSE_GRAPH.constraint_builder.min_score = 0.65

return options
```

# 纯定位

demo_backpack_2d_localization.launch

```xml
<launch>

    <include file="$(find robot_sim_demo)/launch/robot_spawn.launch"/>

    <node name="cartographer_node" pkg="cartographer_ros"
        type="cartographer_node" args="
            -configuration_directory $(find carto)/configuration_files
            -configuration_basename backpack_2d_localization.lua
            -load_state_filename /root/catkin_ws/src/ROS-Academy-for-Beginners/carto/map/carto_2022_04_18_1.pbstream"
        output="screen">
        <remap from="points2" to="/camera/depth/points"/>
    </node>

    <!-- <node name="cartographer_occupancy_grid_node" pkg="cartographer_ros"
        type="cartographer_occupancy_grid_node" args="-resolution 0.05
                                                -pure_localization 1" /> -->
    

    <!-- Map server -->
    <arg name="map_file" default="$(find carto)/map/carto.yaml"/>
    <node name="map_server" pkg="map_server" type="map_server" args="$(arg map_file)" />
    
    <include file="$(find navigation_sim_demo)/launch/include/move_base.launch.xml"/>

    <node name="rviz" pkg="rviz" type="rviz" required="true"
        args="-d $(find cartographer_ros)/configuration_files/demo_2d.rviz" />


</launch>
```

backpack_2d_localization.lua

```lua
include "backpack_2d.lua"

TRAJECTORY_BUILDER.pure_localization_trimmer = {
  max_submaps_to_keep = 3,
}
POSE_GRAPH.optimize_every_n_nodes = 20

return options
```

backpack_2d.lua

```lua
include "map_builder.lua"
include "trajectory_builder.lua"

options = {
  map_builder = MAP_BUILDER,
  trajectory_builder = TRAJECTORY_BUILDER,
  map_frame = "map",
	-- tracking_frame更改为我们机器人的基坐标tf，一般是base_link，
  tracking_frame = "base_footprint",

	-- 以下四个参数与里程计有关，在下面进行单独说明
  odom_frame = "odom",
  published_frame = "base_footprint",
  provide_odom_frame = false,
	use_odometry = true,

  publish_frame_projected_to_2d = false,
  use_pose_extrapolator = true,
  use_nav_sat = false,
  use_landmarks = false,
  num_laser_scans = 1,
  num_multi_echo_laser_scans = 0,
  num_subdivisions_per_laser_scan = 1,
  num_point_clouds = 0,
  lookup_transform_timeout_sec = 0.2,
  submap_publish_period_sec = 0.3,
  pose_publish_period_sec = 5e-3,
  trajectory_publish_period_sec = 30e-3,
  rangefinder_sampling_ratio = 1.,
  odometry_sampling_ratio = 0.3,
  fixed_frame_pose_sampling_ratio = 1.,
  imu_sampling_ratio = 1.,
  landmarks_sampling_ratio = 1.,
}

MAP_BUILDER.use_trajectory_builder_2d = true

TRAJECTORY_BUILDER_2D.submaps.num_range_data = 35
TRAJECTORY_BUILDER_2D.min_range = 0.3
TRAJECTORY_BUILDER_2D.max_range = 8.
TRAJECTORY_BUILDER_2D.missing_data_ray_length = 1.
-- 决定是否使用imu数据
TRAJECTORY_BUILDER_2D.use_imu_data = false

TRAJECTORY_BUILDER_2D.use_online_correlative_scan_matching = true
TRAJECTORY_BUILDER_2D.real_time_correlative_scan_matcher.linear_search_window = 0.1
TRAJECTORY_BUILDER_2D.real_time_correlative_scan_matcher.translation_delta_cost_weight = 10.
TRAJECTORY_BUILDER_2D.real_time_correlative_scan_matcher.rotation_delta_cost_weight = 1e-1

POSE_GRAPH.optimization_problem.huber_scale = 1e2
POSE_GRAPH.optimize_every_n_nodes = 35
POSE_GRAPH.constraint_builder.min_score = 0.65

return options
```

# 参数意义

