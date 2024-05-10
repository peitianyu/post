---
title: Hu不变矩
date: 2022-5-3 12:30:12
categories:
  - slam
  - 理论学习
tags:
  - slam
hidden: true
---

# 参考网址:

[(130条消息) Hu不变矩原理及opencv实现_beihangzxm123的博客-CSDN博客_hu不变矩](https://blog.csdn.net/qq_26898461/article/details/47123405?ops_request_misc=&request_id=&biz_id=102&utm_term=Hu不变矩&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-47123405.142^v9^control,157^v4^control&spm=1018.2226.3001.4187)

[(130条消息) 图像的局部特征研究--Hu不变矩_purgle的博客-CSDN博客_hu不变矩](https://blog.csdn.net/purgle/article/details/50976268?ops_request_misc=%7B%22request%5Fid%22%3A%22165154407916782388032079%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=165154407916782388032079&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-50976268.142^v9^control,157^v4^control&utm_term=hu不变矩值的作用&spm=1018.2226.3001.4187)

[egdw/HU: Hu矩阵 C++实现 (github.com)](https://github.com/egdw/HU)

# 原理

```c
#include <opencv2/opencv.hpp>
#include <ctime>
using namespace std;
using namespace cv;
 
double M[7] = {0}; 
 
vector<Mat> cutImage(Mat image)
{
	vector<Mat> roi;
	int rwidth = image.cols/3;
	int rheight = image.rows/3;
 
	for (int y = 0;y < image.rows-10;y +=rheight)
	{
		for (int x = 0;x <  image.cols-10;x += rwidth)
		{
			Rect rect(x, y, rwidth, rheight);
			rect &= Rect(0, 0, image.cols, image.rows);;
			roi.push_back(image(rect));
		}
	}
	return roi;
}
 
void calcHu(Mat image)
{
	int bmpWidth = image.cols;
	int bmpHeight = image.rows;
	int bmpStep = image.step; 
	int bmpChannels = image.channels();
	uchar* pBmpBuf = image.data;
 
	double m00=0,m11=0,m20=0,m02=0,m30=0,m03=0,m12=0,m21=0;  //中心矩 
	double x0=0,y0=0;                                    //计算中心距时所使用的临时变量（x-x'） 
	double u20=0,u02=0,u11=0,u30=0,u03=0,u12=0,u21=0;        //规范化后的中心矩
	double t1=0,t2=0,t3=0,t4=0,t5=0;//临时变量
	int Center_x=0,Center_y=0;//重心 
	int i,j;            //循环变量
 
	//  获得图像的区域重心(普通矩)
	double s10=0,s01=0,s00=0;  //0阶矩和1阶矩  
	for(j=0;j<bmpHeight;j++)//y
	{
		for(i=0;i<bmpWidth;i++)//x
		{
			s10+=i*pBmpBuf[j*bmpStep+i];
			s01+=j*pBmpBuf[j*bmpStep+i];
			s00+=pBmpBuf[j*bmpStep+i];
		}
	}
	Center_x=(int)(s10/s00+0.5);
	Center_y=(int)(s01/s00+0.5);
 
	//  计算二阶、三阶矩(中心矩)
	m00=s00; 
	for(j=0;j<bmpHeight;j++) 
	{
		for(i=0;i<bmpWidth;i++)//x 
		{ 
			x0=(i-Center_x); 
			y0=(j-Center_y); 
			m11+=x0*y0*pBmpBuf[j*bmpStep+i]; 
			m20+=x0*x0*pBmpBuf[j*bmpStep+i]; 
			m02+=y0*y0*pBmpBuf[j*bmpStep+i]; 
			m03+=y0*y0*y0*pBmpBuf[j*bmpStep+i];
			m30+=x0*x0*x0*pBmpBuf[j*bmpStep+i]; 
			m12+=x0*y0*y0*pBmpBuf[j*bmpStep+i]; 
			m21+=x0*x0*y0*pBmpBuf[j*bmpStep+i]; 
		} 
	} 
 
	//  计算规范化后的中心矩: mij/pow(m00,((i+j+2)/2)
	u20=m20/pow(m00,2); 
	u02=m02/pow(m00,2); 
	u11=m11/pow(m00,2);
	u30=m30/pow(m00,2.5); 
	u03=m03/pow(m00,2.5);
	u12=m12/pow(m00,2.5); 
	u21=m21/pow(m00,2.5);
 
	//  计算中间变量
	t1=(u20-u02); 
	t2=(u30-3*u12); 
	t3=(3*u21-u03); 
	t4=(u30+u12);
	t5=(u21+u03);
 
	//  计算不变矩 
	M[0]=u20+u02; 
	M[1]=t1*t1+4*u11*u11; 
	M[2]=t2*t2+t3*t3; 
	M[3]=t4*t4+t5*t5;
	M[4]=t2*t4*(t4*t4-3*t5*t5)+t3*t5*(3*t4*t4-t5*t5); 
	M[5]=t1*(t4*t4-t5*t5)+4*u11*t4*t5;
	M[6]=t3*t4*(t4*t4-3*t5*t5)-t2*t5*(3*t4*t4-t5*t5);
}
 
int compareHu(double mo[9][7])
{
	int no = 0;
	double sum = 0, min = 100, max = 0;
	
	for (int i = 0;i<9;i++)
	{
		sum += mo[i][0];
		if(mo[i][0]>max)
			max = mo[i][0];
		if(mo[i][0]<min)
			min = mo[i][0];
	}
 
	sum /= 9;
	if(sum - min > max - sum) max = min;
 
	for (int i = 0;i<9;i++)
		if (mo[i][0]==max){ no = i;break;}
	
	//cout<<max<<endl<<no<<endl;
	return no;
}
 
void drawCross(int n, Mat image)
{
	int centerx = 0, centery = 0, widstep = 0, heistep = 0;
 
	widstep = image.cols/6;
	heistep = image.rows/6;
	
	centerx = n%3 * widstep * 2 + widstep;
	centery = n/3 * heistep * 2 + heistep;
	
	Scalar color(0, 0, 255);
	line(image, Point(centerx-20, centery), Point(centerx+20, centery), color, 2);
	line(image, Point(centerx, centery-10), Point(centerx, centery+10), color, 2);
}
 
int main()
{   
	Mat src, binary;
	vector <Mat> srcRoi;
	int count = 12;
	double moment[9][7] = {0};
 
	while (--count)
	{
		char imageName[10];
		sprintf_s(imageName, "%d.jpg", count);
		src = imread(imageName, 1);
		Canny(src, binary, 50, 100);
 
		srcRoi = cutImage(binary);
 
		for (int i = 0;i < 9;i++)
		{
			calcHu(srcRoi[i]);
 
			for (int j = 0;j<7;j++)
				moment[i][j] = M[j];	
		}
 
		int no = compareHu(moment);
		drawCross(no, src);
		imshow(imageName, src);
		waitKey(0);
	}
 
	return 0;
}
```

