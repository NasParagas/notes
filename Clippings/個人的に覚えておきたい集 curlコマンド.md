---
title: "個人的に覚えておきたい集 curlコマンド"
source: "https://qiita.com/fy406/items/6dd2de050e875e52d36a"
author:
  - "[[fy406]]"
published: 2021-09-26
created: 2025-08-26
description: "curlコマンドについての使い方が曖昧だったので、今回はcurlコマンドのオプション等をいくつかまとめました。 基本書式 curlの書式は以下の通りとなっています。 オプションに何も指定しない場合、接続先のHTTPリクエストをそのまま標準出力します。 curl [オプショ..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から3年以上が経過しています。

curlコマンドについての使い方が曖昧だったので、今回はcurlコマンドのオプション等をいくつかまとめました。

## 基本書式

curlの書式は以下の通りとなっています。  
オプションに何も指定しない場合、接続先のHTTPリクエストをそのまま標準出力します。

```text
curl [オプション] [接続先URL]
```

## アクセス先のファイルをダウンロードする

ダウンロードする方法はいくつかありますが、基本は `-O` オプションとなります。  
保存するファイル名を指定する場合は `-o` オプションを使用します。

```text
curl -O [接続先URL]
curl -o [保存先パス/ファイル名] [接続先URL]
```

## ファイルをアップロードする

`-T` オプションを使用すると、ファイルのアップロードを行えます。  
通常あまり使用する機会はないかもしれませんが、Artifactory等のサーバーにファイルをアップロードするとき等に使用されます。  
アップロード対象ファイルがカレントディレクトリ内に無い場合も対象ファイルへのパスを指定することでアップロードができます。

```text
curl -T [パス/ファイル名] [接続先URL]
```

## ユーザー認証を求められる場合

`-u` オプションを追加することで接続先のユーザー認証をコマンド上で追加することができます。  
スクリプト等でユーザー認証からその後の処理までワンラインで実行できます。  
なお、 `-U` オプションの場合は、プロキシ認証となります。

```text
curl -u [ユーザー名]:[パスワード] [接続先URL]
```

## コマンド実行結果を表示させない

コマンド実行時のメッセージを非表示にするには `-s` オプションを使います。  
さらに `-S` を追加するとエラーメッセージのみ表示できます。

```text
curl -s [接続先URL]
```

## リダイレクト先にアクセスしたい場合

接続先のURLがリダイレクトされている場合、 `-L` オプションを使用することでリダイレクト先へアクセスするようになります。

```text
curl -L [接続先URL]
```

[0](https://qiita.com/fy406/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Ffy406%2Fitems%2F6dd2de050e875e52d36a&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Ffy406%2Fitems%2F6dd2de050e875e52d36a&realm=qiita)

[1](https://qiita.com/fy406/items/6dd2de050e875e52d36a/likers)

いいねしたユーザー一覧へ移動

0