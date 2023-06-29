---
title: 在melodic下使用opencv巡线
date: 2022-2-10 12:30:12
categories:
  - slam
  - 第三方学习
tags:
  - ros
---

# 参考网址:

 [sudrag/line_follower_turtlebot: A repo created to simulate a line following turtlebot in Gazebo environment using ROS (github.com)](https://github.com/sudrag/line_follower_turtlebot) 

 [(105条消息) ROS-Melodic下 Gazebo+Turtlebot安装_qty2898734013的博客-CSDN博客_melodic安装gazebo](https://blog.csdn.net/qty2898734013/article/details/114217366) 

另一位大佬的,不过没有我运行成功

 [ROS开发6——Gazebo环境下ROS+OpenCV搭建机器人图像开发案例 | Zenan's Blog (zenanlin1999.github.io)](https://zenanlin1999.github.io/2021/05/10/ROS开发6——opencv+ROS图像开发案例/) 

# 简介

参考的是以为大佬的github项目,基本环境如下:

```shell
ROS Kinetic
Catkin
roscpp package
std_msgs package
message_generation package
OpenCV
```

但由于melodic并没有办法直接安装turtlebot_gazebo所以需要先手动安装turtlebot_gazebo

方法如下

# 安装turtlebot_gazebo

```shell
# 安装gazebo
sudo apt-get install ros-melodic-gazebo-ros-pkgs ros-melodic-ros-control
# 修改配置文件
sudo nano ~/.ignition/fuel/config.yaml
# 注释掉,并加入
# url: https://api.ignitionfuel.org
url: https://api.ignitionrobotics.org

# 如果是vmare虚拟机,需要加入
export SVGA_VGPU10=0
echo "export SVGA_VGPU10=0" >> ~/.bashrc
source ~/.bashrc
# 启动
gazebo
```

## a\安装依赖包

```shell
sudo apt-get install ros-melodic-kobuki-*
sudo apt-get install ros-melodic-ecl-streams
sudo apt-get install ros-melodic-bfl
sudo apt-get install ros-melodic-map-server
sudo apt-get install ros-melodic-move-base
sudo apt-get install ros-melodic-amcl
sudo apt-get install ros-melodic-dwa-local-planner
sudo apt-get install libusb-dev
sudo apt-get install libspnav-dev
sudo apt-get install ros-melodic-joystick-drivers
sudo apt-get install bluetooth
sudo apt-get install libbluetooth-dev
sudo apt-get install libcwiid-dev
```

## b\新建工作空间，准备相关包

```shell
mkdir -p ~/turtlebot_ws/src 
cd ~/turtlebot_ws/src 
git clone git://github.com/turtlebot/turtlebot_simulator
git clone git://github.com/turtlebot/turtlebot.git
git clone git://github.com/turtlebot/turtlebot_apps.git
git clone git://github.com/udacity/robot_pose_ekf
git clone git://github.com/ros-perception/depthimage_to_laserscan.git 
git clone git://github.com/yujinrobot/kobuki_msgs.git
git clone git://github.com/yujinrobot/kobuki_desktop.git
git clone git://github.com/ncnynl/turtlebot_viz
git clone git://github.com/turtlebot/turtlebot_create.git
cd kobuki_desktop/
rm -r kobuki_qtestsuite
git clone git://github.com/toeklk/orocos-bayesian-filtering.git
git clone git://github.com/turtlebot/turtlebot_msgs.git
git clone git://github.com/ros-drivers/joystick_drivers.git
```

## c\复制kobuki和yujin_ocs依赖库到turtlebot_ws/src工作空间下

```
mkdir -p ~/repos/
cd ~/repos/
git clone git://github.com/yujinrobot/kobuki.git
cp -r kobuki/* ~/turtlebot_ws/src/
git clone git://github.com/yujinrobot/yujin_ocs.git
cp -r yujin_ocs/yocs_cmd_vel_mux/ yujin_ocs/yocs_controllers ~/turtlebot_ws/src/
```

## d\编译与添加到环境变量

```shell
cd ~/turtlebot_ws
catkin_make
echo "source ~/turtlebot_ws/devel/setup.bash" >> ~/.bashrc
source ~/.bashrc 
```

## e\测试

```shell
roslaunch turtlebot_gazebo turtlebot_world.launch
```

# 运行opencv巡线

```shell
mkdir catkin_ws
cd catkin_ws
mkdir src
catkin_make
cd ~/catkin_ws/src
git clone https://github.com/sudrag/line_follower_turtlebot.git
cd ..
catkin_make
```

## run

```
cd ~/catkin_ws
source devel/setup.bash
roslaunch line_follower_turtlebot lf.launch
```

