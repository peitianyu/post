---
title: 前轮反馈控制(stanley)
date: 2022-3-16 12:30:12
categories:
  - 路径
  - path_tracking
tags:
  - path_tracking
---

# 参考网址

 [路径规划与轨迹跟踪系列算法学习_第11讲_Stanley法_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1EA411T7wT?from=search&seid=17264326952115523860&spm_id_from=333.337.0.0) 

 [AtsushiSakai/PythonRobotics: Python sample codes for robotics algorithms. (github.com)](https://github.com/AtsushiSakai/PythonRobotics) 

 [(116条消息) Stanley轨迹跟踪算法Python/Matlab算法实现_gophae的博客-CSDN博客_stanley算法](https://blog.csdn.net/gophae/article/details/102765091?ops_request_misc=&request_id=&biz_id=102&utm_term=stanley算法 c实现&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-3-102765091.142^v2^article_score_rank,143^v4^control&spm=1018.2226.3001.4187) 

# 算法简介

通过前轮反馈控制,采用航向偏差与横向偏差(**err = err_phi + err_y**)

![1647750568_1_.png](https://s2.loli.net/2022/03/20/RFxqaQTSHsmnECv.png)

# 代码实现

## 获得最近点index

```matlab
function [error, ind] = calc_target_index(x,y,yaw, cx,cy,L)
x = x + L * cos(yaw);
y = y + L * sin(yaw);
N =  length(cx);
Distance = zeros(N,1);
for i = 1:N
Distance(i) =  sqrt((cx(i)-x)^2 + (cy(i)-y)^2);
end
[value, location]= min(Distance);
ind = location;
  if y<cy(ind) 
        error = -value;
    else
        error = value;
  end
end
```

## stanley

```matlab
function [delta] = stanley_control(x,y,yaw,v,cx,cy,cyaw, ind,ck,L, error)
    tx = cx(ind + 5);
    ty = cy(ind + 5);
    delta_yaw = v * 0.1 * sin(yaw) / L;
%     th_e = pipi(yaw + delta_yaw - cyaw(ind));
    alpha = pipi(atan((ty-y)/(tx-x)) -yaw);
    gain_k  = 1;
    theta = atan(gain_k* error/v);
    delta = alpha + theta;
end
```

## 纵向控制

```matlab
function [a] = PIDcontrol(target_v, current_v, Kp)
	a = Kp * (target_v - current_v);
end
```

