---
title: docker_learning
date: 2024-05-10 12:30:12
categories:
  - 工程
  - 代码工具
tags:
  - 模板
---


## 安装docker

### 安装流程

```shell
# 更新软件源
sudo apt update 
sudo apt upgrade

sudo apt-get install ca-certificates curl gnupg lsb-release

# 添加官方密钥
curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -

# 添加官方源
sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"

# 安装docker
sudo apt-get install docker-ce docker-ce-cli containerd.io

```

### 参考网址
    https://zhuanlan.zhihu.com/p/651148141

## platform_docker
sudo gpasswd -a $USER docker #将登陆用戶加入到docker用戶组中
newgrp docker #更新用戶组
docker ps #测试docker命令是否可以使用sudo正常使用

docker run -it --name armv8-ncnn --platform linux/arm64 -v /root/ws/docker_ws/00docker:/root/ws ros:noetic
/root/ws/docker_ws/00docker 为本地目录
/root/ws 容器目录

docker exec -it  armv8-ncnn bash # 进入容器

## slam_docker
docker run -it --name slam-armv8 --platform linux/arm64 -v /root/ws/docker_ws/slam_docker:/root/ws jimmyteng/slam_cross:v0.1
docker exec -it  slam-armv8 bash

docker run -it --name slam-armv8 --platform linux/arm64 -v /home/tiantian/ws/docker_ws:/root/ws jimmyteng/slam_cross:v0.1

## docker基础命令
docker ps #查看正在运行的容器
docker ps -a #查看所有容器

docker start <container_id> #启动容器
docker stop <container_id> #停止容器

docker rm <container_id> #删除容器
