---
title: ros安装
date: 2021-10-19 12:30:12
categories:
  - slam
  - ros
tags:
  - ros
---

### 安装环境

```
Ubuntu18.04
ros-melodic-desktop-full
```

### 安装

```shell
vim /etc/apt/sources.list.d/ros-latest.list
deb https://mirrors.tuna.tsinghua.edu.cn/ros/ubuntu/ bionic main
sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
sudo apt update
sudo apt-get install ros-melodic-desktop-full
# 初始化
sudo rosdep init
rosdep update
sudo apt install python-rosinstall python-rosinstall-generator python-wstool build-essential
sudo echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

### 一行代码安装ros（包含问题处理）

```shell
wget http://fishros.com/install -O fishros && sudo bash fishros
```

参考视频：

[玩着游戏听着歌，就把ROS装好了！一行代码搞定系列！ (qq.com)](https://mp.weixin.qq.com/s/8hTrKL0N5y9i6s9ujhp0UA)

### 安装问题

`rosdep init`不成功

```shell
wget http://fishros.com/install -O fishros && sudo bash fishros
```

### 卸载

```shell
sudo apt-get autoremove --purge ros-melodic
# 先卸载包
sudo apt-get purge ros-*
# 然后删除依赖，配置
sudo apt-get autoremove
```

### 测试

```shell
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/
catkin_make
source devel/setup.bash
roscore
```

打开新窗口执行

```
rosrun turtlesim turtlesim_node
```

出现小乌龟则安装完成。
