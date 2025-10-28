---
title: "NumPyのarange, linspaceの使い方（連番や等差数列を生成） | note.nkmk.me"
source: "https://note.nkmk.me/python-numpy-arange-linspace/#numpyarange"
author:
  - "[[nkmk]]"
published: 2018-12-25
created: 2025-08-26
description: "NumPyで連番や等差数列など等間隔の配列ndarrayを生成するには、numpy.arange()かnumpy.linspace()を使う。arange()は間隔（公差）を指定、linspace()は要素数を指定するという違いがある。 numpy.arange — NumPy v ..."
tags:
  - "clippings"
---
## NumPyのarange, linspaceの使い方（連番や等差数列を生成）

Modified: | Tags: [Python](https://note.nkmk.me/python/), [NumPy](https://note.nkmk.me/numpy/)

NumPyで連番や等差数列など等間隔の配列 `ndarray` を生成するには、 `numpy.arange()` か `numpy.linspace()` を使う。 `arange()` は間隔（公差）を指定、 `linspace()` は要素数を指定するという違いがある。

- [numpy.arange — NumPy v1.26 Manual](https://numpy.org/doc/stable/reference/generated/numpy.arange.html)
- [numpy.linspace — NumPy v1.26 Manual](https://numpy.org/doc/stable/reference/generated/numpy.linspace.html)

本記事のサンプルコードのNumPyのバージョンは以下の通り。バージョンによって仕様が異なる可能性があるので注意。

```
import numpy as np

print(np.__version__)
# 1.26.1

source: numpy_arange.py
```

## numpy.arange()の使い方（間隔を指定）

`numpy.arange()` はPythonの組み込み関数 `range()` の `ndarray` 版。 `range()` については以下の記事を参照。

- **関連記事:**[Pythonのrange関数の使い方](https://note.nkmk.me/python-range-usage/)

引数の考え方は `range()` と同様。引数の個数によって以下のように等差数列を配列 `ndarray` として生成する。

- `numpy.arange(stop)`
	- `0 ≦ n < stop` で間隔は1
- `numpy.arange(start, stop)`
	- `start ≦ n < stop` で間隔は1
- `numpy.arange(start, stop, step)`
	- `start ≦ n < stop` で間隔は `step`
```
print(np.arange(3))
# [0 1 2]

print(np.arange(3, 10))
# [3 4 5 6 7 8 9]

print(np.arange(3, 10, 2))
# [3 5 7 9]

source: numpy_arange.py
```

`range()` と異なり、浮動小数点数 `float` を引数に指定できる。

```
print(np.arange(0.3, 1.0, 0.2))
# [0.3 0.5 0.7 0.9]

source: numpy_arange.py
```

負の値を指定したときの振る舞いなども `range()` と同じ。該当する値がないときは空の `ndarray` となるのも同様。

```
print(np.arange(-3, 3))
# [-3 -2 -1  0  1  2]

print(np.arange(10, 3))
# []

print(np.arange(10, 3, -2))
# [10  8  6  4]

source: numpy_arange.py
```

データ型 `dtype` は自動的に選択されるが、引数 `dtype` で指定することもできる。

```
a = np.arange(3, 10)
print(a)
# [3 4 5 6 7 8 9]

print(a.dtype)
# int64

a_float = np.arange(3, 10, dtype=float)
print(a_float)
# [3. 4. 5. 6. 7. 8. 9.]

print(a_float.dtype)
# float64

source: numpy_arange.py
```

NumPyのデータ型 `dtype` についての詳細は以下の記事を参照。

- **関連記事:**[NumPyのデータ型dtype一覧とastypeによる変換（キャスト）](https://note.nkmk.me/python-numpy-dtype-astype/)

## numpy.linspace()の使い方（要素数を指定）

### 基本的な使い方

`numpy.linspace()` も等差数列を生成するが、間隔（公差）ではなく要素数を指定する。

第一引数 `start` に最初の値、第二引数 `stop` に最後の値、第三引数 `num` に要素数を指定する。それらに応じた間隔が自動的に算出される。

```
print(np.linspace(0, 10, 3))
# [ 0.  5. 10.]

print(np.linspace(0, 10, 4))
# [ 0.          3.33333333  6.66666667 10.        ]

print(np.linspace(0, 10, 5))
# [ 0.   2.5  5.   7.5 10. ]

source: numpy_linspace.py
```

`start > stop` の場合も適切に処理してくれる。

```
print(np.linspace(10, 0, 5))
# [10.   7.5  5.   2.5  0. ]

source: numpy_linspace.py
```

生成される配列のデータ型 `dtype` は引数 `dtype` で指定できる。デフォルトでは、整数の配列が生成される場合も浮動小数点数 `float` になるので注意。

```
a = np.linspace(0, 10, 3)
print(a)
# [ 0.  5. 10.]

print(a.dtype)
# float64

a_int = np.linspace(0, 10, 3, dtype=int)
print(a_int)
# [ 0  5 10]

print(a_int.dtype)
# int64

source: numpy_linspace.py
```

### stopを含むか指定: 引数endpoint

これまでの例のように、デフォルトでは第二引数 `stop` の値も結果に含まれる。

引数 `endpoint` を `False` とすると、結果に `stop` が含まれない。

```
print(np.linspace(0, 10, 5))
# [ 0.   2.5  5.   7.5 10. ]

print(np.linspace(0, 10, 5, endpoint=False))
# [0. 2. 4. 6. 8.]

source: numpy_linspace.py
```

引数 `endpoint` と間隔 `step` の関係は以下のようになる。

- `endpoint=True` （デフォルト）
	- `step = (stop - start) / (num - 1)`
- `endpoint=False`
	- `step = (stop - start) / num`

### 間隔を取得: 引数retstep

間隔 `step` の値を取得したい場合は引数 `retstep` を `True` とする。

`(結果のndarray, step)` という要素数2のタプルが返される。

```
result = np.linspace(0, 10, 5, retstep=True)
print(result)
# (array([ 0. ,  2.5,  5. ,  7.5, 10. ]), 2.5)

print(type(result))
# <class 'tuple'>

print(result[0])
# [ 0.   2.5  5.   7.5 10. ]

print(result[1])
# 2.5

source: numpy_linspace.py
```

`step` だけ確認したい場合はインデックスで2つ目の要素を指定すればよい。

```
print(np.linspace(0, 10, 5, retstep=True)[1])
# 2.5

print(np.linspace(0, 10, 5, retstep=True, endpoint=False)[1])
# 2.0

source: numpy_linspace.py
```

## 逆順に変換

`numpy.arange()` では引数を適切に指定すれば逆順の配列を取得できるが面倒。

```
print(np.arange(3, 10, 2))
# [3 5 7 9]

print(np.arange(9, 2, -2))
# [9 7 5 3]

source: numpy_arange.py
```

スライス `[::-1]` または `numpy.flip()` を使うと、結果をそのまま逆順にできる。

- **関連記事:**[NumPy配列ndarrayのスライスによる部分配列の選択と代入](https://note.nkmk.me/python-numpy-ndarray-slice/)
- **関連記事:**[NumPy配列ndarrayを上下左右に反転するnp.flip, np.flipud, np.fliplr](https://note.nkmk.me/python-numpy-flip-flipud-fliplr/)
```
print(np.arange(3, 10, 2)[::-1])
# [9 7 5 3]

print(np.flip(np.arange(3, 10, 2)))
# [9 7 5 3]

source: numpy_arange.py
```

`numpy.linspace()` の場合は、デフォルト（ `endpoint=True` ）では第一引数 `start` と第二引数 `stop` の値を入れ替えれば簡単に逆順にできる。スライス `[::-1]` や `numpy.flip()` を使っても結果は同じ。

```
print(np.linspace(0, 10, 5))
# [ 0.   2.5  5.   7.5 10. ]

print(np.linspace(10, 0, 5))
# [10.   7.5  5.   2.5  0. ]

print(np.linspace(0, 10, 5)[::-1])
# [10.   7.5  5.   2.5  0. ]

print(np.flip(np.linspace(0, 10, 5)))
# [10.   7.5  5.   2.5  0. ]

source: numpy_linspace.py
```

`endpoint=False` だと第一引数 `start` と第二引数 `stop` を入れ替えても逆順にならない。スライス `[::-1]` や `numpy.flip()` を使うと簡単。

```
print(np.linspace(0, 10, 5, endpoint=False))
# [0. 2. 4. 6. 8.]

print(np.linspace(10, 0, 5, endpoint=False))
# [10.  8.  6.  4.  2.]

print(np.linspace(0, 10, 5, endpoint=False)[::-1])
# [8. 6. 4. 2. 0.]

print(np.flip(np.linspace(0, 10, 5, endpoint=False)))
# [8. 6. 4. 2. 0.]

source: numpy_linspace.py
```

## 多次元配列に変換

`numpy.arange()` にも `numpy.linspace()` にも形状 `shape` を指定する引数はない。2Dや3Dなど多次元配列の連番や等差数列を生成したい場合は `reshape()` メソッドで変換する。

- **関連記事:**[NumPy配列ndarrayの形状を変換するreshapeの使い方](https://note.nkmk.me/python-numpy-reshape-usage/)
```
print(np.arange(12).reshape(3, 4))
# [[ 0  1  2  3]
#  [ 4  5  6  7]
#  [ 8  9 10 11]]

print(np.arange(24).reshape(2, 3, 4))
# [[[ 0  1  2  3]
#   [ 4  5  6  7]
#   [ 8  9 10 11]]
# 
#  [[12 13 14 15]
#   [16 17 18 19]
#   [20 21 22 23]]]

source: numpy_arange.py
```
```
print(np.linspace(0, 10, 12).reshape(3, 4))
# [[ 0.          0.90909091  1.81818182  2.72727273]
#  [ 3.63636364  4.54545455  5.45454545  6.36363636]
#  [ 7.27272727  8.18181818  9.09090909 10.        ]]

print(np.linspace(0, 10, 24).reshape(2, 3, 4))
# [[[ 0.          0.43478261  0.86956522  1.30434783]
#   [ 1.73913043  2.17391304  2.60869565  3.04347826]
#   [ 3.47826087  3.91304348  4.34782609  4.7826087 ]]
# 
#  [[ 5.2173913   5.65217391  6.08695652  6.52173913]
#   [ 6.95652174  7.39130435  7.82608696  8.26086957]
#   [ 8.69565217  9.13043478  9.56521739 10.        ]]]

source: numpy_linspace.py
```