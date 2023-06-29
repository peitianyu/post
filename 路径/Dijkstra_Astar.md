---
title: Dijkstra与Astar
date: 2022-3-8 12:30:12
categories:
  - 路径
  - path_plan
tags:
  - path_plan
---

# 参考网址:

 [AtsushiSakai/PythonRobotics: Python sample codes for robotics algorithms. (github.com)](https://github.com/AtsushiSakai/PythonRobotics) 

 [基于dijkstra算法，实现路径规划 ](https://github.com/Superone77/AGV_dijkstra) 

 [机器人路径规划、轨迹优化系列课程_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1yT4y1T7Eb?from=search&seid=18009837363580622939&spm_id_from=333.337.0.0) 

 [AStarSearch](https://github.com/sky068/AStarSearch/tree/master/Astar) 

# 算法基本思想

![1646742549_1_.png](https://s2.loli.net/2022/03/08/1SKOmE9rfQBdeU3.png)

# 代码流程

![1646743310_1_.png](https://s2.loli.net/2022/03/08/ao59Z7ADG4E1wYz.png)

# 使用优先级队列实现

```c++
	// 原型:fill (ForwardIterator first, ForwardIterator last, const T& val);
	fill(dist, dist + nV + 1, INF);
	// 原型:priority_queue<Type, Container, Functional>
	// P表示最短距离与定点编号,greater:升序队列,less:降序队列
	priority_queue<P, vector<P>, greater<P> > q;
	dist[s] = 0;
	// 将起点加入优先队列
	q.push(P(0, s));
	while (!q.empty())
	{
		P p = q.top();   //从尚未使用的顶点中找到一个距离最小的顶点,由于是升序队列,所以取出的是最小的
		q.pop(); // 将顶点从优先队列中删除
		int v = p.second; // 取出顶点编号
		if (dist[v] < p.first) // 如果该顶点的距离已经被更新,则跳过
			continue;
        if (v == end) // 如果该顶点是终点,则跳出循环
			break;
		for (int unsigned i = 0; i < G[v].size(); i++){ // 遍历该顶点的所有邻接点
			Edge &e = G[v][i]; // 取出与该顶点相连的边
			int dis = dist[v] + e.cost; // 更新该顶点的距离
			if (dist[e.to] > dis){   // 如果新距离比原来的距离小,则更新
				dist[e.to] = dist[v] + e.cost;
				q.push(P(dist[e.to], e.to));
				G4[v].push_back(e);
			}
			else if (dist[e.to] == dis){
				G4[v].push_back(e);
			}
		}
	}
```

# Astar算法

![1646894719_1_.png](https://s2.loli.net/2022/03/10/FWxtfuIJkzcUpZN.png)

```c++
APoint* CAstar::findWay(APoint *beginPoint, APoint *endPoint,vector< vector<APoint*> >& allPoints)
{
    //传递地图
    _allPoints = allPoints;
    
    _endPoint = endPoint;

    if (_endPoint->type == AType::ATYPE_BARRIER)
    {
        cout<<"终点是障碍"<<endl;
        return nullptr;
    }
    if (*_endPoint == *beginPoint)
    {
        cout<<"起始点相同"<<endl;
        return nullptr;
    }
    
    _openList.push_back(beginPoint);
    beginPoint->type = AType::ATYPE_OPENED;
    // F = G + H
    beginPoint->f = getF(beginPoint);
    //---------
    do
    {
        //获取最小值的节点
        _curPoint = _openList[0];
        _openList.erase(_openList.begin());
        _curPoint->type = AType::ATYPE_CLOSED;
        _closeList.push_back(_curPoint);
        
        if (*_curPoint == *_endPoint)
        {
            cout<<"have find way"<<endl;
            return _curPoint;
        }
        //获取相邻的节点
        vector<APoint*> neVec = getNeighboringPoint(_curPoint);
        for (int i = 0; i<neVec.size(); i++)
        {
            auto tmpoint = neVec[i];
            if (tmpoint->type == AType::ATYPE_CLOSED)
            {
                continue;
            }
            //是否在开放列表里
            if (tmpoint->type != AType::ATYPE_OPENED)
            {
                tmpoint->parent = _curPoint;
                tmpoint->g = _curPoint->g + 10;
                //计算H值
                tmpoint->h = getH(tmpoint);
                //添加到开放列表里
                _openList.push_back(tmpoint);
                tmpoint->type = AType::ATYPE_OPENED;
            }
            else
            {
                //已经在开放列表里
                if (tmpoint->h < _curPoint->h)
                {
                    tmpoint->parent = _curPoint;
                    tmpoint->g = _curPoint->g + 10;
                }
            }
        }
        //排序 F值最小的排在前面
        sort(_openList.begin(), _openList.end(), mySort);
        
    } while (_openList.size()>0);
    
    
    cout<<"---can not find way---"<<endl;
    
    return nullptr;
}
```

