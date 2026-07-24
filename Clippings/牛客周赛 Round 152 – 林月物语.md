---
title: "牛客周赛 Round 152 – 林月物语"
source: "https://anoth3r.top/nkwk152/"
author:
published: 2026-07-12
created: 2026-07-23
description: "牛客周赛 Round 152 题解"
tags:
  - "clippings"
---
## A 小红的倍数构造

**知识点：构造、数位性质**

把 $x$ 拼接两遍，得到的数为 $x \times (10^{len}+1)$ ，一定是 $x$ 的倍数，且数字种类仍然完全相同。

时间复杂度 $\mathcal{O}(|x|)$ 。

```
void solve() {
    string s;
    cin >> s;
    cout << s << s << endl;
}
```

## B 小红的连通块构造

**知识点：构造、网格图、独立集**

在 $7 \times 6$ 的网格中，所有满足 $(i+j)$ 为奇数的位置构成棋盘格，它们两两之间不会上下左右相邻。因此每放一个 $\texttt{1}$ ，就会多出一个大小为 $\texttt{1}$ 的连通块。

这样的格子一共有 $\texttt{21}$ 个，所以当 $k>21$ 时无解；否则依次放 $k$ 个棋盘格位置为 $\texttt{1}$ 即可。

时间复杂度 $\mathcal{O}(1)$ 。

```
void solve() {
    int k;
    cin >> k;
    if (k > 21) return cout << -1 << endl, void();
    for (int i = 0; i < 7; ++i) {
        for (int j = 0; j < 6; ++j) {
            cout << (((i + j) & 1) && (k-- > 0) ? 1 : 0);
        }
        cout << endl;
    }
}
```

## C 小红的基环树

**知识点：图论、度**

题目保证图由一个 $n-1$ 个点的环，再额外连接一个点得到。因此唯一不在环上的点度数为 $\texttt{1}$ ，它连接的边就是要求的额外边。

读入时统计每个点的度数，并记录每个点的一个相邻点。最后找到度数为 $\texttt{1}$ 的点 $u$ ，设它的相邻点为 $v$ ，输出这条边即可。

时间复杂度 $\mathcal{O}(n)$ 。

```
void solve() {
    int n;
    cin >> n;
    unordered_map<int, int> mp;
    vector<int> deg(n + 1);
    for (int i = 1; i <= n; ++i) {
        int u, v;
        cin >> u >> v;
        mp[u] = v, mp[v] = u;
        deg[u]++, deg[v]++;
    }

    for (int i = 1; i <= n; ++i)
        if (deg[i] == 1) return cout << min(i, mp[i]) << " " << max(i, mp[i]) << endl, void();
}
```

## D 小红的计算几何

**知识点：计算几何、面积公式**

设正方形左上角平移的横向距离为 $dx=|a-c|$ ，纵向距离为 $dy=|b-d|$ 。

扫过区域可以看成原本的正方形，加上横向平移扫出的矩形和纵向平移扫出的矩形，因此面积为：

$$
k^2+k(dx+dy)
$$

时间复杂度 $\mathcal{O}(1)$ 。

```
void solve() {
    int k, a, b, c, d;
    cin >> k >> a >> b >> c >> d;
    int dx = abs(a - c), dy = abs(b - d);
    cout << k * k + k * (dx + dy) << endl;
}
```

## E 小红的不相邻取数

**知识点：动态规划、分段压缩**

原数组由若干段相同数字组成。设处理完前若干段后， $f_0$ 表示当前前缀最后一个元素不取的最大和， $f_1$ 表示当前前缀最后一个元素取的最大和。

对于一段 $(x,k)$ ，若 $x \le 0$ ，这一整段不取一定不劣。否则在长度为 $k$ 的连续段中，最多隔一个取一个，并且需要区分上一段末尾是否已经取过。

转移为：

$$
g_0=\max(f_0+\lfloor k/2\rfloor x,\ f_1+\lfloor (k-1)/2\rfloor x)
$$

$$
g_1=\max(f_0+\lceil k/2\rceil x,\ f_1+\lfloor k/2\rfloor x)
$$

其中 $g_0,g_1$ 分别表示当前段处理后最后一个元素不取、取的最大和。

时间复杂度 $\mathcal{O}(n)$ 。

```
constexpr int inf = 2e18 + 9;

void solve() {
    int n;
    cin >> n;
    vector<int> a(n), b(n);
    for (auto &v : a) cin >> v;
    for (auto &v : b) cin >> v;

    int f0 = 0, f1 = -inf;
    for (int i = 0; i < n; ++i) {
        int x = a[i], k = b[i];
        if (x <= 0) {
            f0 = max(f0, f1), f1 = -inf;
            continue;
        }
        int g0 = max(f0 + k / 2 * x, f1 + (k - 1) / 2 * x);
        int g1 = max(f0 + (k + 1) / 2 * x, f1 + k / 2 * x);
        f0 = g0, f1 = g1;
    }

    cout << max(f0, f1) << endl;
}
```

## F 猴子排序的剪枝

**知识点：概率期望、排列计数、动态规划**

把每一轮排序看成一次独立试验。设单轮成功概率为 $p$ ，单轮插入次数为 $X$ ，那么总期望为

$$
\frac{\mathbb{E}(X)}{p}
$$

设每种数值的出现次数分别为 $c_1,c_2,\ldots,c_m$ 。若把相同数值的元素仍视为不同位置，则总排列数为 $n!$，成功当且仅当整个排列按值非降，因此成功排列数为 $\prod c_i!$，即

$$
p=\frac{\prod c_i!}{n!}
$$

再用尾和公式计算 $\mathbb{E}(X)$ 。要进行第 $i+1$ 次插入，等价于前 $i$ 个被插入的元素已经非降。设 $s_i$ 表示长度为 $i$ 的非降前缀方案数。若某个值组大小为 $c$ ，从其中选出并放入前缀 $j$ 个元素的有序方案数为 $A(c,j)$ ，不同值组之间的相对顺序已经被非降条件唯一确定，因此按值组做背包即可得到所有 $s_i$ 。

于是答案为

$$
\sum_{i=0}^{n-1} \frac{s_i}{A(n,i)}\cdot \frac{1}{p}=\frac{\sum_{i=0}^{n-1}s_i\cdot (n-i)!}{\prod c_i!}
$$

时间复杂度 $\mathcal{O}(n^2)$ 。

```
void solve() {
    int n;
    cin >> n;
    vector<int> a(n);
    for (auto &v : a) cin >> v;
    sort(a.begin(), a.end());
    vector<Z> dp(n + 1);
    dp[0] = 1;
    int sum = 0;
    Z down = 1;
    for (int l = 0; l < n;) {
        int r = l;
        while (r < n && a[r] == a[l]) ++r;
        int c = r - l;
        vector<Z> ndp(n + 1);
        for (int i = 0; i <= sum; ++i) {
            Z w = 1;
            for (int j = 0; j <= c; ++j) {
                ndp[i + j] += dp[i] * w;
                w *= c - j;
            }
        }
        dp.swap(ndp);
        sum += c;
        down *= C.fac(c);
        l = r;
    }
    Z ans = 0;
    for (int i = 0; i < n; ++i) ans += dp[i] * C.fac(n - i);

    cout << ans / down << endl;
}
```

## 约定

```
#include <bits/stdc++.h>
using namespace std;

#define int long long
#define pii array<int, 2>
#define endl "\n"

void solve() {

}

signed main() {
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout << fixed << setprecision(20);
    int t = 1;
    // cin >> t;
    for (int i = 0; i < t; ++i) {
        solve();
    }
    return 0;
}
```