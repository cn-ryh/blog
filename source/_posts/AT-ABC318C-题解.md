---
title: AT ABC318C 题解
date: 2024-05-04 09:51:55
tags:
  - AtCoder
  - 题解
---
<!---->
<!--more-->

# AT_abc318_c Blue Spring 题解

## Links

[洛谷](https://www.luogu.com.cn/problem/AT_abc318_c)

[AtCoder](https://atcoder.jp/contests/abc318/tasks/abc318_c)

## Description

你要进行一场旅行。第 $i$ 天的车票是 $F_{i}$ 元。你也可以选择用 $P$ 元买 $D$ 张通票，通票可以在任意一天使用，你可以多次购买通票，但每次必须购买 $D$ 的倍数张，求完成旅行的最小花费。

## Solution

很容易想到，只要我们能找到 $D$ 天车票的总价格大于 $P$，就用 $D$ 张通票代替即可。因此我们可以将单日车票按价格排序，每次取前 $D$ 个判断是否买通票即可。时间复杂度 $\Omicron \left(n\log n \right)$。

## Codes

```cpp
#include <bits/stdc++.h>
using namespace std;
#define int long long
#define max_n 510101
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
    puts("");
}
int n,d,p;
int nums[max_n];
signed main()
{
    read(n),read(d),read(p);
    for(int i = 1;i <= n;i++)
    {
        read(nums[i]);
    }
    sort(nums + 1,nums + n + 1);
    reverse(nums + 1,nums + n + 1);
    int ans = 0;
    for(int i = 1;i <= n / d + 1;i++)
    {
        int sum1 = 0;
        for(int j = (i - 1) * d + 1;j <= i * d && j <= n;j++)
        {
            sum1 += nums[j];
        }
        ans += min(sum1,p);
    }
    writeln(ans);
    return 0;
}
```
