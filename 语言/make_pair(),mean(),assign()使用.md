---
title: make_pair(),mean(),assign()使用
date: 2022-3-7 12:30:12
categories:
  - 语言
  - cpp
tags:
  - cpp
---

# 参考网址:

 [(112条消息) make_pair函数_半尺杆头的博客-CSDN博客_make_pair](https://blog.csdn.net/weixin_41169280/article/details/109861478) 

 [(112条消息) C++STL中vector容器 assign()函数的用法_xlm289348的专栏-CSDN博客_vector的assign函数](https://blog.csdn.net/xlm289348/article/details/8166820?ops_request_misc=%7B%22request%5Fid%22%3A%22164661925516780255270544%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164661925516780255270544&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-4-8166820.pc_search_insert_es_download&utm_term=assign&spm=1018.2226.3001.4187) 

# 代码演示

## make_pair()

```c
return std::make_pair(int, float);
std::pair<int, float> result {loop_id, yaw_diff_rad};
std::pair<float, int> sc_dist_result = distanceBetweenScancontexts( curr_desc, context_candidate ); 
float candidate_dist = sc_dist_result.first;
int candidate_align = sc_dist_result.second;
```

## mean()

```txt
mean()求均值
mean（A）
若A为[矩阵](https://so.csdn.net/so/search?q=矩阵&spm=1001.2101.3001.7020)，则输出每一列的均值（一个向量）
若A为列向量，则输出均值（一个数）
若A为行向量，则也是输出均值（一个数），和列向量一样 
```

代码

```c
Eigen::MatrixXf invariant_key(desc.rows(), 1);
for ( int row_idx = 0; row_idx < desc.rows(); row_idx++ ){
    // 对一行求均值
    Eigen::MatrixXf curr_row = desc.row(row_idx);
    // mean()求均值
    invariant_key(row_idx, 0) = curr_row.mean();
}
```

## assign()

```c
void assign(const_iterator first,const_iterator last);
//将区间[first,last)的元素赋值到当前的vector容器中，或者赋n个值为x的元素到vector容器中，这个容器会清除掉vector容器中以前的内容。
```

```c
v2.assign(v1.begin(), v1.end()-6);
// 将v1从开头到最后第六位赋值给assign
```

