---
title: Sophus安装
date: 2022-05-28 12:30:12
categories:
  - slam
  - 第三方学习
tags:
  - slam
---

# 参考网址:

 [(135条消息) Sophus库使用踩坑_清酒不是九的博客-CSDN博客_卸载sophus](https://blog.csdn.net/qq_34078823/article/details/103891404?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_title~default-0-103891404-blog-105920562.pc_relevant_default&spm=1001.2101.3001.4242.1&utm_relevant_index=3) 

 [(135条消息) Sophus库安装 踩坑心得_sulywang的博客-CSDN博客](https://blog.csdn.net/sulywang/article/details/105920562?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~Rate-1-105920562-blog-77833022.pc_relevant_antiscanv2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~Rate-1-105920562-blog-77833022.pc_relevant_antiscanv2&utm_relevant_index=2) 

 [(135条消息) Sophus库安装及make报错解决记录_weixin_52402390的博客-CSDN博客_sophus安装报错](https://blog.csdn.net/weixin_52402390/article/details/122300882) 

[(161条消息) 高翔视觉SLAM十四讲Sophus安装指南（经历）_新生菜鸟不怕猫的博客-CSDN博客_sophus安装](https://blog.csdn.net/LW20211020/article/details/124204127?ops_request_misc=%7B%22request%5Fid%22%3A%22166329563216782391832842%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=166329563216782391832842&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-124204127-null-null.142^v47^body_digest,201^v3^add_ask&utm_term=sophus安装&spm=1018.2226.3001.4187)

# 安装fmt

下载[fmt](https://fmt.dev/8.1.1/)

```
mkdir build
cd build
cmake ..
make
sudo make install
```

# 安装

```shell
git clone https://ghproxy.com/https://github.com/strasdat/Sophus.git
cd Sophus
mkdir build && cd build
cmake ..
make -j8
make install
```

# 编译问题

 Sophus/sophus/so2.cpp 文件修改

```c
//将
SO2::SO2() 
{ 
    unit_complex_.real() = 1.; 
    unit_complex_.imag() = 0.; 
}
//改为
SO2::SO2() 
{ 
    unit_complex_.real(1.); 
    unit_complex_.imag(0.); 
}
```

