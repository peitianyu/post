---
title: g2o
date: 2022-3-2 12:30:12
categories:
  - slam
  - 第三方学习
tags:
  - slam
---

# 参考网址:

 [slambook2/ch6 at master · gaoxiang12/slambook2 (github.com)](https://github.com/gaoxiang12/slambook2/tree/master/ch6) 

 [(111条消息) 从零开始搭二维激光SLAM --- 基于g2o的后端优化的代码实现](https://blog.csdn.net/tiancailx/article/details/121266020?spm=1001.2014.3001.5501) 

 [(111条消息) 从零开始一起学习SLAM | 掌握g2o顶点编程套路_计算机视觉life-CSDN博客](https://blog.csdn.net/electech6/article/details/88018481) 

 [Slam-Project-Of-MyOwn/slamSimulationTest.cpp at master](https://github.com/Forrest-Z/Slam-Project-Of-MyOwn/blob/master/test/slam_simulation_with_g2o/slamSimulationTest.cpp) 

 [(111条消息) SLAM从0到1之图优化g2o：从看懂代码到动手编写（长文）_3D视觉工坊-CSDN博客](https://blog.csdn.net/Yong_Qi2015/article/details/105259976?ops_request_misc=%7B%22request%5Fid%22%3A%22164620942316780264055749%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164620942316780264055749&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-105259976.pc_search_insert_es_download&utm_term=g2o&spm=1018.2226.3001.4187) 

# 简介

在图优化里,g2o使用非常普遍,需要注意一下关于( vertex)与(edge)概念与选择.实现步骤如下:

-  *创建一个线性求解器LinearSolver* 
-  *创建BlockSolver。并用上面定义的线性求解器初始化* 
-  *创建总求解器solver* 
-  *稀疏优化器（SparseOptimizer）* 
-  *定义图的顶点和边。并添加到SparseOptimizer中* 
-  *设置优化参数，开始执行优化* 

# 高翔博士

```c
#include <iostream>
#include <g2o/core/g2o_core_api.h>
#include <g2o/core/base_vertex.h>
#include <g2o/core/base_unary_edge.h>
#include <g2o/core/block_solver.h>
#include <g2o/core/optimization_algorithm_levenberg.h>
#include <g2o/core/optimization_algorithm_gauss_newton.h>
#include <g2o/core/optimization_algorithm_dogleg.h>
#include <g2o/solvers/dense/linear_solver_dense.h>
#include <Eigen/Core>
#include <opencv2/core/core.hpp>
#include <cmath>
#include <chrono>

using namespace std;

// 曲线模型的顶点，模板参数：优化变量维度和数据类型
class CurveFittingVertex : public g2o::BaseVertex<3, Eigen::Vector3d> {
public:
  EIGEN_MAKE_ALIGNED_OPERATOR_NEW

  // 重置
  virtual void setToOriginImpl() override {
    _estimate << 0, 0, 0;
  }

  // 更新
  virtual void oplusImpl(const double *update) override {
    _estimate += Eigen::Vector3d(update);
  }

  // 存盘和读盘：留空
  virtual bool read(istream &in) {}

  virtual bool write(ostream &out) const {}
};

// 误差模型 模板参数：观测值维度，类型，连接顶点类型
class CurveFittingEdge : public g2o::BaseUnaryEdge<1, double, CurveFittingVertex> {
public:
  EIGEN_MAKE_ALIGNED_OPERATOR_NEW

  CurveFittingEdge(double x) : BaseUnaryEdge(), _x(x) {}

  // 计算曲线模型误差
  virtual void computeError() override {
    const CurveFittingVertex *v = static_cast<const CurveFittingVertex *> (_vertices[0]);
    const Eigen::Vector3d abc = v->estimate();
    _error(0, 0) = _measurement - std::exp(abc(0, 0) * _x * _x + abc(1, 0) * _x + abc(2, 0));
  }

  // 计算雅可比矩阵
  virtual void linearizeOplus() override {
    const CurveFittingVertex *v = static_cast<const CurveFittingVertex *> (_vertices[0]);
    const Eigen::Vector3d abc = v->estimate();
    double y = exp(abc[0] * _x * _x + abc[1] * _x + abc[2]);
    _jacobianOplusXi[0] = -_x * _x * y;
    _jacobianOplusXi[1] = -_x * y;
    _jacobianOplusXi[2] = -y;
  }

  virtual bool read(istream &in) {}

  virtual bool write(ostream &out) const {}

public:
  double _x;  // x 值， y 值为 _measurement
};

int main(int argc, char **argv) {
  double ar = 1.0, br = 2.0, cr = 1.0;         // 真实参数值
  double ae = 2.0, be = -1.0, ce = 5.0;        // 估计参数值
  int N = 100;                                 // 数据点
  double w_sigma = 1.0;                        // 噪声Sigma值
  double inv_sigma = 1.0 / w_sigma;
  cv::RNG rng;                                 // OpenCV随机数产生器

  vector<double> x_data, y_data;      // 数据
  for (int i = 0; i < N; i++) {
    double x = i / 100.0;
    x_data.push_back(x);
    y_data.push_back(exp(ar * x * x + br * x + cr) + rng.gaussian(w_sigma * w_sigma));
  }

  // 构建图优化，先设定g2o
  // 第1步：创建一个线性求解器LinearSolver
  typedef g2o::BlockSolver<g2o::BlockSolverTraits<3, 1>> BlockSolverType;  // 每个误差项优化变量维度为3，误差值维度为1
  // 第2步：创建BlockSolver。并用上面定义的线性求解器初始化
  typedef g2o::LinearSolverDense<BlockSolverType::PoseMatrixType> LinearSolverType; // 线性求解器类型

  // 梯度下降方法，可以从GN, LM, DogLeg 中选
  // 第3步：创建总求解器solver。并从GN, LM, DogLeg 中选一个，再用上述块求解器BlockSolver初始化
  auto solver = new g2o::OptimizationAlgorithmGaussNewton(
    g2o::make_unique<BlockSolverType>(g2o::make_unique<LinearSolverType>()));
  // 第4步：创建终极大boss 稀疏优化器（SparseOptimizer）
  g2o::SparseOptimizer optimizer;     // 图模型
  optimizer.setAlgorithm(solver);   // 设置求解器
  optimizer.setVerbose(true);       // 打开调试输出

  // 第5步：定义图的顶点和边。并添加到SparseOptimizer中
  // 往图中增加顶点
  CurveFittingVertex *v = new CurveFittingVertex();
  v->setEstimate(Eigen::Vector3d(ae, be, ce));
  v->setId(0);
  optimizer.addVertex(v);

  // 往图中增加边
  for (int i = 0; i < N; i++) {
    CurveFittingEdge *edge = new CurveFittingEdge(x_data[i]);
    edge->setId(i);
    edge->setVertex(0, v);                // 设置连接的顶点
    edge->setMeasurement(y_data[i]);      // 观测数值
    edge->setInformation(Eigen::Matrix<double, 1, 1>::Identity() * 1 / (w_sigma * w_sigma)); // 信息矩阵：协方差矩阵之逆
    optimizer.addEdge(edge);
  }
  // 执行优化
  cout << "start optimization" << endl;
  chrono::steady_clock::time_point t1 = chrono::steady_clock::now();
  // 第6步：设置优化参数，开始执行优化
  optimizer.initializeOptimization();
  optimizer.optimize(10);
  chrono::steady_clock::time_point t2 = chrono::steady_clock::now();
  chrono::duration<double> time_used = chrono::duration_cast<chrono::duration<double>>(t2 - t1);
  cout << "solve time cost = " << time_used.count() << " seconds. " << endl;

  // 输出优化值
  Eigen::Vector3d abc_estimate = v->estimate();
  cout << "estimated model: " << abc_estimate.transpose() << endl;

  return 0;
}
```

# 李想大佬

```c
// 添加Vertex
void G2oSolver::AddNode(karto::Vertex<karto::LocalizedRangeScan> *pVertex)
{
  karto::Pose2 odom = pVertex->GetObject()->GetCorrectedPose();
  g2o::VertexSE2 *poseVertex = new g2o::VertexSE2;
  poseVertex->setEstimate(g2o::SE2(odom.GetX(), odom.GetY(), odom.GetHeading()));
  poseVertex->setId(pVertex->GetObject()->GetUniqueId());
  mOptimizer.addVertex(poseVertex);
  ROS_DEBUG("[g2o] Adding node %d.", pVertex->GetObject()->GetUniqueId());
}
// 添加edge
void G2oSolver::AddConstraint(karto::Edge<karto::LocalizedRangeScan> *pEdge)
{
  // Create a new edge
  g2o::EdgeSE2 *odometry = new g2o::EdgeSE2;

  // Set source and target
  int sourceID = pEdge->GetSource()->GetObject()->GetUniqueId();
  int targetID = pEdge->GetTarget()->GetObject()->GetUniqueId();
  odometry->vertices()[0] = mOptimizer.vertex(sourceID);
  odometry->vertices()[1] = mOptimizer.vertex(targetID);
  if (odometry->vertices()[0] == NULL)
  {
    ROS_ERROR("[g2o] Source vertex with id %d does not exist!", sourceID);
    delete odometry;
    return;
  }
  if (odometry->vertices()[1] == NULL)
  {
    ROS_ERROR("[g2o] Target vertex with id %d does not exist!", targetID);
    delete odometry;
    return;
  }

  // Set the measurement (odometry distance between vertices)
  karto::LinkInfo *pLinkInfo = (karto::LinkInfo *)(pEdge->GetLabel());
  karto::Pose2 diff = pLinkInfo->GetPoseDifference();
  g2o::SE2 measurement(diff.GetX(), diff.GetY(), diff.GetHeading());
  odometry->setMeasurement(measurement);

  // Set the covariance of the measurement
  karto::Matrix3 precisionMatrix = pLinkInfo->GetCovariance().Inverse();
  Eigen::Matrix<double, 3, 3> info;
  info(0, 0) = precisionMatrix(0, 0);
  info(0, 1) = info(1, 0) = precisionMatrix(0, 1);
  info(0, 2) = info(2, 0) = precisionMatrix(0, 2);
  info(1, 1) = precisionMatrix(1, 1);
  info(1, 2) = info(2, 1) = precisionMatrix(1, 2);
  info(2, 2) = precisionMatrix(2, 2);
  odometry->setInformation(info);

  // Add the constraint to the optimizer
  ROS_DEBUG("[g2o] Adding Edge from node %d to node %d.", sourceID, targetID);
  mOptimizer.addEdge(odometry);
}
```

# 道峰大佬 - 直接将V计算给edge

```c
// 初始化
slam::optimizer::GraphOptimize optimizer;
optimizer.createOptimizer();
optimizer.setMaxIeration(10);

// 加顶点,加边
Eigen::Vector3f robotPoseCurr( 0.0f, 0.0f, 0.0f );
int keyFrameCount = 0;
optimizer.addVertex( robotPoseCurr, keyFrameCount ); // add a vertex

Eigen::Matrix3d information = Eigen::Matrix3d::Identity(); //information matrix
Eigen::Matrix<float, 3, 3> T1 = slam.v2t( robotPosePrev );
Eigen::Matrix<float, 3, 3> T2 = slam.v2t( robotPoseCurr );
Eigen::Matrix<float, 3, 3> T = T1.inverse() * T2;
Eigen::Vector3f V = slam.t2v( T );
optimizer.addEdge( V, keyFrameCount - 1, keyFrameCount, information ); // add a edge

Eigen::Matrix3d information = 1 * Eigen::Matrix3d::Identity(); //information matrix
Eigen::Matrix<float, 3, 3> T1 = slam.v2t( robotPoseCurr );
Eigen::Matrix<float, 3, 3> T2 = slam.v2t( keyPoses[loopId] );
Eigen::Matrix<float, 3, 3> T = T1.inverse() * T2;
Eigen::Vector3f V = slam.t2v( T );
optimizer.addEdge( V, keyFrameCount, loopId, information ); // add a loop constraint

// 执行
optimizer.execuateGraphOptimization(); // execuate the graph optimization
optimizer.getOptimizedResults(); // get the optimized results
std::vector<Eigen::Vector3f> estimatedPoses = optimizer.getEstimatedPoses();
```

# 设置一元边 - 使用现成的edge

```c
// 往图中增加边    
for ( int i=0; i<N; i++ )
{
    CurveFittingEdge* edge = new CurveFittingEdge( x_data[i] );
    edge->setId(i);
    edge->setVertex( 0, v );                   					
    optimizer.addEdge( edge );
}
```

# 设置二元边( 前后两个位姿 )

```c
index = 1;
for ( const Point2f p:points_2d ){
    g2o::EdgeProjectXYZ2UV* edge = new g2o::EdgeProjectXYZ2UV();
    edge->setId ( index );  // 边的b编号    
    edge->setVertex ( 1, pose );
    edge->setMeasurement ( Eigen::Vector2d ( p.x, p.y ) );  // 设置观测的特征点图像坐标    
    edge->setInformation( Eigen::Matrix<double,1,1>::Identity()*1/(w_sigma*w_sigma) ); // 信息矩阵：协方差矩阵之逆
    optimizer.addEdge ( edge );
    index++;
}
```

