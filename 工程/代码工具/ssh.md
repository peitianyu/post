---
title: ssh
date: 2021-10-19 12:30:12
categories:
  - 工程
  - 代码工具
tags:
  - tool
---

### 参考网址

- [SSH简介及两种远程登录的方法_德prince-CSDN博客_ssh](https://blog.csdn.net/li528405176/article/details/82810342?ops_request_misc=%7B%22request%5Fid%22%3A%22163464883116780261940241%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163464883116780261940241&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-82810342.pc_search_ecpm_flag&utm_term=ssh&spm=1018.2226.3001.4187)

#### 安装:

```shell
sudo apt-get install openssh-client 
sudo apt-get install openssh-server 
# 查看启动情况
ps -e | grep ssh
# 启动 停止 重启
sudo /etc/init.d/ssh start 
sudo /etc/init.d/ssh stop  #server停止ssh服务 
sudo /etc/init.d/ssh restart  #server重启ssh服务
```

### 登录

#### 口令登录

```shell
# 基础登录
ssh ldz@192.168.0.1
# 调用图形界面
ssh -X ldz@192.168.0.1
# 如果客户机的用户名和服务器的用户名相同，登录时可以省略用户名。
ssh 192.168.0.1
# -p修改端口,默认22端口
ssh -p 1234 ldz@192.168.0.1
```

第一次连时需要确认连接,选yes,即可成功

遇到问题:

`@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ @ WARNING: REMOT`

bug解决:

ssh-keygen -R 192.168.0.1

#### 退出

`Ctrl+d`或者`exit`

#### 公钥登录

##### 在本机生成密钥对

```sh
ssh-keygen -t rsa   #-t表示类型选项，这里采用rsa加密算法
cat ~/.ssh/id_rsa.pub
```

##### 将公钥复制到远程主机

```
ssh-copy-id ldz@192.168.0.1
```

