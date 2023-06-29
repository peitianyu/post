---
title: ndt
date: 2022-4-29 12:30:12
categories:
  - slam
  - 理论学习
tags:
  - slam
---

# 参考网址:

[softdream/ndt-algorithm (github.com)](https://github.com/softdream/ndt-algorithm)

# 算法流程

```txt
1. caculateNDTByFirstScan:主要对前一祯数据做网格化,得到FirstmeanPose,与Pose_Cov
2. estimateTransformationOnce:对当前scan做网格化,然后使用poseCov作为sigma噪声与得到FirstmeanPose做高斯牛顿,然后迭代
3. angleNormalize:角度归一化
```

# 代码

具体代码参考[道峰大佬ndt](https://github.com/softdream/ndt-algorithm)

# 重写ndt

```c++

```

