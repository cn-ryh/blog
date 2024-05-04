---
title: AT ARC165B 题解
date: 2024-05-04 09:47:04
tags:
  - AtCoder
  - 题解
---
<!---->
<!--more-->

# AT_arc165_b [ARC165B] Sliding Window Sort 2 题解

## Links

[洛谷](https://www.luogu.com.cn/problem/AT_arc165_b)

[AtCoder](https://atcoder.jp/contests/arc165/tasks/arc165_b)

## Description

给定正整数 $n,k$ 和一个长度为 $n$ 的整数 $P$，你需要选择一个长度为 $k$ 的区间 $[l,l + k - 1]$，将这个区间从小到大排序。

找到操作后最终字典序最大的排列。

$1 \leq k \leq n \leq 2 \times 10^{5}$。

## Solution

这是一篇常数比较大，可能比较麻烦的题解。~~考场上被创死的一道题。~~

考虑我们排序后不会把大的元素移动到原来的前面，因此排序一定不优。

如果我们可以选择一个区间，排序后不变，那么选择这个区间一定是更优的。

考虑怎么判断一个区间排序后不变：在每个 $nums_{i} < nums_{i + 1}$（也就是排序后相对位置会变化）的地方打上标记，如果一个长度为 $k$ 的区间没有标记就代表排序后不变。这部分可以用树状数组实现。

```cpp
int lowbit(int x)
{
    return (-x)&x;
}
void add(int x)
{
    for(;x <= n;x += lowbit(x))
    {
        tree[x]++;
    }
}
int query(int x)
{
    int ans = 0;
    for(;x;x -= lowbit(x))
    {
        ans += tree[x];
    }
    return ans;
}
int query(int l,int r)
{
    if(r < l)
    {
        return 0;
    }
    return query(r) - query(l - 1);
}


// 下面是主函数中的

for(int i = 2;i <= n;i++)
{
    if(nums[i] < nums[i - 1])
    {
        add(i);
    }
}
//   a     a
// 2 1 2 4 3
for(int i = 1;i <= n - k + 1;i++)
{
    if(query(i + 1,i + k - 1) == 0)
    {
        for(int j = 1;j <= n;j++)
        {
            writesp(nums[j]);
        }
        puts("");
        return 0;
    }
}
```

如果必须变化，我们期望变的第一个越往后越好，这样字典序会尽量大，同时，我们期望变的个数少。

于是可以从后向前搜，只要 $nums_{i - 1} < \min{ \{nums_{i \dots i + k - 1} \} }$，我们向前移动排序的区间就不会造成 $nums_{i - 1}$ 改变，而后面可能改变的地方变少了（上一个区间参与排序的 $nums_{i + k}$ 不会参与排序了，相当于我们排序了一个更小的区间，一定不劣）。如果 $nums_{i - 1} > nums_{i}$，此时向前移动会导致第一个变小的位置向前移动，答案更劣，因此这时候需要停止搜索。

这部分的实现使用了 ST 表查找区间最小值。

```cpp
int F[max_n][28];
void ST() {
  for (int i = 1; i <= n; i++) {
    F[i][0] = nums[i];
  }
  int k = log2(n);
  for (int j = 1; j <= k; j++) {
    for (int i = 1; i <= n - (1 << j) + 1; i++) {
      F[i][j] = min(F[i][j - 1], F[i + (1 << (j - 1))][j - 1]);
    }
  }
}
int ST_query(int l, int r) {
  int k = log2(r - l + 1);
  return min(F[l][k], F[r - (1 << k) + 1][k]);
}

// 主函数中

ST();
int beg,flag = n - k + 1; // flag 就是最终选择的排序起点
for(beg = n - k + 1;beg > 1;beg--)
{
    if(nums[beg - 1] < ST_query(beg,beg + k - 2))
    {
        flag = beg - 1;
    }
    if(nums[beg - 1] > nums[beg])
    {
        // cout<<beg<<endl;
        break;
    }
}

sort(nums + flag,nums + flag + k);
for(int i = 1;i <= n;i++)
{
    writesp(nums[i]);
}
puts("");
```

完整代码就不给出了，时间复杂度 $O(n \log n)$。