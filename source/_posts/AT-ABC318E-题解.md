---
title: AT ABC318E 题解
date: 2024-05-04 09:49:32
order: 0
tags:
  - AtCoder
  - 题解
---
<!---->
<!--more-->

# AT_abc318_e Sandwiches 题解

## Links

[洛谷](https://www.luogu.com.cn/problem/AT_abc318_e)

[AtCoder](https://atcoder.jp/contests/abc318/tasks/abc318_e)

## Description

给定一个长度为 $n$ 的序列 $a$，找到满足以下条件的三元组 $(i,j,k)$ 的数量。

- $i < j < k$；
- $a_{i} = a_{k}$；
- $a_{i} \neq a_{j}$。

数据范围：$1 \leq n \leq 3 \times 10^{5}$，$1 \leq a_{i} \leq n$。

## Solution

由于要求 $i < j < k$，我们可以从左向右枚举 $k$，考虑对于这个 $k$ 计算贡献。假设 $a_{k}$ 在之前出现次数是 $cnt_{a_{k}}$，出现的位置集合是 $pos_{a_{k}} = \{pos_{a_{k},1}, \cdots, pos_{a_{k},cnt_{a_{k}}}\}$。先考虑一种较劣的算法：枚举每个 $i$，那么 $j$ 的数量可以很轻松的确定（注意减去中间 $a_{j}$ 和 $a_{k}$ 相同的数量）。于是这个 $k$ 的贡献是：

$$
\sum_{i = 1}^{cnt_{a_{k}}} \left ( k - pos_{a_{k},i} - cnt_{a_{k}} + i \right)
$$

此时复杂度为 $\Omicron (n^{2})$。

考虑拆上面的式子，令其为 $f(k)$：

$$
\begin{aligned}
    f(k)  &= \sum_{i = 1}^{cnt_{a_{k}}}  k - \sum_{i = 1}^{cnt_{a_{k}}} pos_{a_{k},i} - \sum_{i = 1}^{cnt_{a_{k}}} cnt_{a_{k}} + \sum_{i = 1}^{cnt_{a_{k}}}i 
  \\  &=cnt_{a_{k}} \cdot k - \sum_{i = 1}^{cnt_{a_{k}}} pos_{a_{k},i} - cnt_{a_{k}}^{2} + \frac{cnt_{a_{k}} \times (cnt_{a_{k}} + 1)}{2}
 \\ &=cnt_{a_{k}} \cdot k -  \sum_{i = 1}^{cnt_{a_{k}}} pos_{a_{k},i} - \frac{cnt_{a_{k}} \times (cnt_{a_{k}} + 1)}{2}
\end{aligned}
$$

对每个数字为何一个出现位置的前缀和 $sum =  \sum_{i = 1}^{cnt_{a_{k}}} pos_{a_{k},i}$ 就可以 $\Omicron(1)$ 计算每个 $k$ 的贡献了，总时间复杂度 $\Omicron (n)$。



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
int n,ans = 0,nums[max_n],sum[max_n],cnt[max_n];
signed main()
{
    read(n);
    for(int i = 1;i <= n;i++)
    {
        read(nums[i]);
    }
    for(int i = 1;i <= n;i++)
    {
        if(cnt[nums[i]] >= 1)
        {
            ans += cnt[nums[i]] * i - sum[nums[i]] - ((cnt[nums[i]] + 1) * (cnt[nums[i]]) / 2);
        }
        sum[nums[i]] += i;
        cnt[nums[i]]++;
    }
    writeln(ans);
    return 0;
}
```
