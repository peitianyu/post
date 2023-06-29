---
title: pf重采样
date: 2022-5-20 12:30:12
categories:
  - slam
  - 理论学习
tags:
  - slam
---

# 参考网址:

 [(134条消息) 粒子滤波重采样_cjn_的博客-CSDN博客_粒子滤波重采样](https://blog.csdn.net/HERO_CJN/article/details/93458674?ops_request_misc=%7B%22request%5Fid%22%3A%22165302272316782246460201%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=165302272316782246460201&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-93458674-null-null.142^v10^pc_search_result_control_group,157^v4^control&utm_term=低方差重采样&spm=1018.2226.3001.4187) 

 [softdream/MCL_test: Monte Carlo Localization( MCL ) (github.com)](https://github.com/softdream/MCL_test) 

 [无处不在的小土-pr_chapter4 (gaoyichao.com)](https://gaoyichao.com/Xiaotu/?book=probabilistic_robotics&title=pr_chapter4) 

# 简介

重采样这里提两种方式:轮盘赌与低方差重采样

低方差重采样优点在于仅用单一随机数实现在X集合中进行采样

# 轮盘赌重采样

![轮盘赌重采样.png](https://s2.loli.net/2022/05/20/43idRPbjh8pG6ky.png)

```matlab
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
```

# 低方差重采样

![低方差重采样.png](https://s2.loli.net/2022/05/20/nUtkWRL17gNQilJ.png)

## 代码

```c
void lowVarianceSample()
	{	
		std::vector<Particle> particles_temp( particles, particles + ParticleNum );

		float r = ( rand() / (float)RAND_MAX) * (1.0f / (float)ParticleNum ); 

		float c = particles[0].getWeight();

		int i = 0;

		for (int m = 0; m < ParticleNum; m ++) {
			float u = r + (float)m / (float)ParticleNum ;
 		
			while (u > c && i < ParticleNum - 1){ 
				i++;				
				c += particles_temp[i].getWeight();	
			}
			particles[ m ] = particles_temp[i]; 	 
			particles[ m ].setWeight( 1.0f / ParticleNum );
		
			//std::cout << " each weight is : " << particles[m].getWeight() << std::endl;
		}	
	}
```

