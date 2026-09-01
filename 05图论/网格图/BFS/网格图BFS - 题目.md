# 1.状态压缩

[E-Flower_Rainbow_and_Aurora_牛客小白月赛136](https://ac.nowcoder.com/acm/contest/139208/E)



# 2.优先队列 + 按时间小根堆

[D-小L的扩展_2026牛客寒假算法基础集训营6](https://ac.nowcoder.com/acm/contest/120566/D)

```c++
#include <bits/stdc++.h>  
using namespace std;  
#define int long long
#define endl '\n'
const int mod = 1e9 + 7;
const int MOD = 998244353;

int dx[4] = {0, 0, 1, -1}, dy[4] = {1, -1, 0, 0};

void ap() {
	int n, m, a, b; cin >> n >> m >> a >> b;
    
    vector<vector<int>> tim(n, vector<int> (m, 0));
    
    using p = array<int, 3>;//time, x, y
    priority_queue<p, vector<p>, greater<p> > pq;
    vector<vector<int>> dis(n, vector<int> (m, -1));
    
    for (int i = 0; i < a; i++) {
        int x, y; cin >> x >> y; x--, y--;
        
        pq.push({0, x, y});
    }
    
    for (int i = 0; i < b; i++) {
        int x, y, t; cin >> x >> y >> t; x--, y--;
        
        tim[x][y] = t;
    }
    
    int ans = 0;
    while (!pq.empty()) {
        auto[t, x, y] = pq.top();
        pq.pop();
        
        //因为变白色周围可能会有已经染黑的
        if (dis[x][y] != -1) continue;
        
        ans = max(ans, t);
        dis[x][y] = t;
        
        for (int k = 0; k < 4; k++) {
            int nx = x + dx[k], ny = y + dy[k];
            
            if (nx < 0 || nx >= n || ny < 0 || ny >= m || dis[nx][ny] != -1) continue;
            
            //(nx, ny)能够被染黑的时间
            int d = max(t + 1, tim[nx][ny]);
            pq.push({d, nx, ny});
        }
        
    }
    
    cout << ans << endl;
    
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