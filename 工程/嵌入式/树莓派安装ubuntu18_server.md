---
title: 树莓派安装Ubuntu18_server
date: 2021-11-6 12:30:12
categories:
  - 工程
  - 嵌入式
tags:
  - 嵌入式
---

# 参考网址:

[树莓派raspberry 安装ubuntu18.04 server + desktop + ros1_越来越胖了_mengleijin的博客-CSDN博客](https://blog.csdn.net/weixin_36628778/article/details/105056549?ops_request_misc=%7B%22request%5Fid%22%3A%22163048418216780264057617%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=163048418216780264057617&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_v2~rank_v29-1-105056549.pc_search_insert_download&utm_term=ubuntu18.04+server+wlan0&spm=1018.2226.3001.4187)

[树莓派4b安装带桌面的ubuntu18.04 server和ROS melodic系统 - 灰信网（软件开发博客聚合） (freesion.com)](https://www.freesion.com/article/3758800537/)

[树莓派4B安装Ubuntu18.04 + vnc远程桌面_我是大一菜鸡-CSDN博客](https://blog.csdn.net/m0_52364631/article/details/112439570?ops_request_misc=%7B%22request%5Fid%22%3A%22163867892516780261940219%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163867892516780261940219&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-1-112439570.pc_search_mgc_flag&utm_term=树莓派Ubuntu18&spm=1018.2226.3001.4187)

# img镜像下载

pi烧录软件:https://downloads.raspberrypi.org/imager/imager_latest.exe

[树莓派操作系统镜像下载地址](https://www.lxx1.com/3779)

[ubuntu18_server](https://mirrors.tuna.tsinghua.edu.cn/ubuntu-cdimage/releases/bionic/release/ubuntu-18.04.5-preinstalled-server-arm64%2Braspi4.img.xz)

https://mirrors.tuna.tsinghua.edu.cn/ubuntu-cdimage/ubuntu/releases/18.04.5/release/

# 将系统烧入SD卡

使用**win32diskimager**或者**balenaetcher**烧录进去

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210507104309422.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNTcwMDI1,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200603191826555.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NDU4NDYx,size_16,color_FFFFFF,t_70)

# 启动树莓派

将SD卡插入树莓派，并为树莓派连接好电源，显示器，鼠标，键盘，网线。启动树莓派，默认用户名密码都是`ubuntu`，之后需要马上修改密码，按提示操作即可。

# SSH

## 打开 `system-boot` 磁盘下根目录的 `network-config` 文件

```shell
# This file contains a netplan-compatible configuration which cloud-init
# will apply on first-boot. Please refer to the cloud-init documentation and
# the netplan reference for full details:
#
# https://cloudinit.readthedocs.io/
# https://netplan.io/reference
#
# Some additional examples are commented out below

version: 2
ethernets:
  eth0:
    dhcp4: false
    addresses: [192.168.30.110/24]
wifis:
  wlan0:
    access-points:
      HUAWEI_PTY:
        password: pty123456787
    dhcp4: true                         
```

## 修改在相同目录下的 `user-data` 文件，`expire：`后改成`false`。

![image-20211205144213769.png](https://s2.loli.net/2021/12/06/6Jked1uLC9oYIfN.png)

## 启动树莓派

如果是刷完固件后第一次启动，因为第一次需要进行系统配置，所以第一次不会自动连接。第一次上电过两分钟后重新上电稍等片刻就会自动连接wifi了。

# 连网

可以参考:

[Ubuntu Server 18.04 连接 WIFI_zyr920425的博客-CSDN博客](https://blog.csdn.net/zyr920425/article/details/93351457)

编辑,注意需要使用`Tab`键对齐,建议使用能看到对其线的对其工具,比如`sublime`

```shell
sudo nano /etc/netplan/xxxxxxx.yaml
```

```shell
# This file is generated from information provided by the datasource.  Changes
# to it will not persist across an instance reboot.  To disable cloud-init's
# network configuration capabilities, write a file
# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
network:
    version: 2
    ethernets:
        eth0:
            dhcp4: false
            addresses: [192.168.8.110/24]
    wifis:
        wlan0:
            access-points:
                HUAWEI_PTY:
                    password: pty123456787
            dhcp4: true
            optional: true
```

```shell
sudo netplan apply
```



# 换源

```shell
sudo nano  /etc/apt/sources.list
```

参考:[树莓派4安装 18.04ubuntu server（2020.11.2）_lovely_yoshino的博客-CSDN博客_树莓派ubuntu18.04](https://blog.csdn.net/lovely_yoshino/article/details/109448556?ops_request_misc=%7B%22request%5Fid%22%3A%22163867892516780261940219%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163867892516780261940219&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-3-109448556.pc_search_mgc_flag&utm_term=树莓派Ubuntu18&spm=1018.2226.3001.4187)

```sh
deb http://mirrors.aliyun.com/ubuntu-ports bionic main restricted
deb http://mirrors.aliyun.com/ubuntu-ports bionic-updates main restricted
deb http://mirrors.aliyun.com/ubuntu-ports bionic universe
deb http://mirrors.aliyun.com/ubuntu-ports bionic-updates universe
deb http://mirrors.aliyun.com/ubuntu-ports bionic multiverse
deb http://mirrors.aliyun.com/ubuntu-ports bionic-updates multiverse
deb http://mirrors.aliyun.com/ubuntu-ports bionic-backports main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu-ports bionic-security main restricted
deb http://mirrors.aliyun.com/ubuntu-ports bionic-security universe
deb http://mirrors.aliyun.com/ubuntu-ports bionic-security multiverse
 
deb-src http://mirrors.aliyun.com/ubuntu-ports bionic main restricted
deb-src http://mirrors.aliyun.com/ubuntu-ports bionic-updates main restricted
deb-src http://mirrors.aliyun.com/ubuntu-ports bionic universe
deb-src http://mirrors.aliyun.com/ubuntu-ports bionic-updates universe
deb-src http://mirrors.aliyun.com/ubuntu-ports bionic multiverse
deb-src http://mirrors.aliyun.com/ubuntu-ports bionic-updates multiverse
deb-src http://mirrors.aliyun.com/ubuntu-ports bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu-ports bionic-security main restricted
deb-src http://mirrors.aliyun.com/ubuntu-ports bionic-security universe
deb-src http://mirrors.aliyun.com/ubuntu-ports bionic-security multiverse
```

```shell
sudo apt-get update
```

# SSH连接

网线连接好,通过`ip a`查看是否配置好,然后在物理机上尝试`ping`通后连接

```shell
ssh.exe ubuntu@192.168.xx.xx
```

# 安装gcc与g++

```shell
 sudo apt-get install build-essential
```

