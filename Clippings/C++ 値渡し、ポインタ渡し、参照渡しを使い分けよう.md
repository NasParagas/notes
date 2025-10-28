---
title: "C++ 値渡し、ポインタ渡し、参照渡しを使い分けよう"
source: "https://qiita.com/agate-pris/items/05948b7d33f3e88b8967"
author:
  - "[[agate-pris]]"
published: 2016-12-14
created: 2025-08-21
description: "PR: CADDiではバックエンドエンジニア、フロントエンジニア、アルゴリズムエンジニア、SRE等などを募集しています。 C++ では, 関数呼び出しの際, 引数の渡し方が大きく分けて 3 種類ある. それぞれ「値渡し」「ポインタ渡し」「参照渡し」だ. この内, 値渡しとそ..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

[@agate-pris](https://qiita.com/agate-pris)

最終更新日 投稿日 2016年12月14日

[PR: CADDiではバックエンドエンジニア、フロントエンジニア、アルゴリズムエンジニア、SRE等などを募集しています](https://corp.caddi.jp/recruit/eng) 。

C++ では, 関数呼び出しの際, 引数の渡し方が大きく分けて 3 種類ある. それぞれ「値渡し」「ポインタ渡し」「参照渡し」だ. この内, 値渡しとそれ以外の使い方の違いについては初学者であってもそれなりに理解している人が多い.

一方で, ポインタ渡しと参照渡しの使い方の違いについてはあまり理解出来てない人が多い. 本記事では各々の違いについて触れながら, 使用場面の違いについて説明していく.

## 値渡しの基本

値渡しは主に `int`, `float`, `bool`, `char` 等の組み込み型を使う際によく用いる. 値渡しを行うと, 平たく言えばコピーが行われる [^1]. `int` が引数であれば, その数値がコピーされるので, コピーされた引数を変更しても関数を呼び出した側の変数は書き換えられない.

call\_by\_value.cpp

```cpp
# include <iostream>
void twice( int a )
{
    std::cout << a << std::endl;
    a *= 2;
    std::cout << a << std::endl;
}
int main()
{
    int v = 16;
    std::cout << v << std::endl;
    twice( v );
    std::cout << v << std::endl;
}
```

出力

```text
16
16
32
16
```

渡す型のサイズが小さいものであれば問題無いが, サイズの大きな型が引数である場合, そのオブジェクトを構築するための処理時間がかかってしまうので, サイズの大きな型では一般に値渡しは推奨出来ない. 引数として使っていた型が後から変更されてサイズが大きくなる可能性も考慮すれば, 明示的な理由が無い限り組み込み型以外では値渡しを使用すべきではない.

## ポインタ渡しの基本

ポインタ渡しは変数のメモリ上のアドレスを渡す記法である. 値渡しとは異なり, 渡されたアドレスを間接参照する事で, 関数の呼び出し元の変数を書き換える事が出来る.

call\_by\_pointer.cpp

```cpp
# include <iostream>
void twice( int* a )
{
    std::cout << *a << std::endl;
    *a *= 2;
    std::cout << *a << std::endl;
}
int main()
{
    int v = 16;
    std::cout << v << std::endl;
    twice( &v );
    std::cout << v << std::endl;
}
```

出力

```text
16
16
32
32
```

上記のコードには問題がある. それは関数 `twice` が null チェックを行っていない点だ. もし引数に `NULL` [^2] が渡された時, そのポインタを間接参照すれば, 未定義の動作が引き起こされ, プログラムは直ちにクラッシュするだろう. もしクラッシュしなかったとしても, 正常な動作は期待出来ない. C++ でプログラムを書くのであれば, ポインタ渡しを行う際は必ず null チェックを行うべきである.

call\_by\_pointer\_check.cpp

```cpp
# include <iostream>
bool twice( int* a )
{
    if( a == nullptr )
    {
        std::cout << "null" << std::endl;
        return true;
    }
    std::cout << *a << std::endl;
    *a *= 2;
    std::cout << *a << std::endl;
    return false;
}
int main()
{
    twice( nullptr );
    int v = 16;
    std::cout << v << std::endl;
    twice( &v );
    std::cout << v << std::endl;
}
```

出力

```text
null
16
16
32
32
```

特別な理由がない限り, ポインタ渡しを行う際は上記のように null チェックを行うべきである. `nullptr` は C++11 で新たに導入された機能であり, 従来の `NULL` や `0` に代わるより安全な null チェックで用いる事が出来る.

尚, 関数 `twice` は `NULL` を渡された際は `true` を, それ以外の場合は `false` を戻すようにした. 関数の成功失敗を `bool` の戻り値で表す手法は C++ でしばしば用いられる方法であるが, エラーチェックの方法は多種多様であり, そのメリット / デメリットも様々であるため, ここでは扱わない.

## 参照渡しの基本

参照渡しは C には無く, C++ で新たに追加された記法である. その実態は殆どの場合, より安全で, 制約の厳しいポインタであると言って差し支えない.

call\_by\_reference.cpp

```cpp
# include <iostream>
void twice( int& a )
{
    std::cout << a << std::endl;
    a *= 2;
    std::cout << a << std::endl;
}
int main()
{
    int v = 16;
    std::cout << v << std::endl;
    twice( v );
    std::cout << v << std::endl;
}
```

出力

```text
16
16
32
32
```

参照は, 誤解を恐れずに言えば, 元の変数に別名をつける記法である. 関数呼び出しにおいても, 呼び出される関数側においても, ( 仮引数の記述を除き ) 通常の変数と同様の記法で扱うことが出来, ポインタ呼び出しと同様に, 変更すると呼び出し元の変数も書き換えられる.

ポインタとの違いは, 無効値を表す記法が存在しない事だ. このため, 呼び出される関数側ではその参照が有効な参照である事を前提条件として内容を記述出来るし, 呼び出す側では無効な参照を渡さない事を前提にプログラムを記述する事を必然的に, 文法レベルで要求される.

また, C++ には関数のオーバーロードが存在し, 参照渡しとポインタ渡しはオーバーロード可能である. よって, 以下のような記法が可能である.

call.cpp

```cpp
# include <iostream>
void twice( int& a )
{
    std::cout << "call by reference." << std::endl;
    std::cout << a << std::endl;
    a *= 2;
    std::cout << a << std::endl;
}
bool twice( int* a )
{
    std::cout << "call by pointer." << std::endl;
    if( a == nullptr )
    {
        std::cout << "null" << std::endl;
        return true;
    }
    twice( *a );
    return false;
}
int main()
{
    int* p = nullptr;
    twice( p );

    int v = 16;
    int& r = v;
    p = &v;

    std::cout << "twice( v );" << std::endl;
    std::cout << v << std::endl;
    twice( v );
    std::cout << v << std::endl;

    std::cout << "twice( r );" << std::endl;
    std::cout << v << std::endl;
    twice( r );
    std::cout << v << std::endl;

    std::cout << "twice( p );" << std::endl;
    std::cout << v << std::endl;
    twice( p );
    std::cout << v << std::endl;
}
```

出力

```text
call by pointer.
null
twice( v );
16
call by reference.
16
32
32
twice( r );
32
call by reference.
32
64
64
twice( p );
64
call by pointer.
call by reference.
64
128
128
```

ポインタ渡しの関数 `twice` は参照渡しの `twice` に依存しているため, 有効なポインタが渡された場合は `call by pointer` の後に `call by reference` が出力される. 同じコードを 2 度書く事は通常避けるべきであるから, 今回はこのような依存関係となる. 変数 `v` を実引数として関数 `twice` を呼び出した場合と, 参照変数 `r` を実引数として関数 `twice` を呼び出した場合とで, 同じ参照渡しの関数 `twice` が呼び出されている事が分かる.

## 再構築が生じるとしても例外的に値渡しを用いるべき場合

明示的に複製を行う必要がある場合は値渡しを用いるべき場合が存在する.

return\_value.cpp

```cpp
# include <iostream>
void print( int const a )
{
    std::cout << a << std::endl;
}
int main()
{
    int v = 16;
    print( v );
}
```

出力

```text
16
```

関数 `print` の呼び出し時, 引数は値渡しされる. 即ち, オブジェクトのコピーが行われる. 上記の例の場合は `int` というサイズの小さな型であるため, ポインタや参照によって渡す場合のオーバーヘッドと値渡しによって生じるオブジェクトの構築のオーバーヘッドは殆ど変わらない.

上記の場合, ポインタや参照によってオブジェクトを渡した場合, そこから元の値を得るためのオーバーヘッドが生じる. 即ち, 各々, ポインタを間接参照するオーバーヘッドと参照の元を辿るオーバーヘッドである. `int` の様な小さい型の値渡しであれば, 構築されたオブジェクトがレジスタにそのまま乗る事が期待出来る. まぁ, 参照渡しやポインタ渡しであっても上記の場合コンパイラによる最適化で同じコードになるかも知れないが.

## ポインタ渡しであっても null チェックをしない例外

特定の理由により, C 言語と同等の記述しか使用出来ない状況では, null チェックを行わない関数を書く必要性が生じる場合がある. その場合, 関数を呼び出す側に対して `NULL` を渡してはならない事を明記する必用がある.

動的リンクを行う場合のシンボル等でこのような制限が生じる場合がある. その場合, 速度を重視するために null チェックを行わず, null チェックを行う責任は関数を呼び出す側に委ねられる. 必ず null にならないような文脈ではライブラリが null チェックを行う事は余計な処理となるだろう.

## 積極的に const を使おう

C++ においては, 安全のため積極的に `const` を用いるべきである.

```cpp
# include <iostream>
int twice( int const a )
{
    return a * 2;
}
int main()
{
    std::cout << twice( 16 ) << std::endl;
}
```

関数 `twice` は極めて短いが, 例えば乗算を行う前に `a` の値を誤って書き換えてしまうと, 当然誤った値が出力される. このような誤った変更を防ぐため, const 修飾子を用いる.

参照を用いる場合でも有用である.

```cpp
# include <iostream>
void print( int const& a )
{
    std::cout << a << std::endl; 
}
int main()
{
    int v = 4;
    print( v );
}
```

型が const 修飾されていないため, 関数を呼び出す側は値が書き換えられる事がない事が事前に分かるため, 安心して呼び出すことが出来る.

ポインタの場合, 微妙にややこしい.

```cpp
int main()
{
    int v = 16;

    int* const pc = &v;
    // ok
    *pc += 5;
    // ng
//  pc = NULL;

    int const* cp = &v;
    // ng
//  *cp += 5;
    // ok
    cp = NULL;

    int const* const cpc = &v;
    // ng
//  *cpc += 5;
//  cpc = NULL;
}
```

`*` の前の `const` は参照するオブジェクトが不変であること, 後の `const` はアドレスが不変である事を示す.

[3](https://qiita.com/agate-pris/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fagate-pris%2Fitems%2F05948b7d33f3e88b8967&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fagate-pris%2Fitems%2F05948b7d33f3e88b8967&realm=qiita)

[858](https://qiita.com/agate-pris/items/05948b7d33f3e88b8967/likers)

いいねしたユーザー一覧へ移動

695

[^1]: もう少し詳細に言えば, その型のコンストラクタが呼ばれ新しくオブジェクトがスタック領域に構築される.

[^2]: または `nullptr`