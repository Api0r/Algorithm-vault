# 一、位置倍增

[P4155 [SCOI2015] 国旗计划 - 洛谷](https://www.luogu.com.cn/problem/P4155)

- **`st[i][p]` 的定义：** 从第 `i` 个区间（或点）出发，跳 $2^p$ 步之后，**最终到达的那个新区间（或新点）的下标（编号）**。
    
- **转移方程：** `st[i][p] = st[st[i][p - 1]][p - 1]`
    
- **本质：** 维护的是**位置到位置（或状态到状态）的直接映射**。因为每一次“跳跃”的落脚点是一个明确的、现成的区间下标。

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


# 二、权值/带权倍增


[E-小橙的幸运数（hard）_牛客小白月赛129](https://ac.nowcoder.com/acm/contest/128675/E)
模运算，记忆化搜索，基环树，贪心，倍增

- **`st[i][p]` 的定义：** 从余数为 `i` 的状态出发，执行 $2^p$ 次操作后，**数值 $x$ 一共增加了多少（即增量）**。
    
- **转移方程：** `st[i][p] = st[i][p - 1] + st[(i + st[i][p - 1]) % n][p - 1]`
    
- **本质：** 维护的是一个**累加的权重（权值和）**。因为本题你要凑的目标是 `target = c - x`（差值），所以你必须知道“跳了这么多步以后数字变大了多少”，而具体的“下一站位置”是通过 `(当前位置 + 增量) % n` 间接算出来的。

```c++
void ap() {
	int n, c, q; cin >> n >> c >> q;
    vector<int> a(n);
    for (int & i : a) cin >> i;
    
    //c <= 1e9 最大增量为2^32
    vector<vector<int>> st(n, vector<int>(33));//st[i][j] 从ai开始走2^j 步的增加量
    
    for (int i = 0; i < n; i++) {
        st[i][0] = a[i];
    }
    
    for (int j = 1; j <= 32; j++) {
        for (int i = 0; i < n; i++) {
            int mid = (i + st[i][j - 1]) % n;
            st[i][j] = st[i][j - 1] + st[mid][j - 1];
            
        }
    }
    
    
    while (q--) {
        int x; cin >> x;
        if (x == c) {
            cout << "Yes" << endl;
            continue;
        }
        
        if (x > c) {
            cout << "No" << endl;
            continue;
        }
        
        int target = c - x;
        int i = x % n;//当前位置
        for (int j = 32; j >= 0; j--) {
            if (a[i] == 0) break;
            
            if (st[i][j] <= target) {
                target -= st[i][j];
                
                i = (i + st[i][j]) % n;
            }    
        }
        
        if (target == 0) {
            cout << "Yes" << endl;
        } else {
            cout << "No" << endl;
        }
    }
	return ;
}
```

