---
title: "scpコマンドでサーバー上のファイルorディレクトリをローカルに落としてくる"
source: "https://qiita.com/katsukii/items/225cd3de6d3d06a9abcb"
author:
  - "[[katsukii]]"
published: 2015-02-08
created: 2025-08-26
description: "基本 scpは、sshを使ってネットワーク・ホスト間でファイルを安全にコピーするためのコマンド。 scp -i ~/.ssh/secret.pem -r ec2-user@ec2-54-00-00-00.ap-northeast-1.compute.amazonaws.c..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

## 基本

scpは、sshを使ってネットワーク・ホスト間でファイルを安全にコピーするためのコマンド。

```text
scp -i ~/.ssh/secret.pem -r ec2-user@ec2-54-00-00-00.ap-northeast-1.compute.amazonaws.com:/remote/path /local/path
```

## リモートからローカルにコピー

```text
$ scp ユーザ名@リモートのホスト名:コピーしたいリモートのファイル ローカルのコピー先

$ scp user@remoteHost:/home/user/test.txt /local/path
```

## ローカルからリモートにコピー

```text
$ scp コピーしたいローカルのファイルパス ユーザ名@リモートのホスト名:保存したいパス

$ scp /local/test.txt user@remoteHost:/home/user/tmp/
```

## ディレクトリごとコピーしたい場合

\-rオプションを使う。（この場合はリモート→ローカル）

```text
$ scp -r user@remoteHost:/remote/dir /local/dir
```

## 秘密鍵で接続しないといけない場合

```text
scp -i ~/.ssh/secret.pem -r ec2-user@ec2-54-00-00-00.ap-northeast-1.compute.amazonaws.com:/remote/path /local/path
```

## こんなエラーが出た時

## not a regular file

\-rオプションをつけて実行すればOK。

※-rはディレクトリ全体をコピーするオプション

## オプションの説明はこちら

[1](https://qiita.com/katsukii/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fkatsukii%2Fitems%2F225cd3de6d3d06a9abcb&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fkatsukii%2Fitems%2F225cd3de6d3d06a9abcb&realm=qiita)

[![katsukii](https://qiita-user-profile-images.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F18614%2Fprofile-images%2F1473682483?ixlib=rb-4.0.0&auto=compress%2Cformat&lossless=0&w=128&s=f679fe85001b88dd514232e36ce0dae3)](https://qiita.com/katsukii)

[

## @katsukii(雄介 香月)

](https://qiita.com/katsukii)

[RSS](https://qiita.com/katsukii/feed)## [株式会社ペライチ](https://qiita.com/organizations/hotstartupinc)

「ペライチ」を開発する会社です。

[https://peraichi.com/](https://peraichi.com/)

[1015](https://qiita.com/katsukii/items/225cd3de6d3d06a9abcb/likers)

いいねしたユーザー一覧へ移動

874