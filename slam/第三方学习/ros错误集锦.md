---
title: ros错误集锦
date: 2021-10-19 12:30:12
categories:
  - slam
  - ros
tags:
  - ros
---

## “cv_bridge“解决办法

### 出现问题:

```
CMake Error at /opt/ros/kinetic/share/catkin/cmake/catkinConfig.cmake:83 (find_package):
Could not find a package configuration file provided by “cv_bridge” with
any of the following names:

cv_bridgeConfig.cmake
cv_bridge-config.cmake
```

### 解决办法:

[CMake Error ：Could not find a package configuration file provided by “cv_bridge“解决办法](https://www.it610.com/article/1282152021410988032.htm)

## WSL+Ubuntu18.03环境下gazebo9视图黑屏问题的解决

```
# 卸载gazebo与ros
sudo apt-get remove gazebo11 gazebo11-*
sudo apt-get purge ros-*
sudo apt-get install ros-melodic-desktop-full
# 卸载gazebo9
sudo apt-get remove gazebo9 gazebo9-common gazebo9-plugin-base libgazebo9:amd64 libgazebo9-dev:amd64 ros-melodic-gazebo-*  
# 查看
dpkg -l | grep gazebo
# 清楚残余包
sudo dpkg --purge gazebo9
# 安装gazebo11
sudo apt-get install gazebo11 -y
sudo apt-get install libgazebo11-dev
gazebo
```

通过重新安装的方式绕过

[WSL折腾记录之三：WSL+Ubuntu18.03环境下gazebo9视图黑屏问题的解决与升级安装gazebo11](https://blog.csdn.net/weixin_53266173/article/details/110817750?ops_request_misc=%7B%22request%5Fid%22%3A%22162985238916780261959221%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=162985238916780261959221&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v29_ecpm-12-110817750.pc_search_similar&utm_term=gazebo黑屏&spm=1018.2226.3001.4187)

### rosdep init不成功

命令行输入

```shell
wget http://fishros.com/install -O fishros && sudo bash fishros
```

## Could not find a package configuration file provided by "xxxx"

```shell
sudo apt-get install ros-melodic-xxxx 
```

## wsl1+rviz闪退,并报段错误

```shell
# 报错
[ INFO] [1663138835.579682600]: rviz version 1.13.29
[ INFO] [1663138835.582139000]: compiled against Qt version 5.9.5
[ INFO] [1663138835.583938400]: compiled against OGRE version 1.9.0 (Ghadamon)
[ INFO] [1663138835.635607300]: Forcing OpenGl version 0.
Segmentation fault (core dumped)

# 解决
nano ~/.bashrc
export LIBGL_ALWAYS_INDIRECT=0
source ~/.bashrc
```

