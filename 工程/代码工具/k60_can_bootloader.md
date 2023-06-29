---
title: k60_can_bootloader
date: 2021-12-10 12:30:12
categories:
  - 工程
  - 代码工具
tags:
  - 嵌入式
---

### 参考demo:

[peitianyu/k60_bootloader (github.com)](https://github.com/peitianyu/k60_bootloader)

### main.c文件配置

#### 进入mian.c文件加入头文件并引用bootloader_entry()函数

![1.PNG](https://s2.loli.net/2021/12/10/ywEp53mGsnHMbYc.png)

#### 使用ID=0x10发送06 00 00 00 00 00 00 00，表示已经进入app

![2.png](https://s2.loli.net/2021/12/10/SvE7taw8y9ozPbh.png)

#### 判断bootflag是否制一，若是，进入boot模式

![3.png](https://s2.loli.net/2021/12/10/7pd6cl9bwtKPzFT.png)

### canbus.c文件配置

#### 在canbus.c文件中引用头文件，并判断是否0x25发来数据，若有且data._data[0]==1,则bootflag=1

![4.png](https://s2.loli.net/2021/12/10/1nd3eMiXpUtBlAr.png)

### keil配置

#### 将hex烧录地址改为从0x6000开始

![5.png](https://s2.loli.net/2021/12/10/UhGvZDBPQqSeM9Y.png)

### 树莓派使用

#### ./bootloader can0 xx.hex

![7.PNG](https://s2.loli.net/2021/12/10/wkOHJ3reb8AXVyT.png)

### 挂载win10

```shell
sudo mount -t cifs //192.168.2.101/2021.04/CH-K-Lib/Project/1/bootloader/MDK/mk60d10/flash /k60 -o username="pty",password="pty123",sec=ntlmssp,rw
```

