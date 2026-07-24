# 以当前数字结尾的子序列 的最大长度
注：当前数字是子序列的最后一个数字
[D-Longest Subsequence_牛客周赛 Round 131](https://ac.nowcoder.com/acm/contest/127703/D)
![](image/Pasted%20image%2020260319133803.png)```
dfs(i, x) 表示 前i个元素，以数字x结尾的子序列 的最大长度

状态转移：dfs(i, x) = 前i个元素中
- 以x + 1 结尾的最长子序列 的长度 + 1
- 以x - 1 结尾的最长子序列的长度  +1
- (因为 有 且 仅有 这些满足差的绝对值为1)

因为我们不关心i的位置，只需要i是 在其之前就可以，我们关心的是数字x
所以我们可以从前往后遍历，时刻满足了我们已有的数字在当前需要 的 之前
由此，我们只需要维护数字x

状态转移方程：dp(x) = max(dp(x - 1) + 1, dp(x - 1) + 1);
```c++
void solve() {
	int n; cin >> n;
    vector<int> f(n + 1);//f[x]以数字x结尾的 子序列的最大长度
    for (int i = 0; i < n; i++) {
        int x; cin >> x;
        if (x > 0) {
            f[x] = max(f[x], f[x - 1] + 1);
        }
        if (x < n) {
            f[x] = max(f[x], f[x + 1] + 1);
        }
    }
    
    int ans = 0;
    for (int i = 1; i <= n; i++) {
        ans = max(ans, f[i]);
    }
    cout << ans << endl;
    
	return ;
}
```



# 线性DP

容易和贪心混淆

[D-小彩的数组选数_牛客周赛 Round 114](https://ac.nowcoder.com/acm/contest/119273/D)
![](image/Pasted%20image%2020260324222950.png)```

dfs(i) 表示前i元素进行操作，最大的得分

状态转移：
1. 选择当前的元素a[i],那么i - 1位置的元素变为0，dfs(i) = a[i] + dfs(i - 2)
2. 不选，那么dfs(i) = dfs(i - 1);
两者取最大，即dfs(i) = max(dfs(i - 1), dfs(i - 2) + a[i]);

边界条件：i < 0 时，一定为0，因为没有元素的时候得分一定为0

```c++
void solve() {
	int n; cin >> n;
    vector<int> a(n); for (int i = 0; i < n; i++) cin >> a[i];
    

    //dfs(i) = max(dfs(i - 1), dfs(i - 2) + a[i]);
    
//     vector<int> memo(n, -1);
//     auto dfs = [&](this auto&& dfs, int i) ->int {
//         if (i < 0) {
//             return 0;
//         }  
//         int &res = memo[i];
//         if (res != -1) {
//             return res;
//         }
//         return res = max(dfs(i - 1), dfs(i - 2) + a[i]);
//     };
//     int ans = dfs(n - 1);
//     cout << ans << endl;
    
    vector<int> f(n + 2);
    for (int i = 0; i < n; i++) {
        f[i + 2] = max(f[i + 1], f[i] + a[i]);
    }
    cout << f[n + 1] << endl;
    
    
	return ;
}

```


# 枚举DP

[Problem - 2195C - Codeforces](https://codeforces.com/problemset/problem/2195/C?adcd1e=caf4fbu7vzg3rn&csrf_token=6b753960576d8eced608ab6546b27f1e&__cf_chl_tk=3WSxv5UKvd.U8k.vpOn5xI.Wz877gBNbo55N1UY.wdc-1774587398-1.0.1.1-ovvXpq6qX7vMoWh3Y4U.1SPDzHaCRS0SUXfvCBM62qY)
![](image/Pasted%20image%2020260327125943.png)·
题意是：给定一个序列(数组)a，对于每一个相邻的数，都需要满足相邻不相等 或者 相邻 之和不为7
每次操作可以选任意一个数使之变为1-6 中的任意一个数 。求使之满足的最小操作次数

可以用贪心的想法，对于一对相邻不满足的数，我们优先修改靠右边的(我们从左到右修改)，因为我们可以去思考是否可以使右边的和更右边的合法？ 我们举例发现一定可以。所以我们只需要一次贪心遍历即可
```c++
void solve() {

    int n; cin >> n;

    vector<int> a(n + 1); for (int i = 1; i <= n; i++) cin >> a[i];

  

    int ans = 0;

    for (int i = 2; i <= n; i++) {

        if (a[i] == a[i - 1] || a[i] + a[i - 1] == 7) {

            ans++;

            i++;

        }

    }

    cout << ans << endl;

    return ;

}
```

我们也可以采用DP，
定义dfs(i, j) 表示前i个数中，以数字j结尾，其修改的最小次数(最小花费)
那么i - 1 的时候，有四个与之相邻的数字可以，取最小，再考虑当前的数字是否需要花费
![](image/Pasted%20image%2020260327130621.png)·
```c++
void solve() {
    int n; cin >> n;
    vector<int> a(n); for (int i = 0; i < n; i++) cin >> a[i];
    
    map<int, vector<int>> op;
    for (int i = 1; i <= 6; i++) {
        for (int j = 1; j <= 6; j++) {
            if (i != j && i + j != 7) {
                op[i].push_back(j);
            }
        }
    }
    
    vector memo(n, vector<int>(7, -1));
    //dfs(i, j) = min(sum:dfs(i - 1, op[j]).. + (a[i] != j)

    auto dfs = [&](this auto&& dfs, int i, int j) -> int {
        if (i == 0) {
            return a[i] != j ? 1 : 0;
        }

        int &res = memo[i][j];
        if (res != -1) {
            return res;
        }
        
        int mn = 1e9;
        for (auto x : op[j]) {
            mn = min(mn, dfs(i - 1, x));
        }
        // if (a[i] != j) {
        //     return res = mn + 1;
        // } else {
        //     return res = mn;
        // }
        return res = mn + (a[i] != j);
    };
    int ans = 1e9;
    for (int i = 1; i <= 6; i++) {
        ans = min(ans, dfs(n - 1, i));
    }
    cout << ans << endl;
    return ;
}
```

也可以递推
递推不是完全反向，而是从i = 1，2，3.. 每一层向前一层看，逐渐到n - 1层
```c++
int main(){
    vector<vector<int>> mp(7);
    for(int i=1;i<=6;i++){
         for(int j=1;j<=6;j++){
            if(i!=j&&i+j!=7){
                mp[i].push_back(j);
            }
         }
    }

    int t;
    cin>>t;
    while(t--){
        int n;
        cin>>n;
        vector<int> a(n);
  

        for(int i=0;i<n;i++){
            cin>>a[i];
        }

        int dp[7],now[7];//dp为上一个，now为现在这个

        for(int i=1;i<=6;i++){
            dp[i]=(a[0]!=i);
        }

        for(int i=1;i<n;i++){
            for(int j=1;j<=6;j++){
                int mn=1e9;
                for(int x:mp[j]){
                    mn=min(mn,dp[x]);
                }
                now[j]=mn+(a[i]!=j);
            }
            for(int j=1;j<=6;j++)dp[j]=now[j];
        }

        int ans=1e9;
        for(int i=1;i<=6;i++){
            ans=min(ans,dp[i]);
        }
        cout<<ans<<endl;
    }
}
```


# 线性DP
[D-小苯的序列涂色_牛客周赛 Round 137](https://ac.nowcoder.com/acm/contest/130843/D)
![](image/Pasted%20image%2020260329220515.png)
```c++
void solve() {
	int n; cin >> n;
    vector<int> a(n + 1), pre(n + 1); for (int i = 1; i <= n; i++) cin >> a[i];
    for (int i = 1; i <= n; i++) {
        pre[i] = pre[i - 1] ^ a[i];
    }
    
    vector<int> dp(n + 1, 1e18);
    dp[0] = 0;
    for (int i = 1; i <= n; i++) {
        //dp[i] = dp[i - 1] + a[i]; 
        int mn = 1e18;
        for (int j = i; j >= 1; j--) {
            mn = min(mn, dp[j - 1]);
            dp[i] = min(dp[i], (pre[i] ^ pre[j - 1]) + mn);
        }
    }
    cout << dp[n] << endl;
    
	return ;
}
```

dp(i)表示 将前i个整数 染成红色的最小代价

状态转移：dp(i) = min( dp(i - 1) + pre[i] ^ pre[i - 1] , ,,, ,, dp(0) + );
	但是由于可以重复染色，所以，类似的，对于dp(i - 5)，如果dp(i - 2) 更小，那么我们此时的dp(i - 5)的值 应该取dp(i - 2);
为什么可以这样操作？ 因为我们的结果是一样的，我们需要的只是最小代价。本质是枚举dp(i - 1) + 代价, dp(i - 2) + 代价...只是用了可以重复染色，可以用更小的dp代替

# 完全背包 | 附加异或状态 + 无序分拆整数

 ## dp[s]  [p] 表示和为s，异或和为p 的方案数  

**无序整数分拆 DP + 异或和状态维护
[Problem - G - Codeforces](https://codeforces.com/gym/686522/problem/G)
![](image/Pasted%20image%2020260420131511.png)
题意： 给 n p m, 要求 和为 n - p ，异或和为p 的方案数，其结果 % m
## 代码

```c++
#include<bits/stdc++.h>
#define int long long
using namespace std; 

const int N = 505;
int dp[N][N];


signed main () {
    ios::sync_with_stdio(0), cin.tie(0);
    
    int n, p, m; cin >> n >> p >> m;
    int s = n - p;

    dp[0][0] = 1;//注意要初始化，和为0，异或和为0 的方法数只有一种
    
    // 无序分拆标准 DP：枚举用的数字 i
    for (int i = 1; i <= s; i++) {//i 的意思：现在我们 “允许使用大小为 i 的堆” 了。
        // 逆序遍历，保证不重复计数（核心）
        for (int j = i; j <= s; j++) {//可以理解为 背包容量为j
	        
	        //往总和为j - i 的组合中 加一个大小为i的数，得到
            for (int k = 0; k <= s; k++) {
                dp[j][k] = (dp[j][k] + dp[j - i][k ^ i]) % m;
            }
	    }
    }

    cout << dp[s][p] % m << endl;
    
    return 0;
}
```

## **无序整数分拆的标准 DP 方式**


## 1. 核心思想：强制分拆 “非递减”

无序分拆最难的是：

**不重复、不遗漏地统计每一种组合**

解决方法只有一个：

**强制所有分拆按从小到大的顺序出现**

也就是只允许形如：

- `1+1+2`
- `1+3`
- `2+2`
    
    这种**非递减**的组合

不允许：

- `2+1`
- `3+1+1`

这样一来，**每一种分拆只会被算 exactly 一次**。

---

## 2. 循环 `i = 1 ~ s` 到底在干嘛？

## **i 表示：当前允许使用的最大堆大小是 i**

- 一开始 `i=1`：只能用 1
- 然后 `i=2`：可以用 1、2
- 然后 `i=3`：可以用 1、2、3
- ……

每一轮，我们只做一件事：

**在已有的分拆基础上，加入若干个大小为 i 的堆**

这就天然保证了：

**后面加的数 ≥ 前面的数 → 非递减 → 不重复**

---

## 3. 为什么 `j` 从 `i` 开始？

因为你要**加入一个大小为 i 的堆**，

那当前总和 `j` 至少要能放下一个 i：

**j ≥ i**

否则 `j - i` 是负数，根本不可能。

cpp

运行

```
for (int j = i; j <= s; j++)
```

这就是完全背包的写法：

**同一个数字 i 可以用无限次（可以放很多堆 i）**

---

## 4. 转移式到底在说什么？（结合本题）

cpp

运行

```
dp[j][xr] += dp[j - i][xr ^ i];
```

翻译成题目场景就是：

> 我现在要构造一个**总和 j、异或和 xr** 的分拆，
> 
> 并且这个分拆里**至少有一个堆大小是 i**
> 
> 那它一定来自：
> 
> 一个**总和 j−i、异或和 xr ^ i** 的旧分拆，
> 
> 我再往里面**加一个大小为 i 的堆**。

加一个堆 i 后：

- 总和：`(j-i) + i = j`
- 异或和：`(xr ^ i) ^ i = xr`

完美对应。



# 转化型 DP
把「最小修改成本」转化成「最大保留收益」
[Problem - G - Codeforces](https://codeforces.com/contest/2167/problem/G)
![](image/Pasted%20image%2020260514165413.png)
```c++
void ap() {
	int n; cin >> n;
    vector<int> a(n), c(n);
    for (int & i : a) cin >> i;
    for (int & i : c) cin >> i;

    vector<int> dp(n);
    for (int i = 0; i < n; i++) dp[i] = c[i];

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < i; j++) {
            if (a[j] <= a[i]) dp[i] = max(dp[i], dp[j] + c[i]);
        }
    }

    cout << accumulate(c.begin(), c.end(), 0LL) - *max_element(dp.begin(), dp.end()) << endl;
	return ;
}
```
因为题目要求的是整个数组都变为非递减，而非子序列，所以我们可以进行转换

如果我们全部都改，那么必然是可以成立的。那么我们可以从这个角度看，我们只需减去不修改的

即 
总花费 = 全部修改的总成本 − **最多能不修改省下的钱**

因此，我们只需要找到可以省下的，即**花费最多**的 **非递减子序列**   (不要求最长子序列)

由此，dp[i] 表示 以i结尾的 花费最多的 非递减子序列


# 子序列计数型DP
[D-小红的子序列计数_牛客周赛 Round 146](https://ac.nowcoder.com/acm/contest/135882/D)
![](image/Pasted%20image%2020260602150930.png)
```c++
void ap() {
	int n; cin >> n;
    string s; cin >> s;
    
    array<int, 6> dp{};//设 dpr 表示当前已经选出的子序列中，数值模 6 等于 r 的方案数。
    dp[0] = 1;
    
    for (char c : s) {
        int x = c - '0';
        auto ndp = dp;
        for (int r = 0; r < 6; r++) {
            int nr = (r * 10 + x) % 6;
            ndp[nr] = (ndp[nr] + dp[r]) % MOD;
        }
        dp = ndp;
    }
    
    cout << (dp[0] - 1 + MOD) % MOD << endl;
    
	return ;
}
```
![](image/Pasted%20image%2020260602151025.png)
```c++
/*
分析与思路推导
6的倍数 说明必须同时是2和3的倍数 
2的倍数代表末位为偶数 3的倍数代表数位和为3的倍数
本题时间复杂度接近需要n 可以考虑dp 
由于是子序列问题 需要用线性dp 也就是分开考虑 选/不选？或者其他
因为3 所以需要计算当前子序列的数位和 怎样快速计算？ 
自然是统计 到第i项为止 余数分别是012三种情况的数量  因为最终符合条件的是3的倍数 所以要%3
然后还要判断当前第i项 选/不选 判断末尾是不是偶数 但是转移一定要转移的 
所以得出第一个转移方程 dp1[i][j] i是前i项 j是余数 存储的值是子序列的数量
转移方程1只能得出数位和取模后余数是0的数量 还要统计 末尾是偶数的数量
然后第二个转移方程dp2[i][j] 就是以当前第i项为末尾的 余数为j的数量 
那直接用dp2 不就完事了？ 直接统计余数为0且第i项为末尾的子序列数
实则不然 先开始dp2的转移方程推导 
当前第i项为now 设前i-1项得到的数位和为j 当前第i项的数位和为r  
求当前第i项数位和r怎么转移过来的 自然是求now%3加上j 
r已知 0 1 2枚举 now已知 j未知（只是不知道哪个转移过来） j=（r-now%3+3）%3 当前的r减去当前的now 得到i-1的j
dp2[i][r]=dp2[i-1][(r-(now%3)+3)%3]; 但真的是dp2转移吗？ dp2[i-1][j]代表以i-1为结尾的 但是dp2[i][r]是以i为结尾 跟i-1是不是结尾没关系
所以此时 dp1 就派上用场了 dp2[i][r]=dp1[i-1][(r-(now%3)+3)%3] 用dp1 记录全部子序列
那dp1 的转移方程 推导
dp1[i][j]=dp[i-1][] dp1的前i项的结尾为j的答案 
自然是第i项不选 直接从dp1[i-1][j]继承过 第i项选 从dp2[i-1][j]继承过来 
所以 dp1[i][j]=dp1[i-1][j]+dp2[i][j];
*/
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int mod=998244353;
int dp1[200005][3]={};
int dp2[200005][3]={};
signed main(){
    int n;
    cin>>n;
    string s;
    cin>>s;
    dp1[0][0]=1;//前0项末尾为0的自然是0 空项末尾肯定啥也没有啊 是0
    int ans=0;
    for(int i=1;i<=n;i++){
        int val=s[i-1]-'0';//取数字别忘了变形式
        int my=val%3;
        //dp1[0][]已知 但是dp1 更新不了 只能更新dp2
        for(int r=0;r<3;r++){
            dp2[i][r]=(dp1[i-1][(r-my+3)%3]) %mod;//要从dp1继承i-1项前的全部子序列和
        }
        for(int r=0;r<3;r++){
            dp1[i][r]=(dp1[i-1][r]+dp2[i][r])%mod;
        }
        if(val%2==0){//如果当前的数字为偶数 就记录以当前数字为结尾的全部数量
            ans=(ans+dp2[i][0])%mod;
        }
    }
    cout<<ans;
    return 0;
}
```


# 概率DP 、逆元、费马小定理
[I-冰淇淋大危机_第七届武汉纺织大学ACM程序设计竞赛(同步赛）](https://ac.nowcoder.com/acm/contest/136171/I)

![](image/Pasted%20image%2020260607151514.png)
![](image/Pasted%20image%2020260607151555.png)
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

在算法竞赛或组合数学中，我们经常需要计算 $\frac{a}{b} \pmod p$。但是模运算不能直接做除法，我们需要找到 $b$ 的**乘法逆元**（记作 $b^{-1}$），使得：

$$\frac{a}{b} \equiv a \cdot b^{-1} \pmod p$$

根据费马小定理，当 $p$ 为质数（你代码中的 `MOD = 998244353` 恰好是一个大质数）时，有：

$$b^{p-1} \equiv 1 \pmod p$$

我们把左边拆开：

$$b \cdot b^{p-2} \equiv 1 \pmod p$$

两边同时除以 $b$（即乘以 $b$ 的逆元），就得到了：

$$b^{-1} \equiv b^{p-2} \pmod p$$

**结论：** 在模质数 $p$ 的情况下，一个数 $b$ 的逆元，就等于 **$b$ 的 $p-2$ 次方**。你的代码通过 `ksm(x, MOD - 2)`（快速幂）正是利用这个原理，在 $O(\log p)$ 的高效时间复杂度内计算出了 `inv100`（即 $\frac{1}{100}$ 在模 998244353 意义下的等价整数）。


# 期望 DP / 状态机生成函数
[B-薛定谔的迷你哆啦_第七届武汉纺织大学ACM程序设计竞赛(同步赛）](https://ac.nowcoder.com/acm/contest/136171/B)
![](image/Pasted%20image%2020260607185733.png)


# 子序列DP 、 质因数分解 、 路径还原
[D-小红的子序列_牛客周赛 Round 147](https://ac.nowcoder.com/acm/contest/136224/D)
![](image/Pasted%20image%2020260608135300.png)
```c++
const int MX = 1e6 + 5;
vector<int> mn_prime(MX, 0), primes;

void init() {
    for (int i = 2; i < MX; i++) {
        if (mn_prime[i] == 0) {
	        mn_prime[i] = i;
	        primes.push_back(i);
        }

        for (int j = 0, t; j < primes.size() && (t = primes[j] * i) < MX; j++) {
            mn_prime[t] = primes[j];
            if (i % primes[j] == 0) break;
        }
    }
    
}

vector<int> fac(int x) {
    vector<int> ans;
    while (x > 1) {
        int t = mn_prime[x];
        ans.push_back(t);
        while (x % t == 0) x /= t;
    }
    return ans;
}

void ap() {
	int n; cin >> n;
    vector<int> a(n);
    for (int & i : a) cin >> i;
    
    vector<int> dp(MX + 1), pre_idx(n + 1, -1);
    vector<int> last_idx(MX, -1);
    int mx = 0, idx = -1;
    for (int i = 0; i < n; i++) {
        int x = a[i];
        
        vector<int> f = fac(x);
        int m = 1;
        int pre = -1;
        
        for (int p : f) {
            int t = x / p;
            if (last_idx[t] != -1 && dp[t] + 1 > m) {
                m = dp[t] + 1;
                pre = last_idx[t];
            }
        }
        
        pre_idx[i] = pre;
        dp[x] = max(dp[x], m);
        
        last_idx[x] = i;
        
        if (dp[x] > mx) {
            mx = dp[x];
            idx = i;
        }
        
    }
    
    vector<int> ans;
    int i = idx;
    while (i != -1) {
        ans.push_back(a[i]);
        i = pre_idx[i];
    }
    reverse(ans.begin(), ans.end());
    
    cout << ans.size() << endl;
    for (int x : ans) cout << x << " ";
    
    
	return ;
}

```


## 关联笔记
- [[前缀和]] — 前缀和优化 DP
- [[异或]] — 异或相关 DP
- [[数学]] — 逆元、费马小定理（概率 DP 基础）
- [[概率期望]] — 概率 DP 与期望 DP
- [[位运算]] — 位运算 DP
- [[动态规划/概率DP]] — 概率 DP 专题
- [[动态规划/背包]] — 背包 DP 专题
