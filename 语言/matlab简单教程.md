---
title: matlab简单教程
date: 2022-2-22 12:30:12
categories:
  - 语言
  - matlab
tags:
  - matlab
---

# 参考网址

 [MATLAB 快速入门 - MathWorks 中国](https://ww2.mathworks.cn/help/matlab/getting-started-with-matlab.html) 

 [(109条消息) 零基础入门Matlab（一篇两个小时就能学完的入门博客）_灯火阑珊_-CSDN博客_matlab](https://blog.csdn.net/weixin_46125998/article/details/118991929?ops_request_misc=%7B%22request%5Fid%22%3A%22164551230116780366574080%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164551230116780366574080&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-118991929.pc_search_insert_es_download&utm_term=matlab&spm=1018.2226.3001.4187) 

# 注释与窗口命令

```matlab
%% 独占一行的注释（有上下横线分割）
clear all % 普通注释, 清除Workspace中的所有变量（右侧工作区）
clc % 清除Command Window中的所有命令
```

# 数据类型

数字

```matlab
2 + 4
10-7
3*5
8/2
```

 字符与字符串 

```matlab
s = ‘a’ （单引号表示字符串）
==abs(s)==ASCII码
char(97) 输出 a （ASCII码转字符串）
num2str(65) 输出数字65
str=‘I love MATLAB & Machine Learning’
length(str) 字符串长度
doc num2str
```

 矩阵 

```matlab
A = [1 2 3; 4 5 2; 3 2 7]
% 转置
B = A'
% 竖向展开
C = A(
% 逆矩阵
D = inv(A)
% 创建三维全零矩阵(由三个二维矩阵组成)
E = zeros(10,5,3)
% 随机分布(0~1)
E(:,:,1) = rand(10,5)
% 均匀分布
E(:,:,2) = randi(5,10,5)
% 正态分布
E(:,:,3) = randn(10,5)
```

# 元胞数组与结构体

## 元胞数组 -> 类

```matlab
%元胞数组
A = cell(1,6)
A{2} = eye(3) %2021版本前的matlab下标从1开始
A{5} = magic(5)
B = A{5}
```

## 结构体

```matlab
%结构体
books = struct('name',{{'Machine Learning','Data Mining'}},'price',[30,40])
books.name %属性
books.name(1)
books.name{1}
```

# 矩阵操作

## 定义

```matlab
A = [1,2,3,4,5,6,5,4,6]
A = [1 2 3; 4 5 2; 3 2 7]
B = 1:2:9 %第二个参数为步长，不可缺省
B = 1:3:9
C = repmat(B,3,2) %重复执行3行2列
D = ones(2,4) %生成一个2行4列的全1矩阵
E = zeros(2,4)%生成一个2行4列的全0矩阵
```

## 四则运算

```matlab
A = [1 2 3 4; 5 6 7 8]
B = [1 1 2 2; 2 2 1 1]
C = A + B
D = A - B
E = A * B'
F = A .* B % .*表示对应项相乘
G = A / B %相当于A*B的逆 G*B = A  G*B*pinv(B) = A*pinv(B)  G = A*pinv(B),相当于A乘B
H = A ./ B % ./表示对应项相除
```

## 矩阵下标

```matlab
A = magic(5)
B = A(2,3)
C = A(3,:) % :为取全部,那么这条语句表示取第三行
D = A(:,4) %取第四列
[m,n] = find(A > 20) %找到大于20的序号值/矩阵
%取的是索引值
```

# 控制流

## if......else......end

```matlab
a = randi(100, 1);

if a < 30
    disp('small')
elseif a < 80
    disp('medium')
else
    disp('large')
end
```

### switch......case

```matlab
[dayNum, dayString] = weekday(date, 'long', 'en_US');

switch dayString
   case 'Monday'
      disp('Start of the work week')
   case 'Tuesday'
      disp('Day 2')
   case 'Wednesday'
      disp('Day 3')
   case 'Thursday'
      disp('Day 4')
   case 'Friday'
      disp('Last day of the work week')
   otherwise
      disp('Weekend!')
end
```

## for

```matlab
for n = 3:32
   r(n) = rank(magic(n));
end
```

## while(注意:break,continue,return)

```matlab
a = 0; fa = -Inf;
b = 3; fb = Inf;
while b-a > eps*b
   x = (a+b)/2;
   fx = x^3-2*x-5;
   if sign(fx) == sign(fa)
      a = x; fa = fx;
   else
      b = x; fb = fx;
   end
end
x
```

# 绘图

```matlab
%1.二维平面绘图
x = 0:0.01:2*pi %定义x的范围，第二个参数表示步长
y = sin(x)
figure %建立一个幕布
plot(x,y) %绘制当前二维平面图
title('y = sin(x)') %标题
xlabel('x') %x轴
ylabel('sin(x)') %y轴
xlim([0 2*pi]) % x坐标值的范围
```

```matlab
%2.多个二维平面绘图
x = 0:0.01:20;
y1 = 200*exp(-0.05*x).*sin(x);
y2 = 0.8*exp(-0.5*x).*sin(10*x);
figure
[AX,H1,H2] = plotyy(x,y1,x,y2,'plot'); %共用一个x的坐标系，在y上有不同的取值
%设置相应的标签
set(get(AX(1),'Ylabel'),'String','Slow Decay')
set(get(AX(2),'Ylabel'),'String','Fast Decay')
xlabel('Time(\musec)')
title('Multiple Decay Rates')
set(H1,'LineStyle','--')
set(H2,'LineStyle',':')
```

```matlab
%3.三维立体绘图
t = 0: pi/50: 10*pi;
plot3(sin(t),cos(t),t)
xlabel('sin(t)')
ylabel('cos(t)')
zlabel('t')
%hold on
%hold off %不保留当前操作
grid on %把图片绘制出来，在图片中加一些网格线
axis square %使整个图（连同坐标系）呈方体
```

