---
title: 树莓派GPIO
date: 2021-12-6 12:30:12
categories:
  - 工程
  - 嵌入式
tags:
  - 嵌入式
---

# 参考网址:

[树莓派GPIO控制--C语言篇_hu7850的博客-CSDN博客_树莓派c语言控制gpio](https://blog.csdn.net/hu7850/article/details/51785560)

# IO表示

![image-20211210165211671.png](https://s2.loli.net/2021/12/10/wLfgq9kbRzoZD2a.png)



```shell
ubuntu@ubuntu:~/Cmake/build$ gpio readall
 +-----+-----+---------+------+---+---Pi 4B--+---+------+---------+-----+-----+
 | BCM | wPi |   Name  | Mode | V | Physical | V | Mode | Name    | wPi | BCM |
 +-----+-----+---------+------+---+----++----+---+------+---------+-----+-----+
 |     |     |    3.3v |      |   |  1 || 2  |   |      | 5v      |     |     |
 |   2 |   8 |   SDA.1 | ALT0 | 1 |  3 || 4  |   |      | 5v      |     |     |
 |   3 |   9 |   SCL.1 | ALT0 | 1 |  5 || 6  |   |      | 0v      |     |     |
 |   4 |   7 | GPIO. 7 |   IN | 1 |  7 || 8  | 1 | ALT5 | TxD     | 15  | 14  |
 |     |     |      0v |      |   |  9 || 10 | 1 | ALT5 | RxD     | 16  | 15  |
 |  17 |   0 | GPIO. 0 |   IN | 1 | 11 || 12 | 0 | IN   | GPIO. 1 | 1   | 18  |
 |  27 |   2 | GPIO. 2 |   IN | 0 | 13 || 14 |   |      | 0v      |     |     |
 |  22 |   3 | GPIO. 3 |   IN | 0 | 15 || 16 | 0 | IN   | GPIO. 4 | 4   | 23  |
 |     |     |    3.3v |      |   | 17 || 18 | 0 | IN   | GPIO. 5 | 5   | 24  |
 |  10 |  12 |    MOSI | ALT0 | 0 | 19 || 20 |   |      | 0v      |     |     |
 |   9 |  13 |    MISO | ALT0 | 0 | 21 || 22 | 0 | IN   | GPIO. 6 | 6   | 25  |
 |  11 |  14 |    SCLK | ALT0 | 0 | 23 || 24 | 1 | OUT  | CE0     | 10  | 8   |
 |     |     |      0v |      |   | 25 || 26 | 1 | OUT  | CE1     | 11  | 7   |
 |   0 |  30 |   SDA.0 |   IN | 1 | 27 || 28 | 1 | IN   | SCL.0   | 31  | 1   |
 |   5 |  21 | GPIO.21 |   IN | 1 | 29 || 30 |   |      | 0v      |     |     |
 |   6 |  22 | GPIO.22 |   IN | 1 | 31 || 32 | 0 | IN   | GPIO.26 | 26  | 12  |
 |  13 |  23 | GPIO.23 |   IN | 0 | 33 || 34 |   |      | 0v      |     |     |
 |  19 |  24 | GPIO.24 |   IN | 1 | 35 || 36 | 0 | IN   | GPIO.27 | 27  | 16  |
 |  26 |  25 | GPIO.25 |   IN | 0 | 37 || 38 | 0 | IN   | GPIO.28 | 28  | 20  |
 |     |     |      0v |      |   | 39 || 40 | 0 | IN   | GPIO.29 | 29  | 21  |
 +-----+-----+---------+------+---+----++----+---+------+---------+-----+-----+
 | BCM | wPi |   Name  | Mode | V | Physical | V | Mode | Name    | wPi | BCM |
 +-----+-----+---------+------+---+---Pi 4B--+---+------+---------+-----+-----+
```

# `wiringpi`

## 安装wiring

### 直接安装

```shell
git clone git://git.drogon.net/wiringPi
cd wiringPi
./build
build脚本会帮助你编译和安装wiringPi
```

或者通过此[下载]([WiringPi/WiringPi: Gordon's Arduino wiring-like WiringPi Library for the Raspberry Pi (Unofficial Mirror for WiringPi bindings) (github.com)](https://github.com/WiringPi/WiringPi))安装

### raspbian上安装

```shell
sudo apt-get install wiringpi
```

## 测试

**github**上有**examples**可以测试

## **cmake使用wiringpi**库

```cmake
cmake_minimum_required(VERSION 2.8)

project(display)

find_library(WIRINGPI_LIBRARIES NAMES wiringPi)

add_executable(display display.cpp)
target_link_libraries(display ${WIRINGPI_LIBRARIES})
```

# PYTHON

```
https://code.google.com/p/raspberry-gpio-python/
```

# BCM2835 C Library

## 安装

```
wget http://www.airspayce.com/mikem/bcm2835/bcm2835-1.35.tar.gz
tar xvzf bcm2835-1.35.tar.gz
cd bcm2835-1.35
./configure make
sudo make check
sudo make install
```

## 测试

**可以使用examples测试**

