---
title: 学写2d激光slam
date: 2022-2-11 12:30:12
categories:
  - slam
  - 第三方学习
tags:
  - slam
  - ros
---

# 参考网址:

  [xiangli0608/Creating-2D-laser-slam-from-scratch: 从零开始创建二维激光SLAM (github.com)](https://github.com/xiangli0608/Creating-2D-laser-slam-from-scratch) 

 [李想的博客_李太白lx_CSDN博客-从零开始搭二维激光SLAM,激光SLAM,cartographer领域博主](https://blog.csdn.net/tiancailx?spm=1011.2124.3001.5113) 

 [(105条消息) 在linux ubuntu18.04版本中安装ceres solver_YMWM_的博客-CSDN博客_linux 安装ceres](https://blog.csdn.net/YMWM_/article/details/101601345) 

 [(105条消息) 在Ubuntu16.04下安装Ceres_江南古镇的博客-CSDN博客_ceres安装](https://blog.csdn.net/jz1570551401/article/details/78207208?ops_request_misc=%7B%22request%5Fid%22%3A%22164456963316780269873261%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164456963316780269873261&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-3-78207208.pc_search_result_control_group&utm_term=安装ceres&spm=1018.2226.3001.4187) 

 [(105条消息) ubuntu16.04安装g2o_slzlincent的博客-CSDN博客_安装g2o](https://blog.csdn.net/slzlincent/article/details/86555166?ops_request_misc=%7B%22request%5Fid%22%3A%22164457025816780271978715%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164457025816780271978715&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-86555166.pc_search_result_control_group&utm_term=g2o安装&spm=1018.2226.3001.4187) 

 [(105条消息) 【已解决】error: no matching function for call to ‘g2o::BlockSolver..._Bungehurst-CSDN博客](https://blog.csdn.net/lun55423/article/details/109036995) 

# 环境搭建

- melodic
- ceres
- g2o
- gtsam
- ubuntu18

## ubuntu18安装ros1

```shell
sudo nano /etc/apt/sources.list.d/ros-latest.list
deb https://mirrors.tuna.tsinghua.edu.cn/ros/ubuntu/ bionic main
sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
sudo apt update
sudo apt-get install ros-melodic-desktop-full
# 初始化
sudo apt install python-rosinstall python-rosinstall-generator python-wstool build-essential
sudo echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

使用小于解决rosdep问题

```shell
wget http://fishros.com/install -O fishros && sudo bash fishros
```

## 下载github并安装必要库

```shell
get clone https://ghproxy.com/https://github.com/xiangli0608/Creating-2D-laser-slam-from-scratch
```

### ceres安装

依赖安装

```shell
sudo apt-get install cmake
sudo apt-get install libgoogle-glog-dev
sudo apt-get install libatlas-base-dev
sudo apt-get install libeigen3-dev
sudo apt-get install libsuitesparse-dev
sudo apt-get install libgtest-dev
sudo add-apt-repository ppa:bzindovic/suitesparse-bugfix-1319687
sudo apt-get update
sudo apt-get install libsuitesparse-dev
```

安装

```shell
cd Creating-2D-laser-slam-from-scratch/TrirdParty/ceres-solver-1.13.0
mkdir build
cd build
cmake ..
make -j
make install
```

### g2o安装

```shell
git clone https://github.com/RainerKuemmerle/g2o.git
sudo apt-get install libeigen3-dev
sudo apt-get install libsuitesparse-dev
sudo apt-get install qtdeclarative5-dev
sudo apt-get install qt5-qmake
sudo apt-get install libqglviewer-dev
cd g2o
mkdir build
cd build
cmake ..
make -j
make install
```

### gtsam安装

```
cd Creating-2D-laser-slam-from-scratch/TrirdParty/gtsam
mkdir build
cd build
cmake ..
make -j
make install
```

### 修改源文件

```c++
// src/Creating-2D-laser-slam-from-scratch/lesson6/src/g2o_solver/g2o_solver.cc中
// 修改为如下
G2oSolver::G2oSolver()
{
  // 第1步：创建一个线性求解器LinearSolver
  SlamLinearSolver *linearSolver = new SlamLinearSolver();
  linearSolver->setBlockOrdering(false);
  // 第2步：创建BlockSolver。并用上面定义的线性求解器初始化
  SlamBlockSolver *blockSolver = new SlamBlockSolver(std::unique_ptr<SlamLinearSolver>(linearSolver));
  linearSolver->setBlockOrdering(true);
  // 第3步：创建总求解器solver。并从GN, LM, DogLeg 中选一个，再用上述块求解器BlockSolver初始化
  g2o::OptimizationAlgorithmLevenberg *solver =
      new g2o::OptimizationAlgorithmLevenberg(std::unique_ptr<SlamBlockSolver>(blockSolver));
  // 第4步：创建稀疏优化器（SparseOptimizer）
  mOptimizer.setAlgorithm(solver);
}


```

## 编译

```shell
mkdir -p catkin_ws/src
cd catkin_ws/src
git clone https://ghproxy.com/https://github.com/xiangli0608/Creating-2D-laser-slam-from-scratch
cd .. && catkin_make
```

