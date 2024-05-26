---
title: AT ARC165A 题解
top: 100000000
date: 2024-05-04 09:47:41
tags:
  - AtCoder
  - 题解
---
<!---->
<!--more-->

# [ARC165A] Sum equals LCM

## Links

[洛谷](https://www.luogu.com.cn/problem/AT_arc165_a)

[AtCoder](https://www.luogu.com.cn/problem/AT_arc165_a)

## Description

**单测试点存在多组测试数据。**

给定整数 $x$，判断是否有 $n$（$n \geq 2$）个数的和为 $x$，最小公倍数也为 $x$。

$1 \leq T \leq 100$，$2 \leq x \leq 10^{9}$。

## Solution

首先我们考虑到 $1$ 对最大公因数不会造成贡献，因此，如果我们找到了 $p \leq n$ 个数的和 $sum \leq x$ 且最小公倍数 $\operatorname{LCM} = x$ 时，我们可以通过补充 $1$ 来满足题目条件。

在 $a,b \geq 2$ 时，我们可以证明 $a + b \leq a \times b$。

$$\texttt{当}\ a,b \geq 2\ \texttt{时显然}\ \frac{1}{a} + \frac{1}{b} \leq 1$$
$$\texttt{两边同时乘}\ a\times b\ \texttt{得}\ a+b\leq a\times b$$

于是我们只需要知道 $x$ 能否分解为**不同因数**的 $\operatorname{LCM}$ 即可，也就是存在不同的质因数。

直接写一个复杂度 $\sqrt n$ 的分解质因数即可，复杂度 $O(\sum \sqrt{n})$。