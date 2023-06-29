---
title: lio_sam安装使用
date: 2022-9-15 12:30:12
categories:
  - slam
  - 开源框架
tags:
  - lio

---

# 参考网址:

[(161条消息) LIO-sam：安装 - 运行 - 论文介绍 - 源码阅读_非晚非晚的博客-CSDN博客_lio-sam](https://blog.csdn.net/QLeelq/article/details/111088136?ops_request_misc=%7B%22request%5Fid%22%3A%22166315977616800180680290%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=166315977616800180680290&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-111088136-null-null.142^v47^body_digest,201^v3^add_ask&utm_term=LIO-SAM&spm=1018.2226.3001.4187)

# 安装运行

```shell
# 安装gtsam
wget -O ~/Downloads/gtsam.zip https://github.com/borglab/gtsam/archive/4.0.2.zip
cd ~/Downloads/ && unzip gtsam.zip -d ~/Downloads/
cd ~/Downloads/gtsam-4.0.2/
mkdir build && cd build
cmake -DGTSAM_BUILD_WITH_MARCH_NATIVE=OFF ..
sudo make install -j6  //根据自己的cpu核数定，越多越快，本人是6核的，注意不能超出自己电脑的核数

# 配置环境
nano ~/.bashrc
export LD_LIBRARY_PATH=/usr/local/lib/:$LD_LIBRARY_PATH
source ~/.bashrc

# 安装并编译lio_sam
mkdir ~/catkin_ws/src 
cd ~/catkin_ws/src 
git clone https://github.com/TixiaoShan/LIO-SAM.git 
cd ..
catkin_make

# 下载数据
数据链接：链接：https://pan.baidu.com/s/1CbDJPIupCm3yROmM2qOsOw 提取码：ato7

# 运行
roslaunch lio_sam run.launch
rosbag play casual_walk.bag
```

