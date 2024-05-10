---
title: map_tp_map方法实现的回环检测
date: 2022-4-1 12:30:12
categories:
  - slam
  - 理论学习
tags:
  - slam
---

# 参考网址:

 [ A Triangle Feature Based Map-to-map Matching and Loop Closure for 2D Graph SLAM ](https://sci-hub.se/10.1109/ROBIO49542.2019.8961777) 

# 步骤

0. 建图步:通过csm于pl-icp建图

1. 通过SIFT,SURF,ORB等方式对全局地图与子图选择特征角点
2. 通过L1≥L2≥L3的方式设置三角形(L1=|x1-x2|,L2=|x2-x3|,L3=|x1-x3|) ,分别将全局地图与子图x1,x2,x3分别放入三个数组中备用,记为Tk(3)与Tl(3)
3. 根据以下公式,筛选出合适的对应点,但实际上两个距离只能达到大致相等,因此需要加一个阈值,阈值函数如下,找出全等三角形
$$
dist^{k}\left ( t_{k}^{i}\left ( 1 \right ),t_{k}^{i}\left ( 2 \right ) \right )\approx dist^{l}\left ( t_{l}^{i}\left ( 1 \right ),t_{l}^{i}\left ( 2 \right ) \right );\\

dist^{k}\left ( t_{k}^{i}\left ( 2 \right ),t_{k}^{i}\left ( 3 \right ) \right )\approx dist^{l}\left ( t_{l}^{i}\left ( 2 \right ),t_{l}^{i}\left ( 3 \right ) \right );\\

dist^{k}\left ( t_{k}^{i}\left ( 1 \right ),t_{k}^{i}\left ( 3 \right ) \right )\approx dist^{l}\left ( t_{l}^{i}\left ( 1 \right ),t_{l}^{i}\left ( 3 \right ) \right );\\

\frac{\left| x1 -x2 \right|}{min\left ( x1,x2 \right )} < 0.03
$$
4. 根据找出的全等三角形可以得到数组(dX,dY,dTheta),对dTheta在(-pi~pi)上的投票,选出合适的数组(dX,dY,dTheta),通过阈值将不太可能的数组过滤掉
5. 通过二部图匹配,km算法最终得到(dX,dY,dTheta),也即得到了初步定位坐标
6. 使用icp算法做最后的精细定位

# 加速算法

- 在生成三角形时注意可以设置长边阈值,大于最大长边的三角形不生成
- 在进行第3步的时候可以先进行排序,后进行筛选
- 在第四步投票时可以通过部分采样的方式,选取一定量样本,加快投票速度

