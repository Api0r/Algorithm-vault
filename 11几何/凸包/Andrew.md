# Andrew 算法

## 1. 算法背景与数学推导

Andrew 算法（Monotone Chain 算法）是 Graham 扫描法的变种。相比于按极角排序（存在浮点精度与共线基准点问题），Andrew 算法**先按双关键字（ $x$ 升序， $y$ 升序）排序**，然后将凸包拆解为下凸壳（Lower Hull）**与**上凸壳（Upper Hull）分别构建。

### 叉积与转向判断

对于二维平面上的三个点 $A(x_1, y_1)$、 $B(x_2, y_2)$、 $C(x_3, y_3)$，定义有向线段向量 $\vec{AB} = (x_2 - x_1, y_2 - y_1)$ 与 $\vec{BC} = (x_3 - x_2, y_3 - y_2)$。两向量的二维叉积（Cross Product）标量定义为：

$$\vec{AB} \times \vec{BC} = (x_2 - x_1)(y_3 - y_2) - (y_2 - y_1)(x_3 - x_2)$$

叉积的符号反映了从点 $A \to B$ 到 $B \to C$ 的转向关系：

- $\vec{AB} \times \vec{BC} > 0$：严格**向左拐**（逆时针转向）。

- $\vec{AB} \times \vec{BC} < 0$：严格**向右拐**（顺时针转向）。

- $\vec{AB} \times \vec{BC} = 0$： $A, B, C$ **三点共线**。

## 2. 共线点的退化与选择

在构建凸壳时，栈顶现有两点 $A$（次顶）、 $B$（当前顶），新加入点 $C$。是否保留共线点直接决定了弹栈条件：

1. **严格凸包（Strictly Convex，不保留边上共线点）**：

    - 下凸壳逆时针构建时，每次扩展必须**严格向左拐**。

    - 当 $\vec{AB} \times \vec{BC} \le 0$（顺时针或共线）时，中间点 $B$ 均无法作为凸多边形的严格顶点，**必须弹出**。

2. **非严格凸包（Weakly Convex，保留边上的共线点）**：

    - 允许共线前进，仅当折线发生**严格右拐**时才非法。

    - 因此，当且仅当 $\vec{AB} \times \vec{BC} < 0$ 时弹出 $B$；当叉积为 $0$ 时保留点 $B$。

    - **特殊注意**：在逆向构建上凸壳（从右向左）时，必须防止共线点将起点与终点提前弹出；排序后还需过滤完全重合的重复点。

## 3. 构建流程与复杂度

1. **排序与去重**：将点集按 $(x, y)$ 坐标升序排序，并移除完全重叠的重复点。若去重后点数 $\le 2$，则点集本身即为其凸包。

2. **构建下凸壳**：

    - 从左至右（ $i = 0 \to n-1$）遍历点集，用单调栈维护下凸壳点序列。

    - 当栈内至少有 2 个点且新加入点使凸壳发生凹陷（或共线退化）时，执行弹栈，直至满足凸性，将新点入栈。

3. **构建上凸壳**：

    - 从右至左（ $i = n-2 \to 0$）遍历点集。

    - 记录下凸壳的元素个数 $k$，弹栈限制为栈大小不得小于 $k + 1$（保证下凸壳不被破坏）。

    - 同样按叉积条件维护凸性并入栈。

4. **闭合多边形**：由于起点会在最后被重复加入一次，弹出末尾与起点重合的点，即得到按**逆时针排列**的凸包顶点序列。

- **时间复杂度**：排序阶段耗时 $O(n \log n)$，单调栈构建阶段每个点进出栈各至多一次，耗时 $O(n)$。总体时间复杂度为 $O(n \log n)$。

- **空间复杂度**：存储坐标与凸包栈空间为 $O(n)$。

## 4. 现代 C++ 模板实现

以下实现支持整数与浮点坐标模板化，并通过编译期常数或布尔参数开关控制**是否包含共线点**：

```c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <cmath>

template <typename T>
struct Point {
    T x{}, y{};

    constexpr bool operator<(const Point& other) const {
        if (x != other.x) return x < other.x;
        return y < other.y;
    }

    constexpr bool operator==(const Point& other) const {
        return x == other.x && y == other.y;
    }
};

// 计算向量 AB 与 BC 的二维叉积: (B - A) x (C - A)
template <typename T>
constexpr auto cross(const Point<T>& a, const Point<T>& b, const Point<T>& c) {
    using CommonType = decltype(a.x * b.y - a.y * b.x);
    return static_cast<CommonType>(b.x - a.x) * (c.y - a.y) -
           static_cast<CommonType>(b.y - a.y) * (c.x - a.x);
}

/**
 * @brief Andrew (Monotone Chain) 算法求解二维凸包
 * @tparam T 坐标类型 (推荐 int, long long, double)
 * @param pts 输入点集
 * @param include_collinear 是否保留凸包边上的共线点
 * @return 逆时针排列的凸包顶点集合
 */
template <typename T>
std::vector<Point<T>> monotone_chain_convex_hull(std::vector<Point<T>> pts, bool include_collinear = false) {
    int n = static_cast<int>(pts.size());
    if (n <= 2) return pts;

    // 1. 字典序升序排序并去重
    std::sort(pts.begin(), pts.end());
    pts.erase(std::unique(pts.begin(), pts.end()), pts.end());
    n = static_cast<int>(pts.size());
    if (n <= 2) return pts;

    std::vector<Point<T>> hull;
    hull.reserve(2 * n);

    // 2. 构造下凸壳 (从左到右)
    for (int i = 0; i < n; ++i) {
        while (hull.size() >= 2) {
            auto cp = cross(hull[hull.size() - 2], hull.back(), pts[i]);
            // 包含共线点时：仅当严格右拐 (cp < 0) 时弹出
            // 不包含共线点时：右拐或共线 (cp <= 0) 均弹出
            if (include_collinear ? (cp < 0) : (cp <= 0)) {
                hull.pop_back();
            } else {
                break;
            }
        }
        hull.push_back(pts[i]);
    }

    // 3. 构造上凸壳 (从右到左)
    const size_t lower_hull_size = hull.size();
    for (int i = n - 2; i >= 0; --i) {
        while (hull.size() > lower_hull_size) {
            auto cp = cross(hull[hull.size() - 2], hull.back(), pts[i]);
            if (include_collinear ? (cp < 0) : (cp <= 0)) {
                hull.pop_back();
            } else {
                break;
            }
        }
        hull.push_back(pts[i]);
    }

    // 起点 pts[0] 会在最后一步被重复放入一次，需将其剔除
    hull.pop_back();
    return hull;
}

int main() {
    std::ios::sync_with_stdio(false);
    std::cin.tie(nullptr);

    // 示例点集，含内部点与边上共线点
    std::vector<Point<long long>> points = {
        {0, 0}, {1, 0}, {2, 0}, {2, 1}, {2, 2}, {1, 2}, {0, 2}, {0, 1}, {1, 1}
    };

    std::cout << "--- 严格凸包 (排除共线点) ---\n";
    auto strict_hull = monotone_chain_convex_hull(points, false);
    for (const auto& [x, y] : strict_hull) {
        std::cout << "(" << x << ", " << y << ") ";
    }
    std::cout << "\n\n";

    std::cout << "--- 非严格凸包 (保留共线点) ---\n";
    auto full_hull = monotone_chain_convex_hull(points, true);
    for (const auto& [x, y] : full_hull) {
        std::cout << "(" << x << ", " << y << ") ";
    }
    std::cout << "\n";

    return 0;
}
```
