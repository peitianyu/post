---
title: ros教程
date: 2021-11-13 12:30:12
categories:
  - slam
  - ros
tags:
  - ros
---

# 参考网址:

**古月居21讲**

[博客 - 古月居 (guyuehome.com)](https://www.guyuehome.com/blog/index)

**创客智造**

[ROS入门教程-目录 - 创客智造 (ncnynl.com)](https://www.ncnynl.com/archives/201608/496.html)

[(70条消息) ros机器人_wuzhishiwo的博客-CSDN博客](https://blog.csdn.net/wuzhishiwo/category_10362422.html)

[ROS资源整理-学习ROS用得到的一些资料（持续更新）_Jiali Zhang的博客-CSDN博客](https://blog.csdn.net/zhangjiali12011/article/details/91885148?ops_request_misc=%7B%22request%5Fid%22%3A%22162976489016780269870400%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=162976489016780269870400&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v29_ecpm-27-91885148.pc_search_similar&utm_term=古月居ros课程&spm=1018.2226.3001.4187)

**国外教程(需要科学上网)**,可在线模拟,有开源代码

[Learn Robotics from Zero - Robotics & ROS Online Courses (theconstructsim.com)](https://app.theconstructsim.com/en/signup/#/LearningPaths)

**很现成的模拟**舒爽

[智能机器人系统_国防科技大学_中国大学MOOC(慕课) (icourse163.org)](https://www.icourse163.org/course/NUDT-1205969803)

**外文教程**

[Webots: tutorial- (cyberbotics.com)](https://cyberbotics.com/doc/guide/tutorial-8-using-ros)

# 环境

通过`WSL`+`melodic`+`vscode`+`xlaunch`实现`ros`环境模拟

`wsl安装`:[wsl安装与使用 | 某飞行员的随笔 (peitianyu.github.io)](https://peitianyu.github.io/blog/2021/10/19/wsl/)

`melodic安装`:[ros安装 | 某飞行员的随笔 (peitianyu.github.io)](https://peitianyu.github.io/blog/2021/10/19/ros安装/)

`vscode安装`:[vscode | 某飞行员的随笔 (peitianyu.github.io)](https://peitianyu.github.io/blog/2021/11/13/vscode/)

`vscode配置ros:`安装*C/C++*，*CMake*，*CMake Tools*，*Code Runner*，*ROS*，*Chinese* 这些插件

[使用VScode搭建ROS开发环境_白鸟无言的博客-CSDN博客](https://blog.csdn.net/qq_42688495/article/details/107750466?ops_request_misc=%7B%22request%5Fid%22%3A%22163676717216780274154447%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163676717216780274154447&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-107750466.pc_search_mgc_flag&utm_term=vscode+ros&spm=1018.2226.3001.4187)

`xlaunch安装`:[(https://windows.podnova.com/software/3151375.htm)](https://blog.csdn.net/beyond9305/article/details/95494863?ops_request_misc=%7B%22request%5Fid%22%3A%22163676668916780255220278%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=163676668916780255220278&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-14-95494863.pc_search_mgc_flag&utm_term=xlaunch&spm=1018.2226.3001.4187)

下载好之后,配置`~/.bashrc`

```shell
sudo nano ~/.bashrc
# 进入编辑文件,在最后添加上
export DISPLAY=localhost:0
# CTRL+S + CTRL+X保存退出
source ~/.bashrc
```

# 学习路径

## 古月居二十一讲

这一步需要每一个实验都过一遍,编译一遍,不必要太过详尽,重在了解ros流程,之后回来重新复习

- [古月居二十一讲视频](https://www.bilibili.com/video/BV1L44y167ox?p=3)
- [古月居二十一讲笔记](https://blog.csdn.net/weixin_48395629/article/details/109022916?ops_request_misc=%7B%22request%5Fid%22%3A%22162968965016780255230305%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=162968965016780255230305&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-109022916.pc_search_similar&utm_term=古月居ros入门21讲&spm=1018.2226.3001.4187)
- [古月居二十一讲讲义与代码](https://pan.baidu.com/s/1E5jvjjGtjhaeT7ivmySfAg) 密码:4n76
- [ROS探索总结 - 古月居 ](https://www.guyuehome.com/blog/index/category/11)

## ROS-Academy-for-Beginners

- [中国大学MOOC《机器人操作系统入门》讲义](https://sychaichangkun.gitbooks.io/ros-tutorial-icourse163/content/)

- [中国大学MOOC《机器人操作系统入门》课程代码示例](https://github.com/DroidAITech/ROS-Academy-for-Beginners)

注意下载自`己需要的版本`,别下错了,然后有显示的问题,可以试试

`rosdep install --from-paths src --ignore-src --rosdistro=melodic -y`

## navigation

- [ros navigation stack源码学习 - 知乎 (zhihu.com)](https://www.zhihu.com/column/ros-nav)

- [ ROS Navigation stack源码](https://github.com/meiqua/navigation)

## 激光slam

根据以下的几种尝试看源码

- [对比几种在ROS中常用的几种SLAM算法](https://www.cnblogs.com/li-yao7758258/p/8213578.html)
- [ 激光SLAM学习](https://blog.csdn.net/m0_37604894/article/details/89207705?ops_request_misc=&request_id=&biz_id=102&utm_term=激光SLAM&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-3-89207705.pc_search_insert_js_new&spm=1018.2226.3001.4187)
- [ 移动机器人_少磊的博客](https://blog.csdn.net/m0_37340621/category_9607656.html)

## 路径参考

[记得收藏丨ROS开发工程师学习指南 (qq.com)](https://mp.weixin.qq.com/s/-UHXkgg_CjaPW0yXzE4VuA)

# 几款适合copy的源码

[ros_tutorials](https://github.com/ros/ros_tutorials)

[ros_tutorials: ROS Tutorials for beginner](https://github.com/ROBOTIS-GIT/ros_tutorials)

[ros_basic_tutorials](https://github.com/guyuehome/ros_basic_tutorials)

[ROS进阶攻略系列视频课程](https://github.com/guyuehome/ros_advanced_tutorials)

[Tutorials about using the ROS Navigation stack](https://github.com/ros-planning/navigation_tutorials)

[Agvs robot simulation packages for ROS ](https://github.com/RobotnikAutomation/agvs_sim)

