---
title: ros2安装
date: 2021-12-10 12:30:12
categories:
  - slam
  - ros
tags:
  - ros
---

# 参考网址:

[Ubuntu18.04 安装ROS Melodic && Ros2 Dashing踩坑笔记_LukeyAlvin的博客-CSDN博客_ubuntu18安装ros2](https://blog.csdn.net/qq_44717317/article/details/104547474)

[鱼香ROS (fishros.com)](http://fishros.com/#/fish_home)

# 简介

由于ros2很多并不完善,所以采用ros1+ros2的方式实现,这里采用常规+fishros的方式实现安装.最后采用在初始化使用shell脚本选择使用ros版本

# Ros1安装

[ros安装 | 某飞行员的随笔 (peitianyu.github.io)](https://peitianyu.github.io/blog/2021/10/19/ros安装/)

# Ros2安装

```shell
# 设置UTF-8编码
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8
# 更新软件源
sudo apt update && sudo apt install curl gnupg2 lsb-release
curl http://repo.ros2.org/repos.key | sudo apt-key add -
sudo sh -c 'echo "deb [arch=amd64,arm64] http://packages.ros.org/ros2/ubuntu `lsb_release -cs` main" > /etc/apt/sources.list.d/ros2-latest.list'
# 安装
wget http://fishros.com/install -O fishros && sudo bash fishros
# 通过如下命令添加到.bashrc(注意这里如果需要使用两个版本,这一步不需要设置)
echo "source /opt/ros/dashing/setup.bash" >> ~/.bashrc
# RWM功能
sudo apt update
sudo apt install ros-dashing-rmw-opensplice-cpp # for OpenSplice
sudo apt install ros-dashing-rmw-connext-cpp # for RTI Connext (requires license agreement)
# ros1与ros2通信
sudo apt update
sudo apt install ros-dashing-ros1-bridge
```

```shell
# 安装编译工具
sudo apt update && sudo apt install -y \
build-essential \
cmake \
git \
python3-colcon-common-extensions \
python3-pip \
python-rosdep \
python3-vcstool \
wget
# install some pip packages needed for testing
sudo -H python3 -m pip install -U \
argcomplete \
flake8 \
flake8-blind-except \
flake8-builtins \
flake8-class-newline \
flake8-comprehensions \
flake8-deprecated \
flake8-docstrings \
flake8-import-order \
flake8-quotes \
pytest-repeat \
pytest-rerunfailures
# [Ubuntu 16.04] install extra packages not available or recent enough on Xenial
python3 -m pip install -U \
pytest \
pytest-cov \
pytest-runner \
setuptools
# install Fast-RTPS dependencies
sudo apt install --no-install-recommends -y \
libasio-dev \
libtinyxml2-dev
```

```shell
# 测试
mkdir  ~/ros2_ws
cd /ros2_ws
git clone  https://ghproxy.com/https://github.com/zhangrelay/ros2_tutorials
cd ros2_tutorials
colcon build
# 设置环境变量(也可以将之放入.bashrc中)
source /install/setup.sh 
# 第一个终端
cd /ros_ws/ros2_tutorials/install
ros2 run demo_nodes_cpp listener
# 第二个终端
ros2 run demo_nodes_cpp talker
```

# 使用多个ros版本

## 保存一个脚本文件,使用前运行

```shell
#source /opt/ros/melodic/setup.bash
#source /opt/ros/dashing/setup.bash
# ROS 1.0 melodic or ROS 2.0 Dashing
echo Hello alvin! ROS 1.0 or ROS 2.0? 1=Melodic 2=Dashing 
read ROS
if (($ROS==1));then
source /opt/ros/melodic/setup.bash
echo "Melodic"
elif (($ROS==2));then
source /opt/ros/dashing/setup.bash
echo "Dashing"
else
echo "Non-ROS"
fi
```

## !注意:由于使用不通ros,因此安装时注意将之前保存在.bashrc中的

## `# source /opt/ros/melodic/setup.bash`
`# source /opt/ros/dashing/setup.bash`注释掉
