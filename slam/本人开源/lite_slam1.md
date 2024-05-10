---
title: lite_slam - grid_map模块
date: 2022-9-5 12:30:12
categories:
  - slam
  - lite_slam
tags:
  - lite_slam
---

# 参考网址:

[peitianyu/lite_slam (github.com)](https://github.com/peitianyu/lite_slam)

# GridMap框架

```
1. GridMapBase作为基类派生GridMapDownSamlple,GridMapUtils
2. GridManage用于读取,显示,保存GridMap
3. GridSubMap实现子图功能
```

# GridMapBase

### 参考网址

[如何理解概率栅格地图（Probability Grid Map）& 概率更新公式 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/339812617)

### 配置文件

```c++
// 关于配置参数
struct Params
{
    float resolution = 0.05; // meter / pixel
    Eigen::Vector2i size = Eigen::Vector2i(2000, 2000); //地图尺寸
    Eigen::Vector2f origin = Eigen::Vector2f(-60.0f, -60.0f); // 右上角为(0, 0)的建图原点
    float log_odds_p_occ = 0.6f; // 更新地图格式为log(odds), 遇到障碍物会增加此值,反之减小此值,具体接下来代码与文献有讲解
    float log_odds_p_free = 0.4f;
};
```

### 对于概率图理解

```
Q: 为何使用log-odds表示概率图(对数几率)
A: 由于激光打在障碍物上的概率符合正太分布,因此可以写成标准正太分布形式,
   又由于exp()相乘计算复杂,所以将其保存为log-odds形式进行加法运算,加快计算速度.
   当然还有一个考虑,在log-odds在0附近变化剧烈,这样可以尽快将占用与空闲分开
```

### 代码体现

```c++
// 保存格式为MatrixXf m_data
void GridMapBase::SetCellOccupied(const Eigen::Vector2i& cell_index)
{
    if(IsValid(cell_index))
        m_data(cell_index(0), cell_index(1)) += m_params.log_odds_p_occ; // 只是对对应位置的log-odds进行加减操作,减小了计算量
}

void GridMapBase::SetCellFree(const Eigen::Vector2i& cell_index)
{
    if(IsValid(cell_index))
        m_data(cell_index(0), cell_index(1)) -= m_params.log_odds_p_free; // 只是对对应位置的log-odds进行加减操作,减小了计算量
}

float GridMapBase::GetCellProb(const Eigen::Vector2i& cell_index) const 
{
    if(!IsValid(cell_index)) return 0.5f;

    float odds = std::exp(GetCellLogOdds(cell_index)); // log-odds2odds
    float prob = (odds / (odds + 1)); // odds2prob
    if(std::isnan(prob)) return 0;
    return prob;
}
```

### 对于map的常见工具

```c++
// 获得非0最大矩阵
Eigen::Matrix2i GridMapBase::GetMapLimit() const // top-bottom, left-right
{
    Eigen::Matrix2i map_limit = Eigen::Matrix2i::Zero();
    map_limit << m_params.size(1) , 0, m_params.size(0) , 0; 

    for(int i = 0; i < m_params.size(0); i++){
        for(int j = 0; j < m_params.size(1); j++){
            if(fabs(m_data(i,j)) > 1.0f){
                if(i < map_limit(0,0)) {map_limit(0,0) = i;}
                if(i > map_limit(0,1)) {map_limit(0,1) = i;}
                if(j < map_limit(1,0)) {map_limit(1,0) = j;}
                if(j > map_limit(1,1)) {map_limit(1,1) = j;}
            }
        }
    }
    return map_limit;
}

// 判断是否合法
bool GridMapBase::IsValid(const Eigen::Vector2i& cell_index) const
{
    return (cell_index(0) >= 0 && cell_index(0) < m_params.size(0) && cell_index(1) >= 0 && cell_index(1) < m_params.size(1));
}
```

# GridMapUtils

## 框架理解

```
1. 更新概率地图 - bresenham(好处: 只用了整形加减,降低计算量)
2. 世界坐标与地图坐标相互转换
```

## 更新概率地图

### 代码框架

```
输入: 当前robot_in_world_pose与scan_points
0. robot_in_world_pose转化为地图坐标作为起始点
1. 对于每一个scan_point转化为世界坐标作为终点
2. 使用bresenham画线
```

### 参考网址

[Bresenham画线算法 - 简书 (jianshu.com)](https://www.jianshu.com/p/d63bf63a0e28)

### 代码体现

```c++
void GridMapUtils::UpdateByScan(const Eigen::Vector3f& pose_in_world, const std::vector<Eigen::Vector2f>& scan_points)
{
	Eigen::Vector3f pose_in_map = WorldToMapFloat(pose_in_world); // 转化为地图坐标
	Eigen::Vector2i begin_point_in_map_int = Eigen::Vector2i(pose_in_map(0), pose_in_map(1));

	for (Eigen::Vector2f point : scan_points)
	{
		Eigen::Vector2f end_point_in_world = LaserPointToWorld(point, pose_in_world);

		Eigen::Vector2f end_point_in_map = WorldToMapFloat(end_point_in_world);

		Eigen::Vector2i end_point_in_map_int(static_cast<int>( ::round(end_point_in_map(0))), static_cast<int>( ::round(end_point_in_map(1)))); // 转化为世界坐标系下的地图坐标

		if(begin_point_in_map_int != end_point_in_map_int)
			InverseModel(begin_point_in_map_int, end_point_in_map_int); // bresenham画线
	}
}

void GridMapUtils::InverseModel(const Eigen::Vector2i &p0, const Eigen::Vector2i &p1)
{
	BresenhamCellOccupied(p1); // 更新占用

	BresenhamCellFree(p0, p1); // 更新空闲
}

void GridMapUtils::BrasenHam(int x0, int y0, int x1, int y1)
{
	int dx = ::abs( x1 - x0 );
	int dy = ::abs( y1 - y0 );
	bool inter_change = false;
	int e = -dx;// error
	int signX = x1 > x0 ? 1 : ( ( x1 < x0 ) ? -1 : 0 );
	int signY = y1 > y0 ? 1 : ( ( y1 < y0 ) ? -1 : 0 );
	if (dy > dx) {
		int temp = dx; dx = dy; dy = temp; inter_change = true;
	}

	int x = x0, y = y0;
	for (int i = 1; i <= dx; i++) { 
		m_down_map->SetCellFree(Eigen::Vector2i(x, y)); // 更新空闲

		if (!inter_change) {x += signX;}
		else {y += signY;}
		e += 2 * dy;
		if (e >= 0) {
			if (!inter_change) {y += signY;}
			else {x += signX;}
			e -= 2 * dx;
		}
	}
}
```

## 世界坐标与地图坐标相互转换

```
1. 根据对地图的定义,实现世界坐标与地图坐标转换
2. 优化: 填加根据不同地图方向转化坐标的功能
```

### 代码实现(没啥技术含量,  直接看源码)

```c++
Eigen::Vector2f LaserInScaledLaser(const Eigen::Vector2f& laser_point);

Eigen::Vector2f LaserPointToWorld(const Eigen::Vector2f& point_in_laser, const Eigen::Vector3f& pose_in_world);

Eigen::Vector3f WorldToMapFloat(const Eigen::Vector3f& pose_in_world) const; 

Eigen::Vector2f WorldToMapFloat(const Eigen::Vector2f& point_in_world) const;

Eigen::Vector3f MapToWorldFloat(const Eigen::Vector3f& pose_in_map) const;
```

# GridMapDownSamlple

## 降采样地图理解

```
0. 继承GridMapBase
1. 对上层地图长宽降采样为原来一半
2. 采用上层地图四方格log-odds中最大的作为下层地图log-odds值(可根据自己需要定义,比如采用高斯核降采样等)
```

### 代码体现

```c++
DownSampleMap::DownSampleMap(const Params& p)
:GridMapBase(p)
{}

// 若输入原始地图,则将其作为降采样地图 
DownSampleMap::DownSampleMap(const GridMapBase& grid_map)
{
    m_params = grid_map.GetParams();
    m_data.setZero();
    m_data = grid_map.GetData();
}

// 若输入降采样地图,则将其降采样 
DownSampleMap::DownSampleMap(const DownSampleMap& down_map)
{
    // 重新设置配置文件
    m_params = down_map.GetParams();
    m_params.resolution = down_map.GetResolution() * 2.0;
    m_params.size = Eigen::Vector2i(static_cast<int>(down_map.GetSize()(0) / 2.0 + 0.5), static_cast<int>(down_map.GetSize()(1) / 2.0 + 0.5));
    
    // 根据配置文件重新设置地图大小,并初始化
    m_data.resize(m_params.size(0), m_params.size(1)); 
    m_data.setConstant(0); 

    for (int row = 0; row < m_params.size(0); row++){ // 实现了四个方格中取最大值操作
    for (int col = 0; col < m_params.size(1); col++){
        for (int i = 0; i < 2; i++){
        for (int j = 0; j < 2; j++){
            m_data(row, col) = (m_data(row, col) < fabs(down_map.GetCellLogOdds(Eigen::Vector2i(2 * row + i, 2 * col + j)))) ? 
                down_map.GetCellLogOdds(Eigen::Vector2i(2 * row + i, 2 * col + j)):m_data(row, col);   
        }    
        }  
    }
    }
}
```

# GridManage

```
主要用于保存,显示,读取概率地图
```

### 代码实现(没啥技术含量,  直接看源码)

```c++
void SaveGridMap(const std::string& datafile, std::shared_ptr<GridMapBase> grid_map);

void SaveProbMap(const std::string& datafile, std::shared_ptr<GridMapBase> grid_map);

void LoadGridMap(const std::string& datafile, std::shared_ptr<GridMapBase>& grid_map);

void DisplayGridMap(std::shared_ptr<GridMapBase> grid_map, const uint& type);

void SaveMapPng(std::shared_ptr<GridMapBase> grid_map, const std::string& datafile);
```

# GridSubMap

## 总体思路(待完成)

```
1. 
struct{
	uint id;
	Eigen::Vector3f map_pose;
	std::shared_ptr<GridMapBase> gird_map;
};

2. submap回环:
	1.
	- 通过scan_context查找周围几个submap判断回环,减低计算量
	- 根据回环点优化相关submap并更新子图与key_frames
	2.
	- csm + 分支定界 
```

