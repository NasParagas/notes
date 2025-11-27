---
title: "未使用のコンテナ、volumeなどを一括削除"
source: "https://qiita.com/reflet/items/5caa88abcf1e8964783a"
author:
  - "[[reflet]]"
published: 2018-03-08
created: 2025-11-26
description: "概要 dockerを運用していると不要なコンテナやvolumeなどが溜まっている場合があるので、一括削除するためのメモを残す。 停止コンテナ一括削除 status=exitedで停止コンテナの一覧を取得して、一括削除する。 ※ docker container prun..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

## 概要

dockerを運用していると不要なコンテナやvolumeなどが溜まっている場合があるので、一括削除するためのメモを残す。

## 停止コンテナ一括削除

status=exitedで停止コンテナの一覧を取得して、一括削除する。  
※ [docker container prune | Docker Documentation](https://docs.docker.com/engine/reference/commandline/container_prune/)

ターミナル

```bash
$ docker rm \`docker ps -f "status=exited" -q\`

or

$ docker container prune
```

## 未使用のVolumeを一括削除

dangling=trueでコンテナから参照されていないVolumeの一覧を取得して、一括削除する。  
※ [docker volume prune | Docker Documentation](https://docs.docker.com/engine/reference/commandline/volume_prune/)

ターミナル

```bash
$ docker volume rm $(docker volume ls -qf dangling=true)

or

$ docker volume prune
```

## 未使用のイメージを一括削除

※ [docker image prune | Docker Documentation](https://docs.docker.com/engine/reference/commandline/image_prune/)

ターミナル

```bash
$ docker image prune

$ docker image prune -a
```

## 未使用のネットワークを一括削除

※ [docker network prune | Docker Documentation](https://docs.docker.com/engine/reference/commandline/network_prune/)

ターミナル

```bash
$ docker network prune
```

## 未使用なコンテナ, イメージ, ネットワークを一括削除

※ [docker system prune | Docker Documentation](https://docs.docker.com/engine/reference/commandline/system_prune/)

ターミナル

```bash
$ docker system prune

# volumeも破棄する場合はこちら
$ docker system prune --volumes
```

## 参考サイト

- [Docker一括削除コマンドまとめ](https://qiita.com/boiyaa/items/9972601ffc240553e1f3)
- [\[Docker\]未使用のVolumeを一括削除](https://cheaparchitec.wordpress.com/2016/07/11/docker%E6%9C%AA%E4%BD%BF%E7%94%A8%E3%81%AEvolume%E3%82%92%E4%B8%80%E6%8B%AC%E5%89%8A%E9%99%A4/)

[0](https://qiita.com/reflet/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Freflet%2Fitems%2F5caa88abcf1e8964783a&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Freflet%2Fitems%2F5caa88abcf1e8964783a&realm=qiita)

## Qiita Advent Calendar 開催！

[237](https://qiita.com/reflet/items/5caa88abcf1e8964783a/likers)

いいねしたユーザー一覧へ移動

198