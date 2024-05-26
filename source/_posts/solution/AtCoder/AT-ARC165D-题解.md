---
title: AT ARC165D 题解
top: 100000000
date: 2024-05-04 09:45:36
tags:
  - AtCoder
  - 题解
---
<!---->
<!--more-->


# AT_arc165_d [ARC165D] Substring Comparison 题解

## Links

[洛谷](https://www.luogu.com.cn/problem/AT_arc165_d)

[AtCoder](https://atcoder.jp/contests/arc165/tasks/arc165_d)

## Description

给定正整数 $n,m$ 和 $m$ 个形如 $(A_{i},B_{i},C_{i},D_{i})$ 的限制条件。

判断是否存在一个长度为 $n$ 的序列 $P$ 满足 $\forall i \in [1,m]$，$P_{A_{i} \dots B_{i}}$ 字典序小于 $P_{C_{i} \dots D_{i}}$。

$1 \leq n,m \leq 2 \times 10^{3}$。

## Solution

考虑比较字典序时的操作：如果某一位相同，不会造成影响，当一个串先比较完时，这个串更小，否则字典序取决于需要的比较第一个不同的。

因此可以考虑逐步减小限制的区间，具体的，当两个区间均不为空时，我们判断两区间左端是否相同，若相同，区间左端一起向右移动一位即可。

考虑剩下的小于关系构成的有向图：如果存在环，这些数必须相同，大小关系继承到下一位。

如果不存在环，答案成立，直接输出即可。

如果一组限制的第二个区间先被取完，这样的数据无法构造出解，直接输出即可。

实现的时候用并查集记录大小相同的位置，用 tarjan 找环。

## Codes

```cpp
// Problem: D - Substring Comparison
// Contest: AtCoder - AtCoder Regular Contest 165
// URL: https://atcoder.jp/contests/arc165/tasks/arc165_d
// Memory Limit: 1024 MB
// Time Limit: 2000 ms
//
// Powered by CP Editor (https://cpeditor.org)

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
int n, m;
struct node
{
    int to, nxt;
} edge[max_n];
int head[max_n], tot;
void add(int u, int v)
{
    edge[++tot].to = v;
    edge[tot].nxt = head[u];
    head[u] = tot;
}
struct Query
{
    int a, b, c, d;
} que[max_n];
int fa[max_n];
int find(int u)
{
    if (fa[u] == u)
    {
        return u;
    }
    return fa[u] = find(fa[u]);
}
void merge(int u, int v)
{
    if (find(u) == find(v))
    {
        return;
    }
    fa[find(u)] = find(v);
}
int dfn[max_n], low[max_n], timer, st[max_n], vis[max_n];
int col[max_n], c;
bool no_circle;
void tarjan(int u)
{
    st[++st[0]] = u;
    dfn[u] = low[u] = ++timer;
    vis[u] = 1;
    for (int i = head[u]; i; i = edge[i].nxt)
    {
        int v = edge[i].to;
        if (!dfn[v])
        {
            tarjan(v);
            low[u] = min(low[u], low[v]);
        }
        else if (vis[v])
        {
            low[u] = min(low[u], dfn[v]);
        }
    }
    if (dfn[u] == low[u])
    {
        ++c;
        int now;
        do
        {
            now = st[st[0]--];
            col[c] = c;
            merge(u, now);
            vis[now] = 0;
        } while (now != u);
    }
}
signed main()
{
    read(n), read(m);
    for (int i = 1; i <= n; i++)
    {
        fa[i] = i;
    }
    for (int i = 1; i <= m; i++)
    {
        read(que[i].a);
        read(que[i].b);
        read(que[i].c);
        read(que[i].d);
    }
    for (int T = 1; T <= m; T++)
    {
        for (int j = 1; j <= n; j++)
        {
            head[j] = 0;
            dfn[j] = low[j] = 0;
            vis[j] = 0;
            col[j] = 0;
        }
        tot = 0, c = 0, timer = 0;
        for (int i = 1; i <= m; i++)
        {

            while (find(que[i].a) == find(que[i].c) && que[i].a <= que[i].b && que[i].c <= que[i].d)
            {
                ++que[i].a;
                ++que[i].c;
            }
            if (que[i].c > que[i].d)
            {
                puts("No");
                exit(0);
            }
            if (que[i].a <= que[i].b)
            {
                add(find(que[i].a), find(que[i].c));
            }
        }
        for (int j = 1; j <= n; j++)
        {
            if (!dfn[j])
            {
                tarjan(j);
            }
        }
        if (c == n)
        {
            puts("Yes");
            exit(0);
        }
    }
    return 0;
}
```