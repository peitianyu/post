---
title: wsl2+docker+carto
date: 2022-4-16 12:30:12
categories:
  - 工程
  - win
tags:
  - slam
---

# 参考网址:

 [(124条消息) 【亲测有效】离线安装WSL windows 10子系统Ubuntu 20.04方法_青柚创客的博客-CSDN博客_wsl 离线安装](https://blog.csdn.net/vippomelo/article/details/121136484?ops_request_misc=%7B%22request%5Fid%22%3A%22165008013316782094873918%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=165008013316782094873918&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-2-121136484.142^v9^pc_search_result_cache,157^v4^control&utm_term=wsl离线安装&spm=1018.2226.3001.4187) 

 [Ubuntu - Docker — 从入门到实践 (gitbook.io)](https://yeasy.gitbook.io/docker_practice/install/ubuntu) 

  [Docker快速搭建cartographer/cartographer_ros编译开发环境 - AustinZ的个人空间 - OSCHINA - 中文开源技术交流社区](https://my.oschina.net/u/4154543/blog/5312374) 

# 安装wsl2

- 配置好wsl2环境( [wsl安装](https://peitianyu.github.io/blog/2021/10/19/wsl/) ),离线下载[ubuntu18](https://aka.ms/wsl-ubuntu-1804)
- 下载完成后,双击安装

# 安装docker

```shell
# 配置http
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
# 配置docker下载安装环境    
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://mirrors.aliyun.com/docker-ce/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
# 安装
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
# 启动
sudo service docker start
```

# 安装docker_carto

```shell
# 拉取镜像
	# ubuntu18.04
	docker pull fdko11/ros:bionic-melodic-cartographer
	# ubuntu20.04
	docker pull fdko11/ros:focal-noetic-cartographer
# 运行并创建docker
	docker run -it --name="carto" --net=host -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix fdko11/ros:bionic-melodic-cartographer
# 用户名:carto 密码:123456 有sudo权限
# 需要在容器中手动进行一次abseil库编译安装
su carto
cd /home/carto/carto_ws/abseil-cpp/
mkdir build
cd build
cmake -G Ninja   -DCMAKE_BUILD_TYPE=Release   -DCMAKE_POSITION_INDEPENDENT_CODE=ON   -DCMAKE_INSTALL_PREFIX=/usr/local/stow/absl   ..
sudo ninja install
cd /usr/local/stow
sudo stow absl
# 编译carto
su carto
cd /home/carto/carto_ws
source /opt/ros/melodic/setup.bash
catkin_make_isolated --install --use-ninja
```

