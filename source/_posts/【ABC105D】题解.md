---
abbrlink: 【ABC105D】题解
categories: []
catgories: 芝士
date: '2024-02-17T00:12:41.921570+08:00'
mathjax: true
tag: 题解
tags: []
title: 【ABC105D】题解
updated: '2024-02-17T00:12:41.927+08:00'
---
# 题解

## 题意简述

> 给定 $n$ 个数，求这 $n$ 个数中有多少个二元组 $(x,y)$ 满足其中每一个数都是 $m$ 的倍数。

## 思路

前缀和，$(x,y)$ 内每一个数 $\bmod \ m = 0$，可以用 $(sum_y - sum_{x - 1}) \bmod \ m = 0$ 表示。但是这题数据太大，所以要使用 `map`。

如果 $x = 1$，那么 $sum_{x - 1} = sum_0$，所以要先给 $sum_0$ 赋值为 $1$

## AC Code

```cpp
#include<bits/stdc++.h>

using namespace std;

int n, m, x;
map<long long, int> cnt;
long long sum, ans;

int main(){
  cin >> n >> m;
  cnt[0] = 1;
  for (int i = 1; i <= n; i++){
    cin >> x;
    sum += x;
    ans += cnt[sum % m];
    cnt[sum % m]++;
  }
  cout << ans;
  return 0;
}
```
