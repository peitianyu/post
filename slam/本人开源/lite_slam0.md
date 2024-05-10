---
title: lite_slam - 整体框架简介
date: 2022-9-5 12:30:12
categories:
  - slam
  - lite_slam
tags:
  - lite_slam
---

# 参考网址:

[peitianyu/lite_slam (github.com)](https://github.com/peitianyu/lite_slam)

# 环境

```
- Ubuntu18
- Eigen
- opencv(仅用于显示)
```

# 框架

## 建图框架

```
- grid_map作为概率地图
	- map_manage用于显示,保存,读取grid_map
	- down_sample_map用于降采样概率图
- scantomap作为匹配
	- 为加快速度,采用多层地图,即down_sample_map
	- 为加快速度,采用scan_points体素滤波
	- 为加快速度,当迭代到一定程度时会提前退出迭代
- scan_context作为回环检测模块
	- 通过开源nanoflann库进行kdtree搜索得到先验pose
	- 然后通过scantomap进行精确定位得到回环pose
- graph_optimize图优化模块,图优化完后,更新关键帧与地图
```

## 代码实现

```c++
void Run(const Pose2d& prior_pose = Pose2d()) override
{
    static uint is_first = 0;
    if(is_first++ < 20){
        Init(prior_pose); // 建图初始化
        return;
    }
	
    Location(); // 定位
    
    UpdateKeyFrame(); // 更新keyframe与更新地图

    // 显示pose与显示grid_map
    static uint id = 0;
    std::cout<<id++<<" m_estimate_pose: "<<m_estimate_pose.transpose()<<std::endl;
    m_map_manager->DisplayGridMap(m_scan_matcher->GetGridMap(), 1);
}
```

## 纯定位框架

```
- 保存scan_context相关的KeyFrame到txt文本中
- 保存原始grid_map用于纯定位
- scan_context用于重定位,由于场景时agv,所以路线一般固定,可以直接进行kdtree搜索得到先验pose,后scantomap得到精确pose
- scantomap用于纯定位
- 根据不同需求更新地图
```

## 代码实现

```c++
void Run(const Pose2d& prior_pose = Pose2d()) override
{
	static uint is_first = 0;
	if(is_first++ == 20) {Init(prior_pose);} // 纯定位初始化
		
	static bool is_loop_closure = false;
	if(!is_loop_closure){
		if(LoopClosure() != -1) {is_loop_closure = true;} // 重定位,若重定位成功
	}else{   
		Location(); // 定位   
		UpdateKeyFrame(); // 更新keyframe与更新地图

        // 显示
		static uint id = 0;
		std::cout<<id++<<" m_estimate_pose: "<<m_estimate_pose.transpose()<<std::endl;
		m_map_manager->DisplayGridMap(m_scan_matcher->GetGridMap(), 1);
	}
}
```

