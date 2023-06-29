---
title: RRT
date: 2022-3-16 12:30:12
categories:
  - 路径
  - path_plan
tags:
  - path_plan
---

# 参考网址:

 [IR艾若机器人的个人空间_哔哩哔哩_bilibili](https://space.bilibili.com/193098059) 

 [AtsushiSakai/PythonRobotics: Python sample codes for robotics algorithms. (github.com)](https://github.com/AtsushiSakai/PythonRobotics) 

 [(115条消息) 快速随机搜索树（RRT）的MATLAB代码实现_露摇芝麻粒的博客-CSDN博客_快速搜索随机树](https://blog.csdn.net/qq_29648015/article/details/105406878?ops_request_misc=%7B%22request%5Fid%22%3A%22164740720916780357232327%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164740720916780357232327&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-105406878.142^v2^article_score_rank,143^v4^control&utm_term=rrt算法matlab代码&spm=1018.2226.3001.4187) 

## 算法流程

![1647390917_1_.png](https://s2.loli.net/2022/03/16/1txrWDcUu6JLnBI.png)

```txt
0 初始化起点,终点坐标
1 采样点,采样点与步长连线
2 根据一定步长判断是否中间有障碍物,若没有生成新节点
3 一直循环,直到新节点跟终点距离在一定范围内,我们判断为找到终点
```

## 代码实现

有一个小技巧,可以在获得随机点ian加入以终点为随机点的可能性

```python
def planning(self, animation=True):
    """
    rrt path planning

    animation: flag for animation on or off
    """

    self.node_list = [self.start]
    for i in range(self.max_iter):
        # 生成随机节点
        rnd_node = self.get_random_node()
        # 获得最近节点id
        nearest_ind = self.get_nearest_node_index(self.node_list, rnd_node)
        # 加入最近节点列表
        nearest_node = self.node_list[nearest_ind]
        # 根据最近节点计算新节点
        new_node = self.steer(nearest_node, rnd_node, self.expand_dis)
        # 判断新节点是否在障碍物内,如果不是则加入新节点列表
        if self.check_if_outside_play_area(new_node, self.play_area) and \
            self.check_collision(new_node, self.obstacle_list):
            self.node_list.append(new_node)
        # 绘制动画
        if animation and i % 5 == 0:
            self.draw_graph(rnd_node)
        # 计算与终点距离,如果不在障碍物内,则路径规划完成
        if self.calc_dist_to_goal(self.node_list[-1].x,
                                    self.node_list[-1].y) <= self.expand_dis:
            final_node = self.steer(self.node_list[-1], self.end,
                                    self.expand_dis)
            if self.check_collision(final_node, self.obstacle_list):
                return self.generate_final_course(len(self.node_list) - 1)

        if animation and i % 5:
            self.draw_graph(rnd_node)

    return None  # cannot find path
```

```matlab
figure(1);
ImpRgb=imread('newmap.png');
Imp=rgb2gray(ImpRgb);
imshow(Imp)
xL=size(Imp,1);%地图x轴长度
yL=size(Imp,2);%地图y轴长度
hold on
plot(x_I, y_I, 'ro', 'MarkerSize',5, 'MarkerFaceColor','r');
plot(x_G, y_G, 'go', 'MarkerSize',5, 'MarkerFaceColor','g');% 绘制起点和目标点
count=1;
for iter = 1:3000
    p_rand=[];
    %Step 1: 在地图中随机采样一个点x_rand
    % 这里有个技巧,可以在这里加一个几率直接连接到终点
    %提示：用（p_rand(1),p_rand(2)）表示环境中采样点的坐标
    p_rand(1)=ceil(rand()*xL); % rand()生成的是0~1均匀分布的随机数，乘以800再向上取整，数便为[1,800]间的整数
    p_rand(2)=ceil(rand()*yL);
    
    p_near=[];
    %Step 2: 遍历树，从树中找到最近邻近点x_near 
    %提示：x_near已经在树T里
    min_distance = 1000;
    for i=1:count
        distance = sqrt( ( T.v(i).x - p_rand(1) )^2 + ( T.v(i).y - p_rand(2) )^2 );
        if distance < min_distance
            min_distance = distance;
            % 通过index确定树中使用分支
            index = i;
        end
    end
    p_near(1) = T.v(index).x;
    p_near(2) = T.v(index).y;
    
    p_new=[];
    %Step 3: 扩展得到x_new节点
    %提示：注意使用扩展步长Delta
    p_new(1) = p_near(1) + round( ( p_rand(1)-p_near(1) ) * Delta/min_distance );
    p_new(2) = p_near(2) + round( ( p_rand(2)-p_near(2) ) * Delta/min_distance );
    
    %检查节点是否是collision-free
    if ~collisionChecking(p_near,p_new,Imp) 
       continue;
    end
    count=count+1;
    
    %Step 4: 将x_new插入树T 
    %提示：新节点x_new的父节点是x_near
    T.v(count).x = p_new(1);         
    T.v(count).y = p_new(2); 
    T.v(count).xPrev = p_near(1);    
    T.v(count).yPrev = p_near(2);
    T.v(count).dist = min_distance;          
    
    %Step 5:检查是否到达目标点附近 
    %提示：注意使用目标点阈值Thr，若当前节点和终点的欧式距离小于Thr，则跳出当前for循环
    new_distance = sqrt( ( p_new(1) - x_G )^2 + ( p_new(2) - y_G )^2 );
    if new_distance <= Thr
        plot(p_new(1), p_new(2), 'bo', 'MarkerSize',2, 'MarkerFaceColor','b'); % 绘制x_new
        line( [p_new(1) p_near(1)], [p_new(2) p_near(2)], 'Marker','.','LineStyle','-'); %连接x_near和x_new
        line( [x_G p_new(1)], [y_G p_new(2)], 'Marker','.','LineStyle','-'); %连接x_Target和x_new
        break;
    end
    
    %Step 6:将x_near和x_new之间的路径画出来
    %提示 1：使用plot绘制，因为要多次在同一张图上绘制线段，所以每次使用plot后需要接上hold on命令
    %提示 2：在判断终点条件弹出for循环前，记得把x_near和x_new之间的路径画出来
    plot(p_new(1), p_new(2), 'bo', 'MarkerSize',2, 'MarkerFaceColor','b'); % 绘制x_new
    line( [p_new(1) p_near(1)], [p_new(2) p_near(2)], 'Marker','.','LineStyle','-'); %连接x_near和x_new
    hold on;
   
    pause(0.1); %暂停0.1s，使得RRT扩展过程容易观察
end
```

# RRT*

## 参考网址:

 [(115条消息) 【规划】RRT*算法图解_笑扬轩逸的博客-CSDN博客_rrt算法](https://blog.csdn.net/yuxuan20062007/article/details/88843690?ops_request_misc=%7B%22request%5Fid%22%3A%22164740853916781683991506%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164740853916781683991506&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-88843690.142^v2^article_score_rank,143^v4^control&utm_term=rrt*算法&spm=1018.2226.3001.4187) 

 [(115条消息) 运动规划RRT*算法图解_何伯特的博客-CSDN博客_rrt*算法](https://blog.csdn.net/weixin_43795921/article/details/88557317) 

 [(115条消息) 基于matlab的RRT&RRT*算法实现以及可视化_肉bot的博客-CSDN博客_rrt*算法](https://blog.csdn.net/weixin_46723764/article/details/115513924?ops_request_misc=%7B%22request%5Fid%22%3A%22164740853916781683955662%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=164740853916781683955662&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-6-115513924.142^v2^article_score_rank,143^v4^control&utm_term=rrt*算法&spm=1018.2226.3001.4187) 

## 代码流程

两个改进:

- 当前节点重新选择父节点
- 范围内的节点重新连接
- 渐进最优

![RRTstar1.png](https://s2.loli.net/2022/03/16/UwWxEh14opSbCRZ.png)

## 代码

在RRT算法基础上加入了

```python
if self.check_collision(new_node, self.obstacle_list):
    # 找出最近的节点
    near_inds = self.find_near_nodes(new_node)
    # 重新选择父节点
    node_with_updated_parent = self.choose_parent(
    	new_node, near_inds)
    if node_with_updated_parent:
        # 范围内的点进行重新连接
        self.rewire(node_with_updated_parent, near_inds)
        self.node_list.append(node_with_updated_parent)
    else:
    	self.node_list.append(new_node)
```

# Inform RRT*

目的是为了加快渐进最优速度,采用在椭圆中采样的方式,代码中实际上就是将smaple转换为informed_sample

![1647679338_1_.png](https://s2.loli.net/2022/03/19/HpJDULt9MrQ2Rng.png)

算法主要体现在

```python
# 输入Cbest Cmin xCenter C(旋转矩阵)
def informed_sample(self, cMax, cMin, xCenter, C):
    if cMax < float('inf'):
        # 椭圆长短边
        r = [cMax / 2.0,
        math.sqrt(cMax ** 2 - cMin ** 2) / 2.0,
        math.sqrt(cMax ** 2 - cMin ** 2) / 2.0]
        # 构建对焦矩阵
        L = np.diag(r)
        # 圆形采样
        xBall = self.sample_unit_ball()
        # 构建椭圆采样,并平移旋转矩阵
        rnd = np.dot(np.dot(C, L), xBall) + xCenter
        rnd = [rnd[(0, 0)], rnd[(1, 0)]]
    else:
    	rnd = self.sample_free_space()

    return rnd
```

