---
title: "コマンドラインからmp4を倍速に変換する"
source: "https://qiita.com/seigot/items/4d89c42a992569fa1890"
author:
  - "[[seigot]]"
published: 2021-01-22
created: 2025-08-28
description: "コマンドラインからmp4を倍速に変換する備忘録 ffmpegを使って倍速にします 入力：src.mp4 出力：dst.mp4 インストール sudo apt install ffmpeg （例）3.3倍速に変換する ffmpeg -i src.mp4 -vf se..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiitaにログインして、便利な機能を使ってみませんか？

[ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fseigot%2Fitems%2F4d89c42a992569fa1890&realm=qiita) [新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fseigot%2Fitems%2F4d89c42a992569fa1890&realm=qiita)

この記事は最終更新日から3年以上が経過しています。

コマンドラインからmp4を倍速に変換する備忘録

## ffmpegを使って倍速にします

入力： `src.mp4`  
出力： `dst.mp4`

インストール

```sh
sudo apt install ffmpeg
```

（例）3.3倍速に変換する

```sh
ffmpeg -i src.mp4 -vf setpts=PTS/3.3 -af atempo=3.3 dst.mp4
```

（例）1.5倍速に変換する

```sh
ffmpeg -i src.mp4 -vf setpts=PTS/1.5 -af atempo=1.5 dst.mp4
```

## ffmpegで動画から領域を切り出す

`(x,y)=(0,30)` から `1920*950` の領域を切り出す場合

```text
# 事前確認
ffplay -i src.mp4 -vf crop=1920:950:
# 変換
ffmpeg -i src.mp4 -vf crop=1920:950:0:30 dst.mp4
```

## ffmpegで先頭3secを削除

```text
# 事前確認
ffplay -ss 3 -i src.mp4 -c copy
# 変換
ffmpeg -ss 3 -i src.mp4 -c copy dst.mp4
```

## 動画のカットと結合

#### カット

`src.mp4` の `0sec` から `1sec` をカットする場合

```text
ffmpeg -ss 0 -i src.mp4 -t 1 -c copy dst.mp4
```

#### 結合

`part1.mp4` と `part2.mp4` を結合する

事前に結合対象のファイルを `mylist.txt` に列挙

mylist.txt

```sh
file /hoge/fuga/part1.mp4
file /hoge/fuga/part2.mp4
```

以下を実行

```text
ffmpeg -safe 0 -f concat -i mylist.txt -c copy dst.mp4
```

[Ubuntu: ffmpegでmp4のカットと結合](https://www.kwonline.org/memo2/2019/03/07/ffmpeg-cut-and-concat-mp4-on-ubuntu/)

## 参考

[ffmpegを使って動画の再生速度を変えてみる](http://fftest33.blog.fc2.com/blog-entry-36.html)

[0](https://qiita.com/seigot/items/#comments)

コメント一覧へ移動

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fseigot%2Fitems%2F4d89c42a992569fa1890&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fseigot%2Fitems%2F4d89c42a992569fa1890&realm=qiita)

[7](https://qiita.com/seigot/items/4d89c42a992569fa1890/likers)

いいねしたユーザー一覧へ移動

10