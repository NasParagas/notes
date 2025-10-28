---
title: "【find・grep】特定の文字列を含むファイルのリストを取得する方法。"
source: "https://qiita.com/pokari_dz/items/0f14a21e3ca3df025d21"
author:
  - "[[pokari_dz]]"
published: 2016-10-12
created: 2025-08-26
description: "「○○」という文字列を含むファイルをリスト化したい時は、以下のコマンドで。 毎回ぐぐってるので、いいかげんメモ。 ■ファイル名に「○○」という文字列が含まれているファイルのリストを取得。 指定フォルダ直下のみ ls [検索対象フォルダのパス] | grep \"[検索した..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

「○○」という文字列を含むファイルをリスト化したい時は、以下のコマンドで。  
毎回ぐぐってるので、いいかげんメモ。

#### ■ファイル名に「○○」という文字列が含まれているファイルのリストを取得。

指定フォルダ直下のみ

```txt
ls [検索対象フォルダのパス] | grep "[検索したい文字列]"
```

指定フォルダ配下を再帰検索

```txt
find [検索対象フォルダのパス] -type f -name "*[検索したい文字列]*"
```

#### ■ファイルを開いた中身に、「○○」という文字列が含まれているファイルのリスト。

指定フォルダ配下を再帰検索

```txt
grep [検索したい文字列] -rl [検索対象フォルダのパス]
```

※いままで↓を使っていましたが、コメントで上記grepコマンドを使う方法を教えていただきました。（こっちの方が簡単！）

指定フォルダ配下を再帰検索

```txt
find [検索対象フォルダのパス] -type f -print | xargs grep '[検索したい文字列]'
```

[5](https://qiita.com/pokari_dz/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fpokari_dz%2Fitems%2F0f14a21e3ca3df025d21&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fpokari_dz%2Fitems%2F0f14a21e3ca3df025d21&realm=qiita)

[878](https://qiita.com/pokari_dz/items/0f14a21e3ca3df025d21/likers)

いいねしたユーザー一覧へ移動

763