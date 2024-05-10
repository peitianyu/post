---
title: nanoflann
date: 2022-5-9 12:30:12
categories:
  - slam
  - 第三方学习
tags:
  - slam
---

# 参考网址:

 [(131条消息) nanoflann库_Being_young的博客-CSDN博客](https://blog.csdn.net/u013019296/article/details/109377104) 

 [nanoflann: a C++11 header-only library for Nearest Neighbor (NN) search with KD-trees ](https://github.com/jlblancoc/nanoflann) 

# 使用

```cc
// 1.构建kd-tree索引
using my_kd_tree_t = nanoflann::KDTreeSingleIndexAdaptor<
        nanoflann::L2_Simple_Adaptor<num_t, PointCloud<num_t>>,
        PointCloud<num_t>, 3 /* dim */
        >;
my_kd_tree_t index(3 /*dim*/, cloud, {10 /* max leaf */});
// 2.knn 检索
const size_t                   num_results = 1;
size_t                         ret_index;
num_t                          out_dist_sqr;
nanoflann::KNNResultSet<num_t> resultSet(num_results);
resultSet.init(&ret_index, &out_dist_sqr);
index.findNeighbors(resultSet, &query_pt[0], nanoflann::SearchParams(10));
std::cout << "knnSearch(nn=" << num_results << "): \n";
std::cout << "ret_index=" << ret_index
          << " out_dist_sqr=" << out_dist_sqr << std::endl;
```

