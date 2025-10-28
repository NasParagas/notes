---
title: "dockerでvolumeをマウントしたときのファイルのowner問題"
source: "https://qiita.com/yohm/items/047b2e68d008ebb0f001"
author:
  - "[[yohm]]"
published: 2018-03-08
created: 2025-08-26
description: "dockerでvolumeをマウントするときの問題点 docker runするときに-vオプションをつけることによってホストのディレクトリをコンテナ内にマウントすることができる。 ホスト側のファイルをコンテナ内で使いたい場合や、逆にコンテナで作ったファイルにホストからアク..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

[@yohm (Yohsuke Murase)](https://qiita.com/yohm)

最終更新日 投稿日 2018年03月08日

## dockerでvolumeをマウントするときの問題点

docker runするときに `-v` オプションをつけることによってホストのディレクトリをコンテナ内にマウントすることができる。  
ホスト側のファイルをコンテナ内で使いたい場合や、逆にコンテナで作ったファイルにホストからアクセスしたい場合に有用なのだが、ファイルのアクセス権限についてちゃんと考えておかないと問題が起きることがある。

例えば、ホスト内でのユーザーのuidが500だったとしよう。

```shell-session
$ id
uid=500(ec2-user) gid=500(ec2-user) groups=500(ec2-user),10(wheel),497(docker)
```

そこで、volumeをマウントしたコンテナを作ってみる。

```shell-session
$ mkdir -p temp && touch temp/foo                       # 実験用に適当なディレクトリを作ってみる
$ docker run -it -v $(pwd)/temp:/temp busybox    # 先ほど作ったディレクトリを /temp にマウントする
（コンテナ内にて）
# ls -la
total 48
drwxr-xr-x    1 root     root          4096 Mar  6 23:58 .
drwxr-xr-x    1 root     root          4096 Mar  6 23:58 ..
-rwxr-xr-x    1 root     root             0 Mar  6 23:58 .dockerenv
drwxr-xr-x    2 root     root         12288 Feb 20 17:57 bin
（省略）
drwxrwxr-x    2 500      500           4096 Mar  6 23:56 temp
（省略）
```

このように、コンテナ内からはマウントしたファイルのownerがuid=500,gid=500になっている。ここでファイルを作成してみる。

```shell-session
# touch temp/bar
# ls -l temp
total 0
-rw-r--r--    1 root     root             0 Mar  7 00:02 bar
-rw-rw-r--    1 500      500              0 Mar  6 23:56 foo
```

となり、rootがownerのファイルとして作成される。  
ホストOSから同じファイルを見て見ると

```shell-session
$ ls -l temp
total 0
-rw-r--r-- 1 root     root     0 Mar  7 00:02 bar
-rw-rw-r-- 1 ec2-user ec2-user 0 Mar  6 23:56 foo
```

となり、rootの持ち物として作成される。当然このファイルは一般ユーザーからは編集不可になってしまう。

コンテナとホストで相互にファイルのやりとりをしたいときにこの挙動は困ることが多い。

ちなみに **docker for macで試したところ、上記の問題は起きなかった。**  
コンテナ内からはownerがrootとして表示されるが、mac上からは自ユーザーがownerとして表示されている。docker for macの中でうまく解決してくれているようだ。  
以下はlinuxの場合の対処法。

## 解決策

この問題の解決策として以下のページで示されていた方法を紹介する。  
参考: [https://denibertovic.com/posts/handling-permissions-with-docker-volumes/](https://denibertovic.com/posts/handling-permissions-with-docker-volumes/)

まずはうまくいかない方法から紹介する。

## うまくいかない方法1: Dockerfile内でuseraddする

例えば

この方法はイメージをビルドしたマシンと実行するマシンが同じならば問題がない。  
しかし、イメージをビルドする段階でuidを決定しなければならないので、別のマシンでビルドしたイメージは使えず実用的ではない。

## うまくいかない方法2: docker runに-uオプションをつける

`docker run` コマンドには実行するユーザーを指定する `-u` オプションがある。この `-u` でホストOS上のUIDを指定すればよいような気がする。

```shell-session
$ docker run -it -u \`id -u $USER\` debian:jessie /bin/bash
I have no name!@dcb415bad433:/$ id
uid=500 gid=0(root) groups=0(root)
```

こうするとコンテナ内のUIDがホストOSと同じUIDになる。しかし

- gid (group id)が変わっていない
- /etc/passwd の情報とuidが一貫していない

という問題がある。２つ目の問題はファイルを使っているだけなら問題ないが、いくつかのアプリで/etc/passwdを参照することがあり問題が起きることもある。  
つまり、 `-u` のようなオプションでuidを指定したいが、実際に `useradd` を使ってユーザーを作ることが必要となる。

## うまくいく方法1: ENTRYPOINTでuseraddでユーザーを作る

基本的な方針は

- ホストOSでのユーザーのUIDを環境変数で渡す（必要であればGIDも）
- コンテナ内で `useradd` でuidを指定して一般ユーザーを作る
- その一般ユーザーでコマンドを実行する

となる。

Dockerfile

```dockerfile
FROM ubuntu:latest

RUN apt-get update && apt-get -y install gosu
COPY entrypoint.sh /usr/local/bin/entrypoint.sh
RUN chmod +x /usr/local/bin/entrypoint.sh
ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]
```

entrypoint.sh

```bash
# !/bin/bash

USER_ID=${LOCAL_UID:-9001}
GROUP_ID=${LOCAL_GID:-9001}

echo "Starting with UID : $USER_ID, GID: $GROUP_ID"
useradd -u $USER_ID -o -m user
groupmod -g $GROUP_ID user
export HOME=/home/user

exec /usr/sbin/gosu user "$@"
```

これらのファイルを準備して `docker build . -t mybase` コマンドでmybaseイメージを作る。

entrypoint.shの中では、環境変数 `LOCAL_UID` で指定されたuidでユーザーを作っている。  
[gosu](https://github.com/tianon/gosu) というツールはgoで書かれた `su` コマンドのようなもの。普通の `su` コマンドはTTYの問題とか色々と奇妙なことがおきるらしいので、それを回避するためのもの。やっていることは `su` と同じだと思えばよい。  
（参照元の記事ではgosuをインストールする手順がもっと複雑だが、最新のubuntuイメージの場合には `apt-get install -y gosu` するだけでOK）

また参照元記事ではGIDは特に設定していないが、今回はentrypoint.shでGIDも参照するようにした。

試しにこのイメージを実行して見る。

```text
$ docker run -it mybase bash
Starting with UID : 9001
user@d45b2d3198c9:/$ id
uid=9001(user) gid=9001(user) groups=9001(user)
```

今は環境変数を何もしていないので、デフォルトの9001番でユーザーが作られている。

では環境変数を設定して、ボリュームをマウントして見る。

```shell-session
$ docker run -it -v $(pwd)/temp:/temp -e LOCAL_UID=$(id -u $USER) -e LOCAL_GID=$(id -g $USER) mybase bash
# コンテナにて
$ id
uid=500(user) gid=500(user) groups=500(user)
$ ls -l /temp
total 0
-rw-rw-r-- 1 user user 0 Mar  7 01:49 foo
```

となり、ホストOS上でのUID,GIDが適切に設定されるようになったことがわかる。  
コンテナ内でマウントされたディレクトリにファイルを作成しても、ホストOSからは自ユーザーがownerのファイルのように見える。  
コンテナ内とホストOSではユーザー名は異なるが、ファイルシステムはUIDを使ってファイルを管理しているのでこれでうまくいく。

## うまくいく方法2: /etc/passwdと/etc/groupをコンテナにマウントする

別の方法。 `docker run` に `-u` をつけてもうまくいかなかったのは、/etc/passwdとの不整合が起きるためであった。  
これがうまくいくようにするには、ホストOSの/etc/passwdをマウントするという方法がある。

```text
docker run -it -v /etc/group:/etc/group:ro -v /etc/passwd:/etc/passwd:ro -u $(id -u $USER):$(id -g $USER) ubuntu bash
```

ここではコンテナが勝手に/etc/passwdや/etc/groupを書き換えないようにread onlyでマウントしている。  
これでほとんどの場合うまくいく。（ただし、上記のコマンドはDocker for macでは失敗する。/etcがマウントできないため）

しかし、コンテナのイメージを作成する際にすでに一般ユーザーを作って作業を行っている場合、その一般ユーザーで作ったファイルにアクセスできなくなるため問題が起きる。

## うまくいく方法3: Dockerfile内ですでに一般ユーザーが作られている場合

イメージを作る段階ですでに一般ユーザーを作って、そのホーム以下でいろいろと設定しているケースを考える。  
その場合、すでにコンテナ内に存在する一般ユーザーのUIDを `usermod` コマンドで変更し、ホストOSのUIDと合わせるとよい。

方法1とほぼ同様に、スクリプトを以下のように準備する。 `usermod` コマンドで既存ユーザーのUIDを変更している。  
このとき、 `usermod` コマンドはHOME以下のファイルのownerも自動的に切り替えてくれるのでファイルのオーナーを書き換えたりする必要はない。

entrypoint.sh

```bash
usermod -u $USER_ID -o -m user
groupmod -g $GROUP_ID user
```

## まとめ

dockerでvolumeをマウントするとファイルのUIDを適切に設定しなくてはいけない。  
ほとんどの場合は方法2が最も簡単だが、コンテナのイメージにすでに一般ユーザーが作られている場合は方法３を使うのが良さそう。

[6](https://qiita.com/yohm/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fyohm%2Fitems%2F047b2e68d008ebb0f001&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fyohm%2Fitems%2F047b2e68d008ebb0f001&realm=qiita)

[595](https://qiita.com/yohm/items/047b2e68d008ebb0f001/likers)

いいねしたユーザー一覧へ移動

532