---
title: "constexprとconstを正しく使い分ける"
source: "https://qiita.com/saltheads/items/dd65935878a0901fe9e7"
author:
  - "[[saltheads]]"
published: 2016-12-05
created: 2025-08-21
description: "はじめに これまでconst修飾してきたものには２種類あった、ひとつはROM化可能な値、もうひとつは実行時にしか決まらないがいったん初期化したあとは二度と変更されない値である。C++11以降、前者はconstexprが受け持ち、後者はconstが受け持つことになった。 ..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

## はじめに

- これまでconst修飾してきたものには２種類あった、ひとつはROM化可能な値、もうひとつは実行時にしか決まらないがいったん初期化したあとは二度と変更されない値である。C++11以降、前者はconstexprが受け持ち、後者はconstが受け持つことになった。
- constexpr指定子は、constexprの制約を満たした変数の定義、関数と関数テンプレートの宣言、staticデータメンバーの宣言に対して使用できる。

## ２つの区別

- constは型修飾子である。const intはint型であるが、初期化したあとは二度と変更されない変数である。C++11以降、constexprが導入されたあとは、constはRAMにしか配置できない変数に対して使う修飾子となった。
- constexprは型修飾子ではなく、型指定子である。型を修飾するものでなく、ROM化できる、または、ROM化できる可能性がある、という意味を持つ指定子である。

## 結論

- 変数にconstexpr指定子をつけると、それは定数となりinline展開されてプログラムに埋め込まれる。constexpr変数の値はコンパイル時に決定されROM化可能な値である。その目的は、計算処理を実行時ではなくコンパイル時に移して、実行時のプログラムを高速化することにある。
- constexpr変数どうしや、constexpr変数と基本型を組み合わせた演算の結果で初期化した変数もconstexpr変数にできる。
- constexpr関数は、引数にconstexpr変数を与え、constexprの制約を満たした簡単な演算で答えを返す場合、その返り値でconstexpr変数を初期化することができる。
- constexpr関数には、引数に実行時にしか決まらない値を渡して結果をreturnすることもでき、その場合は、その返り値でconstexpr変数を初期化することはできない。実行時にしか決まらない値とは例えば、標準入力、ファイルから読んだ値や乱数である。
- コンパイラは、constexpr変数を初期化する式の右辺が再帰的にすべてconstexprでできているなら、コンパイルを成功させる。もし、右辺がconstexpr変数でできていないことがわかれば、constexpr変数を初期化する文をコンパイルエラーにする。
- constexpr変数を初期化する文がコンパイルできるとすると、右辺は再帰的にすべてコンパイル時に計算してしまえることになり、結果的に計算を実行時からコンパイル時に移して高速化できることになる。
- constexpr変数を初期化する文がコンパイルできるためには、右辺が十分に簡単で、コンパイル時に計算可能なプログラムになっていなければならない。あまりに複雑であればコンパイルは無理と言ってくる。どこまでができて、どこまではできないのかは実際のところ実装依存である。C++11よりC++14のほうがより多くをできる。
- constexprテンプレート関数の場合は、実際にテンプレートを展開してソースコードに埋め込んでから、それがコンパイル時に計算可能であるかどうかを判断するので、テンプレートを定義するところにconstexprと書けるからといって、それを実際に使ったところでconstexprにできるかどうかは実際に使ってみないとわからない。もっというといつもconstexprにできない定義をテンプレートの定義にすることもできるので、簡単でない処理や、実行時にしか決まらない処理をテンプレート関数の中で実行するなら、constexpr指定子をつけてはならない。使うひとを困らせてはならない。

## constexpr変数

コンパイル時に値が求められる値を初期値とする変数にはconstexpr指定子をつけることができる。

C++11

```cpp
constexpr unsigned int bufSize = sizeof("100 100¥n");
static_assert(bufSize == 9, "bufSize is not 9");
char buf[bufSize];
fgets(buf, sizeof(buf), stdin);
const int a = atoi(strtok(buf," ¥n"));
const int b = atoi(strtok(nullptr," ¥n"));
printf("%d¥n", (a+b)/2);
```

最初の行で、bufSizeを9に初期化している。この計算はコンパイラはコンパイル時にできるので、constexpr指定子をつけた変数を初期値9で宣言する文は、コンパイルエラーなくコンパイルできる。

別の例で、

C++11

```cpp
typedef enum Color : int32_t {
  Red = 0, Yellow, Blue
} Color;

constexpr static Color colors[] = { 
  Color::Red, Color::Yellow, Color::Blue 
};
```

列挙型Colorはint32\_tであり、3つの値をとる。static宣言されたColor型の配列は constexpr指定子がつけられ、ROM上に配置される。

## constexpr関数

- もしコンパイル時に関数の引数にconstexpr値が与えられれば、返り値でconstexpr変数を初期化する文はコンパイルに成功する。
- もしコンパイル時に関数の引数にconstexprでない値が与えられたら、返り値でconstexpr変数を初期化する文はコンパイルエラーになる

C++11

```cpp
constexpr int twice(const int n) {
  return n * 2;
}
```

このtwiceを呼び出す式を書く

C++11

```cpp
constexpr int ten = 10;                                     // OK
constexpr int ten2 = twice(ten);                            // OK
constexpr int tenx = twice(static_cast<int>(rand() % 10));  // コンパイルエラーになる
```

constexpr関数の引数にconstexpr変数を与えて、戻り値を使ってconstexpr変数を初期化する文はコンパイルに成功する。それに対し、constexpr関数の引数に実行時にならないと決まらない関数の戻り値をあたえて、戻り値を使ってconstexpr変数を初期化するとそれはコンパイルエラーになる。コンパイル時に値を決めることができないからだ。

ちょっと変えてみよう

C++11

```cpp
int ten = 10;
constexpr int ten2 = twice(ten);     // コンパイルエラーになる
const int ten3 = twice(ten);         // コンパイルエラーにならない
```

constexpr変数の引数にconstexprでない変数を与え、その戻り値でconstexpr変数を初期化しようとすると、コンパイルエラーになる。あくまで結果がROM化できないとならない。

constexpr変数の引数にconstexprでない変数を与え、その戻り値でconstexpr指定されていない変数を初期化する場合は、コンパイルエラーにならない。

このようにconstexpr関数の引数に何を渡すか、constexpr関数の戻り値で何を初期化するかによって、コンパイルエラーがおきたりおきなかったりする。これはプログラマにきちんとROM化できるソースを書くよう促すことになる。

## コーディング規約では

組込みソフトウェア開発向け コーディング作法ガイド ESCR C++ 2.0 では、1.0のときのルールが変更になり、

**M1.10.1 意味のある定数は、名前付きの定数として定義して使用する。**

というルールになり、\*\*「C++11以降では、コンパイル時定数をconstexpr修飾で定義できるので、constexpr修飾による定義を使用する」\*\*と解説され、ROMに配置できる定数は、#defineによるマクロも、const修飾も、すべて使用禁止になった。

このルールができたため、constを使うところは極端に少なくなった。

```c
# include <stdio.h>
# include <string.h>
# include <stdlib.h>

int main() {
  char str[100];
  fgets(str, sizeof(str), stdin);
  const int val1 = atoi(strtok(str, " "));
  const int val2 = atoi(strtok(nullptr, " "));

  if (val1 > val2) {
    printf("%d¥n", val1);
  } else if (val1 == val2) {
    printf("eq¥n");
  } else {
    printf("%d¥n",val2);
  }
  return 0;
}
```

このプログラムで、val1とval2は実行時にしか決まらない値であり、初期化されたあとは二度と変更されない値である。そのため型修飾子constがついたintとして、val1とval2が宣言できる。const修飾しておけば、それ以降、val1やval2を変更するプログラムを書けばコンパイルエラーにしてくれる。const修飾を上手に使えばより品質の高いプログラムが書ける。

このように、関数呼び出しの結果を初期値とする変数が、それ以降書き変わらないときにconstをつけることになる。

## const型修飾がついていて良いもの

- 実行してみないと結果がわからない関数を呼び出し、その関数の戻り値で初期化する変数が、それ以降二度と値が変わらない場合。
- クラスのインスタンスがスタック上からヒープ上に作成される必然性があり、そのクラスのメンバ変数が、コンストラクタ初期化子で初期値が決まったあと、二度と値が変わらない場合。
- 上で、構造体で同様の場合。

## レビュー視点

1. const修飾されている変数を見つけたら、その変数はRAMにしか置けないものになっているか確認する。もしROM化可能だと思ったら、constexpr型指定子に変更できないか考えて指摘する。
2. constexprテンプレート関数の内部に、そもそもROM化できない文や関数呼び出しが入っていないか確認する。どうせ使ったときにコンパイルエラーになるが、そういうコードをコードレビューで見つけたら直しておく。
3. そもそもESCRでは再帰は使ってはいけないことになっているので、constexpr関数の中でも再帰は使ってはならない。
4. constexpr関数やconstexpr関数テンプレートを見つけたら、その戻り値で初期化している変数があり、かつconstexpr型指定されているかどうか確認する。そうでなければ、constexpr関数やconstexpr関数テンプレートの存在意義がない。

以下はつけても無駄なconstexprの例。(コンパイルエラーにならない)

C++14

```cpp
template <typename T>
constexpr inline static T GetFromCin(void) {
  T value;
  cin >> value;
  return value;
}
```

## 補足

レビュー視点3についてはその再帰がコンパイル時に値に計算されてしまうので、ESCRが心配する実行時スタックあふれの問題は発生しない。なので使用を認めるべきだ議論ができるかもしれないが、引き継いでくれる人の理解のために良いので使わない。

組込みだとすぐROM化可能と言ってしまうのだが、ここでのROM化可能の意味は、インストラクションキャッシュにおさまる、という意味である。インストラクションキャッシュにおさまる、つまりコードセグメントにあって、かつ、ごく狭いアドレス範囲に存在する、という意味である。データセグメントにあってはたとえROM化可能でも、遠いアドレスにあってはキャッシュに入らないかもしれない。

constexprのゴールは、インストラクションキャッシュ（とレジスタ）におさまるところに実行時に必要な情報を集めることである。

## 参考文献

- C++によるプログラミングの原則と実践 - 2016/9/2  
	Bjarne Stroustrup (著), 江添 亮 (監修), 株式会社クイープ/遠藤美代子 (その他)  
	[https://www.amazon.co.jp/dp/4048930516/](https://www.amazon.co.jp/dp/4048930516/)
- Effective Modern C++ —C++11/14プログラムを進化させる42項目 - 2015/9/18  
	Scott Meyers (著), 千住 治郎 (翻訳)  
	[https://www.amazon.co.jp/dp/4873117364/](https://www.amazon.co.jp/dp/4873117364/)
- Optimized C++ - 2016/5/21  
	Kurt Guntheroth (著)  
	[https://www.amazon.co.jp/dp/1491922060/](https://www.amazon.co.jp/dp/1491922060/)

[0](https://qiita.com/saltheads/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fsaltheads%2Fitems%2Fdd65935878a0901fe9e7&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fsaltheads%2Fitems%2Fdd65935878a0901fe9e7&realm=qiita)

[210](https://qiita.com/saltheads/items/dd65935878a0901fe9e7/likers)

いいねしたユーザー一覧へ移動

156