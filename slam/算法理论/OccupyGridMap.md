---
title: OccupyGridMap
date: 2022-5-3 12:30:12
categories:
  - slam
  - 理论学习
tags:
  - slam
hidden: true
---

# 参考网址:

[无处不在的小土-pr_chapter9 (gaoyichao.com)](https://gaoyichao.com/Xiaotu/?book=probabilistic_robotics&title=pr_chapter9)

[markcsie/OccupancyGridMapping (github.com)](https://github.com/markcsie/OccupancyGridMapping)

# 原理

```c
GridMapping *laserGridMapping = new GridMapping();
updateGridMap(pose,scan);
	- gridToXY();
	- inverseSensorModel();
showGridMap();
saveGridMap();
```

