
```c++
#include <bits/stdc++.h>
using namespace std;
#define int long long
#define endl '\n'
const int mod = 1e9 + 7;
const int MOD = 998244353;

/*
用途：一维树状数组，默认维护单点加、前缀和与区间和。

接口（均为 1-indexed）：modify(p,x)、ask(p)、ask(l,r)、askKth(k)。
update(l,r,x) 是差分用法：区间加、单点查询；不要与区间和语义混用。
askKth(k) 返回最小的 p 使前缀和 >= k；要求各点权非负，非法 k 返回 n + 1。
*/
template <class T>
struct BIT {
    int n = 0;
    vector<T> bit;

    BIT() = default;
    explicit BIT(int n) {
        init(n);
    }

    void init(int _n) {
        n = _n;
        bit.assign(n + 1, T{});
    }

    template <class A>
    void build(const A &a) {
        fill(bit.begin(), bit.end(), T{});
        for (int i = 1; i <= n; ++i) {
            bit[i] += a[i];
            int j = i + (i & -i);
            if (j <= n) bit[j] += bit[i];
        }
    }

    //将位置 x 增加 v
    void modify(int x, const T &v) {
        assert(1 <= x && x <= n);
        for (; x <= n; x += x & -x) bit[x] += v;
    }

    void update(int l, int r, const T &v) {
        if (l > r) return;
        assert(1 <= l && r <= n);
        modify(l, v);
        if (r < n) modify(r + 1, -v);
    }

    //查询前缀和 [1, x]
    T ask(int x) const {
        x = min(x, n);
        T res{};
        for (; x > 0; x -= x & -x) res += bit[x];
        return res;
    }

    T ask(int l, int r) const {
        if (l > r) return T{};
        assert(1 <= l && r <= n);
        return ask(r) - ask(l - 1);
    }

    int askKth(const T &k) const {
        if (!(k > T{})) return 1;
        int pos = 0;
        T pre{};
        int stp = 1;
        while ((stp << 1) <= n) stp <<= 1;
        for (; stp; stp >>= 1) {
            int nxt = pos + stp;
            if (nxt <= n && pre + bit[nxt] < k) {
                pre += bit[nxt];
                pos = nxt;
            }
        }
        return pos + 1;
    }
};

void ap() {
	int n, q; cin >> n >> q;
    vector<int> a(n + 1), b(n + 1);
    for (int i = 1; i <= n; ++i) cin >> a[i];

    auto calc = [&](int i) {
        return (a[i - 1] + a[i + 1] > 2 * a[i]) ? 1LL : 0LL;
    };

    for (int i = 2; i < n; ++i) b[i] = calc(i);

    BIT<int> tr(n);
    tr.build(b);

    while (q--) {
        int op, x, y;
        cin >> op >> x >> y;
        if (op == 1) {
            a[x] = y;
            for (int i = max(2LL, x - 1); i <= min(n - 1, x + 1); ++i) {
                int v = calc(i);
                tr.modify(i, v - b[i]);
                b[i] = v;
            }
        } else {
            cout << tr.ask(x + 1, y - 1) << endl;
        }
    }

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

## 类模板声明


**以这个为例**

```c++
template <class T> struct BIT{...}

BIT<int> tr(n);
```


`template <class T> struct BIT` 是 C++ 中的**类模板（Class Template）**声明。它的核心思想是**类型参数化**，即把数据类型作为参数抽取出来，让一份结构体代码可以适用于多种不同的数据类型。

  

### 1. 语法拆解

Plaintext

```
template <class T>    struct    BIT
└───┬────────────┘    └─┬──┘   └─┬┘
    │                   │        └─ 结构体名称
    │                   └────────── C++ 自定义复合类型（默认成员公开）
    └────────────────────────────── 模板头部声明（定义类型占位符 T）
```

- **`template <...>`**：告诉编译器紧随其后的结构体不是一个固定的具体类型，而是一个**代码生成的蓝图（模板）**。
    
      
    
- **`<class T>`**（或 `<typename T>`）：
    
      
    - `T` 是一个**类型形参（Type Parameter / 占位符）**。
        
          
        
    - `class` 在此处与 `typename` 完全等价，表示“`T` 代表某种数据类型”。
        
          
        
- **`struct BIT`**：定义模板的名字为 `BIT`。
    
      
    

### 2. 为什么需要类模板？（解决什么痛点）

如果不使用模板，处理不同数据类型时必须手动复制代码：

  

C++

```
// 没有模板时的做法：重复定义结构体
struct BIT_int {
    vector<int> bit;
    void modify(int x, int v) { ... }
};

struct BIT_ll {
    vector<long long> bit;
    void modify(int x, long long v) { ... }
};
```

使用 `template <class T>` 后，只需要写一套逻辑，结构体内部的所有类型都用 `T` 代替：

  

- 存储容器：`vector<T> bit;`
    
      
    
- 修改入参：`void modify(int x, const T &v)`
    
      
    
- 查询返回值：`T ask(int x)`
    
      
    
- 零值初始化：`T{}`（根据类型自动生成默认值，如 `int` 为 `0`，自定义类为其默认构造）
    
      
    

### 3. 编译期特化（Instantiation）

模板本身**不会生成机器代码**，也不会占用内存。只有在代码中显式指定类型并创建实例时，编译器才会按需“填词造句”生成具体的类：

  

C++

```
BIT<int> tr1;            // 编译器自动生成一套 T = int 的代码
BIT<long long> tr2;      // 编译器自动生成一套 T = long long 的代码
BIT<double> tr3;         // 编译器自动生成一套 T = double 的代码
```

### 4. 这里的类型 `T` 需要满足什么条件？

从该树状数组的实现逻辑来看，传入的类型 `T` 必须支持以下运算，否则会在编译期报错：

|**操作语法**|**对应模板中的使用位置**|**说明**|
|---|---|---|
|**`T{}`**|`init()` / `ask()`|支持默认构造（作为零元/初值）|
|**`+=`**|`modify()` / `ask()`|支持累加操作|
|**`-`**|`ask(l, r)` / `update()`|支持取负与减法操作|
|**`>` / `<`**|`askKth()`|若调用倍增查找，需支持大小比较|


## 实例化


```c++
BIT<int> tr(n);
```


这句话解释了 C++ 模板在**编译期**发生的隐式实例化（Implicit Instantiation）过程。

  

### 1. 为什么说“编译器自动生成代码”？

模板（`template <class T> struct BIT`）本身只是一个**代码模版（蓝图）**，它不占用任何内存，也不能直接运行。

  

当编译器在代码中读到 `BIT<int> tr1;` 时，它会像**文本替换**一样，在后台自动生成一份专属于 `int` 类型的结构体代码：

  

C++

```
// 编译器在后台生成的具体类（概念等价代码）：
struct BIT_int {
    int n = 0;
    vector<int> bit;

    void init(int _n) {
        n = _n;
        bit.assign(n + 1, int{}); // int{} 默认为 0
    }

    void modify(int x, const int &v) {
        for (; x <= n; x += x & -x) bit[x] += v;
    }

    int ask(int x) const {
        int res{};
        for (; x > 0; x -= x & -x) res += bit[x];
        return res;
    }
    // ... 其他函数里的 T 也全部被替换为 int
};
```

生成完毕后，编译器再用这个具体的类型去创建变量 `tr1`。

  

### 2. 这个过程带来的优势与代价

- **优势（零运行时开销 / Zero-overhead）**：
    
    替换和生成代码是在**编译期间**完成的。程序运行时没有任何类型判断或多态查找的开销，运行效率与手写一个纯 `int` 版本的树状数组完全一致。
    
      
    
- **代价（代码膨胀 / Code Bloat）**：
    
    如果你在同一个程序里同时写了 `BIT<int>`、`BIT<long long>` 和 `BIT<double>`，编译器就会生成 **3 份**独立的目标二进制代码，会轻微增加可执行文件的大小。
    
      
    

### 3. 本行代码的执行结果

- **`BIT<int>`**：完成模板实例化，确定类型为一个元素类型为 `int` 的树状数组结构体。
    
      
    
- **`tr1`**：在栈上创建一个名为 `tr1` 的对象，此时由于没有传参，会调用默认构造函数 `BIT() = default;`（内部 `n = 0`，`bit` 数组为空，后续需调用 `tr1.init(n)` 初始化后方可使用）。