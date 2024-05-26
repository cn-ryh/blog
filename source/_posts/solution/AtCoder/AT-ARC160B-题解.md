---
title: AT ARC160B 题解
top: 100000000
date: 2024-05-04 10:18:25
tags:
  - AtCoder
  - 题解
---
<!---->
<!--more-->


# [ARC160B] Triple Pair

## Link

[Luogu](https://www.luogu.com.cn/problem/AT_arc160_b)

[AtCoder](https://atcoder.jp/contests/arc160/tasks/arc160_b)

## Description

**本题单测试点多组测试数据。**

每次给出一个正整数 $n$。求满足 $x,y,z \in N_{+}$ 且 $xy,xz,yz \leq n$ 有序三元组 $(x,y,z)$ 的个数，并输出答案对 $998244353$ 取模的结果。

## Solution

Update 2023.8.1：更正了两处笔误。

根据乘法的性质，若 $x \times y \leq n$，则 $x,y$ 中至多有一个大于 $\sqrt{n}$。设满足条件的三元组为 $\left (x,y,z \right )$。令 $x \leq y \leq z$。根据上述结论可以发现 $1 \leq y \leq \sqrt{n}$。我们可以枚举 $y$，根据题目限制得出 $1 \leq x \leq y \leq  \left \lfloor \frac{n}{y} \right \rfloor$。

根据乘法原理，据此得到的答案数量为 $y \times y \times \left ( \left \lfloor \frac{n}{y} \right \rfloor - y\right )$。注意此时的 $x,y,z$ 是有大小关系的。

考虑如何据此计算正确答案，进行分类讨论：

- $x = y = z$ 时，排列只有一种。

- 有两个相同时，排列有三种。

- 全都不同时，排列有六种。

回到上面 $x,y,z$ 的范围：三者相同只有一种情况，$x = y$ 有 $\left \lfloor \frac{n}{y} \right \rfloor - y$ 种情况 $y = z$ 有 $y$ 种情况，剩余为三者不同。最终答案为即为 $\sum_{y = 1}^{\sqrt{n}}\left (6 \times \left  ( \left \lfloor \frac{n}{y} \right \rfloor - y \right ) \times \left (y - 1 \right )+ 3 \times  \left (\left \lfloor \frac{n}{y} \right \rfloor - 1 \right ) + 1 \right )$。

时间复杂度为 $O(\sum{ \sqrt{n}})$。

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
int T, n;
signed main()
{

    read(T);
    while (T--)
    {
        read(n);
        int ans = 0;
        for (int i = 1; i <= sqrt(n); i++) // 枚举中间的数
        {
            int mx = n / i; // 最大的数
            ans = (ans + (mx - i) * (i - 1) * 6 % mo + 3 * (mx - 1) + 1) % mo;
        }
        writeln(ans);
    }
    return 0;
}
```
