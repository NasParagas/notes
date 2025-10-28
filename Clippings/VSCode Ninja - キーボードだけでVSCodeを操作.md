---
title: "VSCode Ninja - キーボードだけでVSCodeを操作"
source: "https://qiita.com/howmuch515/items/cb702993e2d005d2cd61"
author:
  - "[[howmuch515]]"
published: 2025-02-16
created: 2025-09-29
description: "1. はじめに デフォルトのVSCodeの画面は情報が多すぎる！ということで本記事ではVimmerのように無駄のないシンプルな画面で、極力マウスを使わずに快適にコーディングできる「VSCode Ninja」スタイル(勝手に名前つけた)を目指します。 このスタイルの核とな..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiita Conference 2025 Autumn

![](https://cdn.qiita.com/assets/public/client-resources/image-yukihiro_matz-62ee5a1ddc705baf-62ee5a1ddc705baf.png)

まつもとゆきひろ

ベテランによるAI時代のプログラミング（2025年秋版）

[特設サイトで詳細をチェック](https://qiita.com/official-campaigns/conference/2025-autumn)

[@howmuch515](https://qiita.com/howmuch515)

## VSCode Ninja - キーボードだけでVSCodeを操作

最終更新日 投稿日 2025年02月16日

[![](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/57640/d99d6121-22c1-4ebe-90fb-b8b8dc2b96a5.jpeg)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F57640%2Fd99d6121-22c1-4ebe-90fb-b8b8dc2b96a5.jpeg?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=60880983d930cf6dacd482938afd222c)

## 1\. はじめに

デフォルトのVSCodeの画面は情報が多すぎる！ということで本記事ではVimmerのように無駄のないシンプルな画面で、極力マウスを使わずに快適にコーディングできる「VSCode Ninja」スタイル(勝手に名前つけた)を目指します。

このスタイルの核となるのが **Zenモード** です。不要なUIを排除し、純粋にコードに集中できる環境を提供します。そして、Zenモードを最大限に活かすため、Neovim拡張、Quick Open、キーバインドのカスタマイズを駆使して操作性を極限まで高めることを目指します。

## 2\. Zenモードの活用

Zenモードは、VSCodeをシンプルで没入感のある環境にするための重要な機能です。 **VSCode Ninja** のような環境を作るための中心となる要素であり、これを活用することで、余計なUIを削減し、最大限に集中できる環境を構築できます。

- **起動方法** ： `cmd + k, z`
- **主な利点** ：
	- 余計なUI要素を削減し、作業に集中しやすくなる
	- キーボード主体の操作を促進する
	- より高速にコードを書く環境を構築できる

このZenモードを使いやすくするために、Neovim拡張やQuick Open、各種キーバインドの修正を行っています。

## 3\. Vimキーバインドの導入

Vimキーバインドを導入する方法はいくつかありそうですが、VSCode Neovimがおすすめです。VSCodeの裏で本物のNeovimを動かしているため、キーバインドの再現度が高く高速で動きます。

よくVimキーバインドだと、コード定義に移動できないのでは？という声を聞きますが、関数などの上にカーソルを移動させ、 `gd` で定義に移動できます。

## 4\. キーバインド設定

マウスを使わずにキーボードのみで操作するためにキーバインドの設定もいじりましょう。自分は以下のような設定にしています。

## 4.1 エディタのキーバインド設定

| キーバインド | 機能 |
| --- | --- |
| `cmd + k, e` | ファイルエクスプローラを開く |
| `cmd + k, g` | Gitビューを開く |
| `cmd + f` | 全文検索 & 置換 |
| `cmd + [` / `cmd + ]` | 戻る / 進む |
| `shift + cmd + [` / `shift + cmd + ]` | 前のエディタに移動 / 次のエディタに移動 |

## 4.2 ターミナルのキーバインド設定

| キーバインド | 機能 |
| --- | --- |
| `cmd + d` | ターミナルを横に分割 |
| `cmd + t` | 新しいタブ(ターミナルグループ)を開く |
| `cmd + w` | ターミナルを閉じる |
| `cmd + [` / `cmd + ]` | 前のターミナルペインに移動 / 次のターミナルペインに移動 |
| `shift + cmd + [` / `shift + cmd + ]` | 前のタブ(ターミナルグループ)に移動 / 次のタブに移動 |

## 5\. マウス操作を減らすテクニック

## ファイル名検索 & 開く

コマンドパレット(`cmd + p`)でマウスに触れることなく、ファイル検索 & 開くがスムーズに行えます。

## 拡張機能などサイドバーへのアクセス

quick open view(`ctrl + q`)でZenモードを解除せず、サイドバーの項目にアクセスできます。 `ctrl + q` を連続押しすることで次の項目に移動できます。

また、プライマリサイドバーは `cmd + b` 、セカンダリサイドバーは `shift + cmd + b` で開閉できるようにショートカットを割り当てた。

## 6\. まとめ

VSCode Ninjaを目指すためには、Zenモードを中心に環境を最適化し、キーボード操作を徹底的にカスタマイズすることが鍵となります。

今回紹介した設定を導入することで、マウスを使わずに迅速にコードを書ける環境を手に入れることができます。ぜひ、自分の作業スタイルに合った設定を試し、究極の生産性を目指しましょう！

[0](https://qiita.com/howmuch515/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fhowmuch515%2Fitems%2Fcb702993e2d005d2cd61&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fhowmuch515%2Fitems%2Fcb702993e2d005d2cd61&realm=qiita)

[![howmuch515](https://qiita-user-profile-images.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F57640%2Fprofile-images%2F1729963781?ixlib=rb-4.0.0&auto=compress%2Cformat&lossless=0&w=128&s=362c05980638dd44e1a7e7d129e9bcad)](https://qiita.com/howmuch515)

[

## @howmuch515

](https://qiita.com/howmuch515)

Hello, world!

[RSS](https://qiita.com/howmuch515/feed)

## Qiita Conference 2025 Autumn 11月5日(水)~7日(金)開催！

![](https://cdn.qiita.com/assets/public/official_campaigns/qiita_conference_2025_autumn/image-conference_2025_autumn_ogp-7cf3021de31b9ab76a7b3bbaf2909bb5.png)

Qiita Conferenceは、AI時代のエンジニアに贈るQiita最大規模のテックカンファレンスです！

基調講演ゲスト(敬称略)

piacere、牛尾 剛、Esteban Suarez、和田 卓人、seya、ミノ駆動、市谷 聡啓、からあげ、まつもとゆきひろ、みのるん

[イベント詳細を見る](https://qiita.com/official-campaigns/conference/2025-autumn)

[4](https://qiita.com/howmuch515/items/cb702993e2d005d2cd61/likers)

いいねしたユーザー一覧へ移動

0