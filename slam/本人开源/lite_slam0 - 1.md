---
title: lite_slam - 搭建测试环境
date: 2022-9-5 12:30:12
categories:
  - slam
  - lite_slam
tags:
  - lite_slam
---

# 环境

```
- Ubuntu18
- Eigen
- opencv(仅用于显示)
```

# 安装环境

```shell
# g++ / gcc
	sudo apt install g++
# cmake
	sudo apt install cmake
# Eigen
    #1.安装：
    sudo apt-get install libeigen3-dev
    # 2 调整，默认安装路径是：/usr/include/eigen3 
    cd /usr/include/eigen3
    ls
    # $ Eigen  signature_of_eigen3_matrix_library  unsupported  
    # 3.需要执行复制命令，将Eigen文件夹放在/usr/include 下面
    sudo cp Eigen/ .. -R
# opencv
	sudo apt-get install libcv-dev
	sudo apt-get install libopencv-dev
```

# 安装测试数据

## 使用测试data

```
1. 百度云下载:
链接：https://pan.baidu.com/s/19gm9azasi70CXTWbQ7dGrw?pwd=jned 
提取码：jned 
2. 将其复制于文件夹中
3. mkdir build
4. cd build && cmake ..
5. make -j4
```

## 文件夹结构

[![vLMkuQ.png](https://s1.ax1x.com/2022/09/09/vLMkuQ.png)](https://imgse.com/i/vLMkuQ)

## 使用自己的rosbag

```
# 下载下来
cd ~/catkin_ws/src
git clone https://ghproxy.com/https://github.com/peitianyu/bag2txt
cd .. && catkin_make
# 修改launch文件,并运行
<launch>
    <node name="odom2txt" pkg="ros_data2txt" type="odom2txt" respawn="false" output="screen" >
        <param name="file_name"         type="string" value="/mnt/d/file_ws/Learning/ros/bag2txt_ws/log/9_2_odom.txt"/> // 修改
        <param name="topic_name"         type="string" value="/odom"/>  
    </node>

    <node name="laser_scan2txt" pkg="ros_data2txt" type="laser_scan2txt" respawn="false" output="screen" >
        <param name="file_name"         type="string" value="/mnt/d/file_ws/Learning/ros/bag2txt_ws/log/9_2_scan.txt"/> // 修改
        <param name="topic_name"         type="string" value="/scan"/>  
    </node>

    <node name="playbag" pkg="rosbag" type="play"
      args="--clock /mnt/d/file_ws/Learning/ros/bag2txt_ws/bag/scan_odom_2022_9_2.bag" /> // 修改
</launch>

# 运行
roslaunch ros_data2txt bag2txt.launch 自动生成
```

