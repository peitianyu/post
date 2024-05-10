---
title: grid_map技术分享
date: 2024-05-10 12:30:12
categories:
  - slam
  - 理论学习
tags:
  - slam
---

---
marp: true
---

# 概率栅格地图技术分享

## 地图分类及应用场景

## 为什么用栅格地图

## 栅格地图构建

## 结合代码理解

## 栅格地图优化

---

## 地图分类及应用场景

移动机器人常见的地图有三种：尺度地图、拓扑地图、语义地图

**尺度地图**：具有真实的物理尺寸，如**栅格地图、特征地图、点云地图**；常用于地图构建、定位、SLAM、小规模路径规划。

**拓扑地图**：不具备真实的物理尺寸，只表示不同地点的连通关系和距离，如铁路网，常用于超大规模的机器人路径规划。

**语义地图**：加标签的尺度地图，公认SLAM的未来--SLAM和深度学习的结合，常用于人机交互。

![](../imgs/%E5%9C%B0%E5%9B%BE%E5%88%86%E7%B1%BB.jpg)

---

其中对尺度地图进行补充说明，如下图所示

![](../imgs/%E5%B0%BA%E5%BA%A6%E5%9C%B0%E5%9B%BE.jpg)

---

## 为什么用栅格地图

- 优点

  - 简单直观: 非常符合人对于环境的认知

  - 算法适用性: 适用于多种传感器数据融合, 同样方便定位后端的处理(规划, 避障等), 有很强的流通性

  - 可扩展性: 可以灵活调整地图的分辨率, 从而适应不同的应用场景

  - 方便slam计算

    - 栅格地图作为先验信息输入系统对于配准也拥有激励作用

    - 栅格地图, 本身会自带滤波属性, 对于墙面特征等环境拥有一定的鲁棒性

- 缺点

  - 精度问题:由于网格的粒度问题, Grid Map可能无法准确表达环境的细节

  - 内存问题:在大规模环境下, Grid Map的数据量大, 导致存储和计算成本增加

---

## 栅格地图构建

**三个要素**: 当前传感器世界坐标 + 传感器数据 + 更新策略

**更新策略** = 更新单个栅格算法 + 更新地图算法(BrasenHam)

### 更新单个栅格算法(二分类问题)

- hit就设置为占用

- 根据hit更新栅格内的概率

  - 一般我们假设传感器误差符合高斯分布 -> 对数几率更新 (log(odd) = log(occ / free))

    - 更新的快慢取决于传感器误差, 越精准的传感器, 我们可以更新的越快

  - 当然也可以建立我们自己认为可信的模型

---

![对数几率1](../imgs/%E5%AF%B9%E6%95%B0%E5%87%A0%E7%8E%871.png "对数几率1")

---

![对数几率2](../imgs/%E5%AF%B9%E6%95%B0%E5%87%A0%E7%8E%872.png "对数几率2")

--- 

### 更新地图算法(BrasenHam)

![brasenham算法](../imgs/brasenham.png "brasenham算法")

---

## 结合代码理解

```cpp
void GridMap::UpdateByScan(Pose2D key_pose, const std::vector<Point2D>& point_cloud)
{
    Index2D begin_point = ConvertToMapIndex(Index2D(key_pose.m_pos, m_resolution));

    for(const auto& point : point_cloud)
    {
        Index2D end_point = ConvertToMapIndex(Index2D(key_pose.TransformAdd(point), m_resolution));
        UpdateMapLimit(end_point);

        if(!(begin_point == end_point))
            InverseModel(begin_point, end_point);
    }
}

void GridMap::SetCellOccupied(const Index2D& cell_index){
    if(!IsInMapRange(cell_index))
        return;

    constexpr float log_odds_p_occ = 0.9;
    m_data(cell_index.x, cell_index.y) += log_odds_p_occ;
}

void GridMap::SetCellFree(const Index2D& cell_index){
    if(!IsInMapRange(cell_index))
        return;

    constexpr float log_odds_p_free = 0.2;
    m_data(cell_index.x, cell_index.y) -= log_odds_p_free;;
}
```

---

## 栅格地图优化

```

1. 精度问题与内存问题
    我们要求精度时, 势必栅格会比较小, 这样内存就会比较大; 反之亦然
        - 1. 选择合适的地图大小与分辨率
        - 2. 使用更加符合环境的更新策略与更新模型
        - 3. 使用更加高效的数据结构等, 用于降低内存消耗
            - 存储时, 用更低的精度存储
            - 采用稀疏图的方式
            - 采用指针的方式
2. 内存较大时, 如何更新地图
    对外输出时, 由于地图较大, 每次更新整张地图就会变得很消耗资源
        - 1. 使用增量更新策略
        - 2. 将地图变小, 比如规划避障时, 实际上只需要占用与空闲信息
3. 美化角度
    我们使用slam建图时, 会出现地图歪斜与锯齿地图等情况
        - 1. 初始化时, 对地图进行修正
        - 2. 锯齿地图, 如果是显示的话可能需要后期对其做ps操作
4. 多机融合, 多层地图, 自适应地图扩展, 多传感器融合等
```
