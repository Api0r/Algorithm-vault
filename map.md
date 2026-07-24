# it迭代器 - erase
```c++
void solve() {
    int q; cin >> q;
    map<int , int> op;
    int s = 0;
    while (q--) {
        int x, h; cin >> x >> h;
        if (x == 1) {
            s++;
            cout << s;
            op[h]++;
        } else {
            // auto j = op.lower_bound(h + 1);// >= h + 1
            for (auto it = op.begin(); it != op.end();) {
                int y = it->first;
                if (y <= h) {
                    s -= it->second;
                    it = op.erase(it);
                } else {
                    break;
                }
            }
            cout << s;
        }
        if (q != 0) cout << endl;
    }
    return ;
}
```
这里我们需要按顺序获取it->first 的值 ，然后删除当前的键，即erase掉，但是这里不可以用it++遍历
![](image/Pasted%20image%2020260328213530.png)·
因为==erase函数会返回一个指向被删除元素**下一个元素**的迭代器（C++11 起）。通过将返回的迭代器重新赋值给 `it`，你可以继续安全地遍历容器。

**错误写法
```c++
for (auto it = op.begin(); it != op.end(); ++it) {
    if (it->first <= h) {
        s -= it->second;
        op.erase(it);   // it 失效，之后 ++it 是未定义行为
    }
}
```




## 关联笔记
- [前缀和](前缀和.md) — 哈希表配合前缀和
