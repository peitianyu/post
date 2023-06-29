---
title: jetson_nano 踩坑
date: 2021-10-19 12:30:12
categories:
  - 工程
  - 嵌入式
tags:
  - 嵌入式
---

### 参考网址:

[玩转智能硬件之Jetson Nano(一)安装篇 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/166464566)

[Jetson Nano-配置环境_Ay_yzx的博客-CSDN博客](https://blog.csdn.net/Ay_yzx/article/details/113779544)

#### 预先下载:

`balenaEtcher`

[balenaEtcher - Flash OS images to SD cards & USB drives](https://www.balena.io/etcher/)

`系统下载`

官方默认系统:

https://developer.download.nvidia.cn/embedded/L4T/r32_Release_v6.1/Jeston_Nano/jetson-nano-jp46-sd-card-image.zip?xtLGc386JIwQlfgPPGITNMMx3dC47xG6lIybgH0z9HUPzZaP7ZO2h3U3UFhcpzo7QJsYr-1cCnspcgl929SsucJH043bqSzwC_jUQGxqQuJvmNyIMcrmp8IJopYJUgh41KDf32qZNNeEUEquBj5vkUSAUH_46_UJ1VEv5d1IbYE_36EcSGByIJ1Anw

Ubuntu18.04:

https://pan.baidu.com/share/init?surl=QXd34V5FfLcFk2XDgphoig 密码 : tun3

`sd卡格式化工具`

[SD Memory Card Formatter for Windows Download | SD Association (sdcard.org)](https://www.sdcard.org/downloads/formatter/sd-memory-card-formatter-for-windows-download/)

### 安装步骤:

```
1.格式化sd卡
2.使用balenaEtcher烧录下载好的系统
3.插上卡后做初始化设置
```

### jetson三种供电方式:

#### 参考网址:

[Jetson nano 的三种供电方式_Dunkle.T的博客-CSDN博客_jetson nano供电](https://blog.csdn.net/weixin_44350337/article/details/111674531?ops_request_misc=%7B%22request%5Fid%22%3A%22163456105816780262525022%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163456105816780262525022&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-111674531.pc_search_ecpm_flag&utm_term=jetson+供电&spm=1018.2226.3001.4187)

#### 分别为:

- USB供电(默认5v2a 仅低功率),使用时拔去条线帽
- 使用 DC 供电(默认5v 4A),使用时插上跳线帽
- 使用引脚供电(5v3a 无所谓跳线帽)

#### 查看cuda配置

```shell
sudo pip3 install jetson-stats
sudo jtop
```

### 关于ros安装

#### 参考网址:

[ROS_安装 (peitianyu.github.io)](https://peitianyu.github.io/pty_blog.github.io/ros/ros安装/)

#### 其他部分按ros安装教程来即可

比较实用的方案是先按照我的步骤安装ros,之后再通过`fishros一行代码`解决rosdep问题

### 远程连接nano

`ssh.exe usrname@192.168.xxx.xxx`

### jetson nano初始化配置

#### 网络配置

`静态ip设置`:[Ubuntu 18.04配置静态IP地址)](https://www.cnblogs.com/blueyunchao0618/p/11394640.html)

#### root免密登录

`参考网址`:[ubuntu设置root免密登陆](https://blog.csdn.net/qq_44673299/article/details/108658078?ops_request_misc=%7B%22request%5Fid%22%3A%22163463123116780271513740%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=163463123116780271513740&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-2-108658078.pc_search_ecpm_flag&utm_term=Ubuntu18+root免密登录&spm=1018.2226.3001.4187)

### vscode连接jetson nano

#### 参考网址:

[vscode设置ssh进行远程编辑_Quan的博客-CSDN博客](https://blog.csdn.net/sqlquan/article/details/111918019?ops_request_misc=%7B%22request%5Fid%22%3A%22163463106616780264095126%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163463106616780264095126&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-111918019.pc_search_ecpm_flag&utm_term=vscode+ssh&spm=1018.2226.3001.4187)

