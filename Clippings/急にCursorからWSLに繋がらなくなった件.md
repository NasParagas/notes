---
title: "急にCursorからWSLに繋がらなくなった件"
source: "https://qiita.com/Common_/items/8f7d3fef246175477bb7"
author:
  - "[[Common_]]"
published: 2025-02-18
created: 2025-10-27
description: "背景 普段通りCursorでWSLを立ち上げようとしたところ、失敗した。なんか左下の >< GitHub とか表示されるところに、△WSL: Ubuntu-24.024から切断されました と表示されている。 なお、その状態でもターミナルからwslに入ることはできた。 ..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## 背景

普段通りCursorでWSLを立ち上げようとしたところ、失敗した。なんか左下の `>< GitHub` とか表示されるところに、 `△WSL: Ubuntu-24.024から切断されました` と表示されている。

[![スクリーンショット 2025-02-17 212229.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/4011379/623ee0e2-2e63-49e9-a51f-ce46fa67d76d.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F4011379%2F623ee0e2-2e63-49e9-a51f-ce46fa67d76d.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=f937528329d8258f1073640260ec03c6)

なお、その状態でもターミナルからwslに入ることはできた。

## エラー内容

ログを確認する：

Filesync

```bash
[error] Failed to fetch fsConfig File sync is disabled
```

FileSyncに関するエラー。WSLの直接の問題ではない。

ウィンドウ

```bash
[error] No remote extension installed to resolve wsl.:
CodeExpectedError: No remote extension installed to resolve wsl.
```

WSL を扱う Remote - WSL 拡張機能がインストールされていない か、正常に動作していない。

ターミナル

```bash
[warning]
RemoteTerminalBackend#getTerminalLayoutInfo Error
["No remote extension installed to resolve wsl."]
```

ターミナルが WSL に接続できない理由として、WSL のリモート環境がセットアップされていない。

リモートトンネルサービス

```bash
[error] Missing 'tunnelApplicationConfig' or 'tunnelApplicationName' in product.json. Remote tunneling is not available.
```

Remote トンネル (リモート開発用の接続機能) が壊れている可能性。

## 解決策

Remote -WSL拡張を再インストール

1. Remote - WSLをアンインストール
2. Cursorを再起動
3. Remote -WSLをインストール
4. WSLに接続

ここで接続に成功した。

## Ref

ChatGPT にエラー内容全部投げて質問した。  
Remote -WSLをアンストして再インストールは一度やったが、成功したときは一々Cursorの再起動したからそれのお陰だと思う。

たぶんエラーログにあったとおりWSL拡張機能が正常に動作してなかったんだと思う。

```bash
~/.vscode-remote-containers/dist$ la
vscode-remote-containers-server-0.385.0.js
vscode-remote-containers-server-0.394.0.js
```

上記コマンドを試してみると、 `vscode-remote-containers-server-0/XXX.0.js` が失敗したときは2種類あった。正常に動いているときは1つだけだったので、このファイルが競合したかも。

WSLが動かなくなると困るので、また今度同じようなエラーが発生したら試してみることにする。

## 追記

またなったから今度は、 `~/.vscode-remote-containers/dist` の中身を全削除してから Cursor を再起動したら繋がった。

今回も、dist の中には jsファイルが複数あった

[0](https://qiita.com/Common_/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2FCommon_%2Fitems%2F8f7d3fef246175477bb7&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2FCommon_%2Fitems%2F8f7d3fef246175477bb7&realm=qiita)

[![Common_](https://qiita-user-profile-images.imgix.net/https%3A%2F%2Favatars.githubusercontent.com%2Fu%2F114040705%3Fv%3D4?ixlib=rb-4.0.0&auto=compress%2Cformat&lossless=0&w=128&s=b16903637935759caea912b68fdcddb2)](https://qiita.com/Common_)

[

## @Common\_

](https://qiita.com/Common_)

[RSS](https://qiita.com/Common_/feed)

## Qiita Conference 2025 Autumn 11月5日(水)~7日(金)開催！

![](https://cdn.qiita.com/assets/public/official_campaigns/qiita_conference_2025_autumn/image-conference_2025_autumn_ogp-d35d2500cd0420d93f2ed69a8d162d74.png)

Qiita Conferenceは、AI時代のエンジニアに贈るQiita最大規模のテックカンファレンスです！

基調講演ゲスト(敬称略)

piacere、牛尾 剛、Esteban Suarez、和田 卓人、seya、ミノ駆動、市谷 聡啓、からあげ、岩瀬 義昌、まつもとゆきひろ、みのるん and more…

[イベント詳細を見る](https://qiita.com/official-campaigns/conference/2025-autumn)

[1](https://qiita.com/Common_/items/8f7d3fef246175477bb7/likers)

いいねしたユーザー一覧へ移動

2