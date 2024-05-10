---
title: moxa配置
date: 2022-2-10 12:30:12
categories:
  - 工程
  - 嵌入式
tags:
  - 嵌入式
---

# 参考网址:

 [(105条消息) MOXA串口服务器的配置_MAYBE的博客-CSDN博客_moxa串口服务器](https://blog.csdn.net/menshu1892/article/details/72630830?ops_request_misc=%7B%22request%5Fid%22%3A%22164428386616780269899340%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164428386616780269899340&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-3-72630830.pc_search_result_control_group&utm_term=moxa&spm=1018.2226.3001.4187) 

 [如何设置MOXA的无线AP和Client-百度经验 (baidu.com)](https://jingyan.baidu.com/article/295430f18d15490c7e0050c6.html) 

# moxa无线ap

## 登录

```shell
# 新用户有线连接,网址输入 192.168.127.253
username:admin
password:moxa
```

![1644474829018.png](https://s2.loli.net/2022/02/10/NPCF2clJebBoRr7.png)

## 修改静态ip

```shell
# 选择network setting
1\ static
2\ 192.168.xx.xx
3\ 255.255.255.0
```

![1644474843435.png](https://s2.loli.net/2022/02/10/jzrA5b9vkLxCt7o.png)

## 连接到路由

```shell
# WLAN->Basic WLAN Setup
点击site survey查找需要接入ap
submit
# WLAN->WLAN Security Setting
passphrase:密码
# WLAN->Advanced WLAN Setting
参考图片
```

![1644475086084.png](https://s2.loli.net/2022/02/10/vp4QtkeC3OqjEiT.png)

![1644475096295.png](https://s2.loli.net/2022/02/10/zlpNQokyeBbt24r.png)

![1644475162592.png](https://s2.loli.net/2022/02/10/qr8eCUHAPXTztnl.png)