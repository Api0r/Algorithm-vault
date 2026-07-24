[E-镜像_牛客周赛 Round 132](https://ac.nowcoder.com/acm/contest/128672/E)
![[Pasted image 20260319114502.png]]```
```c++
void solve() {
	int a, b, k; cin >> a >> b >> k;
    int m = 1e6;
    
    vector<int> d(m + 1, -1);
    queue<pair<int, int>> q;
    q.emplace(a, 0);
    while (!q.empty()) {
        auto t = q.front();
        q.pop();
        
        int u = t.first, dist = t.second;
        if (d[u] != -1) continue;
        d[u] = dist;
        
        if (u + k <= m) {
            q.emplace(u + k, dist + 1);
        }
        if (u % 10) {
            int v = 0;
            while (u) {
                v = v * 10 + u % 10;
                u /= 10; 
            }
            if (1 <= v && v <= m) {
                q.emplace(v, dist + 1);
            }
        }
    }
    cout << d[b] << endl;
	return ;
}
```


## 关联笔记
- [[图论]] — 图搜索、拓扑排序（BFS 入度法）
