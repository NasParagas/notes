---
title: "Dockerでホストとコンテナ間でのファイルコピー"
source: "https://qiita.com/gologo13/items/7e4e404af80377b48fd5"
author:
  - "[[gologo13]]"
published: 2014-06-01
created: 2025-08-26
description: "コンテナからホストへのコピー docker cp コマンドが使えます。 # コンテナIDを調べる sudo docker cp <コンテナID>:/etc/my.cnf my.cnf ホストからコンテナへのコピー （追記：201..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

[@gologo13 (Yamaguchi Yohei)](https://qiita.com/gologo13)

最終更新日 投稿日 2014年06月01日

## コンテナからホストへのコピー

docker cp コマンドが使えます。

```sh
# コンテナIDを調べる
$ sudo docker ps

$ sudo docker cp <コンテナID>:/etc/my.cnf my.cnf
```

## ホストからコンテナへのコピー

（追記：2016/01/22）  
[Docker 1.8](http://blog.docker.com/2015/08/docker-1-8-content-trust-toolbox-registry-orchestration/) からホストからコンテナへのコピーも docker cp コマンドでサポートされるようになりました！  
[https://docs.docker.com/engine/reference/commandline/cp/](https://docs.docker.com/engine/reference/commandline/cp/)

```text
$ sudo docker cp my.cnf <コンテナID>:/etc/my.cnf
```

~~こちらは `docker cp` コマンドで実現することはできません。~~  
~~現状、Dockerfile に記述して、コピーするしかないみたいです。~~

~~`ADD my.cnf /etc/my.cnf`~~

~~ちなみに、 `docker cp` コマンドでホストからコンテナへのコピーをサポートしてほしいという要望がコミュニティ内でも根強くあるみたいです。~~

~~[dotcloud/docker #905](https://github.com/dotcloud/docker/issues/905)~~

[3](https://qiita.com/gologo13/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fgologo13%2Fitems%2F7e4e404af80377b48fd5&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fgologo13%2Fitems%2F7e4e404af80377b48fd5&realm=qiita)

[![gologo13](https://qiita-user-profile-images.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F1132%2Fprofile-images%2F1473682903?ixlib=rb-4.0.0&auto=compress%2Cformat&lossless=0&w=128&s=6c72a0dab14e26e7393094bbd2c8858c)](https://qiita.com/gologo13)

[

## @gologo13(Yamaguchi Yohei)

](https://qiita.com/gologo13)

[RSS](https://qiita.com/gologo13/feed)

[623](https://qiita.com/gologo13/items/7e4e404af80377b48fd5/likers)

いいねしたユーザー一覧へ移動

531