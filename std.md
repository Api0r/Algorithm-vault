```c++
#include <bits/stdc++.h>
#define int long long
#define double long double
#define endl "\n"
using namespace std;
const int N = 100 + 50;
const int M = 1e5 + 50;
const int mod = 1e9 + 7;
int ksm(int n, int m){
    int res = 1;
    n %= mod;
    while(m){
        if(m & 1){
            res = res * n % mod;
        }
        n = n * n % mod;
        m = m >> 1;
    }
    return res;
}

int fac[N], inv[N];
int init = [](){
    fac[0] = inv[0] = 1;
    for(int i = 1; i < N; i++){
        fac[i] = fac[i - 1] * i % mod;
        inv[i] = ksm(fac[i], mod - 2);
    }
    return 0;
}();

int C(int n, int m){
    return fac[n] * inv[n - m] % mod * inv[m] % mod;
}
int A(int n, int m){
    return fac[n] * inv[m] % mod;
}

// 1. 标准 __int128 读入函数 (支持负数)
__int128 read() {
    __int128 x = 0, f = 1;
    char ch = getchar();
    
    // 过滤掉非数字字符，同时判断正负
    while (ch < '0' || ch > '9') {
        if (ch == '-') f = -1;
        ch = getchar();
    }
    
    // 循环读入数字字符并拼接
    while (ch >= '0' && ch <= '9') {
        x = x * 10 + (ch - '0');
        ch = getchar();
    }
    return x * f;
}

// 2. 标准 __int128 输出函数 (支持负数)
void write(__int128 x) {
    if (x < 0) {
        putchar('-');
        x = -x;
    }
    // 递归地将低位数字压入系统栈，最后从高位到低位依次输出
    if (x > 9) {
        write(x / 10);
    }
    putchar(x % 10 + '0');
}
/*
#define int __int128
int x = read();
write(x);
putchar('\n');
*/



void solve() {

    return;
}

signed main () {
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    while (T--) solve();

    return 0;
}
```