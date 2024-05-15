---
title: AT ARC016D 题解
date: 2024-05-04 08:57:33
order: 0
tags:
  - AtCoder
  - 题解
---
<!---->
<!--more-->

## ARC 016D

### 题目描述

$N$ 个点 $M$ 条边的简单 DAG。$1$ 号点是母港，$N$ 号点是最终目的地，离开 $1$ 号点时 HP 为 $H$。每个回合将等概率随机选取一条出边走过去，花费 $1$ 时间，并发生战斗。每次到达第 $i$ 个点，战斗都将掉 $D_{i}$ 点 HP，HP 必须始终保持为正。

战斗后有两种选择：传送回母港或继续走，如果传送回母港时 HP 为 $C$，则花费 $H - C$ 时间修船。求最优策略下的期望最短时间，若无法到达输出 $-1$，绝对或相对误差不超过 $10^{-6}$ 即算正确。 

$2 \leq N \leq 100$，$1 \leq H \leq 100$，$0 \leq D_i \leq 100$，$D_1 = 0, D_N \neq 0$。答案不超过 $10 ^ 6$。

### 题解

考虑设 $dp_{i,j}$ 表示在点 $i$，剩余血量为 $j$ 到达点 $N$ 的期望时间。令 $edge_{u}$ 为 $u$ 出边到达的点的集合，有如下转移：

$$
dp_{i,j} = \min(dp_{1,H} + H - j,1 + \sum_{v \in edge_{i}}\dfrac{dp_{v,j - D_{v}}}{|edge_{i}|})
$$

然而我们发现，在这个 dp 中我们用还没有确定的 $dp_{1,H}$ 去更新了其他位置的值甚至是 $dp_{1,H}$ 自己，这显然不满足 dp 的要求。不过很容易发现在这个 dp 中只有 $dp_{1,H}$ 的位置形成了环，其他位置都是正常的转移，不难发现这是一个方程组，如果我们能直接解出 $dp_{1,H}$ 就完成了任务。但事实上由于方程过多并且受 $\min$ 的限制，我们很难直接解出这个方程。不过经过观察，如果我们令**转移中使用的** $dp_{1,H}$ 为 $a$，那么**最终计算出的** $dp_{1,H}$ 随 $a$ 递增而单调不降，原因是 $dp_{n,j}$ 的边界不变，转移中如果取 $\min$ 的第一项，$dp_{i,j}$ 变大，否则 $dp_{v,j - D_{v}}$ 也是不减的，于是我们可以二分这个 $a$，并与最终计算出的 $dp_{1,H}$ 进行比较，最终找到 $dp_{1,H} = a$ 作为答案。

时间复杂度是 $O((N + M)\log V)$，其中 $V$ 为答案值域 $10^{6}$。

### Code

```cpp
#include <bits/stdc++.h>
using namespace std;
#define int long long
#define max_n 15101
#define ldb long double
const ldb eps = 1e-8;
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
int n, m, h;
int d[max_n];
ldb mid;
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
ldb dp[121][112];
int vis[1111];
void dfs(int u)
{
    vis[u] = 1;
    for (int i = 1; i <= h; i++)
    {
        dp[u][i] = 0;
    }
    int siz = 0;
    for (int i = head[u]; i; i = edge[i].nxt)
    {
        int v = edge[i].to;
        if (!vis[v])
        {
            dfs(v);
        }
        for (int j = 1; j <= h; j++)
        {
            if (j - d[v] <= 0)
            {
                dp[u][j] = 1e12;
            }
            else
            {
                dp[u][j] += dp[v][j - d[v]];
            }
        }
        ++siz;
    }
    for (int j = 1; j <= h; j++)
    {
        if (u < n)
        {
            if (u == 1 && j == h)
            {
                dp[u][j] = 1 + dp[u][j] / siz;
            }
            else
            {
                dp[u][j] = min(mid + h - j, 1 + dp[u][j] / siz);
            }
        }
        else
        {
            dp[u][j] = 0;
        }
    }
}
ldb check()
{
    memset(vis, 0, sizeof(vis));
    dfs(1);
    if (!vis[n])
    {
        return 1e12;
    }
    return dp[1][h];
}
signed main()
{
    read(n), read(m), read(h);
    for (int i = 1, u, v; i <= m; i++)
    {
        read(u), read(v);
        add(u, v);
    }
    for (int i = 1; i <= n; i++)
    {
        read(d[i]);
    }
    ldb l = 0, r = 1.2e6;
    while (r - l > eps)
    {
        mid = (l + r) / 2.00;
        if (check() > mid)
        {
            l = mid;
        }
        else
        {
            r = mid;
        }
    }

    if (l > 1e6)
    {
        puts("-1");
    }
    else
    {
        printf("%.50Lf", l);
    }
    return 0;
}
```

