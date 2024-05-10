---
title: g2o与ceres-solver安装
date: 2022-4-19 12:30:12
categories:
  - slam
  - 第三方学习
tags:
  - slam
---

# 参考网址:

[(127条消息) ubuntu18.04安装g2o_弓满路长的博客-CSDN博客_ubuntu18.04安装g2o](https://blog.csdn.net/zzcjake/article/details/113817651)

# g2o安装

```sh
# 安装依赖
sudo apt-get install libeigen3-dev libsuitesparse-dev qtdeclarative5-dev qt5-qmake libqglviewer-dev-qt5
# 下载g2o
git clone https://ghproxy.com/https://github.com/RainerKuemmerle/g2o.git
cd g2o && mkdir build && cd build
cmake ..
make -j8
make install
```

# ceres-solver安装

```sh
# 安装依赖
sudo nano /etc/apt/sources.list
deb https://cz.archive.ubuntu.com/ubuntu trusty main universe
sudo apt-get update
sudo apt-get install liblapack-dev libsuitesparse-dev libcxsparse3.1.2 libgflags-dev libgoogle-glog-dev libgtest-dev
# 获取ceres-solver
git clone https://ceres-solver.googlesource.com/ceres-solver
cd ceres-solver && mkdir build && cd build
cmake ..
make -j8
make install
```

