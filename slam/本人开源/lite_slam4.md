---
title: lite_slam - PoseGraphOptimize
date: 2022-9-5 12:30:12
categories:
  - slam
  - lite_slam
tags:
  - lite_slam
---

# 参考网址:

[peitianyu/lite_slam (github.com)](https://github.com/peitianyu/lite_slam)

[versatran01/graphslam: graphslam (github.com)](https://github.com/versatran01/graphslam)

# 参考文献

```
A Tutorial on Graph-Based SLAM
```

# GraphOptimize

```
- 使用了Cholesky分解加速
- 优化方向: 
	- 由于使用的是scan_points具有角度不变性,因此可以将3 * num的H,降低为2 * num,降低计算量
	- 加入残差判断
```

# 流程

```c++
void AddVertex(const uint &id, const Eigen::Vector3f &pose); // 添加顶点 std::map<uint, Eigen::Vector3f> vertex

void AddEdge(const uint &from_id, const uint &to_id, const Eigen::Vector3f &measurement, const Eigen::Matrix3f &info_matrix);// 添加边约束 

void Optimize(); // 通过高斯牛顿优化

const std::vector<Eigen::Vector3f> &GetOptimizedPoses() const; // 获得优化后坐标

const std::map<uint, Eigen::Vector3f> &GetOptimizedVertexes() const; // 获得优化后顶点
```

# 优化

```c++
void GraphOptimize::EstimateOnce()
{
    Eigen::MatrixXf H = Eigen::MatrixXf::Zero(m_vertex.size() * 3, m_vertex.size() * 3); // 之后优化这部分
    Eigen::VectorXf b = Eigen::VectorXf::Zero(m_vertex.size() * 3);
    GetHessianDerived(H, b); 

    Eigen::SimplicialLDLT<Eigen::SparseMatrix<float>> solver;
    solver.compute(H.sparseView());
    if(solver.info() != Eigen::Success)
        return;

    Eigen::VectorXf d_x = Eigen::VectorXf::Zero(m_vertex.size() * 3);
    d_x = solver.solve(b);
    if(solver.info() != Eigen::Success)
        return;

    m_ret_poses.clear();
    for(auto &vertice : m_vertex)
    {
        vertice.second += d_x.segment<3>(vertice.first * 3);
        m_ret_poses.push_back(vertice.second);
    }

    NormalizePose();
}
```

