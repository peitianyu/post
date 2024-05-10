---
title: pf
date: 2022-3-1 12:30:12
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
pf简单步骤
 - 预测:xk(i)=f(xk-1(i))+vk vk~N(0,Q)
 - 更新:wk(i)=fk(yk-h(xk(i)))*wk-1(i)
 - 归一化:wk(i)=wk(i)/(求和)wk(i)
 - 重采样:具体看下边重采样部分
pf步骤
 - 给初值x0~N(u,sigma^2)
 - 生成x0(i),w0(i)=1/n
 - 预测步,生成x1(i)=f(x0(i))+v,v为~N(0,Q)随机数,共n个
 - 更新步,设观测值y1,生成w1(i)=fR(y-h(x1(i)))*w0(i)
 - 将w1(i)归一化,w1(i)=w1(i)/(求和(w1(i)))
 - 此时得到新的例子x1(i),新的权重w1(i)
 - 再由预测步生成x2(i)=f(x1(i))+v
 - 在由更新步生成w2(i)=fR(y-h(x1(i)))*w1(i),归一化
 - 如此递归
```

![pf.png](https://s2.loli.net/2022/03/01/ozr8eB9E1Sq6xJA.png)

# 具体重采样算法

![1646098227_1_.png](https://s2.loli.net/2022/03/01/ERDqpHazQAmuvIf.png)

![1646098247.png](https://s2.loli.net/2022/03/01/uplN9aS3nXbQTA2.png)

# 求期望与方差



```txt
Ex = (积分)[(求和)wi*(狄拉克)(x-xi)]dx =(求和)(wi*xi)
Dx = E(x^2)+(Ex)^2 = (求和)(wi*xi^2)+(Ex)^2
```

# 代码

```matlab
%粒子滤波演示代码
%纸上得来终觉浅，绝知此事要躬行
%演示代码，让机器适应人，代码讲究逻辑性可读性
%工程代码，让人适应机器，强调代码运行速度和性能
%x(i)=sin(x(i-1))+5*x(i-1)/(x(i-1)^2+1)+Q
%y(i)=x(i)^2+R

%注意，滤波之前一定要写成状态方程观测方程形式
%xk=f(xk-1)
%大多数情况下，是不能直接得到xk与xk-1的关系的
%x=f(t)
%要将x=f(t)转化为xk=F(xk-1),具体可以看第八讲，还有更精细的模型，先看完第八讲，再看《数值分析》
%第九章《常微分方程的数值解法》，第八讲实际上就是欧拉法，你们可以用改进欧拉法，或者龙格库塔法得到更精细的
%xk=F(xk-1)
%生成100个信号
t=0.01:0.01:1;
x=zeros(1,100);
y=zeros(1,100);
%t纯粹用来画图，注意和第八讲的区别，第八讲有dt这里没有，因为我直接给出了xk与xk-1的关系，所以不需要dt
%给初值
x(1)=0.1;
y(1)=0.01^2;
%生成真实数据与观测数据
for i=2:100
    x(i)=sin(x(i-1))+5*x(i-1)/(x(i-1)^2+1);
    y(i)=x(i)^2+normrnd(0,1);
end

%PF start
%设粒子集合
n=100;
xold=zeros(1,n);
xnew=zeros(1,n);
xplus=zeros(1,100);%xplus用于存放滤波值，就是每一次后验概率的期望
w=zeros(1,n);
%设置x0(i),可以直接在正态分布采样，如果对初值有自信，也可以让所有粒子都相同
for i=1:n
    xold(i)=0.1;
    w(i)=1/n;
end

%PF核心代码
for i=2:100
    %预测步 由x0推出x1
    for j=1:n
        xold(j)=sin(xold(j))+5*xold(j)/(xold(j)^2+1)+normrnd(0,0.1);%Q
    end

    %预测步完毕
    %更新步
    for j=1:n
        % 因为这里是设置的常数
        %w(j)=w(j)*fR(.....)
        %fR=(2*pi*R)^(-0.5)*exp(-((y(i)-xold(j)^2)^2/(2*R)))
        w(j)=exp(-((y(i)-xold(j)^2)^2/(2*0.1)));
    end

    %归一化
    w=w/sum(w);
    %w/sum(w)与k*w/sum(k*w)结果一模一样
    %(2*pi*R)^(-0.5)是常数
    %w(j)，如果每次都重采样，每次w(j)都会被设为1/n，也是常数
    %所以可以将他们去掉
    %重采样
    %N<1/sum(wi^2),若不是每次都重采样，那么代码第54行就要做相应修改，把w(j)加上去

    %生成c,范围为(0~1)
    c=zeros(1,n);
    c(1)=w(1);
    for j=2:n
        c(j)=c(j-1)+w(j);
    end
    %转盘子，生成随机数，看落在哪个区间
    %首先我们要重采样n个粒子，粒子数要与之前相同
    for j=1:n
        a=unifrnd(0,1);%均匀分布随机数
        for k=1:n
            if (a<c(k))
                xnew(j)=xold(k);
                break;%%%%一定要break，否则重采样粒子会被最后一个粒子覆盖，具体见新的第十讲
            end
        end
    end
    %重采样完毕
    %把新的粒子赋值给xold，为下一步递推做准备
    xold=xnew;
    %所有粒子权重都设为1/n
    for j=1:n
        w(j)=1/n;
    end
    %把每一步的后验概率期望赋值给xplus
    xplus(i)=sum(xnew)/n;    
end
plot(t,x,'r',t,xplus,'b','LineWidth',1)
%y=x^2+R 似然概率是一个多峰分布，y=4，x=2或-2
%非线性问题一步一个坑，处处是雷
%如果问题本身的性质就是强烈的非线性，比如多峰分布这种，粒子滤波并不能化腐朽为神奇
%尽量不要有多峰分布
%粒子滤波的计算速度是大硬伤
%可以尝试没有重采样的粒子滤波代码会是什么样的，自己尝试写写，自己调调参数 
%主要需要调Q,R,重采样,粒子数目

```

# c++实现

```



struct Particle
{
	Pose2D pos;
	float score;
}

enum Status
{
	INIT,
	RUNNING,
	SUCCESS,
	FAIL
}


class ParticleFilter
{

	struct Option
	{
		int particle_num = 100;
	}

	void Process()
	{
		if(m_status == INIT)
		{
			m_particles = GenerateParticle();
			m_status = RUNING;
		}

		if(m_status == RUNING)
		{
			bool is_converge = false;
			Propagate(is_converge);

			if(is_converge)
			{
				m_status = SUCCESS;
				m_out_particle = BestParticle(m_particles);
			}
			else
			{
				if(IsTimeOut())
					m_status = FAIL;

				if(IsTooLowScore() || IsSizeTooSmall())
					ResampleParticles();
			}
		}
	}

	void Propagate(bool& is_converge)
	{
		UpdateParticlePose();

		UpdateParticleScore();

		is_converge = CheckConverge();
	}

	void UpdateParticlePose()
	{
		// 这部分根据里程更新
	}

	void UpdateParticleScore()
	{
		// 这一步根据与周围环境匹配度更新
	}

	void ResampleParticles()
	{
		// 这部分根据粒子分布更新
	}


private:
	Status m_status;
	Option m_option;
	std::vector<Particle> m_particles;
	Particle m_out_particle;
}
```

