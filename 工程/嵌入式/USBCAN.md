---
title: USBCAN/CANalyst-II
date: 2021-11-11 12:30:12
categories:
  - 工程
  - 嵌入式
tags:
  - 嵌入式
---

# 参考网址:

[(完整版)Python读取CANalyst-II分析仪(创芯科技)接口函数_蜡笔小新历险记的博客-CSDN博客_创芯科技can分析仪驱动](https://blog.csdn.net/muyoufansem/article/details/117955581?ops_request_misc=&request_id=&biz_id=102&utm_term=canalyst&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-3-117955581.pc_search_mgc_flag&spm=1018.2226.3001.4187)

[Ubuntu16.04 （ROS）下通过CAN分析仪（USBCAN/CANalyst-II）调试无人车助力转向电机（1）_秋名山的过客-CSDN博客](https://blog.csdn.net/qq_38766896/article/details/109404097?ops_request_misc=%7B%22request%5Fid%22%3A%22163642380516780264058809%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163642380516780264058809&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-109404097.pc_search_mgc_flag&utm_term=canalyst-ii+linux&spm=1018.2226.3001.4187)

[USBCAN在Linux系统下使用说明 (gcgd.net)](http://www.gcgd.net/newsinfo_726.html)

[ubuntu下研华工控机CAN卡驱动的安装与测试_星辰和大海都需要门票的博客-CSDN博客](https://blog.csdn.net/qq_41545537/article/details/106714246?ops_request_misc=%7B%22request%5Fid%22%3A%22163607238916780271515211%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=163607238916780271515211&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-106714246.pc_search_mgc_flag&utm_term=ubuntucan驱动安装&spm=1018.2226.3001.4187)

# 简介

介绍两款使用过的`usb`转`can`的使用方法

一款是广成科技的`USBCAN-I PRO`[资料下载 (gcgd.net)](http://www.gcgd.net/tecrjlist.html)

一款是创芯科技的`CANalyst`[珠海创芯科技有限公司 - 珠海创芯科技 (zhcxgd.com)](https://www.zhcxgd.com/ZLXZ.html)

# 软件安装与使用

## USBCAN:

[沈阳广成科技USBCAN系列产品驱动安装及ECANTools软件使用方法 (gcgd.net)](http://www.gcgd.net/newsinfo_388.html)

[GC-USBCAN-----点击进入文件夹选择需要下载的文件_免费高速下载|百度网盘-分享无限制 (baidu.com)](https://pan.baidu.com/s/10R7zou8yxzarjlwlJkeN1A#list/path=%2FGC-USBCAN-----点击进入文件夹选择需要下载的文件)

## CANalyst:

[(完整版)Python读取CANalyst-II分析仪(创芯科技)接口函数_蜡笔小新历险记的博客-CSDN博客_创芯科技can分析仪驱动](https://blog.csdn.net/muyoufansem/article/details/117955581?ops_request_misc=&request_id=&biz_id=102&utm_term=canalyst&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-3-117955581.pc_search_mgc_flag&spm=1018.2226.3001.4187)

# 编程

## !建议采用官方例程至少不会出错

## 这里推荐两个例程可以验证学习

[VincentCheungM/ESR_canalyst_ros: A warped ROS driver for CANalyst-II and ESR Radar (github.com)](https://github.com/VincentCheungM/ESR_canalyst_ros)

[Andy-jg/RaspberryCanalyst-2: linux use the canalyst-2 (github.com)](https://github.com/Andy-jg/RaspberryCanalyst-2)

# !注意

由于can驱动使用时每次都需要`sudo`,可以加配置,这样就不需要每次都`sudo`了

## 方法

```shell
sudo nano /etc/udev/rules.d/99-myusb.rules
```

输入

```shell
ACTION=="add",SUBSYSTEMS=="usb", ATTRS{idVendor}=="04d8", ATTRS{idProduct}=="0053",
GROUP="users", MODE="0777"
```

