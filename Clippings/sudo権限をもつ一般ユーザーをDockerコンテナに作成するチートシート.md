---
title: "sudo権限をもつ一般ユーザーをDockerコンテナに作成するチートシート"
source: "https://qiita.com/nishina555/items/334f93acd54a919f8206"
author:
  - "[[nishina555]]"
published: 2018-07-29
created: 2025-08-26
description: "プログラミングをしていて「検証環境をつくりたいなー」というときにDockerは非常に役にたちます。 シェルスクリプトのテストをするために『sudo権限をもった一般ユーザーが存在する環境』をDockerで構築したので、環境構築のチートシートを今回用意しました。 一般ユーザーの..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

プログラミングをしていて「検証環境をつくりたいなー」というときにDockerは非常に役にたちます。

シェルスクリプトのテストをするために『sudo権限をもった一般ユーザーが存在する環境』をDockerで構築したので、環境構築のチートシートを今回用意しました。

一般ユーザーの作成手順は、Dockerだけでなく、VPSを借りたときにも利用できるので活用していただければと思います。

## 今回作るDockerコンテナ

作成するコンテナは以下のようなスペックです

- OSはubuntu
- isuconというsudo権限をもつユーザーが存在する
- 一般ユーザー(isucon)のログインシェルは/bin/bash
- 最低限のコマンド(git, less, vimなど)がインストールされている

## Dockerコンテナを立ち上げ、ログインする

`docker run -it` でコンテナの立ち上げてコンテナにログインできます。

今回はubuntuのDockerイメージからshell\_containerというコンテナ名のDockerコンテナを作成しています。

```text
$ docker run -it --name shell_container ubuntu /bin/bash
```

## 必要なコマンドのインストール

ubuntuコンテナには基本的なコマンドもまだ用意されていないので、最低限のコマンドをインストールします

```text
$ apt-get update && apt-get install -y git less vim sudo
```

## sudo権限をもつユーザーを作成する

ユーザーの作成をします。-mでホームディレクトリも作成します。

```text
$ useradd -m isucon
```

isuconユーザーのパスワードを作成します。

```text
$ passwd isucon
```

isuconユーザーにsudo権限を与えます

```text
$ usermod -aG sudo isucon
```

isuconユーザーのログインシェルを変更します。

```text
$ usermod -s /bin/bash isucon
```

## (補足) 作成したDockerコンテナを再利用する場合

docker commitをすることでコンテナの環境をDockerイメージとして保存することができます。

以下のコマンドでshell\_containerというコンテナをshell\_sandboxというイメージ名で保存できます。

```text
$ docker commit shell_container shell_sandbox
```

以下のコマンドでcommitしたshell\_sandboxのイメージからコンテナを立ち上げることができます。

```text
$ docker run -it shell_sandbox /bin/bash
```

## まとめ

今回はdocker runを利用して『sudo権限をもった一般ユーザーが存在する環境』を作る手順の説明をしました。  
今回の詳細は [Dockerでサクっと検証環境(テスト環境)を構築するための手順](https://nishinatoshiharu.com/create-sandbox-with-docker/) でも解説をしているので興味のあるかたはご覧になってください。

by [@nishina555](https://twitter.com/nishina555)

[0](https://qiita.com/nishina555/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fnishina555%2Fitems%2F334f93acd54a919f8206&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fnishina555%2Fitems%2F334f93acd54a919f8206&realm=qiita)

[6](https://qiita.com/nishina555/items/334f93acd54a919f8206/likers)

いいねしたユーザー一覧へ移動

11