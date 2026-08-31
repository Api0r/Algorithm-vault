```c++
/*
用途：单模式串匹配、border 与前缀函数。
约定：前缀函数和匹配位置使用 int；空模式串匹配所有 n+1 个位置。
性能：默认使用宏展开后的 int；若大规模数据因此超时或超空间，可将纯下标、状态或计数局部改为 i32。
*/
struct KMP {

    string p;
    vector<int> pi;

    explicit KMP(const string& s) : p(s) {
        build();
    }

    void build() {
        int n = p.size();
        pi.assign(n, 0);
        for (int i = 1, j = 0; i < n; ++i) {
            while (j && p[i] != p[j]) j = pi[j - 1];
            if (p[i] == p[j]) ++j;
            pi[i] = j;
        }
    }

	//模式串 p 在文本串 s 中所有完整出现的「起始下标（0-indexed）」列表
    vector<int> ask(const string& s) const {
        vector<int> a;
        int n = s.size(), m = p.size();
        if (!m) {
            a.resize(n + 1);
            iota(a.begin(), a.end(), 0);
            return a;
        }
        for (int i = 0, j = 0; i < n; ++i) {
            while (j && s[i] != p[j]) j = pi[j - 1];
            if (s[i] == p[j]) ++j;
            if (j == m) {
                a.push_back(i - m + 1);
                j = pi[j - 1];
            }
        }
        return a;
    }
};
```

```c++
//初始化对象
KMP kmp(pattern);
```

这段代码实现了一个基于 **KMP 算法（Knuth-Morris-Pratt）** 的单模式串匹配模板，封装为结构体 `KMP`。其核心功能是**预处理模式串的前缀函数（Border 数组）**，并在目标文本串中**高效查找所有匹配的起始下标**。

### 1. 核心成员变量

- `string p`：待匹配的**模式串**（Pattern）。

- `vector<int> pi`：前缀函数数组（即 $\pi$ 数组 / `next` 数组）。

    - 定义：$\pi[i]$ 表示子串 $p[0 \dots i]$ 的**最长真前缀**同时也是该子串的**后缀**（即 Border）的长度。

### 2. 核心函数解析

#### **构造函数 `KMP(const string& s)`**

- 使用 `explicit` 避免隐式类型转换。

- 保存模式串 `p` 并立即调用 `build()` 计算前缀函数。

#### **前缀函数预处理 `build()`**

时间复杂度：$O(m)$，其中 $m$ 是模式串长度。

```c++
int n = p.size();
pi.assign(n, 0);
for (int i = 1, j = 0; i < n; ++i) {
    while (j && p[i] != p[j]) j = pi[j - 1]; // 失配时利用已知的 border 回退
    if (p[i] == p[j]) ++j;                   // 字符匹配，长度加 1
    pi[i] = j;                               // 记录子串 p[0...i] 的 pi 值
}
```

- 变量 `j` 表示当前匹配的前缀长度。

- 当 $p[i] \neq p[j]$ 时，失配回跳：$j = \pi[j - 1]$，直到找到相等的字符或 $j = 0$。

- 若匹配成功，将 $j$ 加 1 并赋给 $\pi[i]$。

#### **文本匹配 `ask(const string& s)`**

时间复杂度：$O(n)$，其中 $n$ 是文本串 `s` 的长度。

```c++
vector<int> a;
int n = s.size(), m = p.size();

// 特判：空模式串匹配所有位置 [0, n]
if (!m) {
    a.resize(n + 1);
    iota(a.begin(), a.end(), 0); // 填充 0, 1, 2, ..., n
    return a;
}

for (int i = 0, j = 0; i < n; ++i) {
    while (j && s[i] != p[j]) j = pi[j - 1]; // 失配时模式串指针 j 回跳
    if (s[i] == p[j]) ++j;                   // 当前字符匹配成功
    if (j == m) {                            // 匹配到完整模式串
        a.push_back(i - m + 1);              // 记录起始下标
        j = pi[j - 1];                       // 回跳寻找下一个可能的重叠匹配
    }
}
return a;
```

- **空串边界处理**：若模式串为空（$m=0$），约定它在文本串的每个可能位置（共 $n+1$ 个空隙）都匹配，返回 `[0, 1, ..., n]`。

- **滑动匹配**：遍历文本串字符 $s[i]$，利用 `pi` 数组快速调整模式串指针 $j$，避免主串指针回退。

- **重叠匹配支持**：当 $j == m$（完全匹配）时，记录起始位置 $i - m + 1$，并将 $j$ 回跳至 $\pi[j-1]$，继续支持重叠匹配（例如在 `"aaaa"` 中查找 `"aa"` 会得到 `[0, 1, 2]`）。

### 3. 复杂度与优势

- **时间复杂度**：

    - 预处理阶段：$O(m)$

    - 查询阶段：$O(n)$

    - 总复杂度：$O(n + m)$，避免了朴素匹配的 $O(n \times m)$ 最坏复杂度。

- **空间复杂度**：$O(m)$，仅占用存储模式串和 $\pi$ 数组的空间。
