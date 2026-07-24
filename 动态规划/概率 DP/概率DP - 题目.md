[I-冰淇淋大危机_第七届武汉纺织大学ACM程序设计竞赛(同步赛）](https://ac.nowcoder.com/acm/contest/136171/I)

![[Pasted image 20260607151514.png]]
![[Pasted image 20260607151555.png]]
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
