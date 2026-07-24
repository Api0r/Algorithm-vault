# set模拟打怪 | 二分

[Problem - F - Codeforces](https://codeforces.com/contest/1985/problem/F)
![](image/Pasted%20image%2020260402165410.png)
时间复杂度O(n * log n)
- 每个 `set` 操作复杂度：**O(log n)**
- 每次攻击至少打一点伤害，最多n次循环

每次循环取出set中最早可以打出的操作，然后更新，时间复杂度恒为**O(log n)**
（二分注意细节，很容易wa）

set<pair<int, int>> 模拟
```c++
#include <bits/stdc++.h>
using namespace std;

int main(){
    int t; cin >> t;
    while(t--){
        int h, n; cin >> h >> n;
        vector<int> a(n), c(n);
        for(int& i: a) cin >> i;
        for(int& i: c) cin >> i;
        
        set<pair<long long, int>> S;
        for(int i = 0; i < n; i++){
            S.insert({1, i});
        }
        
        long long last_turn = 1;
        while(h > 0){
            auto [turn, i] = *S.begin();
            S.erase(S.begin());
            last_turn = turn;
            h -= a[i];
            S.insert({turn + c[i], i});
        }
        
        cout << last_turn << "\n";
    }
}
```

二分 | 猜答案
```c++
void solve() {
    int h, n; cin >> h >> n;
    vector<int> a(n), c(n);
    for (int i = 0; i < n; i++) cin >> a[i];
    for (int i = 0; i < n; i++) cin >> c[i];

    auto check = [&](int x) -> bool {
        int s = 0;
        for (int i = 0; i < n && s < h; i++) {
            int cnt = (x - 1) / c[i] + 1;//这里的减一，加一很细节，容易错
            if (cnt >= h) {
                return true;
            }
            s += 1LL * cnt * a[i];
            if (s >= h) {
                return true;
            }
        }
        return s >= h;

    };

    int sum = reduce(a.begin(), a.end(), 0LL);
    if (h - sum <= 0) {
        cout << 1 << endl;
        return;
    }

    int l = 0, r = 1e12;//开1e18就过不了
    while (l + 1 < r) {
        int mid = l + (r - l) / 2;
        if (check(mid) ) {
            r = mid;
        } else {
            l = mid;
        }
    }

    cout << r << endl;
    return ;

}
```

# 进制 | 预处理

[Problem - E - Codeforces](https://codeforces.com/contest/1999/problem/E)
![](image/Pasted%20image%2020260404165336.png)
每次可以选择两个数，进行乘3 和 除以 3 的操作，这个操作可以看成是

```c++
#include <bits/stdc++.h>  
using namespace std;  
#define int long long
#define endl '\n'
const int mod = 1e9 + 7;
const int MOD = 998244353;

const int N = 2e5 + 10;
int pre[N];
int f(int x) {
	int ans = 0;
	while (x) {
		ans++;
		x /= 3;
	}
	return ans;
}

void solve() {
	int l, r; cin >> l >> r;
	cout << pre[r] - pre[l - 1] + f(l) << endl;
	return ;
}

signed main() {
	ios::sync_with_stdio(false);
	cin.tie(nullptr);
	int T = 1;
	cin >> T;
	pre[0] = 0;
	pre[1] = 1;
	for (int i = 1; i < N; i++) {
		pre[i] = pre[i - 1] + f(i);
	}
	while (T--) {
		solve();
	} 
    return 0;
}
```

# 回文串palindrome | mex
[Problem - D - Codeforces](https://codeforces.com/contest/2227/problem/D)
![](image/Pasted%20image%2020260501202417.png)
由于mex的性质，所以每一个回文串必须含0，才会使得mex最大

所以我们只需要看0得位置，有三种情况
- 以第一个0为中心的回文串
- 以第二个0为中心的huiwenc
- 两个0可以位于首位对应的两边

我的思路和题解如出一辙，但是在第三个情况的时候错误
这里可以学习题解的p函数，这是以l，r向两边扩展的回文串计算，同时利用set记录mex

```c++
#include <bits/stdc++.h>  
using namespace std;  
#define int long long
#define endl '\n'
const int mod = 1e9 + 7;
const int MOD = 998244353;

int n;
vector<int> a;

int p(int l, int r) {
    set<int> s;
    for (int i = 0; i <= n; i++) s.insert(i);

    while (l >= 0 && r < 2 * n && a[l] == a[r]) {
        s.erase(a[l]);
        l--, r++;
    }

    return *s.begin();
}

void solve() {
	cin >> n;
    a.resize(2 * n);
    int x = -1, y = -1;
    for (int i = 0; i < 2 * n; i++) {
        cin >> a[i];
        if (a[i] == 0) {
            if (x == -1) {
                x = i;
            } else {
                y = i;
            }
        }
    }

    vector<int> t = a;
    reverse(t.begin(), t.end());
    if (t == a) {
        cout << n << endl;
        return;
    }

    cout << max({p(x, x), p(y, y), p((x + y) / 2, (x + y + 1) / 2)}) << endl;
    
        
	return ;
}

signed main() {
	ios::sync_with_stdio(false);
	cin.tie(nullptr);
	int T = 1;
	cin >> T;
	while (T--) {
		solve();
	} 
    return 0;
}
```


# 后缀最小suf_mn | 贪心

[Problem - E - Codeforces](https://codeforces.com/contest/2227/problem/E)
![](image/Pasted%20image%2020260501200741.png)
记录每个位置的后缀最小值，那么这一列可以移动的数量就是ai - suf_mn[i]
我们可以先记录一个sum为数组和，然后计算suf_mn 的时候减去，最后的sum值就是可以移动的数量

算完 在不删任何cube的情况下的数量，那么我们还需考虑删除一个的情况，我们可以将可以移动的cube扔掉，直接没有了，因为不管后面怎么删，这些块总是会move的，所以可以直接扔了，便于观察

tips:**后缀数组从左到右是 非递减的。**

扔掉之后，我们发现，剩下的就是suf_mn数组！，现在问题变成
在一个非递减的数组中删除一个cube，然后再进行重力shift，使得move的cube数量最大化

因为是非递减的，所以我们发现，只有在删除连续的相等的块的最右边的块的时候，才会产生新的move的cube，move的数量是连续快的长度cnt - 1
![](image/Pasted%20image%2020260501202315.png)

所以问题变成，计算连续的最大的长度cnt

```c++
void solve() {
	int n; cin >> n;
    vector<int> a(n);
    int sum = 0;
    for (int & i : a) {
        cin >> i;
        sum += i;
    }

    if (a.back() == 1) {
        cout << sum - 1 << endl;
        return;
    }

    vector<int> suf_mn(n);
    suf_mn[n - 1] = a[n - 1];
    sum -= suf_mn[n - 1];

    for (int i = n - 2; i >= 0; i--) {
        suf_mn[i] = min(suf_mn[i + 1], a[i]);
        sum -= suf_mn[i];
    }

    int mx = -1, cnt = 1;
    for (int i = 1; i < n; i++) {
        if (suf_mn[i] == suf_mn[i - 1]) {
            cnt++;
        } else {
            mx = max(mx, cnt);
            cnt = 1;
        }
    }

    mx = max(mx, cnt);
    cout << sum + mx - 1 << endl;

	return ;
}
```

# 数学 | 猜.推 公式
[Problem - C - Codeforces](https://codeforces.com/contest/2220/problem/C)
![](image/Pasted%20image%2020260503001119.png)
首先，我们最先想到的是，一个n × m 的gird ，其使用的segments的数量是m×(n + 1) + n × (m + 1)，即2mn + n + m
显然，这个数量需要等于给定的数量，所以有p + 2q = 2mn + m + n;

这里还有一个关键的贪心推导公式，即p >= abs(n - m)
这里给出答案的proof
同余没看懂，但是构造可以看下面的图例理解，即m, n的高度差需要单个的segment去弥补
![](image/Pasted%20image%2020260503001932.png)
然后这里的枚举也很有意思，有数学推导的美
![](image/Pasted%20image%2020260503002206.png)
- 首先假设m >= n
- 然后代入公式有 n <= sqrt(p / 2 + q)
- 所以枚举n的范围就有了
- 我们就只需要每次check一下m是否存在，最后再判断p >= abs(m - n)
```c++
void ap() {
	int p, q; cin >> p >> q;

    int sum = p + 2 * q;
    for (int i = 1; i <= (int)sqrt(p / 2 + q); i++) {
        if ((sum  - i) % (2 * i + 1) == 0) {
            int m = (sum - i) / (2 * i + 1);
            if (abs(i - m) <= p) {
                cout << i << " " << m << endl;
                return;
            }
        }
    }
    cout << -1 << endl;
	return ;
}
```


# 贪心 | exchange argument
[Problem - B - Codeforces](https://codeforces.com/contest/2210/problem/B)
![](image/Pasted%20image%2020260504203746.png)

题意： 
给一个n，一个permutation 数组p，然后进行下面的操作
![](image/Pasted%20image%2020260504204510.png)
```c++
void ap() {
	int n; cin >> n;
    vector<int> a(n);
    for (int & i : a) cin >> i;

    int ans = 0;
    for (int i = 0; i < n; i++) {
        if (a[i] <= i + 1) ans++;
    }
    cout << ans << endl;
	return ;
}
```
![](image/Pasted%20image%2020260506161805.png)


# 数学 | GCD，局部 推广到 全局
[Problem - C1 - Codeforces](https://codeforces.com/contest/2210/problem/C1)
![](image/Pasted%20image%2020260504211532.png)

题意：给一个数组a，对每一个下标，最多只可以进行一次操作，且每次操作一定使ai减小，要求最大可操作的次数
每次操作之后都需要满足 **任意连续区间的gcd和原来的相等**

局部必要性： 我们至少需要使相邻的元素在操作之后满足
可推出全局
![](image/Pasted%20image%2020260504212216.png)

所以我们只需要使相邻的数的gcd满足就可以
![](image/Pasted%20image%2020260504212312.png)
对于一个数ai，改变其会影响到A = gcd(ai, ai - 1),  B = gcd(ai, ai + 1)
我们需要改变之后，这两个gcd都不变， 那么改变之后的 数字a，必须含有因子A和B，又因为操作一定使其变小，所以贪心的想，既含有A因子又含有B因子的数是什么呢？
就是lcm(A， B),所以如果lcm < ai，那么就可以操作
再者，看a0, 和 an - 1;

```c++
void ap() {
	int n; cin >> n;
    vector<int> a(n), b(n);
    for (int & i : a) cin >> i;
    for (int & i : b) cin >> i;

    int ans = 0;
    if (__gcd(a[0], a[1]) < a[0]) ans++;
    if (__gcd(a[n - 1], a[n - 2]) < a[n - 1]) ans++;
    
    for (int i = 1; i < n - 1; i++) {
        int A = __gcd(a[i], a[i - 1]), B = __gcd(a[i], a[i + 1]);
        int lcm = A * B / __gcd(A, B);

        if (lcm < a[i]) ans++;
    }
    
    cout << ans << endl;
    
    
	return ;
}
```

# 数学 | 容斥原理
[Problem - C - Codeforces](https://codeforces.com/contest/2204/problem/C)
![](image/Pasted%20image%2020260506164935.png)
题意： 三个人，每个人给一个数字abc，每个人在自己数字的倍数的天数时， 会去打水，这一天如果只有一个人那么可以有6升，两个则每个人3升，三个则没人2升

要求输出 m 天之后，每个人各自有多少水

```c++
int lcm(int a, int b) {
    return a / __gcd(a, b) * b;
}

int get(int a, int b, int c, int m) {
    int c1 = m / a;
    int c2 = m / lcm(a, b) + m / lcm(a, c);
    int c3 = m / lcm(lcm(a, b), c);

    return (c1 - c2 + c3) * 6 + (c2 - 2 * c3) * 3 + c3 * 2;
}

void ap() {
	int a, b, c, m; cin >> a >> b >> c >> m;

    cout << get(a, b, c, m) << " " << get(b, a, c, m) << " " << get(c, a, b, m) << endl;

	return ;
}
```

![](image/Pasted%20image%2020260506165315.png)
每个圈是每个人各自会去的天数，那么显然，交集就代表着这几天会同时去
容斥原理的关键就是画出图

显然，每个人重复会去的天的标号就是lcm的倍数，天数就是 m /  lcm


# 贪心 | 括号序列
[Problem - C - Codeforces](https://codeforces.com/contest/2224/problem/C)
![](image/Pasted%20image%2020260507140025.png)

```c++
void ap() {
	int n; cin >> n;
    string a, b; cin >> a >> b;

    int x = 0, y = 0;
    bool ok = true;
    for (int i = 0; i < n; i++) {
        if (a[i] == '(' && b[i] == '(') {
            x++, y++;
        } else if (a[i] == ')' && b[i] == ')') {
            x--, y--;
        } else {
            if (x < y) swap(x, y);
            x--, y++;
        }

        if (x < 0 || y < 0) ok = false;
    }

    if (ok && x == 0 && y == 0) {
        cout << "YES" << endl;
    } else {
        cout << "NO" << endl;
    }

	return ;
}
```

# 二分 | 
[Problem - E - Codeforces](https://codeforces.com/contest/2167/problem/E)
![](image/Pasted%20image%2020260514152336.png)
```c++
void ap() {
	int n, k, x; cin >> n >> k >> x;
    vector<int> a(n);
    for (int & i : a) cin >> i;

    a.push_back(-1e9), a.push_back(1e9);
    n += 2;
    sort(a.begin(), a.end());

    int left = 0, right = x + 1;
    while (left + 1 < right) {
        int m = left + (right - left) / 2;
        a[0] = -m, a[n - 1] = x + m;
        
        int s = 0;
        for (int i = 1; i < n; i++) s += max(0, (a[i] - m) - (a[i - 1] + m) + 1);
        if (s >= k) {
            left = m;
        } else {
            right = m;
        }
    }

    a[0] = -left, a[n - 1] = x + left;
    int j = 0;
    for (int i = 1; i < n; i++) {
        for (j = max(j, a[i - 1] + left); j <= min(a[i] - left, x) && k; j++) {
            cout << j << " ";
            k--;
        }
    }
    cout << endl;

	return ;
}
```

# 异或 | 博弈
[Problem - C1 - Codeforces](https://codeforces.com/contest/2171/problem/C1)
![](image/Pasted%20image%2020260516151625.png)
```c++
void ap() {
	int n; cin >> n;
    vector<int> a(n), b(n);
    int x = 0;
    for (int & i : a) cin >> i, x ^= i;
    for (int & i : b) cin >> i, x ^= i;

    if (x == 0) {
        cout << "Tie" << endl;
        return;
    }

    int last_idx = 0;
    for (int i = 0; i < n; i++) {
        if (a[i] ^ b[i]) {
            last_idx = i;
        }
    }

    if (last_idx & 1) {
        cout << "Mai" << endl;
    } else {
        cout << "Ajisai" << endl;
    }
    

	return ;
}
```

![](image/Pasted%20image%2020260516151740.png)


hard：将其看成每一位来看，就是ez。最高位的power最大，所以只用看最高位的ez版本
[Problem - C2 - Codeforces](https://codeforces.com/contest/2171/problem/C2)
![](image/Pasted%20image%2020260516161050.png)
hard版本只是不再是0, 1数。
最终需要比较的是异或值的大小，那么最高位大的一定大
所以我们可以从最高位看起
最高位是哪一位呢？
我们可以根据ez版本来看，每一位其实都是一个ez版本，那么我们是不是只需要看所有数的异或值x
是否为0，来判断交换是否有用？

如果这一位为0，那么可以看成是这一位平局，所以，我们只需要找到x的最高位，
那么只看最高位，ab数组在这一位上都变成了ez版本的01数组
同理

```c++
void ap() {
	int n; cin >> n;
    vector<int> a(n), b(n);
    int x = 0;
    for (int & i : a) cin >> i, x ^= i;
    for (int & i : b) cin >> i, x ^= i;

    if (x == 0) {
        cout << "Tie" << endl;
        return;
    }
    
    int bit = 0;
    for (int i = 0; i < 20; i++) {//x最高位的1
        if (x & (1 << i)) {
            bit = i;
        }
    }

    int idx = 0;
    for (int i = 0; i < n; i++) {
        if ((a[i] ^ b[i]) & (1 << bit)) {//找到谁可以改变最高位的1
            idx = i;
        }
    }

    if (idx & 1) {
        cout << "Mai" << endl;
    } else {
        cout << "Ajisai" << endl;
    }


	return ;
}
```
**Proof 1** ：**所有数的异或值x 为0，那么不论怎么操作，总是平局**
设最优交换后的数组a 的异或值为A
设最优交换后的数组b 的异或值为B

那么有A ^ B = x
即A ^ B = 0; 即A = B

所以，不论怎么交换，即使最优，最后恒会有 A = B


**Proof 2 : x  不为 0，只需要看最后一个可以决定 的位置即可**
首先我们不要被hard版本迷惑了，因为我们看的是bit位，所以还是01串

所以，当x不为0时，x 只能为1 (当然，我们这里只是站在bit的角度看，这就是为什么不要被影响)
即A ^ B = 1

- 当两个数相同时，无论交换与否都不会影响结果
- 当两个数不相同时，交换会影响。称之为有效交换

全局的看，最后一次 有效交换 一定可以改变最终的结果

设ci = ai ^ bi
记最后一次有效交换的位置为last， 显然有c_last = 1

c0 ^ c1 ^ c2 ^ ... c_last ^ 0 ^ 0... = 1

这个等式恒成立，那么，last之前的所有ci的异或值 必然为0

所以最终改变结果的就是last的操作



# 周期旋转 | 下标映射 + 前缀和
[Problem - F - Codeforces](https://codeforces.com/contest/2009/problem/F)
1700
![](image/Pasted%20image%2020260518171916.png)
```c++
void ap() {
	int n, q; cin >> n >> q;
    vector<int> a(n);
    for (int & i : a) cin >> i;

	vector<int> pre(1);//pre[0] = 0;
    for (auto & i : a) pre.push_back(pre.back() + i);
	for (auto & i : a) pre.push_back(pre.back() + i);

    while (q--) {
		int l, r; cin >> l >> r;
		l--, r--;

		int i = l / n, j = r / n;
		l %= n, r %= n;
		
		cout << pre[n] * (j - i + 1) - (pre[i + l] - pre[i]) - (pre[j + n] - pre[j + r + 1]) << endl;
	}
	
	return ;
}
```

b数组中 下标为x的数 所处的 块的位置是 x / n 块 (这里块是从0开始)
所以 i = l / n, j = r / n, 代表的是左，右端点 所在的块的下标

pre[n] * (j - i + 1) 算出 总的和，但是显然会右多余的部分

l %= n, r %= n; 表示l ，r 分别在一个块内的下标

观察发现，x / n 在两个a数组 中 的位置就是这个旋转的块的开头，那么显然，加上后面的n - 1个数 就组成了这一个块
而l 又是在块内的位置，所以我们可以找出其多余的和，利用前缀和找出




# 匹配消除 所需最小次数 | 枚举分割点 or 前后缀分解 + 正难则反

[Problem - B - Codeforces](https://codeforces.com/contest/2230/problem/B)
![](image/Pasted%20image%2020260520202645.png)
```c++
void ap() {
	string s; cin >> s;

    int cnt = 0;
    string t;
    for (char & c : s) {
        if (c == '4') {
            cnt++;
        } else {
            t += c;
        }
    }
    
    int suf = 0;
    for (char & c : t) if (c == '1' || c == '3') suf++;

    int pre = 0;
    int ans = suf;

    for (int i = 0; i < t.size(); i++) {
        if (t[i] == '2') {
            pre++;
        } else {
            suf--;
        }

        ans = max(ans, pre + suf);

    }
    cout << t.size() - ans + cnt << endl;



	return ;
}
```

题意 ： 取出子序列，如果存在子序列，其是4的倍数，那么不符。
给定一个字符串，要求最小的 移除的数量，使得移除之后的字符串 符合。

**这题的关键就是要消除12，32这样的子序列，难点在于求这种情况的最小数量**

- 首先，4一定需要删除，因为我们总可以选出单个的4，其恒是4的倍数
- 又因为只有1，2，3，4这些数字，那么一位数字的情况就只有4
- 然后我们看两位数字的情况
- 我们发现，只有32，12这两种情况
- 先抛开这两种情况的计算，我们顾后看看 操作完这几种情况后的样子
1. 没有4
2. 2 在前 ， 1 和 3 在后，且两者一定没有交叉，(或者一方没有，我们可以通过反证法证明- 如果有交叉的，那么交叉的部分一定产生不符的情况)
- 后半部分的1，3 不可能组成偶数，即不可能有可以组成4的倍数的情况
- 前半部分全是2，也无法
- 23，21，也不符
- 因此，我们其实只需要解决32，12这种情况，再加上4的数量

**正难则反**
要求最小删除数量，我们同理可以求最大保留数量
最小删除数量 = 总的长度 - 最大保留数量

**枚举分割点**
答案肯定是由一条“切割线”分为左右两半(左边为2，右边为1，3)，那我们就**枚举这条切割线的所有可能位置**

就有了下面的代码
```c++
	int suf = 0;
    for (char & c : t) if (c == '1' || c == '3') suf++;

    int pre = 0;
    int ans = suf;

    for (int i = 0; i < t.size(); i++) {
        if (t[i] == '2') {
            pre++;
        } else {
            suf--;
        }

        ans = max(ans, pre + suf);

    }
    cout << t.size() - ans + cnt << endl;
```


#  贪心 构造 | 局部最优 - 全局最优
[Problem - C2 - Codeforces](https://codeforces.com/contest/2229/problem/C2)
![](image/Pasted%20image%2020260525190728.png)
```c++
void ap() {
	int n; cin >> n;
    vector<int> a(n);
    for (int & i : a) cin >> i;

    vector<int> pre(n), suf(n + 1);

    pre[0] = abs(a[0]);
    for (int i = 1; i < n; i++) {
        pre[i] = pre[i - 1] + abs(a[i]);
    }

    suf[n - 1] = a[n - 1];
    for (int i = n - 2; i >= 0; i--) {
        suf[i] = suf[i + 1] + a[i];
    }


    int idx = -1;
    int mx = suf[0];
    for (int i = 1; i < n; i++) {
        if (a[i] < 0) continue;

        int x = pre[i - 1] + suf[i + 1] - a[i];
        if (x > mx) {
            mx = x;
            idx = i;
        }
    }

    if (idx == -1) {
        cout << 0 << endl << endl;
        return;
    }

    int k = 0;
    vector<int> ans;
    for (int i = idx - 1; i >= 0; i--) {
        if (a[i] > 0 && k % 2 == 0 || a[i] < 0 && k % 2 == 1) {
            ans.push_back(i);
            k++;
        }
    }
	//先把idx前面的数都变为负数， 再对idx进行操作
    ans.push_back(idx);

    cout << ans.size() << endl;
    for (int & x : ans) cout << x + 1 << " ";
    cout << endl;

	return ;
}
```

[Problem - C1 - Codeforces](https://codeforces.com/contest/2229/problem/C1)
简单版本就是将所有数变为负数，并且c1已经有了其构造方法、

c1表明，我们可以使一整个数组全部变为负数
有必要性：一定可以使一个前缀全部变为负数
扩展：这个前缀无需以正数结尾
我们无法操作使得所有数变为正数，除了初始全为正数

因此我们**可以将任意前缀全变为负数，构造方法同c1**
要使和最大，那么只需要再操作一次正数就可以将 这个正数 前面的所有数都变为正，而这个数变为负数。

所以操作 正数 的时候，最优的方法就是先将其前面的数 通过c1 的方法变为负数， 再操作这个正数

所以对于一系列的操作，我们只需要看其下标最后的 那个数，其下标记为idx

因为idx之前的操作，不论如何，最值都不可能超过其所有数的绝对值，并且在idx 之前的操作，都无法使得其全为正数

所以我们看一系列的操作时，其最后一个idx按上述是最优的

那么我们可以枚举每一个这样子的 最后一个操作的点idx，记录值最大的点idx，对于这个点进行c1
得到的就是我们想要的 答案




## 关联笔记
- [[牛客竞赛]] — 牛客竞赛笔记
- [[2026 NC CCPC]] — CCPC 真题
