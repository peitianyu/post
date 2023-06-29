---
title: jps
date: 2022-3-18 12:30:12
categories:
  - 路径
  - path_plan
tags:
  - path_plan
---

# 参考网址:

 [(116条消息) JPS算法_Jason.Li_0012的博客-CSDN博客_jps 算法](https://blog.csdn.net/weixin_45929038/article/details/123133488?ops_request_misc=%7B%22request%5Fid%22%3A%22164776742216780265425970%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=164776742216780265425970&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-8-123133488.142^v2^article_score_rank,143^v4^control&utm_term=jps算法&spm=1018.2226.3001.4187) 

 [运动规划_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1Et4y167Ak?p=7) 

 [YYRise/jps: python实现跳点寻路算法（JPS：Jump Point Search） (github.com)](https://github.com/YYRise/jps) 

 [最快速的寻路算法 Jump Point Search - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/290924212) 

# 算法流程

![v2-b2096f0c3630d59238a4d51300c2b8aa_720w.jpg](https://s2.loli.net/2022/03/20/k6nXTPCDLwoVuYc.jpg)

与A*算法流程完全一样,只是对于邻接节点的定义不同, *A*∗算法的邻居节点为几何意义上的邻居，而Jps算法的邻居节点为跳跃所得的邻居。 

```txt
开始
	将起点放入open list中
	while true
		if open list 为空
			搜索失败,结束
		取open list中最小节点
		if 节点为终点
			找到路径,结束
		拓展该节点子节点
		将节点放入closed list中
结束
```

# 修剪邻居节点与跳转规则

![1647822945_1_.png](https://s2.loli.net/2022/03/21/zGTZnNfD8s4yQqa.png)

# jps代码

```python
# 查找路径的入口函数
def find_path(self, s_pos, e_pos):
    self.s_pos, self.e_pos = s_pos, e_pos
    # 构建开始节点
    p = Node(None, self.s_pos, 0, abs(self.s_pos[0]-self.e_pos[0]) + abs(self.s_pos[1]-self.e_pos[1]))
    self.open.append(p)
    while True:
        # 扩展F值最小的节点

        # 如果开放列表为空，则不存在路径，返回
        if not self.open:
            return "not find"
        # 获取F值最小的节点
        idx, p = self.get_min_f_node()
        print("find path with extend_round(%d, %d), open_list = %s" % (p.pos[0], p.pos[1], [[n.pos[0], n.pos[1]] for n in self.open]))
        # 找到路径，生成路径，返回
        if self.is_target(p):
            self.make_path(p)
            return
        # 扩展该节点的子节点
        self.extend_round(p)
        # 把此节点压入关闭列表，并从开放列表里删除
        self.close.append(p)
        del self.open[idx]
```

```python
# 扩展该节点的子节点
def extend_round(self, c):
    # 修剪邻近节点
    nbs = self.prune_neighbours(c)
    print "************[%d, %d] --- %s, parent = [%d, %d]" % (c.pos[0], c.pos[1], nbs, c.pos[0], c.pos[1])
    for n in nbs:
        jp = self.jump_node(n,[c.pos[0], c.pos[1]])# 跳跃节点
        print "expandSuccessors:parent = %s, nb = %s, jp = %s" % ([c.pos[0], c.pos[1]], n, jp)
        if jp:
            if self.node_in_close(jp):
                continue
            g = self.get_g(jp, c.pos)
            h = self.get_h(jp, self.e_pos)
            node = Node(c, jp, c.g + g, h)
            i = self.node_in_open(node)
            if i != -1:
                # 新节点在开放列表
                if self.open[i].g > node.g:
                    # 现在的路径到比以前到这个节点的路径更好~
                    # 则使用现在的路径
                    self.open[i].parent = c
                    self.open[i].g = node.g
                    self.open[i].f = node.g + self.open[i].h
                continue
            self.open.append(node)
```

```python
# 修剪邻居节点
def prune_neighbours(self, c):
        nbs = []
         # 不是起始点
        if c.parent:
            # 进入的方向
            dir = c.get_direction()
            if self.is_pass(c.pos[0] + dir[0], c.pos[1] + dir[1]):
                nbs.append([c.pos[0] + dir[0], c.pos[1] + dir[1]])
            print "dir = ", dir
             # 对角线行走; eg:右下(1, 1)
            if dir[0] != 0 and dir[1] != 0:
                # 下（0， 1）
                if self.is_pass(c.pos[0], c.pos[1] + dir[1]):
                    nbs.append([c.pos[0], c.pos[1] + dir[1]])
                 # 右（1， 0）
                if self.is_pass(c.pos[0]+dir[0], c.pos[1]):
                    nbs.append([c.pos[0]+dir[0], c.pos[1]])
                 # 左不能走且下可走
                if not self.is_pass(c.pos[0] - dir[0], c.pos[1]) and 
                	self.is_pass(c.pos[0], c.pos[1] + dir[1]):
                    # 左下（-1， 1）
                    nbs.append([c.pos[0] - dir[0], c.pos[1] + dir[1]])
                 # 上不能走且右可走
                if not self.is_pass(c.pos[0], c.pos[1]-dir[1]) and 
                	self.is_pass(c.pos[0]+dir[0], c.pos[1]):
                    # 右上（1， -1）
                    nbs.append([c.pos[0]+dir[0], c.pos[1]-dir[1]])
            else:  # 直行
                # 垂直走
                if dir[0] == 0:
                     # 右不能走
                    if not self.is_pass(c.pos[0]+1, c.pos[1]):
                        # 右下
                        nbs.append([c.pos[0]+1, c.pos[1]+dir[1]])
                     # 左不能走
                    if not self.is_pass(c.pos[0]-1, c.pos[1]):
                        # 左下
                        nbs.append([c.pos[0]-1, c.pos[1]+dir[1]])

                else:  # 水平走，向右走为例                     
                     # 下不能走
                    if not self.is_pass(c.pos[0], c.pos[1]+1):
                         # 右下
                         nbs.append([c.pos[0]+dir[0], c.pos[1]+1])
                     # 上不能走
                    if not self.is_pass(c.pos[0], c.pos[1]-1):
                         # 右上
                         nbs.append([c.pos[0]+dir[0], c.pos[1]-1])

        else:
            for d in g_dir:
                if self.is_pass(c.pos[0] + d[0], c.pos[1] + d[1]):
                    nbs.append([c.pos[0] + d[0], c.pos[1] + d[1]])
        print "prune_neighbours c= %s, nbs = %s" % ([c.pos[0], c.pos[1]], nbs)
        return nbs
```

```python
# ↑ ↓ ← → ↖ ↙ ↗ ↘
    def jump_node(self, now, pre):
        dir = [a != b and (a - b)/abs(a-b) or 0 for a, b in zip(now, pre)]
        print "now = %s, pre = %s, dir = %s" %(now, pre, dir)        

        if now == self.e_pos:
            return now

        if self.is_pass(now[0], now[1]) is False:
            return None
        if dir[0] != 0 and dir[1] != 0:
            # 左下能走且左不能走，或右上能走且上不能走
            if (self.is_pass(now[0] - dir[0], now[1] + dir[1]) and not 
                self.is_pass(now[0]-dir[0], now[1])) or (self.is_pass(now[0] + dir[0],
                now[1] - dir[1]) and not self.is_pass(now[0], now[1]-dir[1])):
                return now
        else:
            # 水平方向
            if dir[0] != 0:
                # 右下能走且下不能走， 或右上能走且上不能走
                '''
                * 1 0       0 0 0
                0 → 0       0 0 0
                * 1 0       0 0 0
                '''
                print '水平方向:', self.is_pass(now[0] + dir[0], now[1] + 1), 
                	self.is_pass(now[0], now[1]+1), self.is_pass(now[0] + dir[0], now[1] 					 - 1), self.is_pass(now[0], now[1]-1)
                if (self.is_pass(now[0] + dir[0], now[1] + 1) and not 
                    self.is_pass(now[0], now[1]+1)) or (self.is_pass(now[0] + dir[0], 
                    now[1] - 1) and not self.is_pass(now[0], now[1]-1)):
                    return now
            else: # 垂直方向
                # 右下能走且右不能走，或坐下能走且左不能走
                '''
                0 0 0
                1 ↓ 1
                0 0 0            
                '''
                print '垂直方向:', self.is_pass(now[0] + 1, now[1] + dir[1]), 
                	self.is_pass(now[0] + 1, now[1]), self.is_pass(now[0]-1 , now[1] + 
                    dir[1]), self.is_pass(now[0] - 1, now[1])
                if (self.is_pass(now[0] + 1, now[1] + dir[1]) and not 
                    self.is_pass(now[0]+1, now[1])) or (self.is_pass(now[0] - 1, now[1] +
                    dir[1]) and not self.is_pass(now[0]-1, now[1])):
                    return now

        if dir[0] != 0 and dir[1] != 0:
            t1 = self.jump_node([now[0]+dir[0], now[1]], now)
            t2 = self.jump_node([now[0], now[1] + dir[1]], now)
            if t1 or t2:
                return now
        if self.is_pass(now[0] + dir[0], now[1]) or self.is_pass(now[0], now[1] +
            dir[1]):
            t = self.jump_node([now[0] + dir[0], now[1] + dir[1]], now)
            if t:
                return t

        return None
```

