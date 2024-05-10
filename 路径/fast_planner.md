# Fast_Planner

## 安装fast_planner并仿真测试

```
# 安装Nlopt
git clone https://github.com/stevengj/nlopt.git
cd nlopt
mkdir build
cd build
cmake ..
make -j 
sudo make install
# 安装依赖
 sudo apt-get install libarmadillo-dev
# 编译fast_planner
cd ~/catkin_ws/src
git clone https://github.com/peitianyu/fast-planner-ubuntu20.git
cd .. && catkin_make
# 运行测试程序
roslaunch plan_manage rviz.launch
roslaunch plan_manage kino_replan.launch
```

## 源码分析

### Kinodynamic_Astar

```
整体流程与Astar类似, 不过重新设计了启发函数, 具体Astar实现可以参看'重新理解Dijstra与Astar.md'这篇文章
启发函数:
double KinodynamicAstar::estimateHeuristic(Eigen::VectorXd start, Eigen::VectorXd end, double& optimal_time)
这一步中通过庞特里亚金最小值原理求解最有时间, 通过权重调节最终启发函数cost
```

### Topo_prm

```
1. 
  /*
  1. 通过起点与终点初始化节点图
  2. 通过膨胀关系构建椭圆采样区域, 进行随机采样
  3. 判断采样点是否可视, 如果可视, 则添加到节点图中
  4. 构建邻接节点关系, 并加入到节点图中
  */
  graph = createGraph(start, end);
2. 
  // NOTE: 通过dfs搜索比较合适的路径, 并筛选节点较少的路径
  raw_paths = searchPaths();//搜索的路径
3. 
  /*
  1. 多线程并行剪枝
    a. 离散化路径
    b. 从离散化的路径中, 一步步推进, 选择最短路径
    c. 判断是否有短剪, 如果没有, 则退出
  */
  shortcutPaths();
4. 
  // NOTE: 修剪等效路径
  filtered_paths = pruneEquivalent(short_paths_); 
5. 
  /*
  1. 选择最短路径后, 通过遍历每一条路径长度是否小于ratio_to_short_ * 最短路径长度, 如果小于, 则保留该路径
  2. 将开始和结束点加入到路径中, 重新剪枝
  3. 修剪等效路径
  */
  select_paths = selectShortPaths(filtered_paths, 1); 
6. 
  final_paths_ = select_paths;
```