---
title: ros_rosbag
date: 2021-11-2 12:30:12
categories:
  - slam
  - ros
tags:
  - ros
---
## 参考网址:

[(59条消息) Rosbag详细操作_Sun的博客-CSDN博客](https://blog.csdn.net/qiqiqiqi0000/article/details/114767249?ops_request_misc=%7B%22request%5Fid%22%3A%22163582490616780261970410%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163582490616780261970410&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-2-114767249.pc_search_ecpm_flag&utm_term=rosbag&spm=1018.2226.3001.4187)

[rosbag --clock_newbeixue的博客-CSDN博客](https://blog.csdn.net/newbeixue/article/details/110082489?ops_request_misc=%7B%22request%5Fid%22%3A%22163582694116780271519523%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163582694116780271519523&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-110082489.pc_search_ecpm_flag&utm_term=rosbag+play+--clock&spm=1018.2226.3001.4187)

## Rosbag基础使用

### 保存

```shell
# 保存所有topic
rosbag record -a
# 只记录某些感兴趣的 topic
rosbag record /topic_name1 /topic_name2 /topic_name3
# 指定生成数据包的名字，则用-O /-o 参数
rosbag record -O filename.bag /topic_name1
# launch 文件中使用 rosbag record 命令
<node pkg="rosbag" type="record" name="bag_record" args="/topic1 /topic2"/> 
```

### 显示信息

```shell
rosbag info filename.bag
rosbag info -y filename.bag
# 示例
xiaohu@xiaohu:~/bagfiles$  rosbag info -y 1.bag
path: 1.bag
version: 2.0
duration: 3.295966
start: 1566653873.351150
end: 1566653876.647117
size: 8103
messages: 20
indexed: True
compression: none
types:
    - type: geometry_msgs/Twist
      md5: 9f195f881246fdfa2798d1d3eebca84a
topics:
    - topic: /turtle1/cmd_vel
      type: geometry_msgs/Twist
      messages: 20

```

## rosbag play

```shell
# 简单播放
rosbag play <bagfile>
# -r 2 表示2倍速播放
rosbag play -r 2 <bagfile>
# 循环播放
rosbag play -l  <bagfile>  # -l== --loop
# 只播放一部分topic
rosbag play <bagfile> --topic /topic1
# 开始播放立刻暂停，按空格继续
rosbag play --pause record.bag

```

## rosbag --clock

```shell
rosbag play --clock recorded1.bag
# -k， --keep-alive
rosbag play -k  --clock recorded1.bag
# -r FACTOR, --rate=FACTOR
# 将发布频率降低为原来的10%，留给滤波节点足够的时间进行处理。
rosbag play -r 0.1 --clock recorded1.bag
```

## 常用命令

```shell
rosparam set /use_sim_time true
rosbag play --pause  --clock -k scan_odom.bag --topic /topic1
```

