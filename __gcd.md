
# gcd(m,n)=gcd(m,n−m)

## gcd(x,x+4)=1 当 x 是**奇数**时成立。
由上式得：
gcd(x,x+4)=gcd(x,4)
而4的因子只有2，所以当x不含因子2 的时候，就是互质的，即gcd(x, 4) == 1


# gcd(kx,ky)=∣k∣⋅gcd(x,y), 当gcd(x, y) == 1时

![[Pasted image 20260406205731.png]]
即如果给定一个数组b，和数组a，如果数组a满足ai = bi * b i + 1
则gcd(ai,ai+1)=bi+1⋅gcd(bi,bi+2) .

我们可以通过这个来构造数组a。


# 一个长度为n的环(循环数组),每次步长为k。当__gcd(n, k) =1 时，每一个点都能走到，并且不会重复，最终会回到原点

[Problem - D - Codeforces](https://codeforces.com/gym/106353/problem/D)
**n = 8, k = 3**

在环n上每次走 k 步：

- 所有可达位置 = **gcd (n,k) 的倍数**
- 回到起点的最小步数 = **n / gcd(n,k)**， 即周期 T =  **n / gcd(n,k)**
- 当且仅当 **gcd(n,k)=1** 时，遍历全部 n 个点，周期为 n。
![[Pasted image 20260409112150.png]]
总的来说，如果我们在一个长度为n的循环里边，每次的步长为k，
那么
- 走的路径是一个 周期为 **n / __gcd(n, k)** 的循环
- 如果我们定义0为起始点，那么一个循环中，走过的点是 __gcd(n, k)的倍数 (但是不一定是按照顺序来的)
- 为什么会这样呢？
- 我们先看gcd = 1的时候：我们从0开始，走到下一个0，其间走了n步，即走了 n * k，即下一个0处，即为n * k. 这个数一定是n的倍数，所以一定为0。从这个角度我们可以看见，为什么当__gcd =1 的时候，需要走n步。因为k * (n - 1), k * (n - 2),k * (n - 3),k * (n - 4)...都不是n的倍数。即假设我们走了x 步，那么需要x * k 是 n 的倍数，其实x = T，即n / gcd * k % n == 0



# 要穷尽一个环n，那么步长k 一定要满足__gcd(n, k) == 1

[Problem - C - Codeforces](https://codeforces.com/contest/2217/problem/C)
![[Pasted image 20260409115433.png]]

```c++
void solve() {
	int n, m, a, b; cin >> n >> m >> a >> b;
    int x = __gcd(n, a), y = __gcd(m, b);
    if (x != 1 || y != 1) {
        cout << "NO" << endl;
        return ;
    }
    if (__gcd(m, n) <= 2) {
        cout << "YES" << endl;
        return;
    }
    cout << "NO" << endl;
	return ;
}
```


# 求最小的整数k，使得 k * x % y == 0 则k = y / gcd(x, y);

设 d=gcd(x,y)，令

x=d⋅x′,y=d⋅y′

则 gcd(x′,y′)=1

将分解后的 x 和 y 代入条件 y∣kx，得到：

d⋅y′∣k⋅(d⋅x′)

两边同时消去公共因子 d，条件简化为：

y′∣k⋅x′
由于我们已经知道 x′ 和 y′ 互质（gcd(x′,y′)=1），要让 y′ 整除 k⋅x′，k 必须是 y′ 的倍数
要使 k 最小，k 就必须等于 y′。

### 核心联系

这个问题本质上是在求最小公倍数（LCM）。

我们知道，两个数的最小公倍数与最大公约数满足公式：

lcm(x,y)= x⋅y​ / gcd(x,y)

而我们要求的最小 k 恰好满足 k⋅x=lcm(x,y)。

因此，k=xlcm(x,y)​=gcd(x,y)y​。

这个推导逻辑清晰地连接了**最大公约数**和**最小公倍数**，是算法竞赛中处理数论问题的基础。


这个竖线符号 **∣** 在数学里读作：

# **整除**

## 意思

a∣b

表示：

**a 能整除 b**

也就是

**b 是 a 的倍数**

等价于：

b÷a 没有余数，结果是整数

或者写成：

bmoda=0


## 关联笔记
- [[数学]] — 数学笔记
- [[数论]] — 数论专题
