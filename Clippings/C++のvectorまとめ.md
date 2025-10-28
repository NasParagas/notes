---
title: "C++のvectorまとめ"
source: "https://qiita.com/ysuzuki19/items/df872d91c9c89cc31aee"
author:
  - "[[ysuzuki19]]"
published: 2019-05-02
created: 2025-08-21
description: "はじめに C++でプログラミングをしていて，配列の代わりとしてvectorを普段から使用しています．非常に便利なので，vectorの基本的な使い方や個人的に考える利点についてについてまとめます．他にも利点など思いつきましたら，教えていただけると嬉しいです！ vector..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から3年以上が経過しています。

## はじめに

C++でプログラミングをしていて，配列の代わりとして `vector` を普段から使用しています．非常に便利なので， `vector` の基本的な使い方や個人的に考える利点についてについてまとめます．他にも利点など思いつきましたら，教えていただけると嬉しいです！

## vectorとは

[cppref](https://ja.cppreference.com/w/cpp/container/vector) によると，

> 1. std::vector は動的なサイズの配列をカプセル化するシーケンスコンテナです。

1. std::pmr::vector は多相アロケータを使用するエイリアステンプレートです。

となっている．

私自身， `vector` らしい書き方をしているかはわからないが，配列の代わりとして非常に便利である．使っていて配列より便利と感じる点は，

- var.size()で配列要素数を参照できるため，関数に渡しやすい
- var.size()でインデックスの上限を指定するとセグエラ起こさない
- var.resize(n)で要素数を変更できる
- var.push\_back(d)で1つづつ要素を追加できる
- ＝でコピーできる
- 値渡しのように関数の引数にできる（アドレス渡し・参照渡しも可）
- 関数の戻り値にできる
- `algorithm` でソートできる
- `erase` で指定要素のみ削除できる
- 1行で `string` にargvを格納できる

である．これ以上にも便利な点はあるのだろうが，これだけでも `vector` を使う価値はありそうだ．

## インクルード

`vector` を使用するためには，

include

```cpp
#include <vector>
```

としてインクルードするとよい．

## 宣言および初期化

## 1次元配列

1次元配列の `vector` の宣言は以下． `Type` 部には `int` ， `double` などの基本的な型のみならず，自分で作成した `class` なども使用できる．  
ここで， `n` は要素数を表し， `d` は初期化する値を表すものとする．

declaration\_1d

```cpp
vector<Type> v;
vector<Type> v();
vector<Type> v(n);
vector<Type> v(n, d);
```

## 2次元配列

2次元配列の `vector` の宣言は以下．  
ここで， `m` は `n` と同様，要素数を表すものとする．  
`g++` のバージョンによっては `>>` は `> >` と間に半角スペースがないといけないこともあるので，注意が必要である．

declaration\_2d

```cpp
vector<vector<Type>> vv;
vector<vector<Type>> vv();
vector<vector<Type>> vv(n);
vector<vector<Type>> vv(n, vector<Type>(m));
vector<vector<Type>> vv(n, vector<Type>(m, d));
```

## 3次元配列

ここまできたら必要ないかもしれないが，3次元配列の `vector` の宣言は以下．  
ここで， `l` は `n,m` と同様，要素数を表すものとする．

declaration\_3d

```cpp
vector<vector<vector<Type>>> vvv;
vector<vector<vector<Type>>> vvv();
vector<vector<vector<Type>>> vvv(n, vector<vector<Type>>(m, vector<Type>(l)));
vector<vector<vector<Type>>> vvv(n, vector<vector<Type>>(m, vector<Type>(l, d)));
```

## 要素へアクセス

`vector` では，通常の配列と同じ書き方で要素へアクセスすることができる．

## 1次元配列

例として， `v[]` の `i` 番目に `d` を代入してそれを出力する場合は以下となる．

element\_access

```cpp
v[i] = d;
cout << v[i] << endl;
```

## 2次元配列

例として， `v[][]` の `i,j` 番目に `d` を代入してそれを出力する場合は以下となる．

element\_access

```cpp
v[i][j] = d;
cout << v[i][j] << endl;
```

## 3次元配列

例として， `v[][][]` の `i,j,k` 番目に `d` を代入してそれを出力する場合は以下となる．

element\_access

```cpp
v[i][j][k] = d;
cout << v[i][j][k] << endl;
```

## 要素数の変更

`vector` では，配列の要素数を変更することができる．

## 1次元配列

配列 `v[]` の要素数を `n` 個に変更する場合は以下．

vector\_resize\_1

```cpp
v.resize(n);
```

## 2次元配列

配列 `vv[][]` の要素数を `n x n` に変更する場合は以下．

vector\_resize\_2d\_1

```cpp
vv.resize(n);
for(size_t i=0; i<n; i++){
  vv[i].resize(n);
}
```

vector\_resize\_2d\_2

```cpp
vv.resize(n, vector<Type>(n));
```

## 末尾に要素を追加

## 1次元配列

`v[]` の末尾に `d` を追加する場合は以下．

vector\_append\_data\_1d

```cpp
v.push_back(d);
```

## 2次元配列

`vv[][]` の `i` 番目の末尾に `d` を追加する場合は以下．

vector\_apped\_data\_2d

```cpp
vv[i].push_back(d);
```

## 配列のコピー

## 同じ配列を作成

`vector` 配列をコピーする方法はいくつかある．ここでは， `v1[]` を `v2[]` にコピーする方法をいくつか紹介する．

vector\_copy\_1

```cpp
v2 = v1
```

vector\_copy\_2

```cpp
v2.resize(v1.size());
for(size_t i=0; i<v1.size(); i++){
  v2[i] = v1[i];
}
```

vector\_copy\_3

```cpp
copy(v1.begin(), v1.end(), v2.begin());
```

## 末尾に追加

`v1[]` を `v2[]` の末尾に追加する方法は以下．3つめの方が完結なので嬉しい．

vector\_append\_to\_end\_1

```cpp
int sizeTemp = v2.size();
v2.resize(sizeTemp+v1.size());
for(size_t i=0; i<v1.size(); i++){
  v2[i+sizeTemp] = v1[i];
}
```

vector\_append\_to\_end\_2

```cpp
for(size_t i=0; i<v1.size(); i++){
  v2.push_back(v1[i]);
}
```

vector\_append\_to\_end\_3

```cpp
copy(v1.begin(), v1.end(), back_inserter(v2));
```

## 指定範囲をコピー

`v1[]` の `a~b` を `v2[]` にコピーする方法は以下．後者の方が完結なので嬉しい．

vector\_specified\_range\_copy\_1

```cpp
v2.resize(b-a);
for(size_t i=0; i<v2.size(); i++){
  v2[i] = v1[a+i];
}
```

vector\_specified\_range\_copy\_2

```cpp
copy(v1.begin()+a, v1.begin()+b, v2.begin());
```

## 関数の引数にする

`vector` は簡単に関数の引数にすることができる．また， `vector` のメンバ関数から要素数を参照することができるため，関数内で簡単にループ数を指定できる．ここでは， `総和を求めるプログラム` と `配列をターミナルで視覚化するプログラム` の２つを例として挙げる．ただし，特に理由がない限りは，処理時間などの理由から参照渡しを用いた方が良い．加えて，参照渡しをする際には変数を変更するつもりがなければ `const` をつけた方が良い．

## 配列の総和を求めるプログラムの例

配列の総和を求める場合は以下のような関数を定義すると良い．  
値渡しと参照渡しそれぞれについての関数を記述したのだが，これらについて，データ数 `N = 10000000` でこれらの処理時間を `time` コマンドで計測したところ， `0.105s` の差で参照渡しの方が高速であった．

関数の呼び出しに関しては，どちらも

```text
sumVector(v);
```

と，同じである．

### 値渡し

sum\_function\_example

```cpp
Type sumVector(vector<Type> v){
  Type sum = 0;
  for(size_t i=0; i<v.size(); i++){
    sum += v[i];
  }
  return sum;
}
```

### 参照渡し

sum\_function\_example

```cpp
Type sumVector(const vector<Type>& v){
  Type sum = 0;
  for(size_t i=0; i<v.size(); i++){
    sum += v[i];
  }
  return sum;
}
```

## 配列をターミナルで視覚化するプログラムの例

`vector` では`.size()` で要素数数を参照できるため，以下のように関数内で要素数を用いた処理が可能である．この時，桁が違う数も扱う際に揃えたい場合は， `cout << setw(n) << v[i]` のようにして桁数を指定してしまえば良い．

## 値渡し

view\_function\_example

```cpp
void view(vector<Type> v){
  for(size_t i=0; i<v.size(); i++){
    cout << v[i] << " ";
  }
  cout << endl;
}
void view(vector<vector<Type>> vv){
  for(size_t i=0; i<vv.size(); i++){
    for(size_t j=0; j<vv[i].size(); j++){
      cout << vv[i][j] << " ";
    }
    cout << endl;
  }
}
```

## 参照渡し

view\_function\_example

```cpp
void view(const vector<Type>& v){
  for(size_t i=0; i<v.size(); i++){
    cout << v[i] << " ";
  }
  cout << endl;
}
void view(const vector<vector<Type>>& vv){
  for(size_t i=0; i<vv.size(); i++){
    for(size_t j=0; j<vv[i].size(); j++){
      cout << vv[i][j] << " ";
    }
    cout << endl;
  }
}
```

## 関数の戻り値にする

`vector` の便利な点としては，簡単に戻り値にできる点である．  
`int v[n];`のように宣言した配列であれば，戻り値にできないのでアドレス渡しで余計に配列を引数にする必要があるのだが， `vector` を使うと簡潔なプログラムを記述することができる．  
しかし，便利であるのに対して，戻り値にする際にコピーする時間がかかってしまうため，どちらを使用するのかは使用要素数などで変えた方が良さそうである． `vector` で余計に引数にすることもできる．

ここでは， `ベクトルのスカラー倍` をする関数を例としてあげる．

以下の２つの関数の処理時間の差は `配列の要素数N=10000000` の時に `0.031s` と後者の方が高速であった．

## 戻り値にvector

scalar\_multiplication\_returnVector

```cpp
vector<int> scalar_multiplication(const vector<int>& v, int s){
  vector<int> mul(v.size());
  for(size_t i=0; i<mul.size(); i++){
    mul[i] = v[i]*s;
  }
  return mul;
}
```

## 計算結果を出力するvectorも引数にする

scalar\_multiplication

```cpp
void scalar_multiplication(const vector<int>& v, int s, vector<int>& res){
  res.resize(v.size());
  for(size_t i=0; i<res.size(); i++){
    res[i] = v[i]*s;
  }
}
```

## ソート

## 昇順ソート

ascending\_sort

```cpp
sort(v.begin(), v.end());
```

## 降順ソート

descending\_sort

```cpp
sort(v.begin(), v.end(), greater<int>());
```

## 指定要素の削除

`vector` では`.erase()` を使用することで任意の要素を削除することができる．  
先頭の要素を削除する場合は以下．

vector\_erase\_begin

```cpp
v.erase(v.begin());
```

末尾を削除する場合は以下．

vector\_erase\_end

```cpp
v.erase(v.end() - 1);
```

先頭から `i` 番目の要素を削除する場合は以下．

vector\_erase\_begin5

```cpp
v.erase(v.begin()+i);
```

## argvをvectorに格納する方法

コマンドライン引数を取得するために， `int argc` や `char **argv` を使う．しかし，個人的には `char` は使いづらいので，これを一発で `vector<string>` に格納するのが好きである．書式は以下．

argv\_to\_args

```cpp
#include <vector>
#include <string>

main(int argc, char **argv){
  vector<string> args(argv, argv+argc);
}
```

## Range-Based-for-loopの例

コメントで，「ループはインデックスを用いたループよりもRange-based-loopを用いた方が良い」とあったので，上記の `view()` 関数のRange-Basedのバージョンを記載する．

view\_function\_range\_based

```cpp
void view(const vector<Type>& v){
  for(const auto& e : v){
    cout << e << " ";
  }
  cout << endl;
}
void view(const vector<vector<int>>& vv){
  for(const auto& v : vv){
    for(const auto& e : v){
      cout << e << " ";
    }
    cout << endl;
  }
}
```

参考は [cpprefjpのこのページ](https://cpprefjp.github.io/lang/cpp11/range_based_for.html) である． `e` は `element` の頭文字と考えられる．

## おわりに

まだまだプログラミングをしていくので，他にも便利なことがあったら追記していくつもりです．  
何かオススメがあったらコメントで教えていただけると嬉しいです．

[13](https://qiita.com/ysuzuki19/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fysuzuki19%2Fitems%2Fdf872d91c9c89cc31aee&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fysuzuki19%2Fitems%2Fdf872d91c9c89cc31aee&realm=qiita)

[364](https://qiita.com/ysuzuki19/items/df872d91c9c89cc31aee/likers)

いいねしたユーザー一覧へ移動

317