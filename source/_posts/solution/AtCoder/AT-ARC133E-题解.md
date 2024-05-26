---
title: AT ARC133E 题解
top: 100000000
date: 2024-05-04 10:13:14
tags:
  - AtCoder
  - 题解
---
<!---->
<!--more-->

# [ARC133E] Cyclic Medians 题解

## Links

[Luogu](https://www.luogu.com.cn/problem/AT_arc133_e)

[AtCoder](https://atcoder.jp/contests/arc133/tasks/arc133_e)

## Description

给你四个整数 $N,M,V,A$。

选择两个序列 $x$ 和 $y$，其中 $x$ 的长度为 $n$，$y$ 的长度为 $m$。两个序列中数的值域均为 $[1,V]$。

定义变量 $a$，初始值为 $A$。

依次对所有 $i \in [0, N \times M - 1]$，将 $a$ 替换为 $x_{\left ( i \bmod n \right ) + 1},a,x_{\left ( i \bmod m \right ) + 1}$ 的 **中位数**。

你需要求出对于所有满足条件的序列 $x,y$，经过 $N \times M$ 次操作之后 $a$ 的值之和，答案对 $998\,244\,353$ 取模。


数据范围：

$1 \leq N,M \leq 2 \times 10 ^ {5}$，$1 \leq A \leq 2 \times 10^{5}$。

## Solution


在讲解这道题目之前先**引理：**给定一个长度为 $n$ 的序列 $A$，设最大值为 $mx$，序列中元素和为 $sum$。则有 $\sum_{i = 0}^{mx - 1}{\sum_{j = 1}^{n} \left [A_{j} > i \right]} = sum$。

引理的证明可以自己画一个该序列的柱状图，用一条线从小到大扫描。

接下来考虑这道题，显然对于所有序列，最终的 $a \in [1,V]$。因此我们可以按照上面的方法枚举来统计所有最后的 $a$ 的和。

设最终大于 $k$ 的 $a$ 的数量为 $cnt_{k}$，则答案为 $ \sum_{i = 0}^{V - 1} cnt_{i}$。

接下来我们只需要考虑如何求出所有 $cnt_{i}$ 即可。可以对两个序列进行 0-1 重写（当然，我们并不需要真的重写这个数组，只是用于思考），若当前计算的为 $cnt_k$，大于等于 $k$ 的标为 $1$，否则标为 $0$。

可以发现只要出现了一次 $\left (k,0,0\right)$ 或 $\left(k,1,1\right)$，最终的答案就与 $k$ 无关。且由于相反性，$k = k_{1}$ 时最后一次操作时两个序列所选的为 $\left (0,0\right)$ 和 $k = V - k_{1}$ 时最后一次操作时两个序列所选的为 $\left (1,1\right)$ 的个数是相同的。因此计算出序列数除二即为这部分的答案。

接下来考虑没有出现从两个序列中选取相同数的情况，此时最终答案全为 $A$（即初始值），也即该部分对 $k < A$ 的 $cnt_{k}$ 有贡献。

手模一下在两个序列中取数的过程，令 $G = \gcd(n,m)$，则对于 $x_{(i + t_{1} \cdot G)}$（$1 \leq i < G,0\leq t_{1} < \frac{n}{G}$）只会取到 $y_{(i + t_{2} \cdot G)}$（$1 \leq i < G,0\leq t_{2} < \frac{m}{G}$）。因此，满足的条件为 $\forall i \in [1,G)$：

- $x_{i} = x_{(i + t_{1} \cdot G)}$（$0\leq t_{1} < \frac{n}{G}$）。

- $y_{i} = y_{(i + t_{2} \cdot G)}$（$0\leq t_{2} < \frac{m}{G}$）。

- $x_{i} \neq y_{i}$。

分别计算 $x_{i}$ 转换后为 $0$ 和转换后为 $1$ 的数量即可。方案数为 $k^{\frac{m}{g}}(V - k)^{\frac{n}{g}} + k^{\frac{n}{g}}(V - k)^{\frac{m}{g}} $。利用快速幂求解即可。除了这些数量，剩余的就是出现相同的数量，注意根据上面的推导需要除二，本题有取模，要求 $2$ 的逆元。

## Codes

```cpp
#include <bits/stdc++.h>
using namespace std;
#define int long long
#define mo 998244353
void read(int &p)
{
    p = 0;
    int k = 1;
    char c = getchar();
    while (c < '0' || c > '9')
    {
        if (c == '-')
        {
            k = -1;
        }
        c = getchar();
    }
    while (c >= '0' && c <= '9')
    {
        p = p * 10 + c - '0';
        c = getchar();
    }
    p *= k;
    return;
}
void write_(int x)
{
    if (x < 0)
    {
        putchar('-');
        x = -x;
    }
    if (x > 9)
    {
        write_(x / 10);
    }
    putchar(x % 10 + '0');
}
void writesp(int x)
{
    write_(x);
    putchar(' ');
}
void writeln(int x)
{
    write_(x);
    putchar('\n');
}
int n, m, V, A, ans, gcd__;
int ksm(int a, int b)
{
    int res = 1;
    for (; b; b >>= 1, a = (a * a) % mo)
    {
        if (b & 1)
        {
            res = (res * a) % mo;
        }
    }
    return res;
}
signed main()
{
#if _clang_
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    read(n), read(m), read(V), read(A);
    ans = ksm(V, n + m), gcd__ = __gcd(n, m);
    for (int i = 1; i < V; i++)
    {
        int now = ksm((ksm(i, n / gcd__) * ksm(V - i, m / gcd__) % mo + ksm(V - i, n / gcd__) * ksm(i, m / gcd__) % mo) % mo, gcd__);
        if (A > i) // 满足初始值 > i 的条件才有贡献。
        {
            ans = (ans + now) % mo;
        }
        int all = ksm(V, n + m);
        now = (all - now + mo) % mo;
        ans = (ans + now * ksm(2, mo - 2) % mo) % mo;
    }
    writeln(ans);
    return 0;
}
```