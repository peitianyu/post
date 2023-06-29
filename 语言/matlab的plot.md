---
title: matlab的plot
date: 2022-2-22 12:30:12
categories:
  - 语言
  - matlab
tags:
  - matlab
---

# 参考网址

 [(109条消息) 关于plot函数的一切（史上最全总结）_T_just_for_tomorrow的博客-CSDN博客_plot函数举例](https://blog.csdn.net/T_just_for_tomorrow/article/details/104431429?ops_request_misc=%7B%22request%5Fid%22%3A%22164550615916780274183421%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164550615916780274183421&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-104431429.pc_search_insert_es_download&utm_term=plot&spm=1018.2226.3001.4187) 

# 基本输出形式

```matlab
plot（X,Y，LineSpec）% 这里的X,Y为需要输入参数,LineApec为设置线性，颜色，标记类型
```

# 具体参数如下图

| 线性 | 描述         |
| :--- | :----------- |
| -    | 实线（默认） |
| --   | 虚线         |
| ：   | 虚线         |
| -.   | 点划线       |

| 图像点的形状 | 描述       |
| :----------- | :--------- |
| o            | 圈         |
| +            | 加号       |
| *            | 星号       |
| .            | 点         |
| x            | 十字       |
| s            | 正方形     |
| d            | 菱形       |
| ^            | 向上三角形 |
| v            | 向下三角形 |
| >            | 向右三角形 |
| <            | 左三角形   |
| p            | 五角星     |
| h            | 六角形     |

| 颜色 | 描述 |
| :--- | :--- |
| y    | 黄色 |
| m    | 品红 |
| c    | 青色 |
| r    | 红色 |
| g    | 绿色 |
| b    | 蓝色 |
| w    | 白色 |
| k    | 黑色 |

# 绘制多条线

```matlab
x=linespace(-2*pi,2*pi);   % linespace（x1.x2,n）是均分计算指令，生成x1到x2之间n个行线型矢量
                           % 默认n是100
y1=sin(x);
y2=cos(x);
plot(x,y1,x,y2)
```

#  矩阵做图 

```matlab
y=magic(4)   %magic:生成行列和对角线元素和相等的矩阵，第二章的文章中总结过
plot(y)
```

# 指定线型，颜色和标记

```matlab
x = 0:pi/10:2*pi;
y1 = sin(x);
y2 = sin(x-0.25);
y3 = sin(x-0.5);
plot(x,y1,'g',x,y2,'b--o',x,y3,'c*')
```

#  在特定点标记 

```matlab
x = linspace(0,10);
y = sin(x);
plot(x,y,'-o','MarkerIndices',1:5:length(y))
```

#  显示轴，标题 

```matlab
x = linspace(0,10,140);
y = cos(3*x);
plot(x,y,'Color',[0,0.7,0.9])
 
title('曲线图') %标题
xlabel('x')  %显示x轴是x
ylabel('cos(3x)')  %显示y轴
```

