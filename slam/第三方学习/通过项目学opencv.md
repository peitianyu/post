---
title: 通过项目学opencv
date: 2022-2-21 12:30:12
categories:
  - slam
  - 第三方学习
tags:
  - slam
  - ros
---

# 参考网址

 [sudrag/line_follower_turtlebot: A repo created to simulate a line following turtlebot in Gazebo environment using ROS (github.com)](https://github.com/sudrag/line_follower_turtlebot) 

# 简介

实际上是通过ros的一个巡线项目简单学习ros+c++中opencv的使用.

# lesson1[调用视频数据cv_bridge]

## 参考网址:

 [(109条消息) ROS学习笔记--cv_bridge_喵喵三三的博客-CSDN博客_cv_bridge](https://blog.csdn.net/weixin_40863346/article/details/82216462) 

 [cv_bridge/Tutorials/UsingCvBridgeToConvertBetweenROSImagesAndOpenCVImages - ROS Wiki](http://wiki.ros.org/cv_bridge/Tutorials/UsingCvBridgeToConvertBetweenROSImagesAndOpenCVImages) 

## 实例:

src/line_follower_turtlebot/src/linedetect.cpp中

```c++
void LineDetect::imageCallback(const sensor_msgs::ImageConstPtr& msg) {
  cv_bridge::CvImagePtr cv_ptr;
  try {
    // 总是复制，返回一个可变的CvImage
    cv_ptr = cv_bridge::toCvCopy(msg, sensor_msgs::image_encodings::BGR8);
    // 将转换的数据存放到opencv矩阵格式的img中
    img = cv_ptr->image;
    // cv::imshow("Image window", img);
    cv::waitKey(30);
  }
  catch (cv_bridge::Exception& e) {
    ROS_ERROR("Could not convert from '%s' to 'bgr8'.", msg->encoding.c_str());
  }
}
```

 CvBridge提供几种用于转换为CvImage的函数 

```c++
// Case 1: 用于ros订阅复制使用
CvImagePtr toCvCopy(const sensor_msgs::ImageConstPtr& source,
                    const std::string& encoding = std::string());
CvImagePtr toCvCopy(const sensor_msgs::Image& source,
                    const std::string& encoding = std::string());
 
// Case 2: 用于ros订阅共享使用
CvImageConstPtr toCvShare(const sensor_msgs::ImageConstPtr& source,
                          const std::string& encoding = std::string());
CvImageConstPtr toCvShare(const sensor_msgs::Image& source,
                          const boost::shared_ptr<void const>& tracked_object,
                          const std::string& encoding = std::string());
```

## 完整简单实例

```
#include <ros/ros.h>
#include <image_transport/image_transport.h>
#include <cv_bridge/cv_bridge.h>
#include <sensor_msgs/image_encodings.h>
#include <opencv2/imgproc/imgproc.hpp>
#include <opencv2/highgui/highgui.hpp>
 
static const std::string OPENCV_WINDOW = "Image window";
 
class ImageConverter
{
  ros::NodeHandle nh_;
  image_transport::ImageTransport it_;
  image_transport::Subscriber image_sub_;
  image_transport::Publisher image_pub_;
 
public:
  ImageConverter()
    : it_(nh_)
  {
    // 订阅输入视频，发布输出视频
    image_sub_ = it_.subscribe("/camera/image_raw", 1,
      &ImageConverter::imageCb, this);
    image_pub_ = it_.advertise("/image_converter/output_video", 1);
 	// 创建OPENCV_WINDOWS窗口
    cv::namedWindow(OPENCV_WINDOW);
  }
 
  ~ImageConverter()
  {
  	// 销毁OPENCV_WINDOWS窗口
    cv::destroyWindow(OPENCV_WINDOW);
  }
 
  void imageCb(const sensor_msgs::ImageConstPtr& msg)
  {
    cv_bridge::CvImagePtr cv_ptr;
    try
    {
    	// 转换图像到cv_ptr用于之后opencv的使用
      	cv_ptr = cv_bridge::toCvCopy(msg, sensor_msgs::image_encodings::BGR8);
    }
    catch (cv_bridge::Exception& e)
    {
      	ROS_ERROR("cv_bridge exception: %s", e.what());
      	return;
    }
 
    // 在视频流上画个圈
    if (cv_ptr->image.rows > 60 && cv_ptr->image.cols > 60)
      cv::circle(cv_ptr->image, cv::Point(50, 50), 10, CV_RGB(255,0,0));
 
    // 更新并显示GUI界面
    cv::imshow(OPENCV_WINDOW, cv_ptr->image);
    cv::waitKey(3);
 
    // 输出修改后的视频流
    image_pub_.publish(cv_ptr->toImageMsg());
  }
};
 
int main(int argc, char** argv)
{
  // 初始化image_converter节点
  ros::init(argc, argv, "image_converter");
  // 实例化类
  ImageConverter ic;
  ros::spin();
  return 0;
}
```

# lesson2[对读取数据进行初步处理]

## 参考网址:

 [(109条消息) OpenCV-高斯滤波cv::GaussianBlur_翟天保的博客-CSDN博客](https://blog.csdn.net/zhaitianbao/article/details/117784002?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0.queryctrv4&spm=1001.2101.3001.4242.1&utm_relevant_index=3) 

## 高斯滤波处理

```c++
void GaussianBlur( InputArray src, OutputArray dst, Size ksize,
                   double sigmaX, double sigmaY = 0,
                   int borderType = BORDER_DEFAULT );
```

- InputArray类型的src，输入图像，如Mat类型。
- OutputArray类型的dst，输出图像。
- Size类型的ksize，高斯内核的大小。
- double类型的sigmaX，高斯核函数在X方向的标准偏差。
- double类型的sigmaY，高斯核函数在Y方向的标准偏差。
- int类型的borderType，推断图像边缘像素的边界模式。

## 实例

```c++
cv::Mat LineDetect::Gauss(cv::Mat input) {
  cv::Mat output;
// 使用高斯滤波对图像进行滤波
  cv::GaussianBlur(input, output, cv::Size(3, 3), 0.1, 0.1);
  // cv::imshow("Gaussian", output);
  return output;
}
```

# lesson3[实现巡线检测]

## 步骤

- 转换颜色空间为hsv
- 检测判断黄色,并找出路线轮廓
- 计算轮廓的垂直边界最小矩形，矩形是与图像上下边界平行的,并画出矩形
- 找出图像质心
- 判断质心在图像中间的左侧还是右侧,由此控制车体运动

## 参考网址

 [(109条消息) 【OpenCV3】颜色空间转换——cv::cvtColor()详解_guduruyu的专栏-CSDN博客_cv.cvtcolor](https://blog.csdn.net/guduruyu/article/details/68941554?ops_request_misc=%7B%22request%5Fid%22%3A%22164544832016780269849625%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164544832016780269849625&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-3-68941554.pc_search_insert_es_download&utm_term=cv%3A%3AcvtColor&spm=1018.2226.3001.4187) 

## 具体样例

```c++
int LineDetect::colorthresh(cv::Mat input) {
  // Initializaing variables
  cv::Size s = input.size();
  std::vector<std::vector<cv::Point>> v;
  auto w = s.width;
  auto h = s.height;
  auto c_x = 0.0;
  // Detect all objects within the HSV range
  // 颜色空间转换 输入,输出,映射码
  cv::cvtColor(input, LineDetect::img_hsv, CV_BGR2HSV);
  LineDetect::LowerYellow = {20, 100, 100};
  LineDetect::UpperYellow = {30, 255, 255};
  // 检测黄线,判断颜色在{20, 100, 100}与{30, 255, 255}之间
  // 输出的img_mask为二值图像
  cv::inRange(LineDetect::img_hsv, LowerYellow,UpperYellow, LineDetect::img_mask);
  // cv::imshow("mask", LineDetect::img_mask);
  // 切掉图像上半部分
  img_mask(cv::Rect(0, 0, w, 0.8*h)) = 0;
  // cv::imshow("mask", LineDetect::img_mask);
  // Find contours for better visualization
  // 找到可视化轮廓
  cv::findContours(LineDetect::img_mask, v, CV_RETR_LIST, CV_CHAIN_APPROX_NONE);
  // If contours exist add a bounding,Choosing contours with maximum area
  // 如果轮廓存在,则添加边界,选择包含最大面积的轮廓
  if (v.size() != 0) {
  // printf("%ld\n",v.size());
  // 计算轮廓的垂直边界最小矩形，矩形是与图像上下边界平行的
  // cv::Rect r = cv::boundingRect(v[0]);
  // c_x = r.x + r.width / 2;
  // // 把轮廓用方框框出来
  // cv::rectangle(LineDetect::img_mask, r, cv::Scalar(255, 255, 255), 2);
  // cv::imshow("mask", LineDetect::img_mask);

  auto area = 0;
  auto idx = 0;
  auto count = 0;
  // v.size()表示轮廓的个数,v[count].size()表示轮廓面积
  while (count < v.size()) {
    if (area < v[count].size()) {
       idx = count;
       area = v[count].size();
      //  printf("%ld\n",v[count].size());
    }
    count++;
  }
  // 计算轮廓的垂直边界最小矩形，矩形是与图像上下边界平行的
  // rect: 格式为(x,y,w,h)
  cv::Rect rect = boundingRect(v[idx]);
  cv::Point pt1, pt2, pt3;
  // 坐上角坐标
  pt1.x = rect.x;
  pt1.y = rect.y;
  // 右下角坐标
  pt2.x = rect.x + rect.width;
  pt2.y = rect.y + rect.height;
  // 向右向上移动5个像素
  pt3.x = pt1.x+5;
  pt3.y = pt1.y-5;
  // 画出矩形
  cv::rectangle(input, pt1, pt2, CV_RGB(255, 0, 0), 2);
  // 画出文本框
  cv::putText(input, "Line Detected", pt3,
    CV_FONT_HERSHEY_COMPLEX, 1, CV_RGB(255, 0, 0));
  }
  // Mask image to limit the future turns affecting the output
  // 将左右两边0.3倍的图像清除
  img_mask(cv::Rect(0.7*w, 0, 0.3*w, h)) = 0;
  img_mask(cv::Rect(0, 0, 0.3*w, h)) = 0;
  // cv::imshow("mask", LineDetect::img_mask);
  // Perform centroid detection of line
  // 质心检测
  cv::Moments M = cv::moments(LineDetect::img_mask);
  if (M.m00 > 0) {
    // Calculating centroid
    // 计算质心坐标
    cv::Point p1(M.m10/M.m00, M.m01/M.m00);
    // 画出质心
    cv::circle(LineDetect::img_mask, p1, 10, cv::Scalar(255, 255, 255), 1);
  }
  // 检测的质心的横坐标
  c_x = M.m10/M.m00;
  // Tolerance to chooise directions
  // 方向宽容度
  auto tol = 15;
  // 返回灰度值不为0的像素数，可用来判断图像是否全黑。若全黑，则返回0
  auto count = cv::countNonZero(img_mask);
  // Turn left if centroid is to the left of the image center minus tolerance
  // Turn right if centroid is to the right of the image center plus tolerance
  // Go straight if centroid is near image center
  if (c_x < w/2-tol) {
    LineDetect::dir = 0;
  } else if (c_x > w/2+tol) {
    LineDetect::dir = 2;
  } else {
    LineDetect::dir = 1;
  }
  // Search if no line detected
  if (count == 0) {
    LineDetect::dir = 3;
  }
  // Output images viewed by the turtlebot
  cv::namedWindow("Turtlebot View");
  imshow("Turtlebot View", input);
  return LineDetect::dir;
}
```

## 颜色空间转换

```c++
	void cv::cvtColor(
		cv::InputArray src, // 输入序列
		cv::OutputArray dst, // 输出序列
		int code, // 颜色映射码
		int dstCn = 0 // 输出的通道数 (0='automatic')
	);
```

### 程序样例

```c++
// 颜色空间转换 输入,输出,映射吗
  cv::cvtColor(input, LineDetect::img_hsv, CV_BGR2HSV);
```

##  检测黄线

```c++
// 函数原型（C++）
void inRange(InputArray src, InputArray lowerb, InputArray upperb, OutputArray dst)
```

### 程序样例

```c++
  cv::Mat img_hsv;  
  cv::Scalar LowerYellow;
  cv::Scalar UpperYellow;
  LineDetect::LowerYellow = {20, 100, 100};
  LineDetect::UpperYellow = {30, 255, 255};
// 检测黄线,判断颜色在{20, 100, 100}与{30, 255, 255}之间
  cv::inRange(LineDetect::img_hsv, LowerYellow,UpperYellow, LineDetect::img_mask);
```

## 找出可视化轮廓

### 参考网址:

 [(109条消息) opencv cv.findContours 函数详解_Ibelievesunshine的博客-CSDN博客_cv.findcontours](https://blog.csdn.net/Ibelievesunshine/article/details/105208431?ops_request_misc=%7B%22request%5Fid%22%3A%22164544925616780366547214%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164544925616780366547214&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-3-105208431.pc_search_insert_es_download&utm_term=cv%3A%3AfindContours(&spm=1018.2226.3001.4187) 

### 两种调用方式:

```c++
	void cv::findContours(
		cv::InputOutputArray image, // 输入的8位单通道“二值”图像
		cv::OutputArrayOfArrays contours, // 包含points的vectors的vector
		cv::OutputArray hierarchy, // (可选) 拓扑信息
		int mode, // 轮廓检索模式
		int method, // 近似方法
		cv::Point offset = cv::Point() // (可选) 所有点的偏移
	);
	void cv::findContours(
		cv::InputOutputArray image, // 输入的8位单通道“二值”图像
		cv::OutputArrayOfArrays contours, // 包含points的vectors的vector
		int mode, // 轮廓检索模式
		int method, // 近似方法
		cv::Point offset = cv::Point() //  (可选) 所有点的偏移
	);
```

- cv::RETR_EXTERNAL：表示只提取最外面的轮廓；

- cv::RETR_LIST：表示提取所有轮廓并将其放入列表；

- cv::RETR_CCOMP:表示提取所有轮廓并将组织成一个两层结构，其中顶层轮廓是外部轮廓，第二层轮廓是“洞”的轮廓；

- cv::RETR_TREE：表示提取所有轮廓并组织成轮廓嵌套的完整层级结构。

### 程序样例:

```
cv::findContours(LineDetect::img_mask, v, CV_RETR_LIST, CV_CHAIN_APPROX_NONE);
```

## 计算轮廓的垂直边界最小矩形

```c++
// 计算轮廓的垂直边界最小矩形，矩形是与图像上下边界平行的
// x, y, w, h = cv2.boundingRect(binary) x,y为左上角坐标
  cv::Rect rect = boundingRect(v[idx]);
```

## 找出质心

```c++
cv::moments(LineDetect::img_mask);
(i,j)->(M.m10/M.m00, M.m01/M.m00)质心的图像坐标
```

### 参考网址

 [(109条消息) opencv学习(四十三)之图像的矩moments()_烟雨博客-CSDN博客_cv::moments](https://blog.csdn.net/keith_bb/article/details/70197104) 

