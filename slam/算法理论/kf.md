---
title: kf
date: 2022-2-28 12:30:12
categories:
  - slam
  - 理论学习
tags:
  - slam
---

# 参考网址:

 [忠厚老实的老王的个人空间_哔哩哔哩_bilibili](https://space.bilibili.com/287989852/video?tid=0&page=2&keyword=&order=pubdate) 

# 笔记

```txt
----卡尔曼滤波算法----
预测步
 - 状态方程
 X(K)minus=F*X(K-1)plus
 - 先验估计
 P(K)minus=F*P(K-1)plus*F'+Q
更新步
 - 卡尔曼增益:若R足够大,则更偏向于观测值,若R趋近于0,则更相信预测值
 K=P(K)minus*H'*inv(H*P(K)minus*H'+R)
 - 更新状态量
 X(K)plus=X(K)minus+K*(y(k)-H*X(K)minus)
 - 更新后验概率
 P(K)plus=(I-K*H)*(K)minus
```

# 代码

```matlab
clc ;close ;clear all;
%%%%贝叶斯滤波与卡尔曼滤波第八讲matlab代码%%%%%%
%%%%kalman filter
%X(K)=F*X(K-1)+Q
%Y(K)=H*X(K)+R
%%%第一个问题，生成一段随机信号，并滤波

%生成一段时间t,每隔0.01s采样一次
t=0.1:0.01:1;
L=length(t);

%生成真实信号x，以及观测y
%首先初始化
x=zeros(1,L);
y=x;
y2=x;
%生成信号，设x=t^2
for i=1:L
      x(i)=t(i)^2;
      y(i)=x(i)+normrnd(0,0.1); %正态分布随机数，参数为期望和标准差
      y2(i)=x(i)+normrnd(0,0.1);
end
%%%%%%%%%%%信号生成完毕%%%%



%%%%%%%滤波算法%%%%%%
%%%预测方程观测方程怎么写%%%
%观测方程好写Y(K)=X(K)+R R~N(0,1)
%预测方程不好写%%%%，在这里，可以猜一猜是线性增长，但是大多数问题，信号是杂乱无章的，怎么办
%模型一，最粗糙的建模
%X(K)=X(K-1)+Q
%Y(K)=X(K)+R
%猜Q~N(0,1);

F1=1;
H1=1;
Q1=1;
R1=1;

%初始化x(k)+
Xplus1=zeros(1,L);%plus + 的英语 minus -的英语
%我们会经常用到Xplus,Xminus,Pplus,Pminus

%设置一个初值，假设Xplus1(1)~N(0.01,0.01^2)
Xplus1(1)=0.01;
Pplus1=0.01^2;

%%%卡尔曼滤波算法
% 预测步
%X(K)minus=F*X(K-1)plus
%P(K)minus=F*P(K-1)plus*F'+Q(先验概率)
% 更新步
%K=P(K)minus*H'*inv(H*P(K)minus*H'+R)
% 若R足够大,则更偏向于观测值,若R趋近于0,则更相信预测值
%X(K)plus=X(K)minus+K*(y(k)-H*X(K)minus)
% 更新后验概率
%P(K)plus=(I-K*H)*P(K)minus

for i=2:L
      %%%%预测步%%%%%%
      Xminus1=F1*Xplus1(i-1);
      Pminus1=F1*Pplus1*F1'+Q1;
      %%%%%更新步%%%%%
      K1=(Pminus1*H1')*inv(H1*Pminus1*H1'+R1);
      Xplus1(i)=Xminus1+K1*(y(i)-H1*Xminus1);
      Pplus1=(1-K1*H1)*Pminus1;

end

%模型2
%X(K)=X(K-1)+X'(K-1)*dt+X''(K-1)*dt^2*(1/2!)+Q2
%Y(K)=X(K)+R R~N(0,1)
%此时状态变量X=[X(K) X'(K) X''(K) ]T(列向量）
%Y(K)=H*X+R H=[1 0 0](行向量）
%预测方程
%X(K)=X(K-1)+X'(K-1)*dt+X''(K-1)*dt^2*(1/2!)+Q2
%X'(K)=0*X(K-1)+X'(K-1)+X''(K-1)*dt+Q3
%X''(K)=0*X(K-1)+0*X'(K-1)+X''(K-1)+Q4   %%多项式信号多求几阶导数，总会比较平缓，而
%X''(K)=X''(K-1)+Q3正是描述平缓的随机过程，这种建模相对精细一些，适用范围也较广
%F=1 dt 0.5*dt^2
%  0 1 dt
%  0 0 1
%H=[1 0 0]
%Q= Q2 0 0
%   0 Q3 0
%   0 0  Q4 协方差矩阵

dt=t(2)-t(1);
F2=[1,dt,0.5*dt^2;0,1,dt;0,0,1];%%%此处要注意矩阵是否病态，若dt特别小，易导致矩阵病态或精度丢失
H2=[1,0,0];
Q2=[1,0,0;0,0.01,0;0,0,0.0001];
R2=3;

%%%设置初值%%%%
Xplus2=zeros(3,L);
Xplus2(1,1)=0.1^2;
Xplus2(2,1)=0;
Xplus2(3,1)=0;
Pplus2=[0.01, 0,0;0,0.01,0;0,0,0.0001];

for i=2:L
      %%%预测步%%%
      Xminus2=F2*Xplus2(:,i-1);
      Pminus2=F2*Pplus2*F2'+Q2;
      %%%更新步%%%%
      K2=(Pminus2*H2')*inv(H2*Pminus2*H2'+R2);
      Xplus2(:,i)=Xminus2+K2*(y(i)-H2*Xminus2);
      Pplus2=(eye(3)-K2*H2)*Pminus2;
end

%%%可以进行在线滤波，实时滤波%%%%
%问题2，两个传感器，进行滤波
% Y1(K)=X(K)+R
% Y2(K)=X(K)+R
%H=[1 1]T (列向量) X=X(K)
%H=1 0 0 X=X(K) X'(K) X''(K)
% 1 0 0
F3=[1,dt,0.5*dt^2;0,1,dt;0,0,1];%%%此处要注意矩阵是否病态，若dt特别小，易导致矩阵病态或精度丢失
H3=[1,0,0;1,0,0];
Q3=[1,0,0;0,0.01,0;0,0,0.0001];
R3=[3,0;0,3];%%%%%一定要注意是协方差矩阵

%%%设置初值%%%%
Xplus3=zeros(3,L);
Xplus3(1,1)=0.1^2;
Xplus3(2,1)=0;
Xplus3(3,1)=0;
Pplus3=[0.01, 0,0;0,0.01,0;0,0,0.0001];
for i=2:L
      %%%预测步%%%
      Xminus3=F3*Xplus3(:,i-1);
      Pminus3=F3*Pplus3*F3'+Q3;
      %%%更新步%%%%
      K3=(Pminus3*H3')*inv(H3*Pminus3*H3'+R3);
      Y=zeros(2,1);
      Y(1,1)= y(i);
      Y(2,1)=y2(i);
      Xplus3(:,i)=Xminus3+K3*(Y-H3*Xminus3);
      Pplus3=(eye(3)-K3*H3)*Pminus3;
end

plot(t,x,'r',t,y,'g',t,Xplus1(1,:),'b',t,Xplus2(1,:),'k',t,Xplus3(1,:),'m','LineWidth',2); 
```

# 拓展卡尔曼滤波

```matlab
%EKF代码
%x(k)=sin(3*x(k-1))
%y(k)=x(k)^2
%注意似然概率是多峰分布,具有强烈非线性,当y=4时,不知道x=2还是-2

%%%生成真是信号与观测
t=0.01:0.01:1;
n=length(t);
x=zeros(1,n);
y=zeros(1,n);
x(1)=0.1^2;
for i=2:n
    x(i)=sin(3*x(i-1));
    y(i)=x(i)^2+normrnd(0,0.7);
end

%EKF
Xplus=zeros(1,n);
Pplus=0.1;
Xplus(1)=0.1;
Q=0.1;
R=1;
for i=2:n
    % 对x(k)微分,并赋值给A,代替f的线性化
    A=3*cos(3*Xplus(i-1));
    Xminus=sin(3*Xplus(i-1));
    Pminus=A*Pplus*A'+Q;
    %更新步
    % 用y(k)微分,复制给C,代替h的线性化
    C=2*Xminus;
    K=Pminus*C*inv(C*Pminus*C'+R);
    Xplus(i)=Xminus+K*(y(i)-Xminus^2);
    Pplus=(eye(1)-K*C)*Pminus;
end
% 注意,实际上拓展卡尔曼滤波器,就是把x(k)微分,代替f的线性化,把y(k)微分,代替h的线性化
plot(t,x,'r',t,y,'b',t,Xplus,'m','LineWidth',1)
```

