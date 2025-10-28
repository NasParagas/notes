---
title: "[tips][cpp][ProblemSolver]メモリ上の値をswapで交換"
source: "https://qiita.com/kynea0b/items/d266540994c8c954a595"
author:
  - "[[kynea0b]]"
published: 2024-10-20
created: 2025-09-26
description: "swap 関数自体はシンプル。 たまに使う。例えば、A,B間の距離を求める系の問題とかでは、A <= Bの場合とA >= Bの場合では距離（つまりその後の処理）は同じです。そのような場合にswap()で入れ替えちゃってから処理を書くと場合分けを減らせるってやつです。 c+..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiita Conference 2025 Autumn

![](https://cdn.qiita.com/assets/public/client-resources/image-esteban_suarez-4572fbacb71b34be-4572fbacb71b34be.png)

Vercel Inc. Esteban Suarez

ゼロからv0まで（From 0 to v0）

[特設サイトで詳細をチェック](https://qiita.com/official-campaigns/conference/2025-autumn)

## swap

関数自体はシンプル。

たまに使う。例えば、A,B間の距離を求める系の問題とかでは、 `A <= B` の場合と `A >= B` の場合では距離（つまりその後の処理）は同じです。そのような場合に `swap()` で入れ替えちゃってから処理を書くと場合分けを減らせるってやつです。  
`c++03` ではalgorithm headerに定義されていたようですが、それ以降はutility headerに定義されているそうです。  
変数に割り当てられた値を交換します。 `swap()`

```cpp
#include <iostream>
#include <utility>
using namespace std;
int main(){
  int a = 1000000007;
  int b = 998244353;
  swap(a, b);

  cout << "a " << a << " " << "b " << b << endl;
}
```

output

```cpp
a 998244353 b 1000000007
```

## 参考

- きっかけになった問題

[0](https://qiita.com/kynea0b/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fkynea0b%2Fitems%2Fd266540994c8c954a595&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fkynea0b%2Fitems%2Fd266540994c8c954a595&realm=qiita)

## Qiita Conference 2025 Autumn 11月5日(水)~7日(金)開催！

![](https://cdn.qiita.com/assets/public/official_campaigns/qiita_conference_2025_autumn/image-conference_2025_autumn_ogp-7cf3021de31b9ab76a7b3bbaf2909bb5.png)

Qiita Conferenceは、AI時代のエンジニアに贈るQiita最大規模のテックカンファレンスです！

基調講演ゲスト(敬称略)

piacere、牛尾 剛、Esteban Suarez、和田 卓人、seya、ミノ駆動、市谷 聡啓、からあげ、まつもとゆきひろ、みのるん

[イベント詳細を見る](https://qiita.com/official-campaigns/conference/2025-autumn)

[0](https://qiita.com/kynea0b/items/d266540994c8c954a595/likers)

いいねしたユーザー一覧へ移動

0