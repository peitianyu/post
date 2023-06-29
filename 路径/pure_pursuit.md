---
title: pure_pursuit
date: 2022-3-17 12:30:12
categories:
  - 路径
  - path_tracking
tags:
  - path_tracking
---

# 参考网址

 [路径规划与轨迹跟踪系列算法学习_第10讲_纯跟踪法_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1Ly4y177dF?spm_id_from=333.999.0.0) 

 [(116条消息) 无人车系统（五）：轨迹跟踪Pure Pursuit方法_windSeS的博客-CSDN博客](https://blog.csdn.net/u013468614/article/details/103502743?ops_request_misc=%7B%22request%5Fid%22%3A%22164775329916782092941044%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164775329916782092941044&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-103502743.142^v2^article_score_rank,143^v4^control&utm_term=purepursuit&spm=1018.2226.3001.4187) 

 [AtsushiSakai/PythonRobotics: Python sample codes for robotics algorithms. (github.com)](https://github.com/AtsushiSakai/PythonRobotics) 

# 算法流程:

![1647753144_1_.png](https://s2.loli.net/2022/03/20/V27CkMqFuAxBe3s.png)

# 代码

```python
def search_target_index(self, state):

    # To speed up nearest point search, doing it at only first time.
    if self.old_nearest_point_index is None:
        # search nearest point index
        dx = [state.rear_x - icx for icx in self.cx]
        dy = [state.rear_y - icy for icy in self.cy]
        d = np.hypot(dx, dy)
        ind = np.argmin(d)
        self.old_nearest_point_index = ind
    else:
        ind = self.old_nearest_point_index
        distance_this_index = state.calc_distance(self.cx[ind],
                                                    self.cy[ind])
        while True:
            distance_next_index = state.calc_distance(self.cx[ind + 1],
                                                        self.cy[ind + 1])
            if distance_this_index < distance_next_index:
                break
            ind = ind + 1 if (ind + 1) < len(self.cx) else ind
            distance_this_index = distance_next_index
        self.old_nearest_point_index = ind

    Lf = k * state.v + Lfc  # update look ahead distance #更新前瞻距离,与速度有关

    # search look ahead target point index # 搜索前瞻目标点索引
    while Lf > state.calc_distance(self.cx[ind], self.cy[ind]):
        if (ind + 1) >= len(self.cx):
            break  # not exceed goal
        ind += 1

    return ind, Lf
```

```python
def pure_pursuit_steer_control(state, trajectory, pind):
    ind, Lf = trajectory.search_target_index(state)

    if pind >= ind:
        ind = pind

    if ind < len(trajectory.cx):
        tx = trajectory.cx[ind]
        ty = trajectory.cy[ind]
    else:  # toward goal
        tx = trajectory.cx[-1]
        ty = trajectory.cy[-1]
        ind = len(trajectory.cx) - 1
	
    alpha = math.atan2(ty - state.rear_y, tx - state.rear_x) - state.yaw

    delta = math.atan2(2.0 * WB * math.sin(alpha) / Lf, 1.0)

    return delta, ind
```

