---
title: matlab打印函数
date: 2022-2-22 12:30:12
categories:
  - 语言
  - matlab
tags:
  - matlab
---

# 参考网址

 [(109条消息) Matlab之print,fprint,fscanf,disp函数_WITNESS-CSDN博客_matlab print](https://blog.csdn.net/rushkid02/article/details/7929189) 

# Matlab之print,fprint,fscanf,disp函数

## print - 把函数图形保存下来

```matlab
minbnd = -4*pi;  
maxbnd = 4*pi;  
t = minbnd:0.1*pi:maxbnd;  
plot(t, sin(t), 'g', 'Linewidth', 2);  
line([minbnd, maxbnd], [0, 0]);    %绘制x轴  
axis([-10, 10, -2, 2])             %定义显示的坐标区间:x在(-10,10)之间，y在(-2,2)之间  
grid on;  
title('sin(x)');  
xlabel('x');  
ylabel('sin(x)');  
print('-dpng','sin.png');          %保存为png图片，在Matlab当前的工作目录下  
```

```txt
-dbmp：保存为bmp格式
-djpeg：保存为jpeg格式
-dpng：保存为png格式
-dpcx：保存为pcx格式
-dpdf：保存为pdf格式
-dtiff：保存为tiff格式
```

##  fprintf - 按指定格式写入文本文件

```matlab
% 输出到屏幕
data = [5, 1, 2; 3, 7, 4];  
[row, col] = size(data);  
for i=1:row  % for循环
  for j=1:col  
    fprintf('data(%d, %d) = %d\n', i, j, data(i, j));   %直接输出到屏幕；类似于C语言的输出格式  
  end  
end 
```

```txt
%c    单个字符  
%d    有符号十进制数（%i也可以）  
%u    无符号十进制数  
%f    浮点数（%8.4f表示对浮点数取8位宽度，同时4位小数）  
%o    无符号八进制数  
%s    字符串  
%x    小写a-f的十六进制数  
%X    大小a-f的十六进制数
```

```matlab
% 输出到文件
data = [5, 1, 2; 3, 7, 4];  
[row, col] = size(data);               %求出矩阵data的行数和列数  
  
%加t表示按Windows格式输出换行，即0xOD 0x0A，没有t表示按Linux格式输出换行，即0x0A  
fid=fopen('test.txt', 'wt');           %打开文件  
  
for i=1:row  
  for j=1:col  
    fprintf(fid, '%d ', data(i, j));   %类似于C语言的输出格式  
  end  
  fprintf(fid, '\n');  
end  
fprintf(fid, 'This is a string\n');  
fprintf(fid, '%X', hex2dec('ABCD'));  
fclose(fid);                           %最后不要忘记关闭文件！
```

## fscanf - 文件读取

注意: **fscanf读取数据时会忽略空格，直到回车为止!** 

```matlab
%加t的理由和上面一样  
fid=fopen('d:\test.txt', 'rt');   
%把数据读到data中。其中data是2*3的矩阵  
data=fscanf(fid, '%d', [2, 3]);   
s=fscanf(fid, '%s');  
d=fscanf(fid, '%X');  
%关闭文件  
fclose(fid);  
%打印数据 
disp(data);  
disp(s);  
disp(d); 
```

## disp -  **输出在Matlab命令窗口**

```matlab
%单字符串输出：  
disp('Hello World!');  
  
%不同类型数据输出：  
num1 = 1;  
num2 = 2;  
disp([ num2str(num1), ' + ', num2str(num2), ' = ', num2str(num1+num2)]);  
% 注意,这里的输出都是转换为string类型在输出的
```

