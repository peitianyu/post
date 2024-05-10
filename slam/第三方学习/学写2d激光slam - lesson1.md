---
title: 学写2d激光slam-lesson1
date: 2022-2-11 12:30:12
categories:
  - slam
  - 第三方学习
tags:
  - ros
  - slam
---

# 参考网址:

  [xiangli0608/Creating-2D-laser-slam-from-scratch: 从零开始创建二维激光SLAM (github.com)](https://github.com/xiangli0608/Creating-2D-laser-slam-from-scratch) 

 [李想的博客_李太白lx_CSDN博客-从零开始搭二维激光SLAM,激光SLAM,cartographer领域博主](https://blog.csdn.net/tiancailx?spm=1011.2124.3001.5113) 

# lesson1

## 了解激光雷达数据

```c
# rosmsg show sensor_msgs/LaserScan

std_msgs/Header header	//　数据的消息头
  uint32 seq			//　数据的序号
  time stamp			//　数据的时间戳
  string frame_id		//　数据的坐标系
float32 angle_min		//　雷达数据的起始角度（最小角度）
float32 angle_max		//　雷达数据的终止角度（最大角度）
float32 angle_increment	//　雷达数据的角度分辨率（角度增量）
float32 time_increment	//　雷达数据每个数据点的时间间隔
float32 scan_time		//　当前帧数据与下一帧数据的时间间隔
float32 range_min		//　雷达数据的最小值
float32 range_max		//　雷达数据的最大值
float32[] ranges		//　雷达数据每个点对应的在极坐标系下的距离值
float32[] intensities	//　雷达数据每个点对应的强度值
```

## 对数据进行简单处理

```c
// 第5个点的欧式坐标为
double range = scan_msg->ranges[4];
double angle = scan_msg->angle_min + scan_msg->angle_increment * 4;
double x = range * cos(angle);
double y = range * sin(angle);

// 通过ranges中数据的个数进行雷达数据的遍历
for (int i = 0; i < scan_msg->ranges.size(); i++)
{
	printf("range[i]=%f ",scan_msg->ranges[i]);
}
```

## LIO_SLAM特征点提取

简介:主要是提取特征角点

### 剔除  **inf 或者 nan** 点

```c
// std::isfinite ：输入的值是有效值，返回true
if (!std::isfinite(scan_msg->ranges[i]))
{
// std::cout << " " << i << " " << scan_msg->ranges[i];
continue;
}
```

### 计算曲率

实际上是比较该点与前后各五个点之间差距,大于一定阈值认为是角点,也就是特侦点

```CQL
    // 计算曲率值, 通过当前点前后5个点距离值的偏差程度来代表曲率
    // 如果是球面, 则当前点周围的10个点的距离之和 减去 当前点距离的10倍 应该等于0
    for (int i = 5; i < count - 5; i++)
    {
        float diff_range = new_scan[i - 5] + new_scan[i - 4] +
                           new_scan[i - 3] + new_scan[i - 2] +
                           new_scan[i - 1] - new_scan[i] * 10 +
                           new_scan[i + 1] + new_scan[i + 2] +
                           new_scan[i + 3] + new_scan[i + 4] +
                           new_scan[i + 5];
        // diffX * diffX + diffY * diffY
        scan_curvature_[i] = diff_range * diff_range;
        scan_smoothness_[i].value = scan_curvature_[i];
        scan_smoothness_[i].index = i;
    }
```

### 特侦点提取

使用sort()对于scan_smoothness_进行排序,分成6组,从大到小每组最多选20个点

```c
// 进行角点的提取,将完整的scan分成6部分,每部分提取20个角点
    for (int j = 0; j < 6; j++){
        int start_index = (0 * (6 - j) + count * j) / 6;
        int end_index = (0 * (5 - j) + count * (j + 1)) / 6 - 1;
        // std::cout << "start_index: " << start_index << " end_index: " << end_index << std::endl;

        if (start_index >= end_index)
            continue;

        // 将这段点云按照曲率从小到大进行排序
        std::sort(scan_smoothness_.begin() + start_index,
                  scan_smoothness_.begin() + end_index, by_value());

        int largestPickedNum = 0;
        // 最后的点 的曲率最大，如果满足条件，就是角点
        for (int k = end_index; k >= start_index; k--){
            int index = scan_smoothness_[k].index;
            if (scan_smoothness_[k].value > edge_threshold_){
                // 每一段最多只取20个角点
                largestPickedNum++;
                if (largestPickedNum <= 20){
                    corner_scan.ranges[map_index[index]] = scan_msg->ranges[map_index[index]];
                }
                else{
                    break;
                }
            }
        }
    }
```

