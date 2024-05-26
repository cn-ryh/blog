---
title: AT ARC165C 题解
top: 100000000
date: 2024-05-04 09:46:33
tags:
  - AtCoder
  - 题解
---
<!---->
<!--more-->

# AT_arc165_c [ARC165C] Social Distance on Graph 题解

## Links

[洛谷](https://www.luogu.com.cn/problem/AT_arc165_c)

[AtCoder](https://atcoder.jp/contests/arc165/tasks/arc165_c)

## Description

给定一个无向带权图，将所有节点染成黑色或白色，使得最终相同颜色节点之间距离的最小值最大。

$1 \leq n \leq 2 \times 10^{5}$，$n - 1 \leq m \leq \min(\frac{n \times (n - 1)}{2},2 \times 10^{5})$。

## Solution

貌似比 B 简单一点。

考虑对这张图进行染色的过程：

1. 如果图本身可以染色使得没有相邻顶点颜色相同，这样的染色一定是最优的，此时我们找到相邻两条边权和的最小值即可。

2. 否则，我们一定需要把某些相邻顶点染成相同颜色，那么我们要**尽可能先满足权值小的边相连的点颜色不同**，这样才能保证最小值尽可能大。

由于树始终能构造出相邻顶点颜色不同的染色方法，结合边权从大到小，可以想到在最小生成树上染色。染色后再按照 1. 的步骤进行即可。

需要注意的是可能存在两条最小生成树上相邻边的长度大于这两个点直接相连的边的边权，因此答案要还有对所有连接颜色相同顶点的边权取最小值。

第 1. 显然可以按照第 2. 的方法操作，因此我们不需要判断，直接构建最小生成树染色即可。


如果直接枚举相邻边算答案会超时，所以记录一下每个点连接的最小值和次小值即可。

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

struct node
{
    int to,nxt,val;
}edge[max_n];
int head[max_n],tot;
void add(int u,int v,int w)
{
    edge[++tot].to = v;
    edge[tot].nxt = head[u];
    edge[tot].val = w;
    head[u] = tot;
}
int n,m;
struct Line
{
    int from,to,val;
}G[max_n];
int awa = 0;
bool cmp(Line id1,Line id2)
{
    return id1.val < id2.val;
}
int fa[max_n];
int mn[max_n],mmn[max_n];
int find_fa(int u)
{
    if(fa[u] == u)
    {
        return u;
    }
    return fa[u] = find_fa(fa[u]);
}
vector<int> THU;
void Kruskal() 
{
    int nums = 0;
  sort(G + 1, G + m + 1, cmp);
  for (int i = 1; i <= m; i++) {
    if (find_fa(G[i].from) != find_fa(G[i].to)) {
  //   cout<< G[i].from<<" "<<G[i].to<<" "<<G[i].val<<endl;
    int u = G[i].from,v = G[i].to;
   // cout<<"@"<<mn[u]<<" "<<mmn[u]<<endl;
    if(G[i].val < mn[u])
    {
        mmn[u] = mn[u];
        mn[u] = G[i].val;
    }
    else if(G[i].val < mmn[u])
    {
        mmn[u] = G[i].val;
    }
    if(G[i].val < mn[v])
    {
        mmn[v] = mn[v];
        mn[v] = G[i].val;
    }
    else if(G[i].val < mmn[v])
    {
        mmn[v] = G[i].val;
    }
      add(G[i].from,G[i].to,G[i].val);
      add(G[i].to,G[i].from,G[i].val);
      nums++;
      fa[find_fa(G[i].from)] = find_fa(G[i].to);
    }
    if (nums == n - 1) {
      return;
    }
  }
}
int col[max_n],vis[max_n];
bool dfs(int u,int fa,int c)
{
    vis[u] = 1;
    col[u] = c;
    for(int i = head[u];i;i = edge[i].nxt)
    {
        int v = edge[i].to;
        if(v == fa)
        {
            continue;
        }
        if(vis[v] && col[v] != c)
        {
            return false;
        }
        else if(vis[v])
        {
            continue;
        }
        if(!dfs(v,u,c ^ 1))
        {
            return false;
        }
    }
    return true;
}
signed main()
{
    read(n),read(m);
    for(int i = 1;i <= n;i++)
    {
        fa[i] = i;
        mn[i] = mmn[i] = INT_MAX;
    }
    for(int i = 1,u,v,w;i <= m;i++)
    {
        
        read(u),read(v),read(w);
        G[i] = {u,v,w};
    }
    tot = 0;
    Kruskal();
    dfs(1,1,0);
    int ans = INT_MAX;
    for(int i = 1;i <= n;i++)
    {
       // cout<<mn[i]<<" "<<mmn[i]<<endl;
        if(mmn[i] != INT_MAX)
        {
            ans = min(ans,mn[i] + mmn[i]);
        }
    }
    for(int i = 1;i <= m;i++)
    {
        int u = G[i].from,v = G[i].to,w = G[i].val;
        if(col[u] == col[v])
        {
            ans = min(ans,w);
        }
    }
    writeln(ans);

    return 0;
}
```