---
title: melodic+carto
date: 2022-4-16 12:30:12
categories:
  - slam
  - 开源框架
tags:
  - slam
---

# 参考网址:

 [(124条消息) （实测成功安装）ubuntu18.04+ros(melodic)安装 cartographer安装_我想lu内只狗的博客-CSDN博客](https://blog.csdn.net/lianxindong/article/details/122395589?ops_request_misc=%7B%22request%5Fid%22%3A%22165010532816780357289025%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=165010532816780357289025&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-3-122395589.142^v9^pc_search_result_cache,157^v4^control&utm_term=melodic安装cartographer&spm=1018.2226.3001.4187) 

# 安装carto

```shell
# 安装工具
sudo apt-get update
sudo apt-get install -y python-wstool python-rosdep ninja-build stow
# 创建初始空间
mkdir -p cart_ws/src
cd cart_ws
wstool init src  
# 下载包
cd src
git clone https://ghproxy.com/https://github.com/cartographer-project/cartographer
git clone https://ghproxy.com/https://github.com/cartographer-project/cartographer_ros
git clone -b 1.14.x https://ghproxy.com/https://github.com/ceres-solver/ceres-solver
# 添加依赖
cd .. &&src/cartographer/scripts/install_abseil.sh
sudo rosdep init
rosdep update 
rosdep install --from-paths src --ignore-src --rosdistro=${ROS_DISTRO} -y
# 编译安装
catkin_make_isolated --install --use-ninja
```

# 安装ceres-solver

```sh
git clone https://ceres-solver.googlesource.com/ceres-solver
cd ceres-solver
mkdir build&&cd build
cmake .. 
make -j8&&sudo make install
```

# 测试

[测试包](https://google-cartographer-ros.readthedocs.io/en/latest/data.html#d-cartographer-backpack-deutsches-museum)

```shell
roslaunch cartographer_ros demo_backpack_2d.launch bag_filename:=/home/fairy/Downloads/b0-2014-07-11-11-00-49.bag
roslaunch cartographer_ros demo_backpack_3d.launch bag_filename:=/home/fairy/Downloads/b3-2016-03-01-13-39-41.bag 
```

# 修改~/.bashrc

```sh
sudo echo "source ~/cart_ws/devel_isolated/setup.bash" >> ~/.bashrc
source ~/.bashrc
echo $ROS_PACKAGE_PATH
```

