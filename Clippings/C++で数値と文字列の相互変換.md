---
title: "C++で数値と文字列の相互変換"
source: "https://qiita.com/seal_qiita/items/d62192f8d0b1e4ca8de2"
author:
  - "[[seal_qiita]]"
published: 2020-11-09
created: 2025-08-26
description: "きっかけ そろそろC++ str to intで調べるのがいやになってきたので、記事に残しておきます。C++のcharクラス・stringクラスとintクラス・その他の数値クラスの相互変換のやり方のまとめです。 #早見表 今回のまとめです 元の型 変換したい型 方法..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から1年以上が経過しています。

## きっかけ

そろそろ `C++ str to int` で調べるのがいやになってきたので、記事に残しておきます。C++のcharクラス・stringクラスとintクラス・その他の数値クラスの相互変換のやり方のまとめです。

#早見表  
今回のまとめです

| 元の型 | 変換したい型 | 方法 |
| --- | --- | --- |
| string | 数値 | stox( ) ただしxは変換したい型によって変わる |
| char | int | int(c-'0') |
| 数値 | string | to\_string( ) |

`stox()` 関数の詳細です

| 型 | 変換する関数 |
| --- | --- |
| int | stoi |
| long long | stoll |
| double | stod |
| float | stof |
| long | stol |
| long double | stold |
| unsigned long | stoul |
| unsigned long long | stoull |

#string to int  
string型からint型に変換したい時は `stoi()` 関数を使う。

strtoint.cpp

```cpp
#include <iostream>
#include <string>
using namespace std;

int main(int argc, char* argv[]){
    string S = "123";
    int N = stoi(S);
    cout<<"num:"<<N<<" type:"<<typeid(N).name()<<endl;
}
```

出力は次のようになる。型が `int` だと `i` と出力されるらしい。詳しくはC++11の [typeinfoについて](https://qiita.com/menonon/items/69cd21f82299608505a4) を参照。

```text
num:123 type:i
```

stringから任意の数値型に変換する関数は次の通り。よく使う順。

| 型 | 変換する関数 |
| --- | --- |
| int | stoi |
| long long | stoll |
| doulbe | stod |
| float | stof |
| long | stol |
| long double | stold |
| unsigned long | stoul |
| unsigned long long | stoull |

#char to int  
stringの文字を1文字ずつ取得してintに変換したい時がある。しかし、 `stoi()` はchar型に対応していない。char型をint型に変換するには、文字コードの引き算を行う必要がある。他にやり方は見つからなかった。

chartoint.cpp

```cpp
#include <iostream>
#include <string>
using namespace std;

int main(int argc, char* argv[]){
    string S = "456";
    for(int i=0;i<3;i++){
        int N = int(S[i]-'0');
        cout<<"num:"<<N<<" type:"<<typeid(N).name()<<endl;
    }
}
```

出力は次のようになる。

```text
num:4 type:i
num:5 type:i
num:6 type:i
```

#int to string  
数値型をstring型にしたい時は `to_string()` 関数を使う。

inttostr.cpp

```cpp
#include <iostream>
#include <string>
using namespace std;

int main(int argc, char* argv[]){
    int N = 789;
    string S = to_string(N);
    cout<<"str:"<<S<<" type:"<<typeid(S).name()<<endl;
}
```

出力は次のようになる。多分コンパイラのせいかバージョンのせいで、stringの `typeinfo.name()` がうまく働いていないが、basic\_stringという文字があるのでちゃんとstringになっているはずである。

```text
str:789 type:NSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEE
```

#まとめ  
`C++ 文字列 数値` と調べなくてもよくなりそうです。

| 元の型 | 変換したい型 | 方法 |
| --- | --- | --- |
| string | 数値 | stox( ) ただしxは変換したい型によって変わる |
| char | int | int(c-'0') |
| 数値 | string | to\_string( ) |

[3](https://qiita.com/seal_qiita/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fseal_qiita%2Fitems%2Fd62192f8d0b1e4ca8de2&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fseal_qiita%2Fitems%2Fd62192f8d0b1e4ca8de2&realm=qiita)

[110](https://qiita.com/seal_qiita/items/d62192f8d0b1e4ca8de2/likers)

いいねしたユーザー一覧へ移動

90