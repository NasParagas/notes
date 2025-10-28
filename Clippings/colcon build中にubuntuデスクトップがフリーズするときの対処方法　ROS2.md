---
title: "colcon build中にubuntuデスクトップがフリーズするときの対処方法　ROS2"
source: "https://qiita.com/porizou1/items/efe537a6d427eff4a29f"
author:
  - "[[porizou1]]"
published: 2021-06-19
created: 2025-07-07
description: "ラズパイなどのメモリの少ないPCでROS2を使っていて、SLAMなどの重いパッケージをcolcon build する際に画面がフリーズすることがあります。 その時の対応方法のメモ。 MAKEFLAGS環境変数を設定する colcon build前にMAKEFLAGS環境変..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から1年以上が経過しています。

ラズパイなどのメモリの少ないPCでROS2を使っていて、SLAMなどの重いパッケージをcolcon build する際に画面がフリーズすることがあります。

その時の対応方法のメモ。

## MAKEFLAGS環境変数を設定する

colcon build前にMAKEFLAGS環境変数で並列処理する数を指定

例

```bash
cd ~/ros2_ws
MAKEFLAGS=-j1
colcon build
```

## colcon buildのオプションを使用する

colcon buildの--parallel-workersオプションで同時にビルドするパッケージ数を指定

例

```bash
cd ~/ros2_ws
colcon build --parallel-workers 1
```

## メモリのSWAP領域を増やす

## 参考

colconビルドで便利なオプション

[0](https://qiita.com/porizou1/items/#comments)

コメント一覧へ移動

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fporizou1%2Fitems%2Fefe537a6d427eff4a29f&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fporizou1%2Fitems%2Fefe537a6d427eff4a29f&realm=qiita)

この記事は以下の記事からリンクされています

- [ROS2 HumbleでT265を使う](https://qiita.com/sfc_nakanishi_lab/items/c0e326686cb2b2eaaed2)
- [ubuntu22.04 ROS 2 Humble環境にAutoware Universeをインストールする手順（Source installation）](https://qiita.com/porizou1/items/c9384aaa5fbe1125bf20)

[3](https://qiita.com/porizou1/items/efe537a6d427eff4a29f/likers)

いいねしたユーザー一覧へ移動

1