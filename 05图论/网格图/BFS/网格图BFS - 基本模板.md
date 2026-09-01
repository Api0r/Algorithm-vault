
```c++
int dx[4] = {0, 0, 1, -1}, dy[4] = {1, -1, 0, 0};

int n = grid.size(), m = grid[0].size();

queue<array<int, 2>> q;
vector<vector<int>> dis(n, vector<int> (m, -1));

for (int i = 0; i < n; i++) {
    for (int j = 0; j < m; j++) {
        if (grid[i][j] == 2) {
            q.push({i, j});
            dis[i][j] = 0;
        }
    }
}

while (!q.empty()) {
    auto [i, j] = q.front();
    q.pop();

    for (int k = 0; k < 4; k++) {
        int nx = i + dx[k], ny = j + dy[k];

        if (nx < 0 || nx >= n || ny < 0 || ny >= m || grid[nx][ny] != 1 || dis[nx][ny] != -1) continue;

        //....

        dis[nx][ny] = dis[i][j] + 1;
        q.push({nx, ny});
    }
}

```