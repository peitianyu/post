# 重新理解Dijkstra与Astar

## 理解

```
原理上两者是相同的, 一种深度优先的算法, 找到cost最低的node, 然后在这个node上继续拓展, 直到找到目标点.
Astar的优化点在与更新cost时会加上启发函数(用来增大预估权重)
```

## 以下是代码辅助理解

```
// 节点
struct Node
{
    Point pos;
    float g;
    float h;
    Node *pre_node;

    Node(Point _pos)
        : pos(_pos), g(0), h(0), pre_node(nullptr)
    {}

    Node(Point _pos, float _g, float _h, Node *_pre_node)
        : pos(_pos), g(_g), h(_h), pre_node(_pre_node)
    {
        g += pre_node->g;
    }

    bool cmp(Point new_pos)
    {
        return (pos.x == new_pos.x) && (pos.y == new_pos.y);
    }

    float SumCost() const
    {
        return g + h;
    }

    std::vector<Point> Path() 
    {
        std::vector<Point> path;
        Node *curr_node = this;
        while (curr_node != NULL) {
            path.push_back(curr_node->pos);
            curr_node = curr_node->pre_node;
        }
        std::reverse(path.begin(), path.end());
        return path;
    }
};

std::vector<Point> Astar::FindShorestPathFunc(const Point &start, const Point &dest) 
{
    if(!VerifyNode(start) || !VerifyNode(dest)) return std::vector<Point>();

    std::set<Point> closed_list;

    auto cmp = [](const Node *left, const Node *right) { return left->SumCost() > right->SumCost(); };
    std::priority_queue<Node *, std::vector<Node *>, decltype(cmp)> open_list(cmp);

    open_list.push(new Node(start)); // 给一个起点
    while (!open_list.empty())
    {
        Node *curr_node = open_list.top();
        open_list.pop();

        // 找到目标节点, 返回路径
        if (curr_node->cmp(dest)) 
        {
            std::cout << "closed_list.size(): " << closed_list.size() << std::endl;
            return curr_node->Path();
        }
           
        // 更新closed_list
        if(closed_list.find(curr_node->pos) != closed_list.end())   continue;  
        else                                                closed_list.insert(curr_node->pos);        

        // 更新open_list
        static std::vector<Node> movements = GetMotionModel(); // 四近邻/八近邻
        for (const Node &movement : movements)
        {
            Point new_pos = Point(curr_node->pos.x + movement.pos.x, curr_node->pos.y + movement.pos.y);
            
            // 若节点已经被探索过或者是障碍物则跳过
            if (!VerifyNode(new_pos)) continue;
               
            // 将更新后的节点加入openList
            // open_list.push(new Node(new_pos, GetMovementCost(new_pos) , Heuristic(new_pos, dest), curr_node));      // Astar
            open_list.push(new Node(new_pos, GetMovementCost(new_pos), 0, curr_node));                                 // Dijkstra
        }
    }

    return std::vector<Point>();
}
```