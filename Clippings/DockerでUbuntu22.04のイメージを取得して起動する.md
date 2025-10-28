---
title: "DockerでUbuntu22.04のイメージを取得して起動する"
source: "https://qiita.com/kurokoya/items/1dc40d35fc62bdd7c21b"
author:
  - "[[kurokoya]]"
published: 2023-06-03
created: 2025-09-10
description: "はじめに Dockerイメージをpullして、起動する方法を記事にしています。 Dockerイメージ取得 Ubuntu22.04のDockerイメージをpullします。 docker pull ubuntu:22.04 コンテナ起動 Ubuntu22.04コンテナを..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiitaにログインして、便利な機能を使ってみませんか？

あなたにマッチした記事をお届けします

便利な情報をあとから読み返せます

[ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fkurokoya%2Fitems%2F1dc40d35fc62bdd7c21b&realm=qiita) [新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fkurokoya%2Fitems%2F1dc40d35fc62bdd7c21b&realm=qiita)

この記事は最終更新日から1年以上が経過しています。

## はじめに

Dockerイメージをpullして、起動する方法を記事にしています。

### Dockerイメージ取得

Ubuntu22.04のDockerイメージをpullします。

```text
docker pull ubuntu:22.04
```

### コンテナ起動

Ubuntu22.04コンテナを起動します。

```text
docker run --name myubuntu ubuntu:22.04 /bin/bash
```

### 起動中コンテナ一覧取得

起動しているコンテナ起動を取得します。

```text
docker ps
```

さっき起動したはずですが、何も表示されません。

```text
-zsh ~/Docker/ub22.04 % docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

\-a オプションを付けてみます。

```text
docker ps -a
```

表示されます。Exit（停止）しているようです。

```text
-zsh ~/Docker/ub22.04 % docker ps -a
CONTAINER ID   IMAGE          COMMAND       CREATED         STATUS                     PORTS     NAMES
a9f80ec02728   ubuntu:22.04   "/bin/bash"   7 seconds ago   Exited (0) 5 seconds ago             myubuntu
```

### コンテナ起動（稼働中を維持）

コンテナ起動して稼働中のままにします。

- 方法1 tail -d /dev/nullを実行します。

```text
docker run -d --name myubuntu ubuntu:22.04 tail -f /dev/null
```

実行例

```text
-zsh ~/Docker/ub22.04 % docker run -d --name myubuntu ubuntu:22.04 tail -f /dev/null
b10de1d6d80b0ae8f739f78f8fa50af57c4248ce8a3473d4cfaa8fb77fc5c98d
-zsh ~/Docker/ub22.04 % docker ps
CONTAINER ID   IMAGE          COMMAND               CREATED         STATUS        PORTS     NAMES
b10de1d6d80b   ubuntu:22.04   "tail -f /dev/null"   2 seconds ago   Up 1 second             myubuntu
```

- 方法2 インタラクティブモードで実行します。

```text
docker run -it -d --name myubuntu ubuntu:22.04
```

実行例

```text
-zsh ~/Docker/ub22.04 % docker run -it -d --name myubuntu ubuntu:22.04

626ca84beb9a609a281f71ecc711a645eb07189c1e26f20d6c240df9c2c1738d
-zsh ~/Docker/ub22.04 % docker ps
CONTAINER ID   IMAGE          COMMAND       CREATED         STATUS         PORTS     NAMES
626ca84beb9a   ubuntu:22.04   "/bin/bash"   6 seconds ago   Up 5 seconds             myubuntu
```

### コンテナに入る

docker psコマンドで取得したコンテナIDを指定して稼働中のコンテナに入ります。

```text
docker exec -it 258732c47a12 /bin/bash
```

実行例

```text
-zsh ~/Docker/ub22.04 % docker exec -it 258732c47a12 /bin/bash
root@258732c47a12:/#
```

### コンテナ起動して中に入る

コンテナを起動すると同時にコンテナの中に入ります。

```text
docker run -it --name aaa ubuntu:22.04 /bin/bash
```

実行例

```text
-zsh ~/Docker/ub22.04 % docker run -it --name aaa ubuntu:22.04 /bin/bash
root@cf62b2d3aa64:/#
```

### コンテナの中から抜ける

exitすれば良いです。  
実行例

```text
root@cf62b2d3aa64:/# exit
exit
-zsh ~/Docker/ub22.04 %
```

### コンテナ停止

Ubuntu22.04コンテナを停止します。

```text
docker stop myubuntu
```

### コンテナ削除

Ubuntu22.04コンテナを削除します。

```text
docker rm myubuntu
```

### Dockerイメージ削除

Dockerイメージの一覧を取得します。

```text
docker images
```

Dockerイメージの取得例

```text
-zsh ~/Docker/ub22.04 % docker images
    
REPOSITORY                        TAG          IMAGE ID       CREATED        SIZE
ubuntu                            22.04        2767693332e5   11 days ago    69.2MB
```

IMAGE IDを指定して、Dockerイメージを削除します。

```text
docker rmi 2767693332e5
```

以上です。

[0](https://qiita.com/kurokoya/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fkurokoya%2Fitems%2F1dc40d35fc62bdd7c21b&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fkurokoya%2Fitems%2F1dc40d35fc62bdd7c21b&realm=qiita)

[5](https://qiita.com/kurokoya/items/1dc40d35fc62bdd7c21b/likers)

いいねしたユーザー一覧へ移動

7