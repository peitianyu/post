---
title: win自启动程序
date: 2021-10-19 12:30:12
categories:
  - 工程
  - win
tags:
  - win
---

### 记事本写入脚本

```bash
time  /t  >>c:\test\log1.log

echo  %username%  >>c:\test\log1.log

echo  %computername%  >>c:\test\log1.log
```

另存为`start.bat`，无人见类型为所有类型

### 配置系统

点击`运行`，输入`gpedit.msc`,具体操作如下：

![img](https://img-blog.csdnimg.cn/20190304120752202.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzYyNTU3Nw==,size_16,color_FFFFFF,t_70)

### 重启电脑

可以发现，脚本运行了

![img](https://img-blog.csdnimg.cn/20190304120752279.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzYyNTU3Nw==,size_16,color_FFFFFF,t_70)

