---
title: time
date: 2022-4-26 12:30:12
categories:
  - slam
  - ros
tags:
  - ros
---

# 参考网址:

[(128条消息) ROS学习八、ros中的时间Time,Duration,Timer和Rate（2）_RuiH.AI的博客-CSDN博客_ros时间](https://blog.csdn.net/qq_41035283/article/details/120697331?ops_request_misc=%7B%22request%5Fid%22%3A%22165093841216782390592381%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=165093841216782390592381&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-120697331.142^v9^control,157^v4^control&utm_term=ros当前时间&spm=1018.2226.3001.4187)

# 例程

Time和Duration的数据类型相同：

```C++
int32 sec
int32 nsec
```

获取当前时间:

```c++
ros::Time::init();
ros::Time start = ros::Time::now();
```

转化为ms/s

```c++
// 转为ms
double t_ms = t.toNSec();
// 转为s
double t_s = t.toSec();
```

时间间隔:

```c++
ros::Duration + ros::Duration = ros::Duration
ros::Duration - ros::Duration = ros::Duration
ros::Time + ros::Duration = ros::Time
ros::Time - ros::Time = ros::Duration
```

sleep:

```c++
// 使用方法：
ros::Duration(2.5).sleep(); 
```

ros::rate循环

```c++
// 10Hz
ros::Rate r(10)；
while(ros::ok())
{
	...
	r.sleep();
}
```

