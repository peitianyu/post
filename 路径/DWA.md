---
title: DWA
date: 2022-3-8 12:30:12
categories:
  - 路径
  - path_plan
tags:
  - path_plan
---

# 参考网址:

 [(114条消息) DWA动态窗口法的原理及应用_gophae的博客-CSDN博客_动态窗口法](https://blog.csdn.net/gophae/article/details/105299926?ops_request_misc=%7B%22request%5Fid%22%3A%22164691297316780271566311%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164691297316780271566311&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-105299926.article_score_rank&utm_term=dwa&spm=1018.2226.3001.4187) 

 [ROS 2D导航原理系列教程合集（WHEELTEC）_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV19b4y1d7Hz?p=4) 

 [ShisatoYano/dynamic_window_approach (github.com)](https://github.com/ShisatoYano/dynamic_window_approach) 

 [(114条消息) SLAM学习：DWA算法原理和Python编程实现_YuriFan的博客-CSDN博客_dwa算法实现](https://blog.csdn.net/qq_43711697/article/details/106880637?ops_request_misc=%7B%22request%5Fid%22%3A%22164698653816780265453796%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164698653816780265453796&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-106880637.article_score_rank&utm_term=dwa算法&spm=1018.2226.3001.4187) 

# 流程

![20200621101602368.png](https://s2.loli.net/2022/03/11/YCO1pAGnsjdcoEy.png)

# 代码流程

```txt
for 1:1000 % 迭代1000次
	dwa_control();
	motion();
	if 接近目标点:
		break

dwa_control():
	calc_dynamic_window(); -> calc_final_input();

calc_final_input():
	calc_trajectory()->to_goal_cost->speed_cost->obst_cost->u_traj(v,w)
```

# [参考代码](https://github.com/ShisatoYano/dynamic_window_approach) 

```c
// 初始化状态信息
DynamicWindow calc_dynamic_window(State *st)
{
    DynamicWindow dw = {0.0, 0.0, 0.0, 0.0};

    // from robot specification
    float vs[4] = {MIN_SPD_MS, MAX_SPD_MS, -MAX_YAWRATE_RS, MAX_YAWRATE_RS};

    // from motion model
    float vd[4] = {st->v_ms - MAX_ACCEL_MS2 * DT_S,
                   st->v_ms + MAX_ACCEL_MS2 * DT_S,
                   st->omega_rads - MAX_DYAWRATE_RS2 * DT_S,
                   st->omega_rads + MAX_DYAWRATE_RS2 * DT_S};

    dw.min_v = max(vs[0], vd[0]);
    dw.max_v = min(vs[1], vd[1]);
    dw.min_yr = max(vs[2], vd[2]);
    dw.max_yr = min(vs[3], vs[3]);

    return dw;
}
```

```c
// 生成一系列轨迹
for (float v = dw->min_v; v < dw->max_v; v+=V_RESO) {
    for (float y = dw->min_yr; y < dw->max_yr; y+=YAWRATE_RESO) {
        traj = calc_trajectory(&st_init, v, y);

        // calculate cost
        to_goal_cost = GOAL_COST_GAIN * calc_to_goal_cost(&traj, goal);
        speed_cost = SPD_COST_GAIN * (MAX_SPD_MS - traj.back().v_ms);
        obst_cost = OBST_COST_GAIN*calc_obstacle_cost(&traj, obst, obst_num);
        final_cost = to_goal_cost + speed_cost + obst_cost;

        // search minimum trajectory
        if (min_cost >= final_cost)
        {
            min_cost = final_cost;
            min_u.v_ms = v;
            min_u.omega_rads = y;
            best_traj = traj;
        }
    }
}
// 最后输出
```

