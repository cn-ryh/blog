---
title: AT ABC306H 题解
date: 2024-05-04 10:12:03
tags:
  - AtCoder
  - 题解
---
<!---->
<!--more-->

# AT_abc306_h Balance Scale 题解

## Links

[洛谷](https://www.luogu.com.cn/problem/AT_abc306_h)

[AtCoder](https://atcoder.jp/contests/abc306/tasks/abc306_h)

## Description

有 $N$ 个编号为 $1,2,\dots,N$ 的砝码。有 $M$ 次比较操作，每次比较砝码 $A_{i}$ 和 $B_{i}$，$A_{i}$ 在左侧。

分为三种情况：
1. 左边的砝码更重。
2. 右边的砝码更重。
3. 两边的砝码重量相同。

将每次比较的结果使用字符“>”、“=”或“<”记录下来，形成一个长度为 $M$ 的字符串 $S$。求一共有多少种可能的 $S$。答案对 $998\,244\,353$ 取模。

## Solution

考虑如何对一组确定的砝码判断是否可行，若 $A_{i} < B_{i}$ 从 $A_{i}$ 所在集合向 $B_{i}$ 所在集合连边，若 $A_{i} > B_{i}$ 从 $B_{i}$ 所在集合向 $A_{i}$ 所在集合连边，相等利用并查集合并。如果出现环了无解。

但每条边的方向不知道，考虑到数据范围很小，可以使用状压 dp 枚举一个独立集，表示初始度数为 $0$ 的点。当我们删去其中度数为 $0$ 的点时，仍满足条件，因此我们有转移状态：

$$dp_{i} = \sum_{s \subset i,s \neq \emptyset} dp_{i \setminus s}$$

但这样是错误的，某个集合本身被计算了，但他同时会作为另一个集合的子集被计算。我们容斥掉这些情况即可，考虑集合被算的次数与集合大小有关，简单的推出下面正确的转移方程。

$$dp_{i} = \sum_{s \subset i,s \neq \emptyset} dp_{i \setminus s} \times {(-1)^{ \left ( \left |s \right |+1 \right )}}$$

其中 $\left |s \right |$ 表示集合 $s$ 的元素个数。

## Codes

```cpp
#include <bits/stdc++.h>
using namespace std;
#define int long long 
#define max_n 410101
#define mo 998244353
void read(int &p)
{
    int k = 1;
    p = 0;
    char c = getchar();
    while(c < '0' || c > '9')
    {
        if(c == '-')
        {
            k =  -1;
        }
        c = getchar();
    }
    while(c >='0' && c <= '9')
    {
        p = p * 10 + c - '0';
        c = getchar();
    }
    p *= k;
    return ;
}
void write_(int x)
{
    if(x < 0)
    {
        putchar('-');
        x = -x;
    }
    if(x > 9)
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
int n,m;
int fa[max_n],dp[max_n];
pair<int,int> edge[max_n];
int can[max_n];
int find(int x)
{
    if(fa[x] == x)
    {
        return x;
    }
    return fa[x] = find(fa[x]);
}
void init()
{
    for(int i = 1;i <= n;i++)
    {
        fa[i] = i;
    }
}
int lowbit(int x)
{
    return (-x) & x;
}
int popcount(int x)
{
    int cnt = 0;
    while(x)
    {
        ++cnt;
        x -= lowbit(x);
    }
    return cnt;
}
signed main()
{
    read(n),read(m);
    for(int i = 1;i <= m;i++)
    {
        read(edge[i].first),read(edge[i].second);
    }
    for(int i = 0;i < (1LL << n);i++)
    {
        can[i] = popcount(i);
        init();
        for(int j = 1;j <= m;j++)
        {
            // 两个点都在集合中
            if((i & (1 << edge[j].first - 1)) && (i & (1 << edge[j].second - 1)))
            {
                // 合并
                if(find(edge[j].first) != find(edge[j].second))
                {
                    fa[find(edge[j].first)] = find(edge[j].second);
                    // 注意集合大小要减 1
                    --can[i];
                }
            }
        }
    }
    dp[0] = 1;
    for(int i = 1;i < (1 << n);i++)
    {
        for(int j = i;j;j = i &(j - 1))
        {
            if(can[j] & 1)
            {
                dp[i] += dp[i ^ j];
            }
            else
            {
                dp[i] += mo - dp[i ^ j];
            }
            (dp[i] += mo)%= mo;
        }
    }
    writeln(dp[(1 << n) - 1]);
    return 0;
}
```