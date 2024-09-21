---
abbrlink: atcoder-abc-372-e
categories: []
date: '2024-09-21T23:28:44.113762+08:00'
tags: []
title: 题解：K-th Largest Connected Components
updated: '2024-09-21T23:28:45.719+08:00'
---
# 题意

给定 $n$ 个点 $0$ 条边的无向图和 $q$ 次操作，每次操作分为两种：

- `1 u v` 表示在 $u, v$ 之间建立一条边。
- `2 v k` 表示查询**能够到达 $v$ 的点**中第 $k$ 大的顶点编号。

# 思路

[并查集](https://oi-wiki.org/ds/dsu/)好题，~~这个笨比赛时没想到~~。

题目要求的是能够到达的点中第 $k$ 大的顶点编号，而不是有直接连边的点。直接对每个点记录连出去的边然后手写堆查找肯定是不行的，这时可以考虑把所有点收在若干棵树内，这就叫并查集。

此时在同一棵树内的点就是相互可达的点，此时就可以做了。但是你会发现一些问题：

- 每次查询需要在一整棵树内找第 $k$ 大，TLE。
- 每个点存储了太多子结结点的信息，MLE。

实际上，每次合并可以把两棵树的结点按照从大到小的顺序存储到同一个结点内，这样就省下了每次查询的时间，由于 $k \le 10$，所以每个点不需要存储所有的可达的点，只需要存储编号最大的 $10$ 个即可，这样就省下了空间。

# 代码

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN = 2e5 + 10;

int fa[MAXN];
vector<int> a[MAXN];
int n, q;

int find(int x) {
  return fa[x] == x ? x : fa[x] = find(fa[x]);
}

void merge(int u, int v) {
  int fu = find(u), fv = find(v);
  if (fu == fv) {
    return;
  }
  vector<int> x;
  for (int i : a[fu]) {
    x.push_back(i);
  }
  for (int i : a[fv]) {
    x.push_back(i);
  }
  sort(x.begin(), x.end(), greater<int>());
  a[fu].clear();
  a[fv].clear();
  for (int i = 0; i < min(10, int(x.size())); i++) {
    a[fv].push_back(x[i]);
  }
  fa[fu] = fv;
}

int main() {
  ios::sync_with_stdio(0), cin.tie(0);
  cin >> n >> q;
  for (int i = 1; i <= n; i++) {
    fa[i] = i;
    a[i].push_back(i);
  }
  for (int op, x, y; q; q--) {
    cin >> op >> x >> y;
    if (op == 1) {
      merge(x, y);
    } else {
      x = find(x);
      cout << (y > a[x].size() ? -1 : a[x][y - 1]) << '\n';
    }
  }
  return 0;
}
```
