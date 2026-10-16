
# 1.两个数异或的二进制1 的个数的奇偶性 与 二进制下各自的1的个数的和的奇偶性相同

$$\operatorname{popcount}(u \oplus v) \equiv \operatorname{popcount}(u) + \operatorname{popcount}(v) \pmod 2$$

**简单证明：**

- 令`popcount(u) = a`, `popcount(v) = b`
- 假设a和b中有k个二进制位上都为1

则会有`popcount(u ^ v) = a + b - k * 2`

`k * 2` 为偶数，不会影响奇偶性，因此其奇偶性只由`a + b`决定

