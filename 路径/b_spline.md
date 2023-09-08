# B-Spline

## 优点

```
1. 具有局部控制性：B样条曲线的形状仅由其控制点和节点向量决定，因此可以通过调整这些参数来控制曲线的形状。
   这使得B样条曲线具有局部控制性，即可以在不影响曲线其他部分的情况下，对曲线的局部形状进行修改。
2. 具有平滑性：B样条曲线是由多个局部的Bezier曲线拼接而成的，因此具有平滑性。这意味着曲线的一阶和二阶导
   数连续，因此可以用于需要平滑曲线的应用程序中。
3. 具有逼近性：B样条曲线可以逼近任意曲线，因此可以用于曲线拟合和数据插值。
4. 具有灵活性：B样条曲线可以通过调整节点向量的位置和数量来控制曲线的形状。这使得B样条曲线非常灵活，可以
   用于各种应用程序中。
5. 具有计算效率高：B样条曲线的计算效率比其他曲线类型高，因为它可以通过递推算法来计算。这使得B样条曲线可
   以用于实时应用程序中，如计算机游戏和计算机辅助设计。
```

## [demo](https://github.com/peitianyu/all_test/blob/main/src/common/b_spline.h)

```cpp
// NOTE: 自适应归一化knotes
template <class V, class T>
inline void
BSpline<V,T>::uniform_knots (T const& min, T const& max)
{
    T width = max - min;
    int n_knots = this->points.size() + this->n + 1;
    int segments = this->points.size() - this->n;
    this->knots.clear();
    this->knots.reserve(n_knots);

    for (int i = 0; i < this->n; ++i)
        this->knots.push_back(min);
    for (int i = 0; i < segments + 1; ++i)
        this->knots.push_back(min + T(i) * width / T(segments));
    for (int i = 0; i < this->n - 1; ++i)
        this->knots.push_back(max);
    if (this->n > 0)
        this->knots.push_back(max + T(1));

#if 0
    std::cout << "Made knots: ";
    for (std::size_t i = 0; i < this->knots.size(); ++i)
        std::cout << this->knots[i] << " ";
    std::cout << std::endl;
#endif
}

// NOTE: 递推计算节点
template <class V, class T>
inline V
BSpline<V,T>::evaluate (T const& t) const
{
    /* FIXME: inefficient */
    /* TODO: fast lookup through tables*/
    V p = this->points[0] * this->deboor(0, this->n, t);
    for (std::size_t i = 1; i < this->points.size(); ++i)
        p += this->points[i] * this->deboor(i, this->n, t);
    return p;
}

// NOTE: 通过deboor递推
template <class V, class T>
inline T
BSpline<V,T>::deboor (int i, int k, T const& x) const
{
    if (k == 0) {
        return (x >= this->knots[i] && x < this->knots[i+1]) ? T(1) : T(0);
    }

    float d1 = this->knots[i+k] - this->knots[i];
    float d2 = this->knots[i+k+1] - this->knots[i+1];
    T v1 = d1 > T(0) ? (x - this->knots[i]) / d1 : T(0);
    T v2 = d2 > T(0) ? (this->knots[i+k+1] - x) / d2 : T(0);
    return v1 * deboor(i, k-1, x) + v2 * deboor(i+1, k-1, x);
}
```