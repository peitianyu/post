---
title: matlab导入
date: 2022-2-24 12:30:12
categories:
  - 语言
  - matlab
tags:
  - matlab
---

# 参考网址

  [(110条消息) matlab 数据导入_哆啦A梦的博客-CSDN博客_matlab导入数据](https://blog.csdn.net/a1b2c3d4123456/article/details/45953087?ops_request_misc=%7B%22request%5Fid%22%3A%22164570294216780261995670%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164570294216780261995670&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~sobaiduend~default-2-45953087.nonecase&utm_term=matlab加载数据&spm=1018.2226.3001.4450) 

 [(110条消息) MATLAB-读取pgm图像_FrankDura的博客-CSDN博客_matlab读取pgm](https://blog.csdn.net/OOFFrankDura/article/details/85046359?ops_request_misc=%7B%22request%5Fid%22%3A%22164602516616780366543724%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164602516616780366543724&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-3-85046359.pc_search_insert_es_download&utm_term=matlab读取pgm&spm=1018.2226.3001.4187) 

 [(110条消息) matlab读写pgm文件_freshair9的专栏-CSDN博客_matlab打开pgm文件](https://blog.csdn.net/freshair9/article/details/7078514?ops_request_misc=&request_id=&biz_id=102&utm_term=matlab读取pgm&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~sobaiduweb~default-7-7078514.nonecase&spm=1018.2226.3001.4450) 

# load

```matlab
% Load the file to the matrix, M :
M = load('sample_file.txt')  
```

# matlab读取pgm文件

```matlab
clc,clear,close all;
im1 = imread(‘1.pgm’);
imshow(im1)
% 可知im1时一个二维数组,254表白色,0表黑色,205表未知
```

另一种方式

```matlab
% function disp_ pgm( pgm_image_name)
%不支持文件中有注释
pgm_image_name='tmp. pgm';
f = fopen( pgm_image_name,'r');
if f == -1
    error(['Could not open file ', pgm_image_name]);
end
[imgsize, num]=fscanf(f, 'P5\n%d\n%d\n255\n');
if num~=2,error('error num');end
image=[];
for h=1:imgsize(2)
    image=[image fread(f,imgsize(1),'uint8')];
end
image=image.';
fclose(f);
imshow(image);
写文件
% Load image
% image = imread(imageFile);
% If you have the Image Processing Toolbox, you can uncomment the following
%   lines to allow input of color images, which will be converted to grayscale.
if isrgb(image)
   image = rgb2gray(image);
end
[rows, cols] = size(image);
% Convert into PGM imagefile, readable by "keypoints" executable
f = fopen('tmp. pgm', 'w');
if f == -1
    error('Could not create file tmp. pgm.');
end
fprintf(f, 'P5\n%d\n%d\n255\n', cols, rows);
fwrite(f, image', 'uint8');
fclose(f);
```

