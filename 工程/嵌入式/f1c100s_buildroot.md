---
title: f1c100s_buildroot
date: 2022-2-17 12:30:12
categories:
  - 工程
  - 嵌入式
tags:
  - 嵌入式
---

# 参考网址

 [aodzip/buildroot-tiny200: Buildroot Package for F1C100s/200s (github.com)](https://github.com/aodzip/buildroot-tiny200) 

# 使用

## 安装库文件

```shell
sudo apt install wget unzip build-essential git bc swig libncurses-dev libpython3-dev libssl-dev
sudo apt install python3-distutils
```

## 下载

```
git clone https://github.com/aodzip/buildroot-tiny200
```

## 生成配置文件

```
cd buildroot-tiny200
make widora_mangopi_r3_defconfig
```

## 生成文件

```shell
make -j4
```

