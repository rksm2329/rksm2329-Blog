---
abbrlink: bigint
categories: []
date: '2024-02-10T18:36:58.219972+08:00'
tags: []
title: 高精度
updated: '2024-02-10T18:37:42.545+08:00'
---
# 高精度运算

## 对话框

dalao：高精度这么《简单》的东西怎么还要讲呢？！！

我：巨佬%%%

## 加法

先来想一下竖式加法。

竖式中，是每一位逐一相加，如果有进位，就标记一下。

那么高精度可以采用类似的方法。

循环遍历每一位，逐一相加，再处理进位。

## 减法

跟加法类似。

## 乘法

在竖式乘法中，是每一位逐位相乘，最后相加，所以我们可以利用一下。

## 除法

在除法竖式中，是每一位除以除数，然后拿余数接上后面那个数再来除以除数。

但是，高精除高精里，没办法逐位相除，就可以一直减，减到没有就停下，然后记录次数。

## 取模

差点忘了。。。

取模就是被除数 - 被除数 / 除数 * 除数。

## 压位

你在想什么？压位？不可能！

---

所以现在你已经知道了怎么实现，可以开始写了。

但是，仅限于十进制的算法怎么够呢？当然要加上其他进制！

## 实现

```cpp
#include <iostream>
#include <algorithm>

namespace Integer {
const std::string symbol = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ";
template <size_t kMaxLength, int binary = 10, class T = int> class BigInt {
private:
  T n, a[kMaxLength];
  bool f;

public:
  BigInt() { Init(); }
  T &operator[](int i) { return a[i]; }
  void Init() {
    n = f = 1;
    std::fill(a, a + kMaxLength, 0);
  }
  size_t size() { return n; }
  bool empty() { return !n; }
  friend std::istream &operator>>(std::istream &is, BigInt &a) {
    std::string s;
    is >> s;
    a.n = s.size();
    if (s[0] == '-') {
      a.f = 0;
      a.n--, s.erase(0, 1);
    }
    for (int i = 0; i < a.n; i++) {
      a[i] = (s[a.n - i - 1] >= 'A' && s[a.n - i - 1] <= 'Z'
                  ? s[a.n - i - 1] - 'A' + 10
                  : s[a.n - i - 1] - '0');
    }
    return is;
  }
  friend std::ostream &operator<<(std::ostream &os, BigInt a) {
    for (; a.n > 1 && !a[a.n - 1]; a.n--) {
    }
    if (!a.f) {
      os << '-';
    }
    for (int i = a.n - 1; i >= 0; i--) {
      os << symbol[a[i]];
    }
    return os;
  }
  void operator=(int x) {
    Init();
    if (!x) {
      return;
    }
    if (x < 0) {
      x = -x, f = 0;
    }
    n = 0;
    while (x) {
      a[n++] = x % binary;
      x /= binary;
    }
  }
  void operator=(std::string x) {
    Init();
    int st = 0;
    if (x[0] == '-') {
      f = 0, st++;
    }
    n = 0;
    int len = x.size();
    for (int i = st; i < len; i++) {
      a[n++] = (x[len - i - 1] >= 'A' && x[len - i - 1] <= 'Z' ? x[len - i - 1] - 'A' + 10 : x[len - i - 1] - '0');
    }
  }
  void operator=(BigInt x) {
    Init();
    n = x.n;
    f = x.f;
    for (int i = 0; i < n; i++) {
      a[i] = x[i];
    }
  }
  bool operator==(BigInt x) {
    if (n != x.n) {
      return 0;
    }
    for (int i = n - 1; i >= 0; i--) {
      if (a[i] != x[i]) {
        return 0;
      }
    }
    return 1;
  }
  bool operator!=(BigInt x) { return !operator==(x); }
  bool operator<(BigInt x) {
    if (n == x.n) {
      for (int i = n - 1; i >= 0; i--) {
        if (a[i] != x[i]) {
          return a[i] < x[i];
        }
      }
    }
    return n < x.n;
  }
  bool operator>(BigInt x) {
    if (n == x.n) {
      for (int i = n - 1; i >= 0; i--) {
        if (a[i] != x[i]) {
          return a[i] > x[i];
        }
      }
    }
    return n > x.n;
  }
  bool operator<=(BigInt x) { return !operator>(x); }
  bool operator>=(BigInt x) { return !operator<(x); }
  bool operator!() {
    if (n != 1) {
      return 0;
    }
    return !a[0];
  }
  bool operator==(int x) {
    BigInt y;
    y = x;
    return operator==(y);
  }
  bool operator!=(int x) {
    BigInt y;
    y = x;
    return operator!=(y);
  }
  bool operator<(int x) {
    BigInt y;
    y = x;
    return operator<(y);
  }
  bool operator>(int x) {
    BigInt y;
    y = x;
    return operator>(y);
  }
  bool operator<=(int x) {
    BigInt y;
    y = x;
    return operator<=(y);
  }
  bool operator>=(int x) {
    BigInt y;
    y = x;
    return operator>=(y);
  }
  void ChangeBinary(BigInt &num) {
    BigInt ans;
    ans[0] = 0, ans.n = 1;
    for (int i = num.n - 1; i >= 0; i--) {
      ans = ans * binary;
      ans = ans + num[i];
    }
    num = ans;
  }
  BigInt operator+(BigInt x) {
    BigInt y;
    y.n = std::max(n, x.n);
    int s = 0;
    for (int i = 0; i < y.n; i++) {
      s += a[i] + x.a[i];
      y.a[i] = s % binary;
      s /= binary;
    }
    for (; s; s /= binary) {
      y.a[y.n++] += s % binary;
    }
    return y;
  }
  void operator+=(BigInt x) {
    BigInt z = *this;
    *this = z + x;
  }
  void operator+=(int x) {
    BigInt z = *this;
    *this = z + x;
  }
  BigInt operator+(int x) {
    BigInt y;
    y = x;
    return operator+(y);
  }
  BigInt operator-(BigInt x) {
    BigInt y, z = *this;
    if (z < x) {
      y.f = 0, std::swap(z, x);
    }
    y.n = z.n;
    for (int i = 0; i < y.n; i++) {
      if (z.a[i] < x[i]) {
        z[i + 1]--;
        z[i] += binary;
      }
      y[i] = z[i] - x[i];
    }
    for (; !y[y.n - 1] && y.n > 1; y.n--) {
    }
    return y;
  }
  void operator-=(BigInt x) {
    BigInt z = *this;
    *this = z - x;
  }
  void operator-=(int x) {
    BigInt z = *this;
    *this = z - x;
  }
  BigInt operator-(int x) {
    BigInt y;
    y = x;
    return operator-(y);
  }
  BigInt operator*(BigInt x) {
    BigInt y;
    for (int i = 0; i < n; i++) {
      for (int j = 0; j < x.n; j++) {
        y[i + j] += a[i] * x[j];
      }
    }
    y.n = n + x.n;
    for (int i = 0; i < y.n - 1; i++) {
      y[i + 1] += y[i] / binary, y[i] %= binary;
    }
    for (; !y.a[y.n - 1] && y.n > 1; y.n--) {
    }
    return y;
  }
  void operator*=(BigInt x) {
    BigInt z = *this;
    *this = z * x;
  }
  void operator*=(int x) {
    BigInt z = *this;
    *this = z * x;
  }
  BigInt operator*(int x) {
    BigInt y;
    y = x;
    return operator*(y);
  }
  BigInt operator/(BigInt x) {
    BigInt y, z = *this;
    if (x == y) {
      return x;
    }
    if (binary != 10) {
      std::cout << x << ' ' << z << '\n';
      ChangeBinary(x), ChangeBinary(z);
      std::cout << x << ' ' << z << '\n';
    }
    y.n = z.n - x.n + 1;
    for (int i = y.n - 1; i >= 0; i--) {
      BigInt t = x << i;
      for (; z >= t; z -= t) {
        y.a[i]++;
      }
    }
    for (; !y.a[y.n - 1] && y.n > 1; y.n--) {
    }
    return y;
  }
  void operator/=(BigInt x) {
    BigInt z = *this;
    *this = z / x;
  }
  void operator/=(int x) {
    BigInt z = *this;
    *this = z / x;
  }
  BigInt operator/(int x) {
    BigInt y;
    y = x;
    return operator/(y);
  }
  BigInt operator%(BigInt x) {
    BigInt z = *this;
    return z - z / x * x;
  }
  void operator%=(BigInt x) {
    BigInt z = *this;
    *this = z % x;
  }
  void operator%=(int x) {
    BigInt z = *this;
    *this = z % x;
  }
  BigInt operator%(int x) {
    BigInt y;
    y = x;
    return operator%(y);
  }
  BigInt operator<<(int l) {
    BigInt x;
    for (int i = 0; i < n; i++) {
      x[i + l] = a[i];
    }
    x.n = n + l;
    return x;
  }
  BigInt operator>>(int l) {
    BigInt x;
    x.n = n - l;
    for (int i = 0; i < x.n; i++) {
      x[i] = a[i + l];
    }
    return x;
  }
};
}; // namespace Integer
// upd 2023.12.11 出现了一些 bug，已修正。
```
