---
title: "適当なC++テンプレート入門"
source: "https://qiita.com/hal1437/items/b6deb22a88c76eeaf90c"
author:
  - "[[hal1437]]"
published: 2014-12-27
created: 2025-08-26
description: "はじめまして Qiitaというものがあるそうじゃないですか、 適当にやったことをまとめようと思いまして、 ところで C++にはたくさんの型が存在します。 int float char std::string などなど、自作の型も含めれば無数に存在します。 型というのは不..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

[@hal1437](https://qiita.com/hal1437)

投稿日

## はじめまして

Qiitaというものがあるそうじゃないですか、  
適当にやったことをまとめようと思いまして、

## ところで

C++にはたくさんの型が存在します。  
`int` `float` `char` `std::string`  
などなど、自作の型も含めれば無数に存在します。

型というのは不自由なものでして、  
型を決定してしまうことで、そのコード自体を不自由にしてしまう可能性があります。

例えばこんな関数

add.cpp

```cpp
int Add(int a,int b){
    return a+b;
}
```

Add関数は見ての通り `int` 型のみを受け取り、 `int` 型を返す関数になります。  
もちろん引数に `float` などの浮動小数点型を与えた場合は `int` にキャストされて小数点以下は切り捨てられるってわけですね。

まあ、C++の **オーバーロード** を使えば同名で多重定義することも可能です

add.cpp

```cpp
int Add(int a,int b){
    return a+b;
}
float Add(float a,float b){
    return a+b;
}
```

これによって、 `int` と `float` 限定でadd関数が使用可能になります。

先に上げたように、型は無数に存在するので  
Add関数も無数に書かなければいけません。

ついでにいうと、このままではライブラリの開発者はユーザーがどんな型を使うのか予知する必要があります。

## 関数テンプレート

プログラマは全員が予言者では無いので、  
**あらゆる型に対して適応できる万能のコード** で書く必要があります

add.cpp

```cpp
template<typename T>
T Add(T a,T b){
    return a+b;
}
```

とまあこんな感じで  
`template<class T>` と記述することでTを任意の型として関数内で使用できます。

add.cpp

```cpp
template<typename T,typename U>
T Add(T a,U b){
    return a+b;
}
```

二個以上の引数を取ることもできます。  
この場合T=UでもT≠Uであってもこの関数が使用できます。

また、この関数の場合+演算子を使用できない型はコンパイル時にエラーが吐き出されます。

## テンプレートの特殊化

ある日僕は言われました

> 「俺はfloatが嫌いだ！そのAdd関数、floatの時だけ減算の処理をしてくれ！」

おまかせください。テンプレートには特定の型だけ違う処理をする **テンプレートの特殊化** の機能があります。

add.cpp

```cpp
template <typename T>
T add(T a,T b){
    return a + b;
}

template <>
float add(float a,float b){
    return a - b;
}
```

こんな感じでfloatだけ別の処理にすることもできます。

## 関数クラスメンバエイリアステンプレート

テンプレートには実は何種類かあります

- 関数テンプレート
- クラステンプレート
- メンバテンプレート
- エイリアステンプレート

#### 関数テンプレート

さっきのAdd関数とか

add.cpp

```cpp
template <typename T>
T add(T a,T b){
    return a + b;
}
```

割と使う

#### クラステンプレート

クラスと共に暮らす

myclass.cpp

```cpp
template <typename T>
class myclass{
public:
    T value;
};
```

宣言の仕方は `myclass<int>` のように宣言し、  
テンプレート引数に指定した型を持つようになる。

#### メンバテンプレート

正直、関数テンプレートとほとんど同じ

myclass2.cpp

```cpp
class myclass2{
public:
    int nanika;

public:

    template<typename T>
    T Function(T a,T b);

};
```

メンバ関数にも関数テンプレートは使用できる。それだけ

#### エイリアステンプレート

C++11からtypedefをusingで記述できるようになった

```cpp
typedef TypographicError typo;
using typo = TypographicError;
```

これら２つは同様の意味を持つ。

そしてusingを使ったエイリアスにはテンプレートを使用できる

UsingTemplate.cpp

```cpp
template<typename T,typename U>
T Add2(T a,U b);

template <typename T>
using Add1 = Add2<T,T>;
```

このように、型のエイリアスの作成、テンプレート引数の再編成などが行える。  
でもC++11から

## まとめ

テンプレートを用いることで汎用的なプログラムコードを作成できる。  
ここまで読んでくれた人はきっと既にテンプレートなしでは生きられない体になっているだろう。  
テンプレートマスターになって再利用しやすいコードを書こう！

まあ、テンプレートの真の力はこんなものでは無いんだけどね。  
それは又の機会に…

[2](https://qiita.com/hal1437/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fhal1437%2Fitems%2Fb6deb22a88c76eeaf90c&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fhal1437%2Fitems%2Fb6deb22a88c76eeaf90c&realm=qiita)

[380](https://qiita.com/hal1437/items/b6deb22a88c76eeaf90c/likers)

いいねしたユーザー一覧へ移動

266