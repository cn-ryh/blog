---
title: AT ABC301B 题解
top: 100000000
date: 2024-05-04 10:19:02
tags:
  - AtCoder
  - 题解
---
<!---->
<!--more-->

# [ABC301D] Bitmask 题解

## Links

[Luogu](https://www.luogu.com.cn/problem/AT_abc301_d)

[AtCoder](https://atcoder.jp/contests/abc301/tasks/abc301_d)

## Description

给你一个包含 `?` 的 01 串，将每个 `?` 替换为 `0` 或 `1`。找到对应的二进制数小于 $n$ 的最大值或判断无解。

## Solution

首先将所有 `?` 视为 `0`，此时计算出的值若大于 $n$ 则无解，否则从高位向低位枚举，判断是否能换为 `1`。根据倍增 LCA 和贪心的思想，此时一定可得到最优解策略。理由：若某高位能切换为 `1`，后面还有某低位在不考虑该高位时能切换为 `1`，分两种情况讨论。

- 若可同时替换为 `1`，第一个一定选。
- 如只能选择一个，则选第一个对答案贡献更大。

综上：策略为从高位开始，能换为 `1` 则换为 `1`。注意开 `long long`。

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
int n;
char s[70];
int now_cnt = 0;
signed main()
{
#if _clang_
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    scanf("%s", s + 1);
    int l = strlen(s + 1);
    cin >> n;
    for (int i = 1; i <= l; i++)
    {
        now_cnt <<= 1LL;
        if (s[i] == '1')
        {
            now_cnt++;
        }
    }
    if (now_cnt > n)
    {
        writeln(-1);
        return 0;
    }
    for (int i = 1; i <= l; i++)
    {
        if (s[i] == '?')
        {
            if (now_cnt + (1LL << (l - i)) <= n)
            {
                now_cnt += (1LL << (l - i));
            }
        }
    }
    writeln(now_cnt);
    return 0;
}
```
