---
title: "NumPy配列ndarrayを結合（concatenate, stack, blockなど） | note.nkmk.me"
source: "https://note.nkmk.me/python-numpy-concatenate-stack-block/#numpystack"
author:
  - "[[nkmk]]"
published: 2018-12-13
created: 2025-08-26
description: "複数のNumPy配列ndarrayを結合（連結）するnumpy.concatenate()やnumpy.stack()などの関数の使い方を説明する。 numpy.concatenate()の基本的な使い方結合する配列ndarrayのリストを指定結合する軸（次元）を指定:  ..."
tags:
  - "clippings"
---
## NumPy配列ndarrayを結合（concatenate, stack, blockなど）

Modified: | Tags: [Python](https://note.nkmk.me/python/), [NumPy](https://note.nkmk.me/numpy/)

複数のNumPy配列 `ndarray` を結合（連結）する `numpy.concatenate()` や `numpy.stack()` などの関数の使い方を説明する。

`np.concatenate()` は既存の軸（次元）に沿って結合し、 `np.stack()` は新たな軸に沿って結合する。例えば、 `np.concatenate()` は二次元配列を縦・横に結合し、 `np.stack()` は二次元配列を重ねて三次元配列を生成する。

基本的には `np.concatenate()` と `np.stack()` で対応できるが、特に二次元配列に対しては `np.block()` や `np.vstack()`, `np.hstack()` を覚えておくと便利。

そのほか `np.r_[]` や `np.c_[]` といった方法もあるが、癖があるのでここでは触れない。詳細は以下の公式ドキュメントを参照。

- [numpy.r\_ — NumPy v1.26 Manual](https://numpy.org/doc/stable/reference/generated/numpy.r_.html)
- [numpy.c\_ — NumPy v1.26 Manual](https://numpy.org/doc/stable/reference/generated/numpy.c_.html)

結合ではなく分割については以下の記事を参照。

- **関連記事:**[NumPy配列ndarrayを分割（split, array\_split, hsplit, vsplit, dsplit）](https://note.nkmk.me/python-numpy-split/)

`ndarray` への要素の追加・挿入については以下の記事を参照。

- **関連記事:**[NumPy配列ndarrayの末尾に要素・行・列を追加するappend](https://note.nkmk.me/python-numpy-append/)
- **関連記事:**[NumPy配列ndarrayに要素・行・列を挿入、追加するinsertの使い方](https://note.nkmk.me/python-numpy-insert/)

本記事のサンプルコードのNumPyのバージョンは以下の通り。バージョンによって仕様が異なる可能性があるので注意。

```
import numpy as np

print(np.__version__)
# 1.26.1

source: numpy_concatenate.py
```

## numpy.concatenate()の基本的な使い方

複数の配列 `ndarray` を結合する基本的な関数が `numpy.concatenate()` 。既存の軸（次元）に沿って結合する。

- [numpy.concatenate — NumPy v1.26 Manual](https://numpy.org/doc/stable/reference/generated/numpy.concatenate.html)

### 結合する配列ndarrayのリストを指定

以下の配列を例とする。

```
a1 = np.ones((2, 3), int)
print(a1)
# [[1 1 1]
#  [1 1 1]]

a2 = np.full((2, 3), 2)
print(a2)
# [[2 2 2]
#  [2 2 2]]

source: numpy_concatenate.py
```

`numpy.ones()` や `numpy.full()` については以下の記事を参照。

- **関連記事:**[NumPy配列ndarrayを初期化（zeros, ones, fullなど）](https://note.nkmk.me/python-numpy-zeros-ones-full/)

`numpy.concatenate()` の第一引数に結合したい `ndarray` のリストやタプルを指定する。

```
print(np.concatenate([a1, a2]))
# [[1 1 1]
#  [1 1 1]
#  [2 2 2]
#  [2 2 2]]

source: numpy_concatenate.py
```

3つ以上でも同様。第一引数に指定するリストやタプルの要素数を増やせばよい。

```
a3 = np.full((2, 3), 3)
print(a3)
# [[3 3 3]
#  [3 3 3]]

print(np.concatenate([a1, a2, a3]))
# [[1 1 1]
#  [1 1 1]
#  [2 2 2]
#  [2 2 2]
#  [3 3 3]
#  [3 3 3]]

source: numpy_concatenate.py
```

### 結合する軸（次元）を指定: 引数axis

第二引数 `axis` に結合する軸（次元）を0始まりで指定する。デフォルトは `axis=0` 。

二次元配列の場合、 `axis=0` で縦に、 `axis=1` で横に結合される。

```
a1 = np.ones((2, 3), int)
print(a1)
# [[1 1 1]
#  [1 1 1]]

a2 = np.full((2, 3), 2)
print(a2)
# [[2 2 2]
#  [2 2 2]]

print(np.concatenate([a1, a2], 0))
# [[1 1 1]
#  [1 1 1]
#  [2 2 2]
#  [2 2 2]]

print(np.concatenate([a1, a2], 1))
# [[1 1 1 2 2 2]
#  [1 1 1 2 2 2]]

source: numpy_concatenate.py
```

存在しない軸（次元）を指定するとエラーとなる。新たな軸に対して結合したい、例えば二次元配列を重ねて三次元配列を生成したいというような場合は後述の `numpy.stack()` などを使う。

```
# print(np.concatenate([a1, a2], 2))
# AxisError: axis 2 is out of bounds for array of dimension 2

source: numpy_concatenate.py
```

結合軸以外の軸のサイズが一致していないとエラーとなる。足りない部分が `NaN` で埋められたりはしない。

```
a2_ = np.full((3, 3), 2)
print(a2_)
# [[2 2 2]
#  [2 2 2]
#  [2 2 2]]

print(np.concatenate([a1, a2_], 0))
# [[1 1 1]
#  [1 1 1]
#  [2 2 2]
#  [2 2 2]
#  [2 2 2]]

# print(np.concatenate([a1, a2_], 1))
# ValueError: all the input array dimensions except for the concatenation axis must match exactly, but along dimension 0, the array at index 0 has size 2 and the array at index 1 has size 3

source: numpy_concatenate.py
```

二次元以外の配列でも同様。第二引数 `axis` に軸（次元）を指定する。元の配列に存在しない軸では結合できない。

```
a1 = np.ones(3, int)
print(a1)
# [1 1 1]

a2 = np.full(3, 2)
print(a2)
# [2 2 2]

print(np.concatenate([a1, a2], 0))
# [1 1 1 2 2 2]

# print(np.concatenate([a1, a2], 1))
# AxisError: axis 1 is out of bounds for array of dimension 1

source: numpy_concatenate.py
```

また、元の配列の次元数が異なっている場合はエラーとなる。

```
a1 = np.ones((2, 3), int)
print(a1)
# [[1 1 1]
#  [1 1 1]]

a2 = np.full(3, 2)
print(a2)
# [2 2 2]

# print(np.concatenate([a1, a2], 0))
# ValueError: all the input arrays must have same number of dimensions, but the array at index 0 has 2 dimension(s) and the array at index 1 has 1 dimension(s)

source: numpy_concatenate.py
```

後述の `numpy.block()` や `numpy.vstack()` を使うと、一次元配列と二次元配列の縦結合が可能。

## numpy.stack()で新たな軸（次元）に沿って結合

既存の軸（次元）に沿って結合する `numpy.concatenate()` に対して、 `numpy.stack()` は新たな軸に沿って結合する。新たな軸に沿って配列を積み重ねる（=stackする）イメージ。

- [numpy.stack — NumPy v1.26 Manual](https://numpy.org/doc/stable/reference/generated/numpy.stack.html)

`numpy.concatenate()` と同様に、第一引数に連結する配列 `ndarray` のリスト、第二引数 `axis` に結合する軸（次元）を0始まりで指定する。ただし、 `axis` には結合された配列にとっての軸を指定する。

### 一次元配列の例

一次元配列を例とする。

```
a1 = np.ones(3, int)
print(a1)
# [1 1 1]

a2 = np.full(3, 2)
print(a2)
# [2 2 2]

source: numpy_stack.py
```

`numpy.stack()` は新たな軸で結合するので、結果の配列は一次元多い二次元配列となる。

第二引数 `axis` のデフォルトは `axis=0` 。結果の配列に存在しない軸を指定するとエラーとなる。

```
print(np.stack([a1, a2]))
# [[1 1 1]
#  [2 2 2]]

print(np.stack([a1, a2], 0))
# [[1 1 1]
#  [2 2 2]]

print(np.stack([a1, a2], 1))
# [[1 2]
#  [1 2]
#  [1 2]]

# print(np.stack([a1, a2], 2))
# AxisError: axis 2 is out of bounds for array of dimension 2

source: numpy_stack.py
```

`axis=-1` とすると最後の軸が指定される。この場合は `axis=1` と等価。

```
print(np.stack([a1, a2], -1))
# [[1 2]
#  [1 2]
#  [1 2]]

source: numpy_stack.py
```

入力配列の形状 `shape` が一致していないとエラー。

```
a2_ = np.full(4, 2)
print(a2_)
# [2 2 2 2]

# print(np.stack([a1, a2_]))
# ValueError: all input arrays must have the same shape

source: numpy_stack.py
```

### 二次元配列の例

続いて二次元配列を例とする。

```
a1 = np.ones((3, 4), int)
print(a1)
# [[1 1 1 1]
#  [1 1 1 1]
#  [1 1 1 1]]

a2 = np.full((3, 4), 2)
print(a2)
# [[2 2 2 2]
#  [2 2 2 2]
#  [2 2 2 2]]

source: numpy_stack.py
```

考え方は一次元配列と同様。結果は二次元 + 一次元で三次元配列となる。結果の形状 `shape` を意識すると理解しやすい。

```
print(np.stack([a1, a2]))
# [[[1 1 1 1]
#   [1 1 1 1]
#   [1 1 1 1]]
# 
#  [[2 2 2 2]
#   [2 2 2 2]
#   [2 2 2 2]]]

print(np.stack([a1, a2], 0))
# [[[1 1 1 1]
#   [1 1 1 1]
#   [1 1 1 1]]
# 
#  [[2 2 2 2]
#   [2 2 2 2]
#   [2 2 2 2]]]

print(np.stack([a1, a2], 0).shape)
# (2, 3, 4)

source: numpy_stack.py
```
```
print(np.stack([a1, a2], 1))
# [[[1 1 1 1]
#   [2 2 2 2]]
# 
#  [[1 1 1 1]
#   [2 2 2 2]]
# 
#  [[1 1 1 1]
#   [2 2 2 2]]]

print(np.stack([a1, a2], 1).shape)
# (3, 2, 4)

print(np.stack([a1, a2], 1)[:, 0, :])
# [[1 1 1 1]
#  [1 1 1 1]
#  [1 1 1 1]]

print(np.stack([a1, a2], 1)[:, 1, :])
# [[2 2 2 2]
#  [2 2 2 2]
#  [2 2 2 2]]

source: numpy_stack.py
```
```
print(np.stack([a1, a2], 2))
# [[[1 2]
#   [1 2]
#   [1 2]
#   [1 2]]
# 
#  [[1 2]
#   [1 2]
#   [1 2]
#   [1 2]]
# 
#  [[1 2]
#   [1 2]
#   [1 2]
#   [1 2]]]

print(np.stack([a1, a2], 2).shape)
# (3, 4, 2)

print(np.stack([a1, a2], 2)[:, :, 0])
# [[1 1 1 1]
#  [1 1 1 1]
#  [1 1 1 1]]

print(np.stack([a1, a2], 2)[:, :, 1])
# [[2 2 2 2]
#  [2 2 2 2]
#  [2 2 2 2]]

source: numpy_stack.py
```

結果の配列に存在しない軸を指定するとエラーとなる。

```
# print(np.stack([a1, a2], 3))
# AxisError: axis 3 is out of bounds for array of dimension 3

source: numpy_stack.py
```

`axis=-1` とすると最後の軸が指定される。この場合は `axis=2` と等価。

```
print(np.stack([a1, a2], -1))
# [[[1 2]
#   [1 2]
#   [1 2]
#   [1 2]]
# 
#  [[1 2]
#   [1 2]
#   [1 2]
#   [1 2]]
# 
#  [[1 2]
#   [1 2]
#   [1 2]
#   [1 2]]]

print(np.stack([a1, a2], -1).shape)
# (3, 4, 2)

source: numpy_stack.py
```

入力配列の形状 `shape` が一致していないとエラー。

```
a2_ = np.full((2, 3), 2)
print(a2_)
# [[2 2 2]
#  [2 2 2]]

# print(np.stack([a1, a2_]))
# ValueError: all input arrays must have the same shape

source: numpy_stack.py
```

三次元以上のさらに多次元の場合も同様の考え方。

## numpy.block()で配置を指定して結合

より直感的に複数の配列を結合できるのが `numpy.block()` 。

- [numpy.block — NumPy v1.26 Manual](https://numpy.org/doc/stable/reference/generated/numpy.block.html)

元の配列をどのように配置するかを示したリストを引数に指定する。

配列を横に並べたリストを指定するとその順番で横に結合される。

```
a1 = np.ones((2, 3), int)
print(a1)
# [[1 1 1]
#  [1 1 1]]

a2 = np.full((2, 3), 2)
print(a2)
# [[2 2 2]
#  [2 2 2]]

print(np.block([a1, a2]))
# [[1 1 1 2 2 2]
#  [1 1 1 2 2 2]]

source: numpy_block.py
```

二次元のリスト（ネストしたリスト）で縦方向の配置の指定も可能。

```
print(np.block([[a1], [a2]]))
# [[1 1 1]
#  [1 1 1]
#  [2 2 2]
#  [2 2 2]]

print(np.block([[a1, a2], [a2, a1]]))
# [[1 1 1 2 2 2]
#  [1 1 1 2 2 2]
#  [2 2 2 1 1 1]
#  [2 2 2 1 1 1]]

source: numpy_block.py
```

引数を三次元にすれば結果も三次元になる。

```
print(np.block([[[a1]], [[a2]]]))
# [[[1 1 1]
#   [1 1 1]]
# 
#  [[2 2 2]
#   [2 2 2]]]

print(np.block([[[a1]], [[a2]]]).shape)
# (2, 2, 3)

source: numpy_block.py
```

以下のように一次元配列と二次元配列の結合も可能。配列が一つだけでも `[a3]` のようにしてネストの深さを揃えないとエラーとなるので注意。

```
a3 = np.full(6, 3)
print(a3)
# [3 3 3 3 3 3]

print(np.block([[a1, a2], [a3]]))
# [[1 1 1 2 2 2]
#  [1 1 1 2 2 2]
#  [3 3 3 3 3 3]]

# print(np.block([[a1, a2], a3]))
# ValueError: List depths are mismatched. First element was at depth 2, but there is an element at depth 1 (arrays[1])

source: numpy_block.py
```

要素数に過不足がある場合もエラーとなる。

```
# print(np.block([[a1, a2, a3]]))
# ValueError: all the input array dimensions except for the concatenation axis must match exactly, but along dimension 0, the array at index 0 has size 2 and the array at index 2 has size 1

source: numpy_block.py
```

## numpy.vstack()で縦に結合

`numpy.vstack()` は配列を縦（vertical）に結合する関数。

- [numpy.vstack — NumPy v1.26 Manual](https://numpy.org/doc/stable/reference/generated/numpy.vstack.html)

基本的には `axis=0` とした `numpy.concatenate()` と同等。

```
a1 = np.ones((2, 3), int)
print(a1)
# [[1 1 1]
#  [1 1 1]]

a2 = np.full((2, 3), 2)
print(a2)
# [[2 2 2]
#  [2 2 2]]

print(np.vstack([a1, a2]))
# [[1 1 1]
#  [1 1 1]
#  [2 2 2]
#  [2 2 2]]

print(np.concatenate([a1, a2], 0))
# [[1 1 1]
#  [1 1 1]
#  [2 2 2]
#  [2 2 2]]

source: numpy_vstack.py
```

ただし、 `numpy.vstack()` では一次元の入力配列が二次元に拡張される（形状 `(N, )` が `(1, N)` に変換される）ため、一次元配列を結合して二次元配列にしたり、一次元配列と二次元配列を結合したりできる。

- [numpy/numpy/core/shape\_base.py at v1.26.1 · numpy/numpy](https://github.com/numpy/numpy/blob/v1.26.1/numpy/core/shape_base.py#L220-L289)
```
a1 = np.ones(3, int)
print(a1)
# [1 1 1]

a2 = np.full(3, 2)
print(a2)
# [2 2 2]

print(np.vstack([a1, a2]))
# [[1 1 1]
#  [2 2 2]]

print(np.concatenate([a1.reshape(1, -1), a2.reshape(1, -1)], 0))
# [[1 1 1]
#  [2 2 2]]

source: numpy_vstack.py
```
```
a2_2d = np.full((2, 3), 2)
print(a2_2d)
# [[2 2 2]
#  [2 2 2]]

print(np.vstack([a1, a2_2d]))
# [[1 1 1]
#  [2 2 2]
#  [2 2 2]]

print(np.concatenate([a1.reshape(1, -1), a2_2d], 0))
# [[1 1 1]
#  [2 2 2]
#  [2 2 2]]

source: numpy_vstack.py
```

## numpy.hstack()で横に結合

`numpy.hstack()` は配列を横（horizontal）に結合する関数。

- [numpy.hstack — NumPy v1.26 Manual](https://numpy.org/doc/stable/reference/generated/numpy.hstack.html)

基本的には `axis=1` とした `numpy.concatenate()` と同等。

```
a1 = np.ones((2, 3), int)
print(a1)
# [[1 1 1]
#  [1 1 1]]

a2 = np.full((2, 3), 2)
print(a2)
# [[2 2 2]
#  [2 2 2]]

print(np.hstack([a1, a2]))
# [[1 1 1 2 2 2]
#  [1 1 1 2 2 2]]

print(np.concatenate([a1, a2], 1))
# [[1 1 1 2 2 2]
#  [1 1 1 2 2 2]]

source: numpy_hstack.py
```

ただし、一次元配列に対しては `axis=0` の `numpy.concatenate()` が呼ばれる。

- [numpy/numpy/core/shape\_base.py at v1.26.1 · numpy/numpy](https://github.com/numpy/numpy/blob/v1.26.1/numpy/core/shape_base.py#L293-L359)
```
a1 = np.ones(3, int)
print(a1)
# [1 1 1]

a2 = np.full(3, 2)
print(a2)
# [2 2 2]

print(np.hstack([a1, a2]))
# [1 1 1 2 2 2]

print(np.concatenate([a1, a2], 0))
# [1 1 1 2 2 2]

source: numpy_hstack.py
```

`numpy.vstack()` のように一次元配列が二次元配列に拡張されるわけではないので、一次元配列と二次元配列は結合できない。

```
a1 = np.ones((2, 3), int)
print(a1)
# [[1 1 1]
#  [1 1 1]]

a2 = np.full(2, 2)
print(a2)
# [2 2]

# print(np.hstack([a1, a2]))
# ValueError: all the input arrays must have same number of dimensions, but the array at index 0 has 2 dimension(s) and the array at index 1 has 1 dimension(s)

source: numpy_hstack.py
```

## numpy.dstack()で深さ方向に結合

`numpy.dstack()` は配列を深さ（depth）方向に結合する関数。

- [numpy.dstack — NumPy v1.26 Manual](https://numpy.org/doc/stable/reference/generated/numpy.dstack.html)

基本的には `axis=2` とした `numpy.concatenate()` と同等だが、二次元以下の配列は三次元に拡張されてから結合される。

- [numpy/numpy/lib/shape\_base.py at v1.26.1 · numpy/numpy](https://github.com/numpy/numpy/blob/v1.26.1/numpy/lib/shape_base.py#L660-L715)

二次元配列は形状 `(M, N)` が `(M, N, 1)` に変換される。

```
a1 = np.ones((3, 4), int)
print(a1)
# [[1 1 1 1]
#  [1 1 1 1]
#  [1 1 1 1]]

a2 = np.full((3, 4), 2)
print(a2)
# [[2 2 2 2]
#  [2 2 2 2]
#  [2 2 2 2]]

print(np.dstack([a1, a2]))
# [[[1 2]
#   [1 2]
#   [1 2]
#   [1 2]]
# 
#  [[1 2]
#   [1 2]
#   [1 2]
#   [1 2]]
# 
#  [[1 2]
#   [1 2]
#   [1 2]
#   [1 2]]]

print(np.dstack([a1, a2]).shape)
# (3, 4, 2)

print(np.dstack([a1, a2])[:, :, 0])
# [[1 1 1 1]
#  [1 1 1 1]
#  [1 1 1 1]]

print(np.dstack([a1, a2])[:, :, 1])
# [[2 2 2 2]
#  [2 2 2 2]
#  [2 2 2 2]]

source: numpy_dstack.py
```

以下の `numpy.concatenate()` と同等。

```
print(np.concatenate([a1.reshape(3, 4, 1), a2.reshape(3, 4, 1)], 2))
# [[[1 2]
#   [1 2]
#   [1 2]
#   [1 2]]
# 
#  [[1 2]
#   [1 2]
#   [1 2]
#   [1 2]]
# 
#  [[1 2]
#   [1 2]
#   [1 2]
#   [1 2]]]

source: numpy_dstack.py
```

一次元配列も三次元に拡張される。形状 `(N, )` が `(1, N, 1)` に変換される。

```
a1 = np.ones(3, int)
print(a1)
# [1 1 1]

a2 = np.full(3, 2)
print(a2)
# [2 2 2]

print(np.dstack([a1, a2]))
# [[[1 2]
#   [1 2]
#   [1 2]]]

print(np.dstack([a1, a2]).shape)
# (1, 3, 2)

print(np.dstack([a1, a2])[:, :, 0])
# [[1 1 1]]

print(np.dstack([a1, a2])[:, :, 1])
# [[2 2 2]]

source: numpy_dstack.py
```

以下の `numpy.concatenate()` と同等。

```
print(np.concatenate([a1.reshape(1, -1, 1), a2.reshape(1, -1, 1)], 2))
# [[[1 2]
#   [1 2]
#   [1 2]]]

source: numpy_dstack.py
```