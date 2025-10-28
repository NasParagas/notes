---
title: "INT_MAX - cpprefjp C++日本語リファレンス"
source: "https://cpprefjp.github.io/reference/climits/int_max.html"
author:
  - "[[Koichi Murase]]"
published:
created: 2025-09-02
description: "`int` 型が表現できる値の最大値。"
tags:
  - "clippings"
---
最終更新日時(UTC): 2025年07月13日 04時07分37秒  
Koichi Murase が更新

[履歴](https://github.com/cpprefjp/site/commits/master/reference/climits/int_max.md) [編集](https://github.com/cpprefjp/site/edit/master/reference/climits/int_max.md)

macro

## INT\_MAX

```
#define INT_MAX implementation-defined
```

## 概要

`int` 型が表現できる値の最大値。

`[std::numeric_limits](https://cpprefjp.github.io/reference/limits/numeric_limits.html)<int>::[max()](https://cpprefjp.github.io/reference/limits/numeric_limits/max.html)` と等しいが、 `INT_MAX` は `#if` などのプリプロセッサディレクティブで使用できる。

具体的な値は実装依存であるが、32767（2 <sup>15</sup> - 1）以上であることが規格で定められている。このマクロによって定義される値の型は `int` である。

## 例

```
#include <climits>
#include <iostream>

int main()
{
  std::cout << INT_MAX << '\n';
}
```

```
std::cout << INT_MAX << '\n';
```

### 出力例

```
2147483647
```

## バージョン

### 言語

- C++03
- C++11

### 処理系

- [Clang](https://cpprefjp.github.io/implementation.html#clang): 3.0 ✅, 3.1 ✅, 3.2 ✅, 3.3 ✅, 3.4 ✅
- [GCC](https://cpprefjp.github.io/implementation.html#gcc): 4.3.6 ✅, 4.4.7 ✅, 4.5.3 ✅, 4.5.4 ✅, 4.6.4 ✅, 4.7.3 ✅, 4.8.1 ✅, 4.8.2 ✅, 4.9.0 ✅
- [ICC](https://cpprefjp.github.io/implementation.html#icc):??
- [Visual C++](https://cpprefjp.github.io/implementation.html#visual_cpp): 2003 ✅, 2005 ✅, 2008 ✅, 2010 ✅