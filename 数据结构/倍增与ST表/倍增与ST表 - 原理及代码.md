# 倍增算法和 ST 表

线段上有 $n$ 个点，给定每个点 $i$ 往右边跳 $1$ 步能最远覆盖的点 `jump[i]`。

> **已知条件：** 从任意点出发都能到达最后的点，并且在 $i < j$ 时，必有 `jump[i] <= jump[j]`。


问题提出：
求从i点出发，至少要走多少步，才能到达(覆盖) target点
如果每个点都作为起点进行查询呢？

时间复杂度很明显会到达O(n * n)，会有超时的风险

## 1. 如何构建一张表

可以查询从任意点出发：跳 $1$ 步、跳 $2$ 步、**跳 $4$ 步**、跳 $8$ 步……每回最远能到达的点。

也就是说可以查询，从任意点 $i$ 出发，任意跳 $2^p$ 步，最远能到达的点。

### ST 表 (Sparse Table) 的构建

- **构建方式：** `st[i][p] = st[st[i][p-1]][p-1]`
    
- **参数含义：** $p$ 代表跳 $2^p$ 步。
    
- **表的大小：** $n \times \log n$
    

## 2. 如何快速计算任意的两点之间，最少跳几步能到达？

利用 ST 表，从 $x$ 到 $y$ 跳跃的过程中：

1. 先从**最大步长**开始尝试。
    
2. 每次步长**减少一半**去尝试。
    
3. 最终得到答案。
    

- **时间复杂度：** $O(\log n)$


```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long

bool cmp(const array<int, 3> & a, const array<int, 3> & b) {
    return a[1] < b[1];
}

int jump(int i, int m, int power, vector<array<int, 3>> & line, vector<vector<int>> & st) {
    int target = line[i][1] + m, cur = i, next, ans = 1;
    for (int p = power; p >= 0; p--) {
        next = st[cur][p];
        if (next != 0 && line[next][2] < target) {
            ans += 1 << p;
            cur = next;
        }
    }
    return ans + 1;
}

signed main () {
    ios::sync_with_stdio(0), cin.tie(0);
    
    int n, m; cin >> n >> m;
    vector<array<int, 3>> line(2 * n + 1);
    for (int i = 1; i <= n; i++) {
        line[i][0] = i;
        cin >> line[i][1] >> line[i][2];
    }

    int power = __lg(n);
    vector<vector<int>> st(2 * n + 1, vector<int> (power + 1));
    
    for (int i = 1; i <= n; i++) {
        if (line[i][1] > line[i][2]) { 
            line[i][2] += m;
        }
    }

    sort(line.begin() + 1, line.begin() + 1 + n, cmp);//
    for (int i = 1; i <= n; i++) {
        line[i + n][0] = line[i][0];
        line[i + n][1] = line[i][1] + m;
        line[i + n][2] = line[i][2] + m;
    }

    int e = n << 1;
    int arrive = 1;
    for (int i = 1; i <= e; i++) {
        while (arrive + 1 <= e && line[arrive + 1][1] <= line[i][2]) {
            arrive++;
        }
        st[i][0] = arrive;
    }

    for (int p = 1; p <= power; p++) {
        for (int i = 1; i <= e; i++) {
            st[i][p] = st[st[i][p - 1]][p - 1];
        }
    }

    vector<int> ans(n + 1);
    for (int i = 1; i <= n; i++) {
        ans[line[i][0]] = jump(i, m, power, line, st);
    }

    for (int i = 1; i <= n; i++) {
        cout << ans[i] << " ";
    }

    return 0;
}
```
