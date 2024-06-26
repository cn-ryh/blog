---
title: ST表
date: 2024-04-26 23:24:46
tags:
top: 43000
---

ST 表讲解

<!-- more -->

## 简介

### 定义

ST 表是用于解决**可重复贡献**问题的数据结构。

{% note info 什么是可重复贡献问题  %}

即一个元素无论计算多少遍都不影响最终答案的问题。

若运算符 $\mathop{opt}$ 满足 $x \mathop{opt} x = x$，则对序列上的 $\mathop{opt}$ 进行区间询问是可重复贡献问题。

例如：
- $\max(x,x) = x$，因此查询区间最大值为可重复贡献问题，类似的，查询区间最小值也为可重复贡献问题。
- $\gcd(x,x) = x$，因此查询区间最大公因数为可重复贡献问题，类似的，查询区间最小公倍数也为可重复贡献问题。

相应的，四则运算等不属于可重复贡献问题。
{% endnote %}

### 关于 ST 表

ST 表是基于**倍增**的数据结构，可以在 $O(n\log n)$ 的时间内预处理数列，并在 $O(1)$ 的时间内完成查询，但 ST 表**不支持修改**。

## 算法介绍

### 构建



使用二维数组存储 ST 表，则 $ST[i][j]$ 表示**以 $i$ 为左端点，长度为 $2^{j}$** 的区间（即 $[i,i + 2^j - 1]$）的预处理值（可能为最大值、最小值、$\gcd$ 等）。


我们将 $ST[1 \sim n][j]$ 称为 $j$ 层，则构建时从 $0$ 层开始，每层**单个区间长度为上一层的二倍**。

{%note primary%}
根据上一层的两个子区间构建当前区间的方法（以最大值为例）：

由于构建时每个区间的长度都为 $2^{k}$，可以直接拆分为两个 $2^{k-1}$ 的子区间，例如 $[3,10]$ 可以拆分为 $[3,6] \And [7,10]$。

每次取左右两个子区间最大值，得到的便是当前区间的最大值。
{%endnote%}


例如，构建序列 $\{1,4,6,2,7,3,9,10,0\}$ 的**最大值** ST 表，过程如下：


$ST[0]$ 初始化为本身：
<table>
<tr><td>$ST[0]$</td><td>1</td><td>4</td><td>6</td><td>2</td><td>7</td><td>3</td><td>9</td><td>10</td><td>0</td></tr></table>

开始构建：

<table><tr><td>$ST[0]$</td><td>1</td><td>4</td><td>6</td><td>2</td><td>7</td><td>3</td><td>9</td><td>10</td><td>0</td></tr>
<tr><td>$ST[1]$</td><td>$\max(1,4) = 4$</td><td>$\max(4,6) = 6$</td><td>$\max(6,2)=6$</td><td>$\max(2,7)=7$</td><td>$\max(7,3) = 7$</td><td>$\max(3,9)=9$</td><td>$\max(9,10) = 10$</td><td>$\max(10,0) = 10$</td><td>留空</td></td></tr>
<tr><td>$ST[2]$</td><td>$\max(4,6) = 6$</td><td>$\max(6,7) = 7$</td><td>$\max(6,7)=7$</td><td>$\max(7,9)=9$</td><td>$\max(7,10) = 10$</td><td>$\max(9,10) = 10$</td><td>留空</td><td>留空</td><td>留空</td></td></tr>
<tr><td>$ST[3]$</td><td>$\max(6,10) = 10$</td><td>$\max(7,10) = 10$</td><td>留空</td><td>留空</td><td>留空</td><td>留空</td><td>留空</td><td>留空</td><td>留空</td></td></tr>
</table>


至此，整张 ST 表构建完毕，建议先阅读查询方法，理解后再背诵。

{% note success 此部分代码 %} 
```cpp
int n,nums[max_n];
int ST[max_n][30];
void build()
{
    for(int i = 1;i <= n;i++)
    {
        ST[i][0] = nums[i];
    }
    // << j 表示 2 的 j 次方
    for(int j = 1;(1LL << j) <= n;j++)
    {
        // 区间左端点
        for(int i = 1;i + (1LL << j) - 1 <= n;i++)
        {
            ST[i][j] = max(ST[i][j - 1],ST[i + (1LL << (j - 1))][j - 1]);
        }
    }
    return ;
}
```
{% endnote %}


### 查询

在构建完成整个 ST 表后，我们便要处理询问，当询问 $[L,R]$ 区间的最值时，如何从表中找出合适的数据获得答案呢？

如果 $R-L+1$（$[L,R]$ 区间的长度）恰好为 $2^{k}$，则我们只需要找到 $ST[L][k]$ 即可；

但大多数时候并没有这样的特殊长度，又该如何处理呢？

我们可以选择一个最大的 $2^{k} \leq R-L+1$，这样我们将 $[L,R]$ 分为 $[L,L+2^{k}-1]$、$[R-2^{k}+1,R]$ 两段，这两段可能有重合，但一定能够覆盖整个查询区域（当 $k$ 最大时，$2^{k + 1} = 2 \times 2^{k} > R-L+1$，则两个小区间一定能覆盖整个区间）。

因此，我们只需要查询 $\max(ST[L,k],ST[R-2^{k}+1][k])$ 即可。


{% note success 此部分代码 %} 
```cpp
int query(int l,int r)
{
    // 不存在的区间
    if(r < l)
    {
        return 0;
    }
    // __lg 为取整数的 log2
    int k = __lg(r - l + 1);
    return max(st[l][k],st[r - (1LL << k) + 1][k]);
}
```
{% endnote %}


## 习题

### 例题

