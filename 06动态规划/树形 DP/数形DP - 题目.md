# 一 、

[J-菇菇嘎嘎历险记_第二十三届宁波大学程序设计竞赛（同步赛）](https://ac.nowcoder.com/acm/contest/134896/J)
```c++
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

const long long INF = 1e18; // 用一个足够大的值表示非法状态/无穷大
int n;
vector<long long> a;
vector<vector<int>> adj;
vector<vector<long long>> dp;

void dfs(int u, int p) {
    long long sum_min = 0;
    long long min_delta = INF;
    
    bool has_black_child = false;
    bool is_leaf = true;

    dp[u][1] = a[u]; // 自身染黑的初始代价

    for (int v : adj[u]) {
        if (v == p) continue;
        is_leaf = false; // 有实际的孩子，不是叶子节点
        
        dfs(v, u);

        // 累加孩子节点的最优状态
        sum_min += min(dp[v][0], dp[v][1]);
        
        // 记录把孩子从白变黑所需的最小额外代价
        min_delta = min(min_delta, dp[v][1] - dp[v][0]);

        // 检查在最优选择下，是否有孩子已经是黑色
        if (dp[v][1] <= dp[v][0]) {
            has_black_child = true;
        }
    }

    // 计算 dp[u][1]
    dp[u][1] += sum_min;

    // 计算 dp[u][0] (u 染白)
    if (is_leaf) {
        dp[u][0] = INF; // 叶子节点不能染白
    } else {
        if (has_black_child) {
            dp[u][0] = sum_min; // 自然满足条件
        } else {
            dp[u][0] = sum_min + min_delta; // 强行拉一个孩子变黑
        }
    }
}

int main() {
    // 优化输入输出
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    if (!(cin >> n)) return 0;

    a.resize(n + 1);
    for (int i = 1; i <= n; ++i) {
        cin >> a[i];
    }

    adj.resize(n + 1);
    for (int i = 0; i < n - 1; ++i) {
        int u, v;
        cin >> u >> v;
        adj[u].push_back(v);
        adj[v].push_back(u);
    }

    // dp[u][0] 为白，dp[u][1] 为黑
    dp.assign(n + 1, vector<long long>(2, 0));

    // 从根节点 1 开始 DFS，父节点设为 0
    dfs(1, 0);

    // 最终答案为根节点染白或染黑的最小值
    cout << min(dp[1][0], dp[1][1]) << "\n";

    return 0;
}
```

## 1. 状态定义

对于树上的每个节点 $u$，它自己可以染成黑色或白色。因此我们为每个节点定义两个 DP 状态：

- $dp[u][0]$：以 $u$ 为根的子树中，**$u$ 染成白色**时，该子树的最小总代价。
    
- $dp[u][1]$：以 $u$ 为根的子树中，**$u$ 染成黑色**时，该子树的最小总代价。
    

## 2. 状态转移方程

我们从根节点（节点 1）开始进行 DFS，自底向上计算。假设当前节点为 $u$，它的孩子节点集合为 $v \in \text{children}(u)$。

### 情况一：$u$ 染成黑色（计算 $dp[u][1]$）

当 $u$ 染成黑色时，它已经满足了它父亲（如果存在）可能需要的“至少一个黑孩子”的条件。同时，因为 $u$ 本身是黑色的，**它的孩子 $v$ 染成黑色或白色都可以**，没有额外的限制。

- 基本初始代价：$dp[u][1] = a_u$（加上自身的权值）。
    
- 转移方程：
    
    $$dp[u][1] = a_u + \sum_{v \in \text{children}(u)} \min(dp[v][0], dp[v][1])$$
    

### 情况二：$u$ 染成白色（计算 $dp[u][0]$）

当 $u$ 染成白色时，自身权值不计入。但它对孩子有严格要求：**必须至少有一个孩子 $v$ 染成黑色**。

1. **如果 $u$ 是叶子节点**：它没有孩子，无法满足条件，所以这是一个非法状态。我们可以将其初始化为一个极大值：$dp[u][0] = \infty$。
    
2. **如果 $u$ 不是叶子节点**：
    
    - 如果我们不管约束，让每个孩子都选最优的（即 $\min(dp[v][0], dp[v][1])$），我们求和得到：
        
        $$\text{sum\_min} = \sum_{v \in \text{children}(u)} \min(dp[v][0], dp[v][1])$$
        
    - **检查约束**：如果在上述最优选择中，**已经至少有一个孩子选了黑色**（即存在某个 $v$ 使得 $dp[v][1] \le dp[v][0]$），那么这个“至少一个”的条件自然满足了。此时 $dp[u][0] = \text{sum\_min}$。
        
    - **强制调整**：如果所有的孩子在最优选择下**都选了白色**（即对所有 $v$，都有 $dp[v][0] < dp[v][1]$），那就不合法了。我们必须“牺牲”一个孩子，强行把它改成黑色。为了让总代价增加得最少，我们要挑一个改动代价最小的孩子 $v$，也就是让 $dp[v][1] - dp[v][0]$ 最小的那个。
        
    - 因此，我们维护一个差值的最小值：$\Delta = \min_{v \in \text{children}(u)} (dp[v][1] - dp[v][0])$。
        
    - 最终：$dp[u][0] = \text{sum\_min} + \Delta$。

33