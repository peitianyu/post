---
title: huogh_transform
date: 2022-9-19 12:30:12
categories:
  - slam
  - 理论学习
tags:
  - 直线检测

---

# 参考网址:

[ 霍夫线变换原理_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1T44y1Y7kL/?spm_id_from=333.788&vd_source=745fd1b1f3e42bb544237f6d0bf78bb2)

[mars_mapping/occ_line_detector.cpp at master · Forrest-Z/mars_mapping (github.com)](https://github.com/Forrest-Z/mars_mapping/blob/master/occ_line_detection/src/occ_line_detector.cpp)

[(164条消息) 霍夫变换直线检测（Line Detection）原理及示例_leonardohaig的博客-CSDN博客_霍夫变换直线检测原理](https://blog.csdn.net/leonardohaig/article/details/87907462?ops_request_misc=&request_id=&biz_id=102&utm_term=霍夫变化检测直线&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-87907462.142^v47^body_digest,201^v3^add_ask&spm=1018.2226.3001.4187)

# 原理

```
通过霍夫变换,得到霍夫空间下的(p, theta),对不同角度进行累加,若得到相近的p,则认为处于同一直线
==> 霍夫变换: p = x*cos(theta) + y*sin(theta), 细分theta与p(这里一般指地图分辨率)
```

# 代码实现

```c++
#pragma omp parallel for
for(int i = 0;i < height;i++) // 遍历地图
{
    for(int j = 0;j < width;j++)
    {
        int index = j + i * width;
        if(i == 0 && j == 0)
            continue;

        if(map_->data[index] >= 60) // 占用值大于60,认为障碍物
        {
            double x_ = (double)j + 0.5;
            double y_ = (double)i + 0.5;

            double phi = atan2(y_,x_); // 计算角度
            double R = sqrt(pow(x_,2) + pow(y_,2)) * resolution; // 计算距离

            // #pragma omp parallel for
            for(int k = 0;k < angle_size; k++) // 关键
            {
                double angle = (double)k *  angle_resolution; // 角度分辨率
                double r = R*cos( angle/180.0*M_PI - phi);
                // int theta_index = (angle-origin_a)/angle_resolution;
                int theta_index = k;
                int r_index = (r-origin_r)/r_resolution;
                int hough_index = r_index*angle_size + theta_index;
                hough_map[hough_index]++; // 累加器
            }
        }
    }
}  
```

