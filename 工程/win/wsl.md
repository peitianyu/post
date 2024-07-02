---
title: wsl安装与使用
date: 2021-10-19 12:30:12
categories:
  - 工程
  - win
tags:
  - tool
---

### wsl安装

参考网址：

[Windows 终端安装 | Microsoft Docs](https://docs.microsoft.com/zh-cn/windows/terminal/get-started)

[玩转Linux(1)——安装Windows亲儿子Linux系统之WSL之最全攻略之最佳体验之究极无敌舒服_衡与墨的博客-CSDN博客](https://hengyumo.blog.csdn.net/article/details/102544521)

管理员身份运行Powershell

```shell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
 wsl --install -d Ubuntu
```

### wsl使用

- #### 进入linux系统

命令行输入`wsl`或者`bash`，进入linux系统

- #### linux初始配置

##### 添加sudo，这样就不用输密码了

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191014120116452.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191014142719338.png)

修改

```shell
%sudo ALL=(ALL:ALL) ALL
# 改为
%sudo ALL=(ALL:ALL) NOPASSWD:ALL
```

- #### 图形界面

参考网址：

[WSL2运行图像应用或图形界面_Alisebeast的博客-CSDN博客_wsl2 图形界面](https://blog.csdn.net/Alisebeast/article/details/106680267)

[在WSL中打开与显示图片的简单方法_neetneves的博客-CSDN博客](https://blog.csdn.net/qq_45036130/article/details/105623451)

```shell
echo "export DISPLAY=localhost:0" >> ~/.bashrc
source ~/.bashrc
```

测试查看

```shell
echo $DISPLAY
# 或者
xclock
```

- #### 更新软件源

参考网址：[Ubuntu 更换国内源_quanwei的博客-CSDN博客_ubuntu换源](https://blog.csdn.net/qq_35451572/article/details/79516563)

- #### wsl命令详解

|                             命令                             |          含义          |
| :----------------------------------------------------------: | :--------------------: |
|                          wsl或bash                           |     打开默认子系统     |
|                       wsl -d Ubuntu18                        |   打开Ubuntu18子系统   |
|                          wsl -l -o                           | 可安装的有效分发的列表 |
|                   wsl --install -d Ubuntu                    |     安装默认Ubuntu     |
|                        wsl --shutdown                        |       关闭子系统       |
| wsl --import Ubuntu-20.04 e:\ubuntu d:\ubuntu20.04.tar --version 2 |   导入已存的操作系统   |
|         wsl --export Ubuntu-20.04 e:\ubuntu20.04.tar         |     导出操作子系统     |
|                          wsl -l -v                           |     显示所有子系统     |
|                     wsl -s Ubuntu-20.04                      |  将分发版设置为默认值  |
|                         bash -c "ls"                         |   windows命令提示符    |
|                wsl --unregister Ubuntu-20.04                 |       注销分发版       |
|                  wslconfig /s Ubuntu-18.04                   |     修改默认子系统     |

- #### *wsl*-terminal

参考网址：

[mskyaxl/wsl-terminal: Terminal emulator for Windows Subsystem for Linux (WSL) (github.com)](https://github.com/mskyaxl/wsl-terminal)

最常用的依旧是`Windows Terminal`参考网址

[Windows Terminal配置_人间世-CSDN博客](https://blog.csdn.net/rjszz1314/article/details/111354738?ops_request_misc=&request_id=&biz_id=102&utm_term=windows terminal&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-4-111354738.pc_search_ecpm_flag&spm=1018.2226.3001.4187)

[Windows Terminal 新终端下载安装_Archon-CSDN博客_terminal 下载](https://blog.csdn.net/c13232906050/article/details/90698772?ops_request_misc=%7B%22request%5Fid%22%3A%22163439638216780261990702%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=163439638216780261990702&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-4-90698772.pc_search_ecpm_flag&utm_term=windows+terminal离线安装&spm=1018.2226.3001.4187)

##### Windows Terminal安装

方式一：

微软商店安装

[购买 Windows Terminal - Microsoft Store zh-CN](https://www.microsoft.com/zh-cn/p/windows-terminal/9n0dx20hk701?rtc=1&activetab=pivot:overviewtab)

方式二：

[Windows Terminal 新终端下载安装_Archon-CSDN博客_terminal 下载](https://blog.csdn.net/c13232906050/article/details/90698772?ops_request_misc=%7B%22request%5Fid%22%3A%22163439638216780261990702%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=163439638216780261990702&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-4-90698772.pc_search_ecpm_flag&utm_term=windows+terminal离线安装&spm=1018.2226.3001.4187)

方式三：

https://github.com/microsoft/Terminal 

使用Visual Studio打开构建运行

## wsl1使用串口
参考网址:
  https://blog.csdn.net/toopoo/article/details/89237614
```
  Windows10的串口，在WSL中也可以直接使用，其对应关系就是
  COMx对应WSL的/dev/ttySx，例如COM4对应WSL的/dev/ttyS4。

  有了这个对应关系，在WSL中使用minicom就很方便了，例如：
  minicom -D /dev/ttyS4 -b 115200
  如果需要保存log到文件并在文件名中添加创建时间，可以使用如下命令：
  minicom -D /dev/ttyS4 -b 115200 -c on -C minicom-log-$(date +%Y-%m-%d_%H_%M_%S).txt
  如果使用默认端口和波特率那就是
  minicom -c on -C minicom-log-$(date +%Y-%m-%d_%H_%M_%S).txt
```

