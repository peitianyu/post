---
title: scan_context
date: 2022-3-6 12:30:12
categories:
  - slam
  - 理论学习
tags:
  - slam
---

# 参考网址:

 [(112条消息) Scan Context 学习记录_春至冬去-CSDN博客_scancontext](https://blog.csdn.net/weixin_46181372/article/details/116130105) 

 [Lidar定位：Scan Context - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/359523177) 

 [irapkaist/scancontext: Global LiDAR descriptor for place recognition and long-term localization ](https://github.com/irapkaist/scancontext) 

 [原创投稿 | ScanContext 论文详解 - 用途：Lidar SLAM 回环检测、空间描述符 - 哔哩哔哩 (bilibili.com)](https://www.bilibili.com/read/cv10348019) 

 [(112条消息) 激光闭环检测Scancontext_weixin_35536487的博客-CSDN博客_scancontext](https://blog.csdn.net/weixin_35536487/article/details/113545839) 

 [Slam-Project-Of-MyOwn/src at master · softdream/Slam-Project-Of-MyOwn (github.com)](https://github.com/softdream/Slam-Project-Of-MyOwn/tree/master/src) 

# 流程

![1646575561_1_.png](https://s2.loli.net/2022/03/06/L8eBVCXyU4ZGT15.png)

- **给定一帧点云，划分成20个环，每个环分成60等份，一共1200个格子**

- **每个格子存里面点的最大高度值（z值），这样一帧点云就用一个二维图像表示了，想象成一个带高度的俯视图，或者地形图，记为scan context**

- **scan context进一步计算列的均值，得到一个1x60的向量，记为sector key；计算行的均值，得到一个20x1的向量，记为ring key**

- **用sector key构造kd-tree，并且执行knn搜索**

- **对于候选匹配scan context，首先要左右循环偏移一下，对齐，实际会用sector key去对齐，得到一个偏移量**

- **对候选匹配scan context，施加偏移量，然后作比较**

# 代码

## make scanContext

```c
// 一帧点云生成scan context
MatrixXd SCManager::makeScancontext( pcl::PointCloud<SCPointType> & _scan_down )
{
    // 激光点数量
    int num_pts_scan_down = _scan_down.points.size();

    const int NO_POINT = -1000;
    // 这里矩阵的记法跟paper的示意图是一致的
    MatrixXd desc = NO_POINT * MatrixXd::Ones(PC_NUM_RING, PC_NUM_SECTOR);

    SCPointType pt;
    float azim_angle, azim_range; // wihtin 2d plane
    int ring_idx, sctor_idx;
    // 遍历激光点
    for (int pt_idx = 0; pt_idx < num_pts_scan_down; pt_idx++)
    {
        pt.x = _scan_down.points[pt_idx].x; 
        pt.y = _scan_down.points[pt_idx].y;
        // 让高度大于0，所有点的高度都加2，不影响匹配结果
        pt.z = _scan_down.points[pt_idx].z + LIDAR_HEIGHT; // naive adding is ok (all points should be > 0).

        // 距离
        azim_range = sqrt(pt.x * pt.x + pt.y * pt.y);
        // 角度，0~360°
        azim_angle = xy2theta(pt.x, pt.y);

        // 距离超过80米的点不考虑了
        if( azim_range > PC_MAX_RADIUS )
            continue;

        // 计算这个点落在哪个bin里面，下标从1开始数
        ring_idx = std::max( std::min( PC_NUM_RING, int(ceil( (azim_range / PC_MAX_RADIUS) * PC_NUM_RING )) ), 1 );
        sctor_idx = std::max( std::min( PC_NUM_SECTOR, int(ceil( (azim_angle / 360.0) * PC_NUM_SECTOR )) ), 1 );

        // 用z值，也就是高度来更新这个格子，存最大的高度；
        if ( desc(ring_idx-1, sctor_idx-1) < pt.z ) // -1 means cpp starts from 0
            desc(ring_idx-1, sctor_idx-1) = pt.z; // update for taking maximum value at that bin
    }
    
    // reset no points to zero (for cosine dist later)
    for ( int row_idx = 0; row_idx < desc.rows(); row_idx++ )
        for ( int col_idx = 0; col_idx < desc.cols(); col_idx++ )
            if( desc(row_idx, col_idx) == NO_POINT )
                desc(row_idx, col_idx) = 0;

    return desc;
} 
```

##  make Ringkey

```c
// 由scan context计算ring key，Nx1
MatrixXd SCManager::makeRingkeyFromScancontext( Eigen::MatrixXd &_desc )
{
    // 每行计算一个均值
    Eigen::MatrixXd invariant_key(_desc.rows(), 1);
    for ( int row_idx = 0; row_idx < _desc.rows(); row_idx++ )
    {
        Eigen::MatrixXd curr_row = _desc.row(row_idx);
        invariant_key(row_idx, 0) = curr_row.mean();
    }

    return invariant_key;
} 
```

##  make Sectorkey 

```c
// 由scan context计算sector key，1xM
MatrixXd SCManager::makeSectorkeyFromScancontext( Eigen::MatrixXd &_desc )
{
    // 每列计算一个均值
    Eigen::MatrixXd variant_key(1, _desc.cols());
    for ( int col_idx = 0; col_idx < _desc.cols(); col_idx++ )
    {
        Eigen::MatrixXd curr_col = _desc.col(col_idx);
        variant_key(0, col_idx) = curr_col.mean();
    }

    return variant_key;
} 
```

## 调整偏移,获得偏移量

按列均值,目的是,便于通过调整偏移量来实现对scanContext进行比较

```c
// _vkey1、_vkey2是两个sector key
// 对_vkey2做循环偏移，计算与_vkey1最佳匹配时的偏移量
int SCManager::fastAlignUsingVkey( MatrixXd & _vkey1, MatrixXd & _vkey2)
{
    int argmin_vkey_shift = 0;
    double min_veky_diff_norm = 10000000;
    for ( int shift_idx = 0; shift_idx < _vkey1.cols(); shift_idx++ )
    {
        // 矩阵的列，循环右移shift个单位
        MatrixXd vkey2_shifted = circshift(_vkey2, shift_idx);
        // 直接相减，sector key是1xN的矩阵
        MatrixXd vkey_diff = _vkey1 - vkey2_shifted;

        // 范数
        double cur_diff_norm = vkey_diff.norm();
        // 记录距离最小时对应的循环偏移量
        if( cur_diff_norm < min_veky_diff_norm )
        {
            argmin_vkey_shift = shift_idx;
            min_veky_diff_norm = cur_diff_norm;
        }
    }

    return argmin_vkey_shift;
} 
```

## 闭环检测

```c
// 接口二，执行闭环检测
std::pair<int, float> SCManager::detectLoopClosureID ( void )；

/**
 * 计算两个scan context之间的距离
*/
std::pair<double, int> SCManager::distanceBtnScanContext( MatrixXd &_sc1, MatrixXd &_sc2 )
{
    // 1. fast align using variant key (not in original IROS18)
    // 计算sector Key,也就是sector最大高度均值组成的数组，1xN
    MatrixXd vkey_sc1 = makeSectorkeyFromScancontext( _sc1 );
    MatrixXd vkey_sc2 = makeSectorkeyFromScancontext( _sc2 );
    // 这里将_vkey2循环右移，然后跟_vkey1作比较，找到一个最相似（二者做差最小）的时候，记下循环右移的量
    int argmin_vkey_shift = fastAlignUsingVkey( vkey_sc1, vkey_sc2 );

    // 上面用sector key匹配，找到一个初始的偏移量，但肯定不是准确的，再在这个偏移量左右扩展一下搜索空间
    const int SEARCH_RADIUS = round( 0.5 * SEARCH_RATIO * _sc1.cols() ); // a half of search range 
    std::vector<int> shift_idx_search_space { argmin_vkey_shift };
    for ( int ii = 1; ii < SEARCH_RADIUS + 1; ii++ )
    {
        shift_idx_search_space.push_back( (argmin_vkey_shift + ii + _sc1.cols()) % _sc1.cols() );
        shift_idx_search_space.push_back( (argmin_vkey_shift - ii + _sc1.cols()) % _sc1.cols() );
    }
    std::sort(shift_idx_search_space.begin(), shift_idx_search_space.end());

    // 2. fast columnwise diff 
    int argmin_shift = 0;
    double min_sc_dist = 10000000;
    for ( int num_shift: shift_idx_search_space )
    {
        // 把scan context循环右移一下
        MatrixXd sc2_shifted = circshift(_sc2, num_shift);
        // 计算两个scan context之间的距离
        double cur_sc_dist = distDirectSC( _sc1, sc2_shifted );
        if( cur_sc_dist < min_sc_dist )
        {
            argmin_shift = num_shift;
            min_sc_dist = cur_sc_dist;
        }
    }
    // 最小scan context距离，偏移量
    return make_pair(min_sc_dist, argmin_shift);
}
```

# 注意

scanContext方法只是用于简单的位姿对比,对于精确位姿还需要最后使用更加精确的手段进行定位,比如icp,scantomap,图优化都可以.

# 简单介绍回环检测与图优化结合方式

- 首先通过sccan_to_map检测是否超过一定距离与角度,若是,设为关键帧
- 将关键帧保存数组
- 生成scanContext并加入图优化顶点
- 检测是否有回环,若有,则根据记录下来的顶点,scan,运动坐标进行图优化,更新坐标
- 重新建图:先删除之前所有建图信息, 根据估计的关键姿态重建地图 