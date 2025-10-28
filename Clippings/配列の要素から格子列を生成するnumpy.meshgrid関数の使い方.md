---
title: "配列の要素から格子列を生成するnumpy.meshgrid関数の使い方"
source: "https://deepage.net/features/numpy-meshgrid.html"
author:
  - "[[DeepAge]]"
published: 2017-07-09
created: 2025-08-26
description: "NumPyの関数であるnp.meshgridはmatplotlibでグラフを描画する際、格子点を作りたいときや組み合わせを生成したいときに便利な機能です。本記事では、np.meshgridの使い方について解説しました。"
tags:
  - "clippings"
---
`np.meshgrid` 関数は、x, y, …の各座標の要素列から格子座標を作成するために使います。

例えば、xが0~4、yが0~4の36点の格子点の各座標の要素列を求めたいとしましょう。Pythonコードを使用すると、以下のように求めることもできます。

```python
>> xx = np.array([[x for x in range(5)] for _ in range(5)])
>> xx
array([[0, 1, 2, 3, 4],
       [0, 1, 2, 3, 4],
       [0, 1, 2, 3, 4],
       [0, 1, 2, 3, 4],
       [0, 1, 2, 3, 4]])
>> yy = np.array([[y for _ in range(5)] for y in range(5)])
>> yy
array([[0, 0, 0, 0, 0],
       [1, 1, 1, 1, 1],
       [2, 2, 2, 2, 2],
       [3, 3, 3, 3, 3],
       [4, 4, 4, 4, 4]])
```

`np.meshgrid` を使用すると、各軸の要素数に応じて柔軟にグリッドを生成することができます。覚えておくと可視化や組み合わせの要素列を作成するときに便利なので、是非この機会に使い方を覚えてみてください。

## meshgrid

まずは、 `meshgrid` 関数のAPIドキュメントから見ていきましょう。

**numpy.meshgrid(x1, x2,…, xn, indexing=’xy’,sparse=False,copy=True)**

#### params:

| パラメータ名 | 型 | 概要 |
| --- | --- | --- |
| `x1, x2, ...,xn` | 1次元配列 | それぞれの軸における格子点を求めたい格子の座標を指定します。 |
| `indexing` | {‘xy’ or ‘ij’} | (省略可能)初期値’xy’   生成される配列の形状(shape)をどのように設定するかを指定します。(あとで詳しく解説します) |
| `sparse` | bool   (True or False) | (省略可能)初期値False   `True` にするとスパースグリッドの配列を返してメモリを節約します。 |
| `copy` | bool | (省略可能)初期値True   Falseにすると、メモリを節約するために、元の配列のビューを返します。 |

#### returns:

指定した各軸の要素列から各軸方向の格子列を返します。

`np.meshgrid` の操作は下図のようなイメージになります。x座標とy座標の要素の入った配列を指定すると、各軸のグリッドの要素を返します。

![メッシュグリッドのイメージ図](https://deepage.net/img/numpy/meshgrid/meshgrid-explanation.jpg)

### 格子点の作成

実際のコードを見ながら使い方を見ていきましょう。

```python
In [1]: import numpy as np

In [2]: a = np.array([0, 1, 2])

In [3]: b = np.array([0, 4]) # まずは1次元配列を2つ作る。

In [4]: aa, bb = np.meshgrid(a,b) # 基本的な操作から。

In [5]: aa
Out[5]:
array([[0, 1, 2],
       [0, 1, 2]])

In [6]: bb
Out[6]:
array([[0, 0, 0],
       [4, 4, 4]])

In [7]: c = np.array([0, 9]) # 新たにもう１つ追加する。

In [8]: aaa, bbb, ccc = np.meshgrid(a,b,c) # 3つの軸を持つ配列が３つ生成される。

In [9]: aaa
Out[9]:
array([[[0, 0],
        [1, 1],
        [2, 2]],

       [[0, 0],
        [1, 1],
        [2, 2]]])

In [10]: bbb
Out[10]:
array([[[0, 0],
        [0, 0],
        [0, 0]],

       [[4, 4],
        [4, 4],
        [4, 4]]])

In [11]: ccc
Out[11]:
array([[[0, 9],
        [0, 9],
        [0, 9]],

       [[0, 9],
        [0, 9],
        [0, 9]]])
```

### indexing引数を指定

`indexing` 引数を指定してみます。デフォルトは座標軸順の `xy` ですが、 `ij` にすると行列の順序でグリッドが生成されます。

```python
In [12]: aa2, bb2 = np.meshgrid(a, b, indexing='xy') # まずはデフォルトの値である'xy'から。

In [13]: aa2
Out[13]:
array([[0, 1, 2],
       [0, 1, 2]])

In [14]: bb2
Out[14]:
array([[0, 0, 0],
       [4, 4, 4]])

In [15]: aa3, bb3 = np.meshgrid(a, b, indexing = 'ij') # 'ij'にしてみる。

In [16]: aa3
Out[16]:
array([[0, 0],
       [1, 1],
       [2, 2]])

In [17]: bb3
Out[17]:
array([[0, 4],
       [0, 4],
       [0, 4]])

In [18]: aaa1, bbb1, ccc1 = np.meshgrid(a, b, c, indexing = 'xy') # ３次元の場合でも同様のことを確かめてみる。

In [19]: aaa1
Out[19]:
array([[[0, 0],
        [1, 1],
        [2, 2]],

       [[0, 0],
        [1, 1],
        [2, 2]]])

In [20]: bbb1
Out[20]:
array([[[0, 0],
        [0, 0],
        [0, 0]],

       [[4, 4],
        [4, 4],
        [4, 4]]])

In [21]: ccc1
Out[21]:
array([[[0, 9],
        [0, 9],
        [0, 9]],

       [[0, 9],
        [0, 9],
        [0, 9]]])

In [22]: aaa2, bbb2, ccc2 = np.meshgrid(a, b, c, indexing='ij') # 'ij'にする。

In [23]: aaa2
Out[23]:
array([[[0, 0],
        [0, 0]],

       [[1, 1],
        [1, 1]],

       [[2, 2],
        [2, 2]]])

In [24]: bbb2
Out[24]:
array([[[0, 0],
        [4, 4]],

       [[0, 0],
        [4, 4]],

       [[0, 0],
        [4, 4]]])

In [25]: ccc2
Out[25]:
array([[[0, 9],
        [0, 9]],

       [[0, 9],
        [0, 9]],

       [[0, 9],
        [0, 9]]])
```

#### スパースグリッドでメモリを節約

次に、 `sparse` を指定します。 `True` にすると、スパースグリッドを生成して、メモリを節約します。

```python
In [26]: av, bv = np.meshgrid(a, b, sparse = True) # ベクトルが作られるのでav, bvのようにvの添え字をつけるのが一般的。  

In [27]: av
Out[27]: array([[0, 1, 2]])

In [28]: bv
Out[28]:
array([[0],
       [4]])

In [29]: aav, bbv, ccv = np.meshgrid(a, b, c, sparse=True) # ３次元でも同様のことができる。

In [30]: aav
Out[30]:
array([[[0],
        [1],
        [2]]])

In [31]: bbv
Out[31]:
array([[[0]],

       [[4]]])

In [32]: ccv
Out[32]: array([[[0, 9]]])
```

## グラフでの利用

この `meshgrid` 関数が最もよく使われるのは [matplotlib](https://deepage.net/features/numpy-matplotlib.html) を利用したグラフ描画のときでしょう。この関数の機能を使ってグラフを描いていきます。

次のようなコードをファイルにして実行すると、簡単に格子点を作ることができます。

```python
import numpy as np
import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D # 3次元グラフをかくときに使います。

x = np.linspace(-2, 2, 100)
y = np.linspace(-2, 2, 100)

xx, yy = np.meshgrid(x, y) # 格子点となるx,y座標を作成。  
ret = np.sin(xx**2+yy**2)
def plot1(x, y, ret):
    plt.gca().set_aspect('equal', adjustable='box') # グラフの縦横の比をそろえるコマンド   
    plt.contourf(x, y, ret>0, cmap=plt.cm.bone) # 条件(ret>0)を満たす部分を白、満たさない部分を黒とする。
    plt.show()

def plot2(xx, yy, ret):
    fig = plt.figure()
    ax = fig.add_subplot(111, projection='3d')
    ax.plot_wireframe(xx, yy, ret)
    ax.set_xlim([-2, 2])
    ax.set_ylim([-2, 2])
    ax.set_zlim([ret.min(), ret.max()])
    plt.show()
```

`plot1(x, y, ret)` を実行するとが0より大きいかどうかで色分けされた2Dグラフがプロットされます。

![2Dグラフ](https://deepage.net/img/numpy/meshgrid/graph1.png)

`plot2(xx, yy, ret)` を実行するとが3Dグラフでプロットされます。

![3Dグラフ](https://deepage.net/img/numpy/meshgrid/graph2.png)

このように、非常に多くの格子点でも数行のコードで実現することができます。