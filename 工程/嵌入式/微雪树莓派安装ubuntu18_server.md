---
title: 微雪树莓派安装ubuntu18_server
date: 2022-3-30 12:30:12
categories:
  - 工程
  - 嵌入式
tags:
  - 嵌入式
---

# 参考网址:

 [CM4 Burn EMMC - Waveshare Wiki](https://www.waveshare.net/wiki/CM4_Burn_EMMC) 

# 步骤

- 安装 [rpiboot 软件](https://www.waveshare.net/w/upload/f/f3/Rpiboot_setup.zip)
- 将 BOOT开关拨至ON 
- 打开 rpiboot软件,会出现如下,说明已经连接上树莓派

 ![CM4 Burn EMMC 5.png](https://www.waveshare.net/w/upload/0/06/CM4_Burn_EMMC_5.png)

- 安装[树莓派镜像烧录器](https://downloads.raspberrypi.org/imager/imager_latest.exe)
- 下载[ubuntu8_server]( [Index of /ubuntu-cdimage/ubuntu/releases/18.04.5/release/ | 清华大学开源软件镜像站 | Tsinghua Open Source Mirror](https://mirrors.tuna.tsinghua.edu.cn/ubuntu-cdimage/ubuntu/releases/18.04.5/release/) )
- 使用树莓派烧录器安装ubuntu18_server
- 完成后重启树莓派,修改boot下`network-config`与`user-data`

```shell
version: 2
ethernets:
  eth0:
    dhcp4: false
    addresses: [192.168.8.110/24]
  eth1:
    dhcp4: false
    addresses: [192.168.8.111/24]
wifis:
  wlan0:
    access-points:
      HUAWEI_PTY:
        password: pty123456787
    dhcp4: true                         
```

```shell
ssh_pwauth: true
chpasswd:
  expire: false
  list:
  - ubuntu:ubuntu
```

-  USB2.0 默认是关闭的，如需打开需要在config.txt中添加 dtoverlay=dwc2,dr_mode=host 
- 将 BOOT开关拨至OFF,连接显示屏查看ip
- 使用`sudo systemctl enable ssh`与`sudo systemctl start ssh`启动ssh
- 通过ssh ubuntu@192.168.8.110连接树莓派