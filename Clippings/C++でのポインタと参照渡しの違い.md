---
title: "C++でのポインタと参照渡しの違い"
source: "https://qiita.com/sakana_hug/items/c2e9850b373d8f894398"
author:
  - "[[sakana_hug]]"
published: 2024-06-24
created: 2025-08-26
description: "はじめに C++におけるポインタと参照は、どちらも他の変数（オブジェクト）を指し示すためのものであり、関数に対してデータを渡す際などに用いられます。しかし、ポインタと参照にはいくつかの重要な違いがあります。ポインタと参照渡しの違いについてまとめていきます。 ポインタ（P..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から1年以上が経過しています。

[@sakana\_hug (さかなはぐ)](https://qiita.com/sakana_hug)

投稿日

### はじめに

C++におけるポインタと参照は、どちらも他の変数（オブジェクト）を指し示すためのものであり、関数に対してデータを渡す際などに用いられます。しかし、ポインタと参照にはいくつかの重要な違いがあります。ポインタと参照渡しの違いについてまとめていきます。

### ポインタ（Pointer）

ポインタは、メモリ上の特定のアドレスを保持する変数です。ポインタを使うと、間接的にデータを操作することができます。

#### 宣言と使用

```cpp
int value = 42;
int* ptr = &value;  // ポインタ ptr は value のアドレスを保持する

std::cout << *ptr << std::endl;  // 42: ポインタを使って値を間接的に取得
```

#### 主な特徴

1. **値の変更**: ポインタが指すアドレスを変更することができます。
	```cpp
	int value1 = 42;
	int value2 = 100;
	int* ptr = &value1;
	ptr = &value2;  // ptr は別の変数を指すようになる
	```
2. **NULLポインタ**: ポインタはNULLまたはnullptrに設定することができ、どのオブジェクトも指さないことを示します。
	```cpp
	int* ptr = nullptr;  // ptr は何も指していない
	```
3. **演算**: ポインタに対して算術演算（アドレスの増減）が可能です。
	```cpp
	int arr[3] = {1, 2, 3};
	int* ptr = arr;
	ptr++;  // ptr は次の配列要素を指すようになる
	```

### 参照（Reference）

参照は、既存の変数の別名（エイリアス）を提供するためのものです。参照を使うと、その変数を直接操作することができます。

#### 宣言と使用

```cpp
int value = 42;
int& ref = value;  // 参照 ref は value の別名

std::cout << ref << std::endl;  // 42: 参照を使って値を取得
```

#### 主な特徴

1. **初期化の必要性**: 参照は宣言時に初期化する必要があり、後で別の変数を指すように変更することはできません。
	```cpp
	int value1 = 42;
	int& ref = value1;
	// int& ref;  // エラー: 参照は初期化が必要
	```
2. **NULL参照**: 参照はNULLに設定することができず、必ず有効なオブジェクトを指していなければなりません。
3. **演算不可**: 参照には算術演算を適用することができません。

### 比較

| 特徴 | ポインタ | 参照 |
| --- | --- | --- |
| 初期化 | 後で初期化可能 | 宣言時に初期化が必要 |
| NULL | NULLまたはnullptrに設定可能 | NULLに設定不可 |
| アドレスの変更 | ポインタが指すアドレスを変更可能 | 一度初期化されたら変更不可 |
| 演算 | 算術演算が可能 | 演算不可 |
| 使用例 | 動的メモリアロケーション、配列操作 | 関数引数のエイリアス、返り値 |
| シンタックス | `*` 演算子でデリファレンス | 直接アクセス |

### 例

#### ポインタを使った関数

```cpp
void increment(int* ptr) {
    (*ptr)++;
}

int main() {
    int value = 42;
    increment(&value);
    std::cout << value << std::endl;  // 43
    return 0;
}
```

#### 参照を使った関数

```cpp
void increment(int& ref) {
    ref++;
}

int main() {
    int value = 42;
    increment(value);
    std::cout << value << std::endl;  // 43
    return 0;
}
```

### まとめ

ポインタと参照はどちらも他の変数を指し示すために使われますが、使い方や特性に違いがあります。ポインタはより柔軟であり、アドレス操作や動的メモリ管理に適しています。一方、参照は簡潔で安全性が高く、関数の引数や返り値として使うのに適しています。それぞれの特徴を理解し、適切な場面で使い分けることが重要です。

[3](https://qiita.com/sakana_hug/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fsakana_hug%2Fitems%2Fc2e9850b373d8f894398&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fsakana_hug%2Fitems%2Fc2e9850b373d8f894398&realm=qiita)

[1](https://qiita.com/sakana_hug/items/c2e9850b373d8f894398/likers)

いいねしたユーザー一覧へ移動

1