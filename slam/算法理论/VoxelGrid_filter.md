---
title: VoxelGrid_filter
date: 2022-4-27 12:30:12
categories:
  - slam
  - 理论学习
tags:
  - slam
---

# 参考网址:

[(129条消息) 体素栅格对点云下采样（从基础写起）——源码解析C++_weixin_44494725的博客-CSDN博客_体素栅格](https://blog.csdn.net/weixin_44494725/article/details/103156435?ops_request_misc=%7B%22request%5Fid%22%3A%22165106602716781483792182%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=165106602716781483792182&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~baidu_landing_v2~default-1-103156435.nonecase&utm_term=体素滤波+c%2B%2B&spm=1018.2226.3001.4450)

[nestormh/voxel_odometry: Voxel grid and particle filter based approach for estimating odometry. ](https://github.com/nestormh/voxel_odometry)

# 简介

所谓**下采样**是对点云的一种滤波，目的是减少点云数目,并尽可能保持点云原有形状，从而减少计算量。
所谓**体素栅格**是将点云空间划分为一个个极小的格子，格子里包含几个点。
对体素栅格里面的点取平均或者加权平均，得到一个点，代替原来的几个点。
显然，栅格选的越大，滤波后点云点数越少，速度快，但是会对原来的点云过度模糊;
栅格选的越小，作用反之。

# 代码

```c++
Mat samplePCByQuantization(Mat pc, float xrange[2], float yrange[2], float zrange[2], float sampleStep, int weightByCenter)
{
  std::vector< std::vector<int> > map;
//map.size()为体素栅格个数
//map[i].size()为当前体素栅格包含的点数，里面存的是pc的点索引
  int numSamplesDim = (int)(1.0/sampleStep);
//numSamplesDim：采样个数。sampleStep：相对采样长度
//sampleStep×D=实际采样距离
//同时，D/实际采样距离=采样个数=numSamplesDim
  float xr = xrange[1] - xrange[0];//包围盒子的边长
  float yr = yrange[1] - yrange[0];
  float zr = zrange[1] - zrange[0];

  int numPoints = 0;

  map.resize((numSamplesDim+1)*(numSamplesDim+1)*(numSamplesDim+1));
//此处resize的是map的size，表示体素栅格的个数
//map.size():体素栅格的个数

  // OpenMP might seem like a good idea, but it didn't speed this up for me
  //#pragma omp parallel for
  for (int i=0; i<pc.rows; i++)//遍历pc中每一个点i
  {
    const float* point = pc.ptr<float>(i);
//定义指针* point指向pc的第i个元素（六维）
    // quantize a point
//  xr/numSamplesDim=xr×sampleStep=实际采样距离
//(point[0]-xrange[0])/实际采样距离：代表当前坐标point[0]在第(int) 几个采样距离处
    const int xCell =(int) ((float)numSamplesDim*(point[0]-xrange[0])/xr);
    const int yCell =(int) ((float)numSamplesDim*(point[1]-yrange[0])/yr);
    const int zCell =(int) ((float)numSamplesDim*(point[2]-zrange[0])/zr);
    const int index = xCell*numSamplesDim*numSamplesDim+yCell*numSamplesDim+zCell;

    /*#pragma omp critical
        {*/
    map[index].push_back(i);//把具有相同xCell、yCell、zCell的点i存到同一个索引index下面
//此时，map就是一个个体素栅格，里面包含了若干个点
    //  }
  }

  for (unsigned int i=0; i<map.size(); i++)
  {
    numPoints += (map[i].size()>0);
  }
//把map[i]中所有的点数目统计起来，就是pc的总点数
  Mat pcSampled = Mat(numPoints, pc.cols, CV_32F);//cols代表x,y,z,nx,ny,nz六列
  int c = 0;

  for (unsigned int i=0; i<map.size(); i++)//对每一个体素栅格
  {
    double px=0, py=0, pz=0;//下采样后的坐标
    double nx=0, ny=0, nz=0;//下采样后的法线

    std::vector<int> curCell = map[i];//curCell：向量包含当前体素栅格的所有点
//map[i]里存的是PC中点的索引（int）
    int cn = (int)curCell.size();//cn:每个体素栅格包含的点数
    if (cn>0)//因为map[i]可能为空
    {
      if (weightByCenter)//体素栅格求重心
      {
        int xCell, yCell, zCell;//第几个cell
        double xc, yc, zc;//体素栅格的质心坐标
        double weightSum = 0 ;
        zCell = i % numSamplesDim;//这个i是map的index，
//index = xCell*numSamplesDim*numSamplesDim+yCell*numSamplesDim+zCell;
        yCell = ((i-zCell)/numSamplesDim) % numSamplesDim;
        xCell = ((i-zCell-yCell*numSamplesDim)/(numSamplesDim*numSamplesDim));//第几个cell
//0.5 * (double)xr/numSamplesDim：半个格子的长度
//(double)xr/numSamplesDim：一个格子的长度
//xCell* (double)xr/numSamplesDim 前面所有整数个格子的长度
        xc = ((double)xCell+0.5) * (double)xr/numSamplesDim + (double)xrange[0];//xc：体素栅格的重心坐标x
        yc = ((double)yCell+0.5) * (double)yr/numSamplesDim + (double)yrange[0];
        zc = ((double)zCell+0.5) * (double)zr/numSamplesDim + (double)zrange[0];

        for (int j=0; j<cn; j++)//体素栅格中每一点
        {
          const int ptInd = curCell[j];//ptInd是pc中的点的索引
          float* point = pc.ptr<float>(ptInd);
          const double dx = point[0]-xc;
          const double dy = point[1]-yc;
          const double dz = point[2]-zc;//计算体素栅格中每一点与质心的距离：d
          const double d = sqrt(dx*dx+dy*dy+dz*dz);
          double w = 0;

          if (d>EPS)
          {
            // it is possible to use different weighting schemes.
            // inverse weigthing was just good for me
            // exp( - (distance/h)**2 )
            //const double w = exp(-d*d);
            w = 1.0/d;//由d计算权重，inverse weigthing
          }

          //float weights[3]={1,1,1};
          px += w*(double)point[0];
          py += w*(double)point[1];
          pz += w*(double)point[2];
          nx += w*(double)point[3];
          ny += w*(double)point[4];
          nz += w*(double)point[5];

          weightSum+=w;
        }
        px/=(double)weightSum;
        py/=(double)weightSum;
        pz/=(double)weightSum;
        nx/=(double)weightSum;
        ny/=(double)weightSum;
        nz/=(double)weightSum;
      }

//在一个体素栅格中：px=（x1*1/d1+x2*1/d2+...+xcn1/dcn）/（1/d1+1/d2+...1/dcn）
//py，pz同理
//得到加权质心
      else
      {//普通求平均的质心
        for (int j=0; j<cn; j++)
        {
          const int ptInd = curCell[j];
          float* point = pc.ptr<float>(ptInd);

          px += (double)point[0];
          py += (double)point[1];
          pz += (double)point[2];
          nx += (double)point[3];
          ny += (double)point[4];
          nz += (double)point[5];
        }

        px/=(double)cn;
        py/=(double)cn;
        pz/=(double)cn;
        nx/=(double)cn;
        ny/=(double)cn;
        nz/=(double)cn;

      }
//定义指针pcData指向pcSampled的第c个元素
      float *pcData = pcSampled.ptr<float>(c);
      pcData[0]=(float)px;
      pcData[1]=(float)py;
      pcData[2]=(float)pz;

      // 法向量标准化
      double norm = sqrt(nx*nx+ny*ny+nz*nz);

      if (norm>EPS)
      {
        pcData[3]=(float)(nx/norm);
        pcData[4]=(float)(ny/norm);
        pcData[5]=(float)(nz/norm);
      }
      //#pragma omp atomic
      c++;

      curCell.clear();//清空当前体素栅格
    }
  }

  map.clear();
  return pcSampled;
}
```

二维体素滤波

```c++
LaserScan Voxelfilter(LaserScan scan, float Resolution)
{
    float min_x = -10.0;
    float min_y = -10.0;
    float max_y = 10.0;

    int lenY = (int)(std::round((max_y - min_y) / Resolution));
    std::map<int,int> rebuf;
    LaserScan result;
    for(auto&point:scan)
    {
        int x = point.x();
        int y = point.y();
        // 仅采用4m内scan数据
        float dis = sqrt(x*x + y*y);
        if(dis > 4.0)
            continue;
        int xGrid = (int)(std::round((point.x - min_x) / Resolution));
        int yGrid = (int)(std::round((point.y - min_y) / Resolution));
        int index = yGrid * lenY + xGrid;
        if(!rebuf[index])
        {
            rebuf[index] = 1;
            result.push_back(point);
        }
    }
    return result;
}
```

