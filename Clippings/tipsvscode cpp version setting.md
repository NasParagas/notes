---
title: "[tips][vscode] cpp version setting"
source: "https://qiita.com/kynea0b/items/251a08e4b13e2eb9db97"
author:
  - "[[kynea0b]]"
published: 2024-10-12
created: 2025-08-21
description: "Vscodeのcpp version std::set s = {'a', 'b', 'c'}; // キー2の要素が含まれているか if (s.contains('b')) { std::cout << \"contain\" << std::endl; ..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

[@kynea0b](https://qiita.com/kynea0b)

最終更新日 投稿日 2024年10月12日

## Vscodeのcpp version

```cpp
std::set<char> s = {'a', 'b', 'c'};

// キー2の要素が含まれているか
if (s.contains('b')) {
  std::cout << "contain" << std::endl;
} else {
  std::cout << "doesn't contain" << std::endl;
}
```

`contains` 関数はcpp+20から導入された関数です。vscodeの設定がc++20以上出ないと次のようなエラーを出力します。  
[![スクリーンショット 2024-10-12 14.24.35.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/443312/0607a840-d75f-151a-5c23-2797dbdd9866.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F443312%2F0607a840-d75f-151a-5c23-2797dbdd9866.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=0978f0dcce7fe2d97dc84d2d91fd5cfb)

同じこと言ってる人いた。  
[https://stackoverflow.com/questions/69456226/class-stdmap-has-no-member-contains-in-visual-studio-code](https://stackoverflow.com/questions/69456226/class-stdmap-has-no-member-contains-in-visual-studio-code)

ただし、code runnnerに次のコマンドを指定してる場合は、 `-std=c++20` としてコンパイラがコンパイルするので実行されます（当たり前か）

```text
g++ -std=c++20 hoge.cpp -o hoge
```

```cpp
#include <iostream>

int main() {
  std::cout << __cplusplus << std::endl;
  return 0;
}
```

## Vscodeでcppのversionを設定する方法

設定もしくはSettingを開く。  
[![スクリーンショット 2024-10-12 14.19.14.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/443312/c9ed7bf5-a006-a56b-e826-6103e370f332.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F443312%2Fc9ed7bf5-a006-a56b-e826-6103e370f332.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=eaaf26180326108d78a01e23142c6d60)

`cppStandard` を検索。

[![スクリーンショット 2024-10-12 14.19.03.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/443312/fd6fd806-f059-8453-78e6-80bc8dbc939a.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F443312%2Ffd6fd806-f059-8453-78e6-80bc8dbc939a.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=b2f382dbb346368f419a4d3e79bec392)

pulldownから、ユーザー、ワークスペース両方の設定を `c++20` に選択。

これでコンパイルエラーが綺麗に消えます。めでたし。

## intelliSenseMode

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

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fkynea0b%2Fitems%2F251a08e4b13e2eb9db97&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fkynea0b%2Fitems%2F251a08e4b13e2eb9db97&realm=qiita)

[0](https://qiita.com/kynea0b/items/251a08e4b13e2eb9db97/likers)

いいねしたユーザー一覧へ移動

0