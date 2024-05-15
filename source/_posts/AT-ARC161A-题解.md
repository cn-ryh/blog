---
title: AT ARC161A 题解
date: 2024-05-04 09:43:49
order: 0
tags:
  - AtCoder
  - 题解
---
<!---->
<!--more-->

# [ARC161A] Make M 题解

## Link

[洛谷](https://www.luogu.com.cn/problem/AT_arc161_a)

[AtCoder](https://atcoder.jp/contests/arc161/tasks/arc161_a)

## Description

给定长度为 $n$ 的整数序列 $S$，其中 $n$ 为奇数。问能否重新排列该序列，使得对于所有偶数下标 $i$，有 $S_{i} > S_{i - 1}$ 且 $S_{i} > S_{i + 1}$。

## Solution

根据题目，可以想到将最大的几个数从大到小放在下标为偶数的位置。剩余的数按照从大到小的顺序放在其他位置。

简单证明合理性：若有 $i,j,x,y$ 满足 $S_{i} > S_{j}$，$S_{x} > S_{y}$ 且 $S_{x},S_{y}$ 均不在序列中前 $\lfloor \frac{n}{2} \rfloor$ 大，$S_{i},S_{j}$ 放在新数列中偶数下标位置，$S_{x},S_{y}$ 放在新数列中奇数下标位置，若原来将 $S_{x}$ 放在 $S_{j}$ 旁满足条件，则 $S_{i} > S_{j} > S_{x} > S_{y}$。则交换后一定满足条件。

因此将序列排序之后按照如上规则制造结果序列，最后判断一次即可，时间复杂度 $O(n \log n)$。

## Codes

```cpp
#include <bits/stdc++.h>
using namespace std;
#define int long long
#define max_n 200101
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
int nums[max_n];
int ans[max_n];
signed main()
{
#if _clang_
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    read(n);
    for (int i = 1; i <= n; i++)
    {
        read(nums[i]);
    }
    sort(nums + 1, nums + n + 1);
    int mid = n / 2 + 1, r = n;
    for (int i = 1; i <= n; i++)
    {
        if (i % 2)
        {
            ans[i] = nums[mid--];
        }
        else
        {
            ans[i] = nums[r--];
        }
    }
    for (int i = 2; i < n; i += 2)
    {
        if (ans[i] <= ans[i - 1] || ans[i] <= ans[i + 1])
        {
            puts("No");
            return 0;
        }
    }
    puts("Yes");
    return 0;
}
```
