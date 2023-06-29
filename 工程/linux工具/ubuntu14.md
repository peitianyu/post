---
title: ubuntu14
date: 2021-11-23 12:30:12
categories:
  - 工程
  - linux工具
tags:
  - linux
---

# 安装ssh

```shell
# 安装服务
sudo apt-get install openssh-server
# 查看ssh服务是否启动
sudo ps -e |grep ssh
# 启动ssh服务
sudo service ssh start
# 配置服务
sudo gedit /etc/ssh/sshd_config
#找到下面相关配置：
# Authentication:
LoginGraceTime 120
PermitRootLogin prohibit-password
StrictModes yes

#更改为：
# Authentication:
LoginGraceTime 120
#PermitRootLogin prohibit-password
PermitRootLogin yes
StrictModes yes
# 重启ssh
sudo service ssh restart
```

# 换源

```shell
cp /etc/apt/sources.list /etc/apt/sources.list.bak 
sudo nano /etc/apt/sources.list 
```

```shell
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ trusty main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ trusty main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ trusty-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ trusty-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ trusty-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ trusty-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ trusty-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ trusty-security main restricted universe multiverse
 
# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ trusty-proposed main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ trusty-proposed main restricted universe multiverse
```

```shell
sudo apt update
sudo apt upgrade
```

# 右键没有terminal

```shell
sudo apt-get install nautilus-open-terminal 
sudo reboot
```

