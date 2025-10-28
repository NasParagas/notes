---
title: "【図解】Dockerの全体像を理解する -前編-"
source: "https://qiita.com/etaroid/items/b1024c7d200a75b992fc"
author:
  - "[[etaroid]]"
published: 2019-01-15
created: 2025-08-21
description: "この記事は何か イメージやコンテナなどの基本からdocker-compose、docker-machine, docker swarmなどのDocker周りの様々な概念の全体像を整理して、Dockerの仕組みを理解するための記事 前編では「コンテナ、イメージ、Docke..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から3年以上が経過しています。

[![docker.png](https://qiita-image-store.s3.amazonaws.com/0/209909/38170416-92c6-512d-e356-96be623ac3ca.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F209909%2F38170416-92c6-512d-e356-96be623ac3ca.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=3d615a0fc79f7648c001de66d6d5a681)

## この記事は何か

イメージやコンテナなどの基本からdocker-compose、docker-machine, docker swarmなどのDocker周りの様々な概念の **全体像を整理して、Dockerの仕組みを理解する** ための記事

前編では「コンテナ、イメージ、DockerHubでのイメージ共有」について書いて行きます。

### 対象読者

・Dockerって何？  
・Dockerちょっと勉強したけどDocker compose？ Docker machine？ Docker Swarm？ 色々ありすぎて意味不明  
という方

## Dockerとは何か

Docker社が提供する\*\*「コンテナ型仮想化技術」\*\* を実現するプロダクト

## 仮想化？

PCやサーバといったマシンにインストールされているOS(ホストOS)の上に、別のマシンを仮想的に立ち上げる事

簡単に言うと「パソコンの中に仮想パソコンを起動する」のが仮想化です。

従来の仮想化(ホスト型仮想化)は以下の図のように、ホストOS上に仮想化ソフト/ハイパーバイザを使用して、仮想マシン/ゲストOSを立ち上げ、その仮想マシンの中でミドルウェアの環境構築をし、プログラムを実行してアプリケーションを動かすという構造になっています。

[![スクリーンショット 2019-01-16 17.06.13.png](https://qiita-image-store.s3.amazonaws.com/0/209909/4d473346-7045-e969-765f-3489c56d4145.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F209909%2F4d473346-7045-e969-765f-3489c56d4145.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=43004fb9c9e7659daab0c63aaed74f4c)

## コンテナ型仮想化？

一方で、Dockerが提供するのは **コンテナ型仮想化** です。

コンテナ型仮想化は以下の図のような構造になっています。

[![スクリーンショット 2019-01-15 16.03.20.png](https://qiita-image-store.s3.amazonaws.com/0/209909/99612c11-42b0-9485-7ba4-203b118b9545.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F209909%2F99612c11-42b0-9485-7ba4-203b118b9545.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=487b628fe2aaf21d8de97c62947936b9)

従来のホスト型仮想化とは異なり、ゲストOSを起動せずに、ホストOSの上に動作しているDocker Engineから **コンテナと呼ばれるミドルウェアの環境構築がされた実行環境を作成し、その中でアプリケーションを動作させます。**

これによって従来のホスト型仮想化よりも圧倒的に **軽量に動作する** のがコンテナ型仮想化の特徴です。

## で、Docker使うと何が嬉しいの？

世の中のほとんどのソフトウェアは1人のプログラマの力で開発されているわけではなく、複数の開発者が協力して作成していますが、複数人でアプリケーション/システム開発を円滑に進めるにはどうしたら良いでしょうか。

最も重要なプログラムコードの共有には、今日Gitというバージョン管理の仕組みが使用され、自分の書いたプログラムコードをチームで共有するためのサービスとしてGithubやBitbucket、Gitlabなどがあります。

ですが、プログラムコードを円滑に共有することができたとしても、その **プログラムの実行環境を共有しないとプログラムが正常に動作しない** ということは多々あります。

開発者が使用しているコンピュータはMac OSX、Windowsなどの違いがあったり、人によって様々なソフトウェアが入っているなど、実行環境を共有するのは容易ではありません。  
そこで、仮想化によってフラットな仮想マシンを作成し、その仮想マシンを作成する設定などを共有することで実行環境を共有することが考えられますが、従来のホスト型仮想化では、動作がとても重く、共有するためのファイルも軽量とは言えませんでした。

一方で、Dockerのコンテナ型仮想化は、軽量な動作と環境ファイル共有を実現しています。  
**プログラムの実行環境を管理するための仕組みとしてDocker** があり、 **それを共有するための仕組みとして [DockerHub](https://hub.docker.com/) などのサービス** があります。

Dockerを使用することの一番のメリットは「ソフトウェアの実行環境を、複雑なアーキテクチャであっても、Dockerを使って管理することで、簡単にどんなマシンにでも共有できる」という点です。

以降で、Dockerの各要素について見て行きます。

## ⓪ Docker Engine

Dockerを利用するための常駐プログラム

Docker for mac、Docker for Windows、Docker ToolboxなどのソフトウェアをPCにInstallすることで、常駐プログラムとしてDocker Engineが動作し、Dockerを利用することができるようになります。

## ① イメージ(Image)

コンテナ(=アプリケーションの実行環境)を起動するのに必要な設定ファイルをまとめたもの  
\*\*「Imageはコンテナの元であり、Imageからコンテナを起動する」\*\*と理解すると良いと思います。  
DockerではこのImageを共有することで、様々なマシンで同じコンテナ(実行環境)を動作させることができるのです。

## Tag

Dockerのimageにはtagという概念があります。  
tagとは\*\*\*「imageのversion」\*\*\*のことです。

例えば、以下のDocker Hubの画像のようにnginxイメージには 「1.14-perl」や「alpine」のような様々なtagが存在し、どのversionを使用するかを選択できます。

[![スクリーンショット 2019-01-15 17.25.57.png](https://qiita-image-store.s3.amazonaws.com/0/209909/cdaee054-5a87-1efd-dda0-91f265983d33.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F209909%2Fcdaee054-5a87-1efd-dda0-91f265983d33.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=230eaa916c3aed05249fb5d0c1c4ddb0)

tag名を何も指定しないと 自動的に「latest」タグが使用されるようになっています。

image名とタグ名を: で区切って

```text
nginx:latest
nginx:1.14-perl
```

のように表されます。

## imageの構造

イメージは以下のような2つの特徴を持ちます。

1. layer構造になっている
2. 一度作成されたimageは編集不可能 (読み取り専用)
[![スクリーンショット 2019-01-15 17.37.22.png](https://qiita-image-store.s3.amazonaws.com/0/209909/7fbfbe89-81b5-91e8-1efa-31aa48ac4e1c.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F209909%2F7fbfbe89-81b5-91e8-1efa-31aa48ac4e1c.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=893ba0321b3c3a34ba967b771e9cf60b)

イメージはレイヤー構造になっており、1つのミドルウェアをインストールするにつき、1レイヤーが重なっていくイメージ(1インストールコマンドで1レイヤー積み重なるという認識)

また、imageのレイヤーはRead onlyであり、編集不可能で、imageからコンテナを起動した際に作られるコンテナレイヤーのみが編集可能となっています。

これは、imageからコンテナを起動して、そのコンテナ内で何らかのミドルウェアをインストールした場合に、それがコンテナレイヤーに保存されるということであり、そのコンテナから再びimageを生成(commit)することもできるようになっています。

## Imageを用意する

ローカルマシンに取得しているImage一覧は

```text
$ docker images
```

で確認することができます。

さて、コンテナを実行するには、そのコンテナの元となるimageを用意しなくてはなりません。

imageを用意する方法には

1. 他人が作ったimageを取得する(主にDocker Hubから)
2. 自分で作る(他人のimageを改良する)

があります。

以下に順番に見て行きます。

### 1\. Docker Hubからのimage取得

Docker Hubには様々なミドルウェアが入ったImageが公開されているので、自分が使用したい環境に応じて以下のコマンドでImageを指定して取得します。

```text
$ docker pull イメージ名     #Docker Hubなどからイメージを取得
```

### 2\. Dockerfileからのイメージ作成

Dockerfiile という名前のtextファイルを作り、その中にimage build(image作成)に関わる設定を記述して、そのDockerfileからイメージを自作します。

様々な記述ができますが、ここでは基礎的な以下の関数のみを紹介します。

Dockerfile

```txt
FROM イメージ名:タグ名　　　　　　　#どのimageを元にimageを作成するか
RUN パッケージ等インストールコマンド     #ここに記述したコマンドを実行してミドルウェアをインストールし、imageのレイヤーを重ねる
CMD コマンド指定　　　　　　　　#コンテナが作成された後で実行するコマンドを指定する
```

```text
$ docker build -t ビルドしたイメージに付けるイメージ名 .
```

\-tオプションで作成したimageに名前をつけます。

また、image作成する際にdocker daemonに転送されるファイルの範囲をビルドコンテキストといい、イメージ名の後に指定します。(今回は. \[カレントディレクトリ\])

buildコマンド実行時に設定として読み込むDockerfileはデフォルトではビルドコンテキスト上の `Dockerfiile` が読み込まれるようになっているので、ビルドコンテキストにDockerfileを置いておく必要があります。  
※ 別途 `-f Dockerfile名` で指定することもできます。

一度buildしたimageを、Dockerfileの変更無しに再びbuildする際にはbuild cacheが効いて、以前buildした情報を見に行くようになっているので、RUNにupdateコマンドなどを記述していたとしても普通にbuildし直した場合は、そのコマンドは実行されないことになる点に注意が必要です。

cacheを無効にしてbuildする場合は

```text
$ docker build —no-cache -t docker-whale
```

とします。

## ② コンテナ

imageを用意したら、そのイメージからコンテナ(=アプリケーションの実行環境)が起動できます。

## imageからコンテナの起動

```text
$ docker create --name コンテナに付ける名前 イメージ名
```

でイメージからコンテナを作成

```text
$ docker start コンテナ名
```

でコンテナを起動

これまで見てきた指定したイメージをDockerHubから取得して、コンテナを作成・起動するコマンドとして

```text
$ docker run イメージ名
```

があります。

docker pull:DockerHubからイメージを取得  
docker create ：取得したイメージからコンテナを作成  
docker start ：作成したコンテナを起動

を同時に実行するコマンドがdocker run コマンドです。

## コンテナの操作コマンド

### コンテナ一覧を確認

```text
$ docker ps
```

で現在実行中のコンテナを表示

```text
$ docker ps -a
```

で現在存在しているコンテナ一覧を表示

### コンテナの詳細を確認

```text
$ docker inspect コンテナ名
```

で指定したコンテナの詳細情報を表示

### コンテナの作成

```text
$ docker create —name コンテナにつける名前 -it イメージ名 /bin/bash
```

\-i はコンテナの標準入力を取得して双方向接続するオプション  
\-t はコンテナ内にTTYを割り当てるオプション  
コンテナでシェル実行をして、フォアグラウンドで実行状態にしておきたい場合に -itの組み合わせで使われる  
これをつけないとシェルがすぐに終了してコンテナが停止してしまう

### コンテナの起動

```text
$ docker start コンテナ名
```

でコンテナを起動

### コンテナの一時停止

```text
$ docker pause コンテナ名
```

で、コンテナを一時停止できる

### コンテナの再開

```text
$ docker unpause コンテナ名
```

でコンテナの一時停止を解除できる

### コンテナの再起動

```text
$ docker restart コンテナ名
```

でコンテナが再起動される

### コンテナの停止

```text
$ docker stop コンテナ名
```

でコンテナが停止

コンテナが終了後、そのままローカルに残っている状態はExitとなることに注意が必要です。  
使わないコンテナは削除しておかないとハードディスクが圧迫されます。

### コンテナの削除

```text
$ docker rm コンテナ名
```

でコンテナを削除

```text
$ docker rm -f コンテナ名
```

で強制削除

### 起動中のコンテナのシェルへ接続

接続方法はattachとexecの2つがあります。

```text
$ docker attach 起動中コンテナ名
```

起動中のコンテナがシェルを実行している場合は、シェルに接続できるが、Deamonしか起動していない場合にはDeamonの標準入出力に接続されてしまうことに注意して下さい。

attachで接続して、exitで抜けると起動中のコンテナが終了してしまうのが特徴です。  
起動時に -itフラグをつけている場合は、control + p, control + qで抜けるとコンテナは終了しません。

```text
$ docker exec -it 起動中のコンテナ名 /bin/bash
```

exitで抜けることができ、抜けてもコンテナが停止することはないのが特徴です。

### コンテナからイメージを作成

```text
$ docker commit コンテナ名 image名：タグ名
```

コンテナの状態を、指定した「image名：タグ名」でイメージとして保存するコマンド

```text
$ docker history イメージ名
```

で、そのイメージの変更履歴を確認できますが、docker commitでイメージを作成すると、コンテナの内部でどんな変更を加えたのかはどこにも記録として残らず、とても使いにくいimageになってしまう点に注意が必要です。

そのため、基本的にはDockerfileからimageに変更を加えてイメージ自作をするべきです。

## ここまでの全体像

[![スクリーンショット 2019-01-22 23.22.18.png](https://qiita-image-store.s3.amazonaws.com/0/209909/7da0cb86-e305-7fc0-1fe1-d7be4d86ffba.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F209909%2F7da0cb86-e305-7fc0-1fe1-d7be4d86ffba.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=94559bb8ad8083554b8b009569610c5b)

## ③ Docker Hub (Container Registry)

## Docker Hubでのimage共有

Githubと殆ど同じです。  
Dockerhubのアカウントを持っている必要があります。

手順としては

1. Docker hub上でリポジトリを作成する
2. ローカルでimageを作成
3. ローカルのコマンドラインで以下のコマンドを使ってdocker hubへログイン

4.以下のコマンドでpushしたいimageにタグ付け

```text
$ docker tag <pushしたいimage名> <docker hub ID>/<新規image名>:<タグ名>
```

5.以下のコマンドでローカルのimageをリモートリポジトリにpushする

```text
$ docker push ＜docker hub ID＞ / ＜image名＞：＜タグ名＞
```

## ここまでの全体像

前編では、仮想化の解説から始め、

⓪Docker Engine  
①Image  
②コンテナ  
③Container Registry (DockerHub等)

という4つの要素について見て行きました。

これらをまとめると、以下の図のような全体像になります。

[![スクリーンショット 2019-01-16 19.41.09.png](https://qiita-image-store.s3.amazonaws.com/0/209909/458f31af-db20-fa4a-1e7b-59faaff07a80.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F209909%2F458f31af-db20-fa4a-1e7b-59faaff07a80.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=8dc9ac944e5cf085f5006491bc72ffd8)

## 中編に続く

間違っている点などあればコメント頂けると嬉しいです。

[10](https://qiita.com/etaroid/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fetaroid%2Fitems%2Fb1024c7d200a75b992fc&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fetaroid%2Fitems%2Fb1024c7d200a75b992fc&realm=qiita)

[5901](https://qiita.com/etaroid/items/b1024c7d200a75b992fc/likers)

いいねしたユーザー一覧へ移動

6444