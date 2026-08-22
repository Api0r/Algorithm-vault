
例如数组 `[1,1,1,2,2]`，其中第 1 小、第 2 小和第 3 小的数都是 1，第 4 小和第 5 小的数都是 2。

- 第 `k` 小等价于：求最小的 `x`，满足 `≤x` 的数至少有 `k` 个。
- 第 `k` 大等价于：求最大的 `x`，满足 `≥x` 的数至少有 `k` 个。


>注 1：一般规定 k 从 1 开始，而不是像数组下标那样从 0 开始。
>
>注 2：部分题目也可以用堆解决。


# 问题再现

这种问题也叫排名问题
- 求第k大
- 前k大之 和、积、差...


# 核心思路

**先求出第 $k$ 大（小）的临界值 $X$，再基于 $X$ 进行后续的一切计算。**

**单调性转化（判定问题）**

- 求**第 $k$ 小**：二分值 $X$，统计 $\le X$ 的元素个数 $cnt$。若 $cnt \ge k$，说明第 $k$ 小 $\le X$（向左缩）；
    
- 求**第 $k$ 大**：二分值 $X$，统计 $\ge X$ 的元素个数 $cnt$。若 $cnt \ge k$，说明第 $k$ 大 $\ge X$（向右缩）。


[D-Flower_Rainbow_and_Grid_牛客小白月赛136](https://ac.nowcoder.com/acm/contest/139208/D)
```c++
#include <bits/stdc++.h>  
using namespace std;  
#define int long long
#define endl '\n'
const int mod = 1e9 + 7;
const int MOD = 998244353;

//每一行单减
//每一列单增

//n * log(m) 找到第k大的数字
//n * log(m) 求前k大的数字之和
//2 * n * log(m) * T

void ap() {
	int n, m, k; cin >> n >> m >> k;
    
    //二分, cnt >= k，越小越true
    //n * log(m)
    auto check = [&](int x) -> bool {
        int cnt = 0;
        for (int i = 1; i <= n; i++) {
            //每一行二分
            //计算每一行 >= x 的数字的数量
            int left = 0, right = m + 1;
            while (left + 1 < right) {
                int mid = left + (right - left) / 2;
                
                int t = i * i - mid * mid;
                if (t >= x) left = mid;
                else right = mid;
            }
            
            cnt += left;//left - 1 + 1
        }
        
        return cnt >= k;
    };
    
    
    //二分找第k大的数字，cnt >= k
    int left = 1 - m * m - 1, right = n * n - 1 + 1;
    while (left + 1 < right) {
        int mid = left + (right - left) / 2;
        
        if (check(mid)) {//越小越true，缩left
            left = mid;
        } else {
            right = mid;
        }
    }
    
    //得到 第k大的数字
    int x = left;
    
    int ans = 0, cnt = 0;
    for (int i = 1; i <= n; i++) {
        
        int left = 0, right = m + 1;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            
            int t = i * i - mid * mid;
            if (t >= x) left = mid;
            else right = mid;
        }
        
        //当前这一行中，>= x 的数量，及 >= x 的数字之和
        cnt += left;
        ans += left * i * i - left * (left + 1) * (2 * left + 1) / 6;
    }
    
    //数字x可能会有重复
    //这个很有意思
    ans += (k - cnt) * x;
    
    cout << ans << endl;
    
	return ;
}

signed main() {
	ios::sync_with_stdio(false);
	cin.tie(nullptr);
	int T = 1;
	cin >> T;
	while (T--) {
		ap();
	} 
    return 0;
}
```
