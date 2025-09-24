---
abbrlink: cf1905d-solution
categories: 芝士
date: '2025-09-23T23:06:13.580+08:00'
tags: 题解
title: 'CF1905D Cyclic MEX 题解'
updated: '2025-09-23T23:06:13.580+08:00'
mathjax: true
---

## 题意
给定一个序列，求这个序列所有的循环移位中的 $f(A) = \sum \limits_{i = 1} ^ n \text{mex} ([A_1, A_2, \dots, A_i])$ 最大值。

## 思路
发现题解区还没有基于势能分析法的做法，发一个。

推式子题。对于一个序列 $A_1, A_2, \dots, A_n$ 而言。

它的一个循环移位 $A_{t} = A_{k + 1}, A_{k + 2}, \dots, A_n, A_1, \dots, A_k$，对于 $1 \le i \le n - k$，有：

$$
\text{mex} ([{A_{t}}_1, {A_t}_2, \dots, {A_t}_i]) = \min (\text{mex} ([A_1, A_2, \dots, A_{i + k}]), \min \limits_{1 \le j \le k} A_j)
$$

而对于 $n - k + 1 \le i \le n$，我们发现多余的部分到了前面，所以：

$$
\text{mex} ([{A_t}_1, {A_t}_2, \dots, {A_t}_i]) = \min (\text{mex} ([A_1, A_2, \dots, A_n]), \min \limits_{i + k - n + 1 \le j \le k} A_j)
$$

上述答案可以尝试使用数据结构维护。但是，我们发现线段树没有方法直接求区间取 $\min$、区间求和。但是，如果我们尝试分析暴力区间修改的时间复杂度，会发现：如果我们记录区间最大值、次大值、最大值的个数以及区间和，如果当前需要取 $\min$ 的数 $x > \max$，那么无需操作；如果 $\max' \le x \le \max$，那么这个区间就有 $cnt$ 个数需要取 $\min$；否则直接递归操作。这样，时间复杂度为 $O(m \log n)$，其中 $m$ 为操作次数。

注意：不能使用 `set` 求初始答案，这样会超时。可以利用排列的性质，$O(n)$ 地求。

```cpp
#include <bits/stdc++.h>

using namespace std;
using ll = long long;

const int MAXN = 1e6 + 10, INF = 2e9;

int n, a[MAXN], ans[MAXN];

struct Node {
  ll sum, mx, cmx, cnt;
};

struct Tag {
  int val;
  bool operator==(const Tag &oth) const {
    return val == oth.val;
  }
};

struct SegTree {
  Node dat[MAXN << 2], E = {0, 0, 0, 0};
  Tag tag[MAXN << 2], I = {INF};
  Node comb(const Node &dat1, const Node &dat2) {
    Node res = E;
    res.sum = dat1.sum + dat2.sum;
    if (dat1.mx > dat2.mx) {
      res.mx = dat1.mx;
      res.cmx = max(dat2.mx, dat1.cmx);
      res.cnt = dat1.cnt;
    } else if (dat1.mx < dat2.mx) {
      res.mx = dat2.mx;
      res.cmx = max(dat1.mx, dat2.cmx);
      res.cnt = dat2.cnt;
    } else {
      res.mx = dat1.mx;
      res.cmx = max(dat1.cmx, dat2.cmx);
      res.cnt = dat1.cnt + dat2.cnt;
    }
    return res;
  }
  Tag F(const Tag &tag1, const Tag &tag2) {
    return {min(tag1.val, tag2.val)};
  }
  Node f(const Node &dat, const Tag &tag) {
    if (dat.mx <= tag.val) return dat;
    return {dat.sum + (tag.val - dat.mx) * dat.cnt, tag.val, dat.cmx, dat.cnt};
  }
  void down(int root) {
    if (tag[root] == I) return;
    tag[root << 1] = F(tag[root << 1], tag[root]);
    tag[root << 1 | 1] = F(tag[root << 1 | 1], tag[root]);
    dat[root << 1] = f(dat[root << 1], tag[root]);
    dat[root << 1 | 1] = f(dat[root << 1 | 1], tag[root]);
    tag[root] = I;
  }
  void build(int root, int l, int r, bool f) {
    tag[root] = I;
    if (l == r) {
      if (f) {
        dat[root] = {INF, INF, -1, 1};
      } else {
        dat[root] = {ans[l], ans[l], -1, 1};
      }
      return;
    }
    int mid = l + r >> 1;
    build(root << 1, l, mid, f);
    build(root << 1 | 1, mid + 1, r, f);
    dat[root] = comb(dat[root << 1], dat[root << 1 | 1]);
  }
  void modify(int root, int l, int r, int L, int R, Tag t) {
    if (L > R || dat[root].mx <= t.val) return;
    if (L <= l && R >= r && dat[root].cmx < t.val) {
      dat[root] = f(dat[root], t);
      tag[root] = F(tag[root], t);
      return;
    }
    down(root);
    int mid = l + r >> 1;
    if (L <= mid) modify(root << 1, l, mid, L, R, t);
    if (R > mid) modify(root << 1 | 1, mid + 1, r, L, R, t);
    dat[root] = comb(dat[root << 1], dat[root << 1 | 1]);
  }
  Node query(int root, int l, int r, int L, int R) {
    if (L <= l && R >= r) return dat[root];
    if (L > r || R < l) return E;
    down(root);
    int mid = l + r >> 1;
    return comb(query(root << 1, l, mid, L, R), query(root << 1 | 1, mid + 1, r, L, R));
  }
} T1, T2;

void Solve() {
  cin >> n;
  for (int i = 1; i <= n; i++) {
    cin >> a[i];
  }
  int mi = INF;
  ans[n] = n;
  for (int i = n - 1; i >= 1; i--) {
    mi = min(mi, a[i + 1]);
    ans[i] = mi;
  }
  T1.build(1, 1, n, 0);
  T2.build(1, 1, n, 1);
  ll res = T1.query(1, 1, n, 1, n).sum;
  mi = INF;
  for (int i = 1; i < n; i++) {
    T1.modify(1, 1, n, i + 1, n, {a[i]});
    if (i == 1) {
      T2.modify(1, 1, n, i, i, {n});
    } else {
      T2.modify(1, 1, n, 2, i, {a[i]});
    }
    res = max(res, T1.query(1, 1, n, i + 1, n).sum + T2.query(1, 1, n, 1, i).sum);
  }
  cout << res << '\n';
}

int main() {
  ios::sync_with_stdio(0), cin.tie(0);
  int T;
  for (cin >> T; T--; Solve());
  return 0;
}
```