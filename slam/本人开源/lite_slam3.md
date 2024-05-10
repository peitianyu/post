---
title: lite_slam - ScanContext
date: 2022-9-5 12:30:12
categories:
  - slam
  - lite_slam
tags:
  - lite_slam
---

# 参考网址:

[peitianyu/lite_slam (github.com)](https://github.com/peitianyu/lite_slam)

[scan_context | 某飞行员的随笔 (peitianyu.github.io)](https://peitianyu.github.io/blog/2022/03/06/scan_context/)

# ScanContext

## 代码流程

```
- 构建KeyFrame(id, pose, key, scan_context)
	- 输入(id, pose, scan)MakeScanContext构造scan_context
	- 这里key使用scan_context在ring方向上pca构造
- AddKeyFrame添加关键帧用于之后的匹配
- DetectLoopClosure()寻找回环id并输出回环坐标

- 关键:
	* scan_context构建
	* key构建
	* nanoflann构建kdtree并搜索key
	* AlignScanContext()对齐scan_context获得先验坐标
```

# 原理简述

```
1. 将距离分成60份,角度分成60份,组成60*60矩阵,然后对距离pca化得到60维向量
2. 通过kdtree搜索key找到回环
3. 通过对齐60*60矩阵找到粗匹配,距离偏差与角度偏差,得到d_pose
4. 在loop_pose基础变换得到粗匹配值
```

## 代码体现

```c++
// AddKeyFrame()
void ScanContext::AddKeyFrame(const size_t &id, const Eigen::Vector3f& curr_pose, const std::vector<Eigen::Vector2f> &scan)
{
    Eigen::MatrixXf scan_context = MakeScanContext(scan);
    KeyFrame key_frame = KeyFrame{id, curr_pose, scan_context};
    m_key_frames->push_back(key_frame);
}

// 构建pca_key
Eigen::VectorXf CalculatePcaForKeyResult()
{
    Eigen::MatrixXf tmp_scan_context = scan_context;
    tmp_scan_context.colwise() -= tmp_scan_context.rowwise().mean();
    Eigen::MatrixXf cov = tmp_scan_context.transpose() * tmp_scan_context * (1 / tmp_scan_context.rows() - 1);
    Eigen::SelfAdjointEigenSolver<Eigen::MatrixXf> eigen_solver(cov);

    return eigen_solver.eigenvectors().rightCols(1);
}

// 回环流程
const int ScanContext::DetectLoopClosure(const std::vector<Eigen::Vector2f> &scan, Eigen::Vector3f &loop_pose)
{
    Eigen::MatrixXf scan_context = MakeScanContext(scan);
    KeyFrame key_frame = KeyFrame{std::numeric_limits<size_t>::max(), Eigen::Vector3f::Zero(), scan_context};

    if(m_key_frames->size() < m_params.num_exclude_recent + 1){ // 排除最接近的一定数量特征匹配
        return -1;
    }

    std::vector<size_t> indexes(m_params.leaf_max_size);
    FindNearestNeighbor(key_frame, *m_key_frames, indexes); // nanoflann搜索

    float min_dist = std::numeric_limits<float>::max(); // 求最近的一个
    size_t nearest_id = 0;
    for(size_t &index: indexes){
        float dist = (m_key_frames->key_frames[index].key_pose.head(2) - key_frame.key_pose.head(2)).norm();
        if(dist < min_dist){
            min_dist = dist; nearest_id = index;
        }
    }
    
    if(min_dist < m_params.min_dist){ 
        Eigen::Vector3f delta_pose = AlignScanContext(scan_context, m_key_frames->key_frames[nearest_id].scan_context);
        loop_pose = TransformAdd(m_key_frames->key_frames[nearest_id].key_pose, delta_pose); //通过对齐输出回环先验坐标
        NormalizePose(loop_pose);
        return m_key_frames->key_frames[nearest_id].id;
    }

    return -1;
}

// 构建scan_context
Eigen::MatrixXf ScanContext::MakeScanContext(const std::vector<Eigen::Vector2f> &scan)
{
    Eigen::MatrixXf scan_context(m_params.num_ring, m_params.num_sector);
    scan_context.setZero();

    for(auto point: scan)
    {
        float angle = std::atan2(point(0), point(1)) / M_PI * 180.0f + 180.0f;
        float dist = std::hypot(point(0), point(1));

        if(dist >= m_params.min_range && dist <= m_params.max_range){
            int ring = std::max(std::min(m_params.num_ring - 1, static_cast<int>(round((dist / m_params.max_range) * m_params.num_ring))), 0);
            int sector = std::max(std::min(m_params.num_sector - 1, static_cast<int>(round((angle / 360.0f) * m_params.num_sector))), 0);

            scan_context(ring, sector) += 1;
        } 
    }
    return scan_context;
}

// 对齐scan_context用于输出先验坐标
Eigen::Vector3f ScanContext::AlignScanContext(const Eigen::MatrixXf &curr_scan_context, const Eigen::MatrixXf &loop_scan_context)
{
    Eigen::MatrixXf tmp_scan_context = loop_scan_context;
    float sum = std::numeric_limits<float>::max();
    int d_sector = 0;
    for(int i = 0; i < m_params.num_sector; i++) // 对列进行查找最接近的,求解偏差角度
    {
        tmp_scan_context.setZero();
        tmp_scan_context.rightCols(i) = loop_scan_context.leftCols(i);
        tmp_scan_context.leftCols(m_params.num_sector - i) = loop_scan_context.rightCols(m_params.num_sector - i);
        
        float d_sum = 0;
        for(int j = 0; j < m_params.num_sector; j++){
            d_sum += fabs(tmp_scan_context.col(j).sum() -  curr_scan_context.col(j).sum());
        }

        if( sum > d_sum){
            sum = d_sum; d_sector = i;
        }
    }

    sum = std::numeric_limits<float>::max();
    int d_ring = 0;
    for(int i = 0; i < m_params.num_ring; i++)// 对行进行查找最接近的,求解偏差距离
    {
        tmp_scan_context.setZero();
        tmp_scan_context.topRows(i) = loop_scan_context.bottomRows(i);
        tmp_scan_context.bottomRows(m_params.num_ring - i) = loop_scan_context.topRows(m_params.num_ring - i);
        
        float d_sum = 0;
        for(int j = 0; j < m_params.num_ring; j++){
            d_sum += fabs(tmp_scan_context.row(j).sum() -  curr_scan_context.row(j).sum());
        }

        if( sum > d_sum){
            sum = d_sum; d_ring = i;
        }
    }
    
    int tmp_d_ring = m_params.num_ring - d_ring;
    if(tmp_d_ring > m_params.num_ring / 2) tmp_d_ring - m_params.num_ring; // 求解距离偏差id

    
    float angle = static_cast<float>((d_sector + 0.5) * 360 / m_params.num_sector) / 180.0f * M_PI; // 求解预测偏差角度
    float min_dist = static_cast<float>(tmp_d_ring + 0.5) * m_params.max_range / m_params.num_ring; // 求解预测偏差距离
    if(angle > M_PI) angle -= 2 * M_PI;
    
    return Eigen::Vector3f(min_dist * cos(angle), min_dist * sin(angle), angle); // 输出d_pose
}
```

