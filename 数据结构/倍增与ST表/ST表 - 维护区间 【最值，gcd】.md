
**可重复贡献的区间查询问题**
区间最大最小值，区间公约数， 区间按位与，区间按位或

但是区间求和就不符合

构建过程时间复杂度O(n * logn)， 单次查询时间复杂度O(1)
代码量小，但是不可以修改，并且开的空间需要较大，需要n * logn


# 一、
[P2880 [USACO07JAN] Balanced Lineup G - 洛谷](https://www.luogu.com.cn/problem/P2880)

```c++
#include<bits/stdc++.h>
using namespace std;
#define int long long

signed main () {
    int n, q; cin >> n >> q;
    vector<int> a(n);
    for (int & i : a) cin >> i;

    int power = __lg(n);
    vector<vector<int>> mx_st(n + 1, vector<int>(power + 1));
    vector<vector<int>> mn_st(n + 1, vector<int>(power + 1));
    for (int i = 0; i < n; i++) mx_st[i][0] = a[i], mn_st[i][0] = a[i]; 

    for (int p = 1; p <= power; p++) {
        for (int i = 0; i + (1 << p) - 1 < n; i++) {//注意的越界问题
            mx_st[i][p] = max(mx_st[i][p - 1], mx_st[i + (1 << (p - 1))][p - 1]);
            mn_st[i][p] = min(mn_st[i][p - 1], mn_st[i + (1 << (p - 1))][p - 1]);
        }
    }
    
    for (int i = 0; i < q; i++) {
        int l, r; cin >> l >> r;
        l--, r--;

        int p = __lg(r - l + 1);
        cout << max(mx_st[l][p], mx_st[r - (1 << p) + 1][p]) - min(mn_st[l][p], mn_st[r - (1 << p) + 1][p]) << endl;
    }

    return 0;
}
```

# 二、
[P1890 gcd 区间 - 洛谷](https://www.luogu.com.cn/problem/P1890)

```c++
#include<bits/stdc++.h>
using namespace std;

signed main () {
    int n, m; cin >> n >> m;
    vector<int> a(n);
    for (int & i : a) cin >> i;

    int power = __lg(n);
    vector<vector<int>> st(n, vector<int>(power + 1));

    for (int i = 0; i < n; i++) {
        st[i][0] = a[i];
    }

    for (int p = 1; p <= power; p++) {
        for (int i = 0; i + (1 << p) - 1 < n; i++) {
            st[i][p] = __gcd(st[i][p - 1], st[i + (1 << (p - 1))][p - 1]);
        }
    }

    
    for (int i = 0; i < m; i++) {
        int l, r; cin >> l >> r;
        l--, r--;

        int p = __lg(r - l + 1);
        cout << __gcd(st[l][p], st[r - (1 << p) + 1][p]) << endl;
    }

    return 0;
}
```

[UVA11235 Frequent values - 洛谷](https://www.luogu.com.cn/problem/UVA11235)
