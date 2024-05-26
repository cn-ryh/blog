---
title: AT ABC301C 题解
top: 100000000
date: 2024-05-04 10:19:54
tags:
  - AtCoder
  - 题解
---
<!---->
<!--more-->

# [ABC301C] AtCoder Cards 题解

## Links

[Luogu](https://www.luogu.com.cn/problem/AT_abc301_c)

[AtCoder](https://atcoder.jp/contests/abc301/tasks/abc301_c)

## Description

给定两个等长的字符串，只包含小写字母和 `@` 字符。

判断能否通过重新排列某一字符串并将 `@` 换为任意 `a`，`t`，`c`，`o`，`d`，`e`，`r` 中的字符使得两个字符串变得完全相同。

## Solution

统计两个字符串中各个字符的数量，若数量不等的不是 `a`，`t`，`c`，`o`，`d`，`e`，`r` 中的字符则不能完成。否则判断 `@` 字符是否够用即可，若数量少于差异数量则无法完成。

## Codes

```cpp
#include <bits/stdc++.h>
using namespace std;
#define int long long
#define max_n 210101
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
char s1[max_n], s2[max_n];
int cnt1[max_n], cnt2[max_n];
map<char, int> mp;
signed main()
{
#if _clang_
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    scanf("%s", s1 + 1);
    scanf("%s", s2 + 1);
    mp['a'] = 1;
    mp['t'] = 1;
    mp['c'] = 1;
    mp['o'] = 1;
    mp['d'] = 1;
    mp['e'] = 1;
    mp['r'] = 1;
    int n = strlen(s1 + 1);
    for (int i = 1; i <= n; i++)
    {
        if (s1[i] == '@')
        {
            ++cnt1[0];
        }
        if (s2[i] == '@')
        {
            ++cnt1[0];
        }
        cnt1[s2[i]]--;
        cnt1[s1[i]]++;
    }
    for (char c = 'a'; c <= 'z'; c++)
    {
        if (mp[c])
        {
            cnt1[0] -= abs(cnt1[c]);
        }
        else if (cnt1[c] != 0)
        {
            puts("No");
            return 0;
        }
    }
    if (cnt1[0] < 0)
    {
        puts("No");
    }
    else
    {
        puts("Yes");
    }
    return 0;
}
```
