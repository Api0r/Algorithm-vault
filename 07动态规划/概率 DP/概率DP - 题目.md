
# 1.

[I-冰淇淋大危机_第七届武汉纺织大学ACM程序设计竞赛(同步赛）](https://ac.nowcoder.com/acm/contest/136171/I)

![](../../image/Pasted%20image%2020260607151514.png)
![](../../image/Pasted%20image%2020260607151555.png)
```c++
const int MOD = 998244353;

int ksm(int a, int b) {
    a %= MOD;
    int ans = 1;
    while (b) {
        if (b & 1) ans = ans * a % MOD;
        a = a * a % MOD;
        b >>= 1;
    }
    return ans;
}

int nv(int x) {
    return ksm(x, MOD - 2);
}

void ap() {
	int n, m; cin >> n >> m;
    vector<array<int, 2>> a(n);
    for (int i = 0; i < n; i++) {
        cin >> a[i][0] >> a[i][1];
    }
    
    if (m <= n) {
        cout << 0;
        return;
    }
    int inv100 = nv(100);
    vector<int> dp(m + 1, 0);
    dp[m] = 1;
    
    for (int i = 0; i < n ; i++) {
        int p1 = (a[i][0] * inv100) % MOD;
        int p2 = (a[i][1] * inv100) % MOD;
        
        vector<int> ndp(m + 1, 0);
        for (int j = 0; j <= m; j++) {
            if (dp[j] == 0) continue;
            
            if (j >= 2) {
                ndp[j - 1] = (ndp[j - 1] + dp[j] * p1) % MOD;
                
                ndp[j - 2] = (ndp[j - 2] + dp[j] * p2) % MOD;
            } else if (j == 1) {
                ndp[0] = (ndp[0] + dp[1]) % MOD;
            } else if (j == 0) {
                ndp[0] = (ndp[0] + dp[0]) % MOD;
            }
        }
        dp = move(ndp);
    }
    
    int ans = 0;
    for (int i = 1; i <= m; i++) {
        ans = (ans + dp[i]) % MOD;
    }
    cout << ans;
    
	return ;
}
```



# 2.概率DP + 矩阵快速幂加速

[L-小L的游戏2_2026牛客寒假算法基础集训营6](https://ac.nowcoder.com/acm/contest/120566/L)

如果z很小的情况
代码如下
```c++
#include <bits/stdc++.h>  
using namespace std;  
#define int long long
#define endl '\n'
const int mod = 1e9 + 7;
const int MOD = 998244353;

const int MAXZ = 1000005;
int dp[MAXZ][2]; // dp[i][0]: 小 L 到达 i 的概率; dp[i][1]: fallleaves01 到达 i 的概率

void ap() {
    int m1, m2, n1, n2, z, p, q;
    if (!(cin >> m1 >> m2 >> n1 >> n2 >> z >> p >> q)) return;

    int inv_p = (1 - p + MOD) % MOD;
    int inv_q = (1 - q + MOD) % MOD;

    // 初始化：起点为 0，小 L 第一步依赖 fallleaves01 在 0 位置
    for (int i = 0; i <= z; ++i) {
        dp[i][0] = dp[i][1] = 0;
    }
    dp[0][1] = 1;

    // 线性 DP 递推
    for (int i = 1; i <= z; ++i) {
        // 小 L 的转移
        int from_m1 = (i >= m1 ? dp[i - m1][1] : 0);
        int from_m2 = (i >= m2 ? dp[i - m2][1] : 0);
        dp[i][0] = (from_m1 * p % MOD + from_m2 * inv_p % MOD) % MOD;

        // fallleaves01 的转移
        int from_n1 = (i >= n1 ? dp[i - n1][0] : 0);
        int from_n2 = (i >= n2 ? dp[i - n2][0] : 0);
        dp[i][1] = (from_n1 * q % MOD + from_n2 * inv_q % MOD) % MOD;
    }

    // 统计小 L 一步跨越或到达 >= z 的总胜率
    int ans = 0;
    int t = max(m1, m2);
    for (int i = max(0LL, z - t); i < z; ++i) {
        if (i + m1 >= z) ans = (ans + dp[i][1] * p) % MOD;
        if (i + m2 >= z) ans = (ans + dp[i][1] * inv_p) % MOD;
    }

    cout << ans << endl;
    return;
}

signed main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int T = 1;
//  cin >> T;
    while (T--) {
        ap();
    } 
    return 0;
}
```

但是`1 ≤ z ≤ 1e18`
所以需要矩阵快速幂加速dp

