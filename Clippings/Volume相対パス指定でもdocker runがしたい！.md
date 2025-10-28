---
title: "Volume相対パス指定でもdocker runがしたい！"
source: "https://qiita.com/KentoDodo/items/24117025924d64998110"
author:
  - "[[KentoDodo]]"
published: 2017-09-19
created: 2025-08-26
description: "結論 相対パスの先頭に$PWDを付けることで解決できます。 現象 docker runのVolumeを相対パス指定がしたい。 なぜなら、例えばGitHubのREADME.mdのUsageを書くときに、「このファイルのあるディレクトリ上で以下のコマンドを実行してね」という..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

## 結論

相対パスの先頭に `$PWD` を付けることで解決できます。

## 現象

`docker run` のVolumeを相対パス指定がしたい。  
なぜなら、例えばGitHubの `README.md` のUsageを書くときに、「このファイルのあるディレクトリ上で以下のコマンドを実行してね」という形で実行コマンドを書きたいからです。絶対パスで記述してしまうと、そのまま使えないのです。

ところが・・・

```text
$ docker build -t original_ubuntu .  # dockerbuildファイルが現在いるディレクトリ内に存在しているものとする
$ docker run -it -v .:/temp original_ubuntu
docker: Error response from daemon: create .: volume name is too short, names should be at least two alphanumeric characters.
See 'docker run --help'.
```

ひえぇ〜😅  
どうやら、 `-v` のホスト側ディレクトリは絶対パスもしくは名前を指定するということで、相対パスの指定はできないようです。  
（参考： [http://docs.docker.jp/engine/reference/run.html#volume](http://docs.docker.jp/engine/reference/run.html#volume) ）

## 解決法

## (簡単)$PWDをつける

相対パスの冒頭に環境変数 `$PWD` を付ければ解決できます。

```text
$ docker run -it -v $PWD:/temp original_ubuntu
```

MacOSXやUbuntuなどでは、環境変数 `$PWD` によって実行中のディレクトリの絶対パスを取得できるのを利用しています。  
なお、万が一、環境変数 `$PWD` が設定されていないOS上で実行する場合は、上述の `$PWD` を `$(pwd)` に置き換えてください。

## docker-composeを使う

`docker-compose` の `volumes` を使えば解決できます。（これだけのために使うのは変ですが。。。）

具体的には、まず `docker-compose` をインストールします。（詳細な説明は省略します。）

次に、 `dockerfile` と同じディレクトリに `docker-compose.yml` を用意します。

docker-compose.yml

```yaml
version: "3"

services:
  main:
    build:
      context: .
      dockerfile: dockerfile
    container_name: original_ubuntu
    volumes:
      - ./:/temp
```

最後に、以下のコマンドを実行すれば、起動します。

```text
$ docker-compose run main
```

## 最後に

大きなプロジェクトでは環境変数などでパスの管理をする方が良いのでしょうが、  
小さなプロジェクトやテスト的に色々試してみた結果を残したいときに是非お使いください。

[0](https://qiita.com/KentoDodo/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2FKentoDodo%2Fitems%2F24117025924d64998110&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2FKentoDodo%2Fitems%2F24117025924d64998110&realm=qiita)

[65](https://qiita.com/KentoDodo/items/24117025924d64998110/likers)

いいねしたユーザー一覧へ移動

46