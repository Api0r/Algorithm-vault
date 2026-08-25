# K.拯救猫猫
[拯救猫猫 - 题目详情 - HydroOJ](https://hydro.ac/d/HBCPC2026/p/HBCPC2026M)
![](image/Pasted%20image%2020260528135751.png)
![](image/Pasted%20image%2020260528135812.png)
## 思路：“可撤销功能”的普通并查集
- 第一次遍历：找到起点，终点并记录下来。找到dog并存储在数组中，因为就是按顺序找所以不用排序
- 遍历dog，将其视线标记，预处理其视线
- 首先所有狗都不睡，跑一遍并查集，将安全点的起终点连通，这也是作为版本0的并查集
- 依次枚举dog，将其睡着，将其睡着后的安全点跑一遍并查集。并记录每次merge的历史，以便后面的撤销
- 撤销到0版本的并查集，回到上面 继续枚举，一旦枚举到可以连通，直接 返回

```c++
#include <bits/stdc++.h>  
using namespace std;  
#define int long long
#define endl '\n'

int n, m, total_cells;
vector<int> fa, sz;

// 记录并查集修改历史的结构体
struct History {
    int u, v, old_sz_u;
};
vector<History> history_stack;

// 初始化并查集
void init_dsu(int n) {
    fa.resize(n + 1);
    sz.resize(n + 1, 1);
    for (int i = 1; i <= n; i++) fa[i] = i;
    history_stack.clear();
}

// 注意：可撤销并查集绝对不能用路径压缩！只能纯往上找
int find(int x) {
    while (x != fa[x]) x = fa[x];
    return x;
}

// 合并，并记录历史
void merge_elements(int x, int y) {
    int fx = find(x);
    int fy = find(y);
    if (fx == fy) return;
    
    // 按秩合并：把小的挂到大的上面
    //sz[fx] >= sz[fy]
    if (sz[fx] < sz[fy]) swap(fx, fy);
    
    // 记录 fy 挂到 fx 之前，fx 的大小状态
    history_stack.push_back({fx, fy, sz[fx]});
    
    fa[fy] = fx;
    sz[fx] += sz[fy];
}

// 撤销到指定的历史版本大小
void rollback(int target_size) {
    while (history_stack.size() > target_size) {
        History h = history_stack.back();
        history_stack.pop_back();
        
        fa[h.v] = h.v;       // 把小的孤立出来
        sz[h.u] = h.old_sz_u; // 还原 大的 size
    }
}

bool is_dog(char c) {
    return (c == 'U' || c == 'D' || c == 'L' || c == 'R');
}

void solve() {
    cin >> n >> m;
    total_cells = n * m;
    
    vector<string> a(n);
    for (int i = 0; i < n; i++) cin >> a[i];

    int start_pos = 0, end_pos = 0;
    struct Dog { int x, y; char dir; int id; };
    vector<Dog> dogs;
    int dog_idx = 1;

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            if (a[i][j] == 'S') start_pos = i * m + j + 1;
            if (a[i][j] == 'E') end_pos = i * m + j + 1;
            if (is_dog(a[i][j])) {
                dogs.push_back({i, j, a[i][j], dog_idx++});
            }
        }
    }

    vector<vector<int>> cover_cnt(n, vector<int>(m, 0));
    vector<vector<int>> owner(n, vector<int>(m, 0));

    // 预处理狗的射线
    for (auto &d : dogs) {
        int dx = 0, dy = 0;
        if (d.dir == 'U') dx = -1;
        if (d.dir == 'D') dx = 1;
        if (d.dir == 'L') dy = -1;
        if (d.dir == 'R') dy = 1;

        int nx = d.x + dx, ny = d.y + dy;
        while (nx >= 0 && nx < n && ny >= 0 && ny < m) {
            if (a[nx][ny] == '#' || is_dog(a[nx][ny])) break;
            cover_cnt[nx][ny]++;
            owner[nx][ny] = d.id;
            nx += dx; ny += dy;
        }
    }

    // 初始化并查集
    init_dsu(total_cells);

    int dx[] = {-1, 1, 0, 0};
    int dy[] = {0, 0, -1, 1};

    // 1. 构建安全格子的基础连通底色
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            if (a[i][j] != '#' && !is_dog(a[i][j]) && cover_cnt[i][j] == 0) {
                int u = i * m + j + 1;
                for (int k = 0; k < 4; k++) {
                    int nx = i + dx[k], ny = j + dy[k];
                    if (nx >= 0 && nx < n && ny >= 0 && ny < m) {
                        if (a[nx][ny] != '#' && !is_dog(a[nx][ny]) && cover_cnt[nx][ny] == 0) {
                            int v = nx * m + ny + 1;
                            merge_elements(u, v);
                        }
                    }
                }
            }
        }
    }

    // 记录下基础底色合并完之后的栈大小
    int base_history_size = history_stack.size();

    // 如果不睡着本来就通
    if (find(start_pos) == find(end_pos)) {
        cout << dogs[0].x + 1 << " " << dogs[0].y + 1 << endl;
        return;
    }

    // 2. 枚举每只狗
    for (auto &d : dogs) {
        int id = d.id;

        // 模拟当前狗睡觉：连通它的独占格子
        int rdx = 0, rdy = 0;
        if (d.dir == 'U') rdx = -1;
        if (d.dir == 'D') rdx = 1;
        if (d.dir == 'L') rdy = -1;
        if (d.dir == 'R') rdy = 1;

        int nx = d.x + rdx, ny = d.y + rdy;
        while (nx >= 0 && nx < n && ny >= 0 && ny < m) {
            if (a[nx][ny] == '#' || is_dog(a[nx][ny])) break;
            
            if (cover_cnt[nx][ny] == 1 && owner[nx][ny] == id) {
                int u = nx * m + ny + 1;
                for (int k = 0; k < 4; k++) {
                    int nnx = nx + dx[k], nny = ny + dy[k];
                    if (nnx >= 0 && nnx < n && nny >= 0 && nny < m) {
                        if (a[nnx][nny] != '#' && !is_dog(a[nnx][nny])) {
                            if (cover_cnt[nnx][nny] == 0 || (cover_cnt[nnx][nny] == 1 && owner[nnx][nny] == id)) {
                                int v = nnx * m + nny + 1;
                                merge_elements(u, v);
                            }
                        }
                    }
                }
            }
            nx += rdx; ny += rdy;
        }

        // 检查连通性
        if (find(start_pos) == find(end_pos)) {
            cout << d.x + 1 << " " << d.y + 1 << endl;
            return;
        }

        // 关键一步：单只狗检查完不通，立刻一键撤销，恢复到基础安全底色！
        rollback(base_history_size);
    }

    cout << "-1 -1" << endl;
}

signed main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int T_cases;
    if (cin >> T_cases) {
        while (T_cases--) {
            solve();
        }
    }
    return 0;
}
```

## 关联笔记
- [并查集](并查集.md) — 可撤销并查集在 CCPC 中的应用
- [可撤销并查集](02数据结构/并查集/可撤销并查集.md) — 可撤销并查集
