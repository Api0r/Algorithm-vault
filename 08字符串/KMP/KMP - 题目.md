# 1.差分数组 + 模式匹配（KMP）+ 前缀和查询

[I-Harmonious-subarrays_2026江西八校集训联盟第二场联赛](https://ac.nowcoder.com/acm/contest/139947/I)

```c++
#include <bits/stdc++.h>
using namespace std;
#define int long long
#define endl '\n'
const int mod = 1e9 + 7;
const int MOD = 998244353;

// 适配泛型/long long 的 KMP 模板
template <typename T>
struct KMP {
    vector<T> p;
    vector<int> pi;

    explicit KMP(const vector<T>& s) : p(s) {
        build();
    }

    void build() {
        int n = p.size();
        pi.assign(n, 0);
        for (int i = 1, j = 0; i < n; ++i) {
            while (j && p[i] != p[j]) j = pi[j - 1];
            if (p[i] == p[j]) ++j;
            pi[i] = j;
        }
    }

    vector<int> ask(const vector<T>& s) const {
        vector<int> a;
        int n = s.size(), m = p.size();
        if (!m) {
            a.resize(n + 1);
            iota(a.begin(), a.end(), 0);
            return a;
        }
        for (int i = 0, j = 0; i < n; ++i) {
            while (j && s[i] != p[j]) j = pi[j - 1];
            if (s[i] == p[j]) ++j;
            if (j == m) {
                a.push_back(i - m + 1);
                j = pi[j - 1];
            }
        }
        return a;
    }
};

void ap() {
	int n, m, q; cin >> n >> m >> q;
    vector<int> a(n), b(m);
    for (int & i : a) cin >> i;
    for (int & i : b) cin >> i;

    vector<int> s(n - 1), t(m - 1);
    for (int i = 0; i < n - 1; i++) s[i] = a[i + 1] - a[i];
    for (int i = 0; i < m - 1; i++) t[i] = b[i + 1] - b[i];

    KMP<int> kmp(t);
    vector<int> pos = kmp.ask(s);

    vector<int> ok(n, 0);
    for (int & x : pos) ok[x] = 1;

    vector<int> pre(n + 1);
    for (int i = 0; i < n; i++) {
        pre[i + 1] = pre[i] + ok[i];
    }

    while (q--) {
        int l, r; cin >> l >> r;
        l--, r--;

        if (r - l + 1 < m) {
            cout << 0 << endl;
            continue;
        }

        if (m == 1) {
            cout << r - l + 1 << endl;
            continue;
        }

        int right = r - m + 1;
        cout << pre[right + 1] - pre[l] << endl;
    }
	return ;
}

signed main() {
	ios::sync_with_stdio(false);
	cin.tie(nullptr);
	int T = 1;
//	cin >> T;
	while (T--) {
		ap();
	}
    return 0;
}
```

这道题的核心是**差分数组 + 模式匹配（KMP）+ 前缀和查询**。

### 解题思路

1. **转化“和谐”条件（差分）**：

    对于两个长度为 $m$ 的序列 $a$ 和 $b$，存在常数 $x$ 使得 $a_i + x = b_i$（即 $a_i - b_i = -x$ 为常数）。

    这等价于：**相邻元素的差值完全相等**。

    即对于所有 $1 \le i < m$：

    $$a_{i+1} - a_i = b_{i+1} - b_i$$

2. **序列匹配（KMP）**：

    - 将序列 $t$ 做差分得到长度为 $m - 1$ 的模式串 $\Delta t$：$\Delta t_i = t_{i+1} - t_i$。

    - 将序列 $s$ 做差分得到长度为 $n - 1$ 的文本串 $\Delta s$：$\Delta s_i = s_{i+1} - s_i$。

    - 采用 KMP 算法，在 $\Delta s$ 中匹配 $\Delta t$：

        - 若 $m = 1$，任何长度为 1 的子数组都与 $t$ 和谐，所有位置均匹配。

        - 若 $m > 1$，若从 $\Delta s$ 的下标 $p$ 开始（0-indexed）成功匹配了长度为 $m - 1$ 的 $\Delta t$，则说明以 $s$ 中的第 $p$ 个位置（0-indexed）为起点的子数组与 $t$ 和谐。

3. **前缀和处理区间查询**：

    - 设一个数组 $is\_match[p] = 1$ 表示从位置 $p$ 起始的长度为 $m$ 的子数组与 $t$ 和谐，否则为 $0$。

    - 对 $is\_match$ 构造前缀和数组 $pref$。

    - 对于每次询问 $[l, r]$（转为 0-indexed 为 $[l-1, r-1]$）：

        - 满足条件的起点 $p$ 必须满足：$l - 1 \le p$ 且 $p + m - 1 \le r - 1 \implies p \le r - m$。

        - 若 $r - l + 1 < m$，则合法起点区间为空，答案为 $0$。

        - 否则，查询区间 $[l - 1, r - m]$ 内 $1$ 的个数，即 $pref[r - m + 1] - pref[l - 1]$。

### 复杂度分析

- **预处理时间复杂度**：计算差分 $O(n + m)$，KMP 构建与匹配 $O(n + m)$，前缀和预处理 $O(n)$。

- **单次询问时间复杂度**：$O(1)$。

- **总时间复杂度**：$O(n + m + q)$，在 $n, m, q \le 2 \cdot 10^5$ 规模下可轻松在 100ms 左右通过。

- **空间复杂度**：$O(n + m)$。


## 差分后下标对应关系

看差分前后的下标对应关系，核心技巧是：**“差分数组的下标 $i$，代表的是原数组中以 $i$ 为起点的相邻两项关系”**。

### 1. 元素层面的映射关系

假设原数组为 $a$（长度为 $n$，下标 $0 \dots n-1$），差分数组为 $s$（长度为 $n-1$，下标 $0 \dots n-2$）：

- $s[0] = a[1] - a[0]$（代表以 **$0$** 起始的相邻差）
    
- $s[1] = a[2] - a[1]$（代表以 **$1$** 起始的相邻差）
    
- $\dots$
    
- $s[k] = a[k+1] - a[k]$（代表以 **$k$** 起始的相邻差）
    

### 2. 区间/子串层面的映射关系

在原数组中，一个长度为 $m$、以 $p$ 为起点的子数组包含 $m$ 个数：

$$[a[p], a[p+1], a[p+2], \dots, a[p+m-1]]$$

这个子数组内部一共有 $m-1$ 个相邻差值，分别对应：

- $a[p+1] - a[p] \implies s[p]$
    
- $a[p+2] - a[p+1] \implies s[p+1]$
    
- $\dots$
    
- $a[p+m-1] - a[p+m-2] \implies s[p+m-2]$
    

即：**原数组中以 $p$ 开始、长度为 $m$ 的子数组，恰好对应差分数组中以 $p$ 开始、长度为 $m-1$ 的连续子段**。

### 3. KMP 匹配结果的下标含义

当你在差分数组 $s$ 中匹配长度为 $m-1$ 的模式差分数组 $t$ 时：

- 若 KMP 返回匹配成功的位置为 `idx`：
    
    - 这意味着差分数组中从 `idx` 到 `idx + (m - 1) - 1` 这段子串匹配成功。
        
    - 映射回原数组，**完全对应的就是以 `idx` 为起点的长度为 $m$ 的子数组**（即 $a[idx \dots idx + m - 1]$）。
        
- **结论**：KMP 在差分数组中找到的起始下标 `idx`，**直接就是原数组中该匹配段的起始下标 `idx`**，无需做任何偏移（$+1$ 或 $-1$）。
    

### 4. 询问区间 $[l, r]$ 的起点范围推导

当询问原数组区间 $[l, r]$（0-indexed）时：

- 起点 $p$ 不能小于左端点：$p \ge l$
    
- 终点 $p + m - 1$ 不能超过右端点：$p + m - 1 \le r \implies p \le r - m + 1$
    
- 所以合法的起始下标区间为：
    
    $$p \in [l, \; r - m + 1]$$
    

只要记录每个位置 $p$ 是否是一个成功的起点（`ok[p] = 1`），查询 $[l, r - m + 1]$ 内 `1` 的数量即可。