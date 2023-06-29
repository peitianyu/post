---
title: icp
date: 2022-4-23 12:30:12
categories:
  - slam
  - 理论学习
tags:
  - slam
---

# 参考网址:

[(128条消息) 迭代最近点 ICP 详细推导（C++实现）_Alan Lan的博客-CSDN博客_迭代最近点算法](https://blog.csdn.net/A_L_A_N/article/details/81329728?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.edu_weight&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.edu_weight)

[(128条消息) ICP算法的原理与实现_酷小川的博客-CSDN博客_icp算法](https://blog.csdn.net/kksc1099054857/article/details/80280964?ops_request_misc=%7B%22request%5Fid%22%3A%22165072453016782094836291%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=165072453016782094836291&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-80280964.142^v9^control,157^v4^control&utm_term=icp算法&spm=1018.2226.3001.4187)

[(128条消息) 基于kdtree与svd的迭代最近点ICP算法的matlab实现_头顶日渐发凉的博客-CSDN博客](https://blog.csdn.net/jinxianlovelss/article/details/107418509)

[zjudmd1015/icp (github.com)](https://github.com/zjudmd1015/icp)

[ClayFlannigan/icp: iterative closest point (github.com)](https://github.com/ClayFlannigan/icp)

[Gregjksmith/Iterative-Closest-Point](https://github.com/Gregjksmith/Iterative-Closest-Point)

# 步骤

```txt
求质心,求t
去中心化
计算SVD,求R
迭代
```

# 代码

```cpp
#include <fstream>
#include <sstream>
#include <iostream>
#include <vector>
#include <Eigen/Eigen>
 
void ICP(const std::vector<Eigen::Vector3f>& p1, const std::vector<Eigen::Vector3f>& p2,
         Eigen::Matrix3f& R_12, Eigen::Vector3f& t_12) {
    assert(p1.size() == p2.size());
 
    // center of mass
    size_t N = p1.size();
    Eigen::Vector3f p1_center, p2_center;
    for (int i = 0; i < N; ++i) {
        p1_center += p1.at(i);
        p2_center += p2.at(i);
    }
    p1_center /= N;
    p2_center /= N;
 
    // remove the center
    std::vector<Eigen::Vector3f> q1(N), q2(N);
    for (int i = 0; i < N; ++i) {
        q1[i] = p1.at(i) - p1_center;
        q2[i] = p2.at(i) - p2_center;
    }
 
    // compute q2*q1^T
    Eigen::Matrix3f H = Eigen::Matrix3f::Zero();
    for (int i = 0; i < N; ++i) {
        H += q2.at(i) * q1.at(i).transpose();
    }
 
    // SVD on H
    Eigen::JacobiSVD<Eigen::Matrix3f> svd(H, Eigen::ComputeFullU | Eigen::ComputeFullV);
    Eigen::Matrix3f U = svd.matrixU();
    Eigen::Matrix3f V = svd.matrixV();
 
    R_12 = V * U.transpose();
    t_12 = p1_center - R_12 * p2_center;
}
 
int main(int argc, char** argv) {
    Eigen::AngleAxisf angle_axis(M_PI / 3, Eigen::Vector3f(0, 0, 1));
    Eigen::Matrix3f R_12_ = angle_axis.matrix();
    Eigen::Vector3f t_12_(1, 2, 3);
    std::cout << "except R_12:\n" << R_12_ << std::endl;
    std::cout << "except t_12:\n" << t_12_.transpose() << std::endl;
 
    std::vector<Eigen::Vector3f> p1, p2;
    Eigen::Vector3f point;
 
    std::ifstream fin("/tmp/bunny.txt");
    std::string line;
    while (getline(fin, line)) {
        std::stringstream ss(line);
        ss >> point.x();
        ss >> point.y();
        ss >> point.z();
        p2.push_back(point);
        p1.push_back(R_12_ * point + t_12_);
    }
    fin.close();
 
    Eigen::Matrix3f R_12;
    Eigen::Vector3f t_12;
    ICP(p1, p2, R_12, t_12);
 
    std::cout << "result R_12:\n" << R_12_ << std::endl;
    std::cout << "result t_12:\n" << t_12.transpose() << std::endl;
 
    return 0;
}
```