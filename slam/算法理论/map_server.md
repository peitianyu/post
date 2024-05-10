---
title: map_server
date: 2021-11-5 12:30:12
categories:
  - slam
  - 理论学习
tags:
  - ros
---

# 参考网址:

[(60条消息) map server 功能和作用_天才樱木-CSDN博客_map_server](https://blog.csdn.net/datase/article/details/82495600)

# 保存

```shell
rosrun map_server map_saver -f mymap
```

我们会得到两个文件,`mymap.pgm`,`mymap.yaml`

`mymap.pgm`为0到1的灰度图

`mymap.yaml`为配置文件

```shell
# 文件名
image: testmap.png
# 分辨率
resolution: 0.1
# 初始点
origin: [0.0, 0.0, 0.0]
# 占用阈值
occupied_thresh: 0.65
# 为自由阈值
free_thresh: 0.196
# 是否应该颠倒白/黑 自由/被占用的语义(阈值的解释不受影响)
negate: 0
```

# 发布节点

```shell
rosrun map_server map_server mymap.yaml
```

## 主题

**map_metadata** ([nav_msgs/MapMetaData](http://docs.ros.org/api/nav_msgs/html/msg/MapMetaData.html))

```shell
# 这包含了关于占领网格特征的基本信息

# 地图加载的时间
time map_load_time
# 地图的分辨率 [m/cell]
float32 resolution
# Map width [cells]
uint32 width
# Map height [cells]
uint32 height
# 地图的原点[m, m, rad]。这是地图中单元格(0,0)的真实姿态。
geometry_msgs/Pose origin
```

**map** ([nav_msgs/OccupancyGrid](http://docs.ros.org/api/nav_msgs/html/msg/OccupancyGrid.html))

```shell
# 这代表了一个二维网格地图，其中每个单元格代表占用概率。

Header header 
# uint32 seq
# time stamp
# string frame_id

#地图的元数据
MapMetaData info

# 映射数据，按行主顺序，从(0,0)开始。占用概率在[0,100]范围内。未知是1。
int8[] data
```

**static_map** ([nav_msgs/GetMap](http://docs.ros.org/api/nav_msgs/html/srv/GetMap.html))

```shell
# Get the map as a nav_msgs/OccupancyGrid
---
nav_msgs/OccupancyGrid map
```

