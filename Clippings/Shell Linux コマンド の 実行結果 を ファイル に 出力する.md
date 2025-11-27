---
title: "Shell Linux コマンド の 実行結果 を ファイル に 出力する"
source: "https://qiita.com/miriwo/items/4e9f48af5d22b0ab8548"
author:
  - "[[miriwo]]"
published: 2019-10-05
created: 2025-11-07
description: "目的 Linuxでコマンドを実行したときのすべてのログをファイルに記載し、ターミナル上にも出力する方法を知る。 書き方の例 下記にShellの処理を記載する。 $ 実行コマンド 2>&1 | tee 結果を記載するファイル名 より具体的な例 コマンドech..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

## 目的

- Linuxでコマンドを実行したときのすべてのログをファイルに記載し、ターミナル上にも出力する方法を知る。

## 書き方の例

- 下記にShellの処理を記載する。

```shell
$ 実行コマンド 2>&1 | tee 結果を記載するファイル名
```

## より具体的な例

- コマンド `echo "Hello miriwo"` の実行結果をテキストファイル「result.txt」に記載する。
- 下記にShellの処理を記載する。

```shell
$ echo "Hello miriwo" 2>&1 | tee result.txt
```

[0](https://qiita.com/miriwo/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fmiriwo%2Fitems%2F4e9f48af5d22b0ab8548&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fmiriwo%2Fitems%2F4e9f48af5d22b0ab8548&realm=qiita)

この記事は以下の記事からリンクされています

- [Qiitaを1年間ほぼ毎日投稿した話と転職の話](https://qiita.com/miriwo/items/681ff186f4d6f9057135)
- [本日までの自分の記事一覧](https://qiita.com/miriwo/items/18271aa5503ab5799d8a)

## Qiita Conference 2025 Autumn 11月5日(水)~7日(金)開催！

![](https://cdn.qiita.com/assets/public/official_campaigns/qiita_conference_2025_autumn/image-conference_2025_autumn_ogp-d35d2500cd0420d93f2ed69a8d162d74.png)

Qiita Conferenceは、AI時代のエンジニアに贈るQiita最大規模のテックカンファレンスです！

基調講演ゲスト(敬称略)

piacere、牛尾 剛、Esteban Suarez、和田 卓人、seya、ミノ駆動、市谷 聡啓、からあげ、岩瀬 義昌、まつもとゆきひろ、みのるん、 Null-Sensei

[イベント詳細を見る](https://qiita.com/official-campaigns/conference/2025-autumn)

## Qiita Advent Calendar 開催！

[2](https://qiita.com/miriwo/items/4e9f48af5d22b0ab8548/likers)

いいねしたユーザー一覧へ移動

2