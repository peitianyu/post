---
title: kd树
date: 2022-2-24 12:30:12
categories:
  - slam
  - 理论学习
tags:
  - slam
---

# 参考网址

 [学生视频-KD树_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1d5411w7f5/?spm_id_from=333.788.recommend_more_video.0) 

 [(110条消息) 详解KDTree_爱冒险的技术宅-CSDN博客_kdtree](https://blog.csdn.net/silangquan/article/details/41483689?ops_request_misc=%7B%22request%5Fid%22%3A%22164559383916780269851564%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164559383916780269851564&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~top_positive~default-1-41483689.nonecase&utm_term=kdtree&spm=1018.2226.3001.4450) 

 [(110条消息) 【PCL模块解析 05 之KDTree】01 KDTree原理及代码解析_水亦心的博客-CSDN博客](https://blog.csdn.net/shuiyixin/article/details/88988069?ops_request_misc=&request_id=&biz_id=102&utm_term=kdtree&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~sobaiduweb~default-2-88988069.nonecase&spm=1018.2226.3001.4450) 

 [KD树 - Earendil - 博客园 (cnblogs.com)](https://www.cnblogs.com/earendil/p/8135074.html) 

amcl内部也存在kdtree,可以i拿来看看

# 算法流程

分为生成kdtree与搜索树两部分

## 结构体

```c++
struct data{    
    double x = 0;    
    double y = 0;    
};       
struct Tnode{    
    struct data dom_elt;    
    int split;    
    struct Tnode * left;    
    struct Tnode * right;    
};    
```

## 生成kdtree

- **多所有数据的各个维度做方差**
- **选择方差大的方向进行,排序选择中位数作为分割点,并保存分割维度**
- **然后在该维度判断大小,小的放在左侧,大的放在右侧**
- **保存左右节点**
- **然后递归**

## 搜索树

- **如果Kd是空的，则设dist为无穷大返回**   
- **判断分割维度,判断进入左树还是右树,递归,最终找到最接近的叶子节点**
- **判断目标点与叶子节点距离是否大于目标点到超平面距离,若不大于,则叶子节点为最终最接近点,否则回溯上一父节点**
- **比较父节点空间另一叶子节点,若距离最短,则为最近点**

# 代码

```c++
#include <iostream>    
#include <algorithm>    
#include <stack>    
#include <math.h>    
using namespace std;    
/*function of this program: build a 2d tree using the input training data  
 the input is exm_set which contains a list of tuples (x,y)  
 the output is a 2d tree pointer*/    
    
    
struct data    
{    
    double x = 0;    
    double y = 0;    
};    
    
struct Tnode    
{    
    struct data dom_elt;    
    int split;    
    struct Tnode * left;    
    struct Tnode * right;    
};    
    
bool cmp1(data a, data b){    
    return a.x < b.x;    
}    
    
bool cmp2(data a, data b){    
    return a.y < b.y;    
}    
    
bool equal(data a, data b){    
    if (a.x == b.x && a.y == b.y)    
    {    
        return true;    
    }    
    else{    
        return false;    
    }    
}    
// 调用ChooseSplit函数选择分割维度和分割点
void ChooseSplit(data exm_set[], int size, int &split, data &SplitChoice){    
    /*compute the variance on every dimension. Set split as the dismension that have the biggest variance. 
    Then choose the instance which is the median on this split dimension.*/    
    // 计算每个维度的方差。将split设置为方差最大的维度。然后选择实例，它是这个分割维度上的中值。
    /*compute variance on the x,y dimension. DX=EX^2-(EX)^2*/  
    // 数据方差大表明沿该坐标轴方向上的数据分散得比较开，在这个方向上进行数据分割有较好的分辨率；  
    double tmp1,tmp2;    
    tmp1 = tmp2 = 0;    
    for (int i = 0; i < size; ++i)    
    {    
        tmp1 += 1.0 / (double)size * exm_set[i].x * exm_set[i].x;    
        tmp2 += 1.0 / (double)size * exm_set[i].x;    
    }    
    double v1 = tmp1 - tmp2 * tmp2;  //compute variance on the x dimension    
        
    tmp1 = tmp2 = 0;    
    for (int i = 0; i < size; ++i)    
    {    
        tmp1 += 1.0 / (double)size * exm_set[i].y * exm_set[i].y;    
        tmp2 += 1.0 / (double)size * exm_set[i].y;    
    }    
    double v2 = tmp1 - tmp2 * tmp2;  //compute variance on the y dimension    
        
    split = v1 > v2 ? 0:1; //set the split dimension    
        
    if (split == 0)    
    {    
        // sort排序规则：按照x坐标排序
        sort(exm_set,exm_set + size, cmp1);    
    }    
    else{    
        sort(exm_set,exm_set + size, cmp2);    
    }    
        
    //set the split point value 
    // 设置分割点值,取中值  
    SplitChoice.x = exm_set[size / 2].x;    
    SplitChoice.y = exm_set[size / 2].y;    
        
}    
    
Tnode* build_kdtree(data exm_set[], int size, Tnode* T){    
    // call function ChooseSplit to choose the split dimension and split point    
    if (size == 0){    
        return NULL;    
    }    
    else{ 
        // 若为0则按x维切割，否则按y维切割 
        int split; 
        // 选择实例  
        data dom_elt;    
        // 调用ChooseSplit函数选择分割维度和分割点
        ChooseSplit(exm_set, size, split, dom_elt); 
        data exm_set_right [100];    
        data exm_set_left [100];    
        int sizeleft ,sizeright;    
        sizeleft = sizeright = 0;    
            
        if (split == 0)    
        {    
            for (int i = 0; i < size; ++i)    
            {    
                // 判断是否等于分割点，若小于于则放入左子树，若大于则放入右子树,否则不做处理
                if (!equal(exm_set[i],dom_elt) && exm_set[i].x <= dom_elt.x)    
                {    
                    exm_set_left[sizeleft].x = exm_set[i].x;    
                    exm_set_left[sizeleft].y = exm_set[i].y;    
                    sizeleft++;    
                }    
                else if (!equal(exm_set[i],dom_elt) && exm_set[i].x > dom_elt.x)    
                {    
                    exm_set_right[sizeright].x = exm_set[i].x;    
                    exm_set_right[sizeright].y = exm_set[i].y;    
                    sizeright++;    
                }    
            }    
        }    
        else{    
            for (int i = 0; i < size; ++i)    
            {    
                    
                if (!equal(exm_set[i],dom_elt) && exm_set[i].y <= dom_elt.y)    
                {    
                    exm_set_left[sizeleft].x = exm_set[i].x;    
                    exm_set_left[sizeleft].y = exm_set[i].y;    
                    sizeleft++;    
                }    
                else if (!equal(exm_set[i],dom_elt) && exm_set[i].y > dom_elt.y)    
                {    
                    exm_set_right[sizeright].x = exm_set[i].x;    
                    exm_set_right[sizeright].y = exm_set[i].y;    
                    sizeright++;    
                }    
            }    
        }    
        T = new Tnode;    
        T->dom_elt.x = dom_elt.x;    
        T->dom_elt.y = dom_elt.y;    
        T->split = split;    
        // 递归
        T->left = build_kdtree(exm_set_left, sizeleft, T->left);    
        T->right = build_kdtree(exm_set_right, sizeright, T->right);    
        return T;       
    }    
}    
    
// 计算欧式距离   
double Distance(data a, data b){    
    double tmp = (a.x - b.x) * (a.x - b.x) + (a.y - b.y) * (a.y - b.y);    
    return sqrt(tmp);    
}    
    
// 寻找最近点
void searchNearest(Tnode * Kd, data target, data &nearestpoint, double & distance){    
        
    //1. 如果Kd是空的，则设dist为无穷大返回    
    //2. 向下搜索直到叶子结点    
        
    stack<Tnode*> search_path;    
    Tnode* pSearch = Kd;    
    data nearest;    
    double dist;    
        
    while(pSearch != NULL)    
    {    
        //pSearch加入到search_path中; 
        // stack容器 - 先进后出
        // 一开始放入根结点, 然后根据条件放入左右子树 
        // 支持在这里进行push(), pop(), top()操作  
        search_path.push(pSearch);    
            
        if (pSearch->split == 0)    
        {    
            if(target.x <= pSearch->dom_elt.x) /* 如果小于就进入左子树 */    
            {    
                pSearch = pSearch->left;    
            }    
            else    
            {    
                pSearch = pSearch->right;    
            }    
        }    
        else{    
            if(target.y <= pSearch->dom_elt.y) /* 如果小于就进入左子树 */    
            {    
                pSearch = pSearch->left;    
            }    
            else    
            {    
                pSearch = pSearch->right;    
            }    
        }    
    }    
    //取出search_path最后一个赋给nearest    
    nearest.x = search_path.top()->dom_elt.x;    
    nearest.y = search_path.top()->dom_elt.y; 
    // printf("x=%f, y=%f\n", nearest.x, nearest.y);
    // 弹出pSearch的top   
    search_path.pop();    
        
        
    dist = Distance(nearest, target);    // 计算找到的叶节点与目标点的欧式距离
    //3. 回溯搜索路径    
        
    Tnode* pBack;    
        
    while(search_path.size() != 0)    
    {    
        //取出search_path最后一个结点赋给pBack    
        // 回溯到上一层节点
        pBack = search_path.top();    
        search_path.pop();    
            
        if(pBack->left == NULL && pBack->right == NULL) /* 如果pBack为叶子结点 */    
        {       
            // 如果pBack为叶子结点，则计算pBack与目标点的欧式距离，并与nearestpoint比较，如果更小，则更新nearestpoint
            if( Distance(nearest, target) > Distance(pBack->dom_elt, target) )    
            {    
                nearest = pBack->dom_elt;    
                dist = Distance(pBack->dom_elt, target);    
            }    
        }     
        else           
        {    
            // 定义s为分割线
            int s = pBack->split;
            // 为0时，分割线为x轴
            if (s == 0)    
            {    
                if( fabs(pBack->dom_elt.x - target.x) < dist) /* 如果以target为中心的圆（球或超球），半径为dist的圆与分割超平面相交， 那么就要跳到另一边的子空间去搜索 */    
                {    
                    if( Distance(nearest, target) > Distance(pBack->dom_elt, target) )    
                    {    
                        nearest = pBack->dom_elt;    
                        dist = Distance(pBack->dom_elt, target);    
                    }    
                    if(target.x <= pBack->dom_elt.x) /* 如果target位于pBack的左子空间，那么就要跳到右子空间去搜索 */    
                        pSearch = pBack->right;    
                    else    
                        pSearch = pBack->left; /* 如果target位于pBack的右子空间，那么就要跳到左子空间去搜索 */    
                    if(pSearch != NULL)    
                        //pSearch加入到search_path中    
                        search_path.push(pSearch);    
                }    
            }    
            else {    
                //  如果以target为中心的圆（球或超球），半径为dist的圆与分割超平面相交
                if( fabs(pBack->dom_elt.y - target.y) < dist) /* 如果以target为中心的圆（球或超球），半径为dist的圆与分割超平面相交， 那么就要跳到另一边的子空间去搜索 */    
                {    
                    // 若找到的最近点与目标点距离大于上层父节点,则将父节点变为最近节点
                    if( Distance(nearest, target) > Distance(pBack->dom_elt, target) )    
                    {    
                        nearest = pBack->dom_elt;    
                        dist = Distance(pBack->dom_elt, target);    
                    }    
                    if(target.y <= pBack->dom_elt.y) /* 如果target位于pBack的左子空间，那么就要跳到右子空间去搜索 */    
                        pSearch = pBack->right;    
                    else    
                        pSearch = pBack->left; /* 如果target位于pBack的右子空间，那么就要跳到左子空间去搜索 */    
                    if(pSearch != NULL)    
                       // pSearch加入到search_path中    
                        search_path.push(pSearch);    
                }    
            }    
                
        }    
    }       
    nearestpoint.x = nearest.x;    
    nearestpoint.y = nearest.y;    
    distance = dist;    
        
}    
    
int main(){    
    // x,y
    data exm_set[100]; //assume the max training set size is 100    
    double x,y;    
    int id = 0;    
    cout<<"Please input the training data in the form x y. One instance per line. Enter -1 -1 to stop."<<endl;    
    while (cin>>x>>y){    
        if (x == -1)    
        {    
            break;    
        }    
        else{    
            exm_set[id].x = x;    
            exm_set[id].y = y;    
            id++;    
        }    
    }    
    struct Tnode * root = NULL;    
    root = build_kdtree(exm_set, id, root);    
        
    data nearestpoint;    
    double distance;    
    data target;    
    cout <<"Enter search point"<<endl;    
    while (cin>>target.x>>target.y)    
    {    
        searchNearest(root, target, nearestpoint, distance);    
        cout<<"The nearest distance is "<<distance<<",and the nearest point is "<<nearestpoint.x<<","<<nearestpoint.y<<endl;    
        cout <<"Enter search point"<<endl;    
    
    }    
}  
```

