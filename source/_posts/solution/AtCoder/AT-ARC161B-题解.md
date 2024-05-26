---
title: AT ARC161B 题解
top: 100000000
date: 2024-05-04 10:15:16
tags:
  - AtCoder
  - 题解
---
<!---->
<!--more-->

# [ARC161B] Exactly Three Bits 题解

## Link

[洛谷](https://www.luogu.com.cn/problem/AT_arc161_b)

[AtCoder](https://atcoder.jp/contests/arc161/tasks/arc161_b)

## Description

找到不大于 $n$ 的最大正整数 $x$ 满足 $x$ 的二进制表示中恰好有 $3$ 位为 $1$。

## Solution

进行分类讨论：

- 当 $n$ 的二进制表示中有不少于 $3$ 位是 $1$ 是直接选择前三个，剩下的填 $0$。

- 当 $n$ 的二进制表示有 $2$ 位是 $1$ 时，无法通过全取 $1$ 满足条件，那么应该将第二个 $1$ 变为 $0$，然后取后面的两个 $1$，可以这么做的条件是第二个 $1$ 后面至少有两位。如不满足则须将第一个 $1$ 变为 $0$，在后面选连续三个 $1$，条件是后面至少有 $3$ 位，仍不满足只能输出 $-1$。

- 当 $n$ 的二进制表示有 $1$ 位是 $1$ 时，无法通过全取 $1$ 满足条件，则须将第一个 $1$ 变为 $0$，在后面选连续三个 $1$，条件是后面至少有 $3$ 位，仍不满足只能输出 $-1$。

**注意位运算的手动 `1LL`**，我在这里 WA 了三次。

## Codes

```cpp
#include <bits/stdc++.h>
using namespace std;
#define int long long
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
int ans[1000];
signed main()
{
#if _clang_
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    read(T);
    while (T--)
    {
        read(n);
        int cnt = 0, p = 0, nd = 0;
        while (n)
        {
            if (n & 1)
            {
                nd++;
            }
            ans[++cnt] = (n & 1);
            n >>= 1;
        }

        int res = 0, tot = 0;
        if (nd >= 3)
        {
            for (int i = cnt; i >= 1; i--)
            {
                res <<= 1LL;
                if (ans[i] == 1 && tot < 3)
                {
                    res |= 1LL;
                    tot++;
                }
            }
            writeln(res);
        }
        else
        {
            int p[3] = {0, 0, 0};
            int ct = 0;
            for (int i = cnt; i; i--)
            {
                if (ans[i] == 1)
                {
                    p[++ct] = i;
                }
            }
            if (ct == 1 || p[2] <= 2)
            {
                if (p[1] <= 3)
                {
                    puts("-1");
                }
                else
                {
                    writeln((1LL << (p[1] - 2)) + (1LL << (p[1] - 3)) + (1LL << (p[1] - 4)));
                }
                continue;
            }
            else
            {
                writeln((1LL << (p[1] - 1)) + (1LL << (p[2] - 2)) + (1LL << (p[2] - 3)));
            }
        }
    }
    return 0;
}
```
