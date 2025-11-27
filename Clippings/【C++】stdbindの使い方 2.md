---
title: "【C++】std::bindの使い方"
source: "https://qiita.com/tkyaji/items/1aa7bb01658e848d3ef4"
author:
  - "[[tkyaji]]"
published: 2014-12-26
created: 2025-11-07
description: "C++のstd::bindの使い方。 使おうと思った時に忘れてるのでメモ。 main.cpp # include void test_function(int a, int b) { printf(\"a=%d, b=%d\n\", a, b)..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

C++のstd::bindの使い方。  
使おうと思った時に忘れてるのでメモ。

main.cpp

```cpp
# include <iostream>

void test_function(int a, int b) {
    printf("a=%d, b=%d\n", a, b);
}

int main(int argc, const char * argv[]) {
    auto func1 = std::bind(test_function, std::placeholders::_1, std::placeholders::_2);
    func1(1, 2);
    // -> a=1, b=2

    auto func2 = std::bind(test_function, std::placeholders::_1, 9);
    func2(1);
    // -> a=1, b=9

    return 0;
}
```

std::bindは何をしてくれるかというと、  
**指定した関数をラップしたstd::functionを作る**  
ということです。

`std::placeholders::_n` というのがわかりづらいですが、これは  
**作ったstd::functionを呼び出す時の引数**  
を表しています。

上の例の場合、こんな感じの関数(std::function)が作られると考えると良いと思います。(あくまでイメージ)

```cpp
auto func1 = std::bind(test_function, std::placeholders::_1, std::placeholders::_2);
  ↓ // こんなイメージの関数(std::function)ができる
void func1(?1, ?2) {
    test_function(?1, ?2);
}

auto func2 = std::bind(test_function, std::placeholders::_1, 9);
  ↓ // こんなイメージの関数(std::function)ができる
void func2(?1) {
    test_function(?1, 9);
}
```

*?1,?2* と書いたのは `std::placeholders` を表しています。  
func2の例の場合は、2つ目の引数を固定値にしているので、実行時に固定値が渡されます。

`std:: placeholders` は最初は理解しづらいですが、プログラムからSQLを実行する際の、  
"**?**" をイメージすると良いかもしれません。

```sql
select * from table_name where id=?;
```

と書いた時のバインド変数 "**?**" が `std::placeholders::_1` にあたります。  
この定義したSQLがstd::bindの戻り値(std::function)で、SQL(std::function)を実行する時に、  
"**?**" (`std::placeholders`)に引数を渡して呼び出すという感じです。

ちなみにインスタンスメソッドを指定したい場合は、std::bindの第2引数にインスタンスを渡します。

main.cpp

```cpp
# include <iostream>

class Test {
public:
    void test_function(int a, int b) {
        printf("a=%d, b=%d\n", a, b);
    }
};

int main(int argc, const char * argv[]) {
    auto test = Test();
    auto func1 = std::bind(&Test::test_function, test, std::placeholders::_1, std::placeholders::_2);
    func1(1, 2);
    
    return 0;
}
```

自分自身のメソッドを呼び出す場合は `this` を指定すればOK。

main.cpp

```cpp
# include <iostream>

class Test {
public:
    void test_function(int a, int b) {
        printf("a=%d, b=%d\n", a, b);
    }
    
    void bind_test() {
        auto func1 = std::bind(&Test::test_function, this, std::placeholders::_1, std::placeholders::_2);
        func1(1, 2);
    }
};

int main(int argc, const char * argv[]) {
    
    auto test = Test();
    test.bind_test();
    
    return 0;
}
```

以上です。

[0](https://qiita.com/tkyaji/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Ftkyaji%2Fitems%2F1aa7bb01658e848d3ef4&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Ftkyaji%2Fitems%2F1aa7bb01658e848d3ef4&realm=qiita)

## Qiita Conference 2025 Autumn 11月5日(水)~7日(金)開催！

![](https://cdn.qiita.com/assets/public/official_campaigns/qiita_conference_2025_autumn/image-conference_2025_autumn_ogp-d35d2500cd0420d93f2ed69a8d162d74.png)

Qiita Conferenceは、AI時代のエンジニアに贈るQiita最大規模のテックカンファレンスです！

基調講演ゲスト(敬称略)

piacere、牛尾 剛、Esteban Suarez、和田 卓人、seya、ミノ駆動、市谷 聡啓、からあげ、岩瀬 義昌、まつもとゆきひろ、みのるん、 Null-Sensei

[イベント詳細を見る](https://qiita.com/official-campaigns/conference/2025-autumn)

## Qiita Advent Calendar 開催！

[76](https://qiita.com/tkyaji/items/1aa7bb01658e848d3ef4/likers)

いいねしたユーザー一覧へ移動

79