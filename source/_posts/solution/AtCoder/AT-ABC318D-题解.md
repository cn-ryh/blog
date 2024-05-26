---
title: AT ABC318D 题解
top: 100000000
date: 2024-05-04 09:51:21
tags:
  - AtCoder
  - 题解
---
<!---->
<!--more-->

# AT_abc318_d General Weighted Max Matching 题解

## Links

[洛谷](https://www.luogu.com.cn/problem/AT_abc318_d)

[AtCoder](https://atcoder.jp/contests/abc318/tasks/abc318_d)

## Description

有一张 $n$ 个点的带权无向完全图，找出一些边使得这些边的端点没有重合且边权和最大。输出最大边权和。

## Solution

搜索即可，重点在于复杂度分析。都先我们确定一件事：一定会选择 $\left \lfloor \frac{n}{2} \right \rfloor$ 条边，原因是边权非负，显然对于边数更少的我们可以多选边，答案更优。

因此极限情况下要选 $8$ 条边。我们知道，$16$ 个点的无向完全图有 $\frac{16 \times 15}{2} = 120$ 条边。因此第一条边有 $120$ 种选择，之后，与这条边两个端点相连的就都不能选了，于是下一轮的可选边数是 $120 - 15 \cdot 2 + 1 =  91$，以此类推，算出我们选边的方案数不超过 $10^{8}$。当然，我们选边是顺序无关的，因此我们搜索的时候可以强制按编号从小到大的顺序搜索。于是总方案为 $\frac{10^{8}}{8!} < 3000$，复杂度优秀！

## Codes

```cpp
#include <bits/stdc++.h>
using namespace std;
#define max_n 121
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
    puts("");
}
int mp[20][20];
int cnt,n;
int vis[max_n];
int ans = 0;
struct Line
{
    int u,v,w;
}edge[max_n];
void dfs(int now,int res,int ls)
{
    if(now + 2 > n)
    {
        ans = max(ans,res);
        return ;
    }
    for(int i = ls + 1;i <= cnt;i++)
    {
        int u = edge[i].u,v = edge[i].v,w = edge[i].w;
        if(vis[u] || vis[v])
        {
            continue;
        }
        vis[u] = vis[v] = 1;
        dfs(now + 2,res + w,i);
        vis[u] = vis[v] = 0;
    }
}
signed main()
{
    read(n);
    for(register int i = 1,w;i < n;i++)
    {
        for(register int j = i + 1;j <= n;++j)
        {
            read(w);
            edge[++cnt] = {i,j,w};
        }
    }
    dfs(0,0,0);
    writeln(ans);
    return 0;
}
```
