---
title: はじめてのDockerfile
source: https://qiita.com/suzu12/items/c4bc47c0df6ec9b9290b
author:
  - "[[suzu12]]"
published: 2021-09-05
created: 2025-08-26
description: はじめに 前回の記事では Dockerレジストリにあるイメージを pull してそのイメージを使用してコンテナを立てました。 このようにレジストリで提供されている Docker イメージを使用してコンテナをたてることもできますが、 自分で手を加えて自分の使いたい形でコンテ...
tags:
  - clippings
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から3年以上が経過しています。

## はじめに

[前回の記事](https://qiita.com/suzu12/items/315fba934de11af826bd) では  
Dockerレジストリにあるイメージを pull してそのイメージを使用してコンテナを立てました。  
このようにレジストリで提供されている Docker イメージを使用してコンテナをたてることもできますが、  
自分で手を加えて自分の使いたい形でコンテナをたてることもできます。

1. [ゼロから始めるDocker入門](https://qiita.com/suzu12/items/315fba934de11af826bd)
2. [はじめてのDockerfile](https://qiita.com/suzu12/items/c4bc47c0df6ec9b9290b) ←今回はここ
3. [はじめてのDocker compose](https://qiita.com/suzu12/items/4ec2cc8c1a7c23aa112b)

## 対象読者

- `Dockerfile` を作成して `image` の作成を試したい方」
- `Docker instruction` にどのようなものがあるか知りたい方

## 目次

- [はじめに](https://qiita.com/suzu12/items/#%E3%81%AF%E3%81%98%E3%82%81%E3%81%AB)
- [対象読者](https://qiita.com/suzu12/items/#%E5%AF%BE%E8%B1%A1%E8%AA%AD%E8%80%85)
- [目次](https://qiita.com/suzu12/items/#%E7%9B%AE%E6%AC%A1)
- [Dockerfile](https://qiita.com/suzu12/items/#dockerfile)
	- [Sample](https://qiita.com/suzu12/items/#sample)
	- [dokcer build.](https://qiita.com/suzu12/items/#dokcer-build-)
	- [dokcer build -f \[PATH/Dockerfile\].](https://qiita.com/suzu12/items/#dokcer-build--f-pathdockerfile-)
	- [docker run](https://qiita.com/suzu12/items/#docker-run)
- [Docker instruction](https://qiita.com/suzu12/items/#docker-instruction)
- [CMD と ENTRYPOINT の違い](https://qiita.com/suzu12/items/#cmd-%E3%81%A8-entrypoint-%E3%81%AE%E9%81%95%E3%81%84)
- [ADD と COPY の違い](https://qiita.com/suzu12/items/#add-%E3%81%A8-copy-%E3%81%AE%E9%81%95%E3%81%84)
- [後片付け](https://qiita.com/suzu12/items/#%E5%BE%8C%E7%89%87%E4%BB%98%E3%81%91)
- [おわりに](https://qiita.com/suzu12/items/#%E3%81%8A%E3%82%8F%E3%82%8A%E3%81%AB)
- [参考文献](https://qiita.com/suzu12/items/#%E5%8F%82%E8%80%83%E6%96%87%E7%8C%AE-2)

## Dockerfile

`Dockerfile` とは **Dockerイメージ** の設計図となるもので  
テキストファイル形式で作成してそのファイルからイメージを作成することができます。

### Sample

イメージを作成するために実際に　 `Dockerfile` 　を作成していきます。  
適当なディレクトリに移動してそのなかで　 `Dockerfile` 　を作成します。  
ディレクトリ内は `Dockerfile` のみにします。  
（後に出てくるビルドコンテキストというものでカレントディレクトリを指定することが多いため。）  
（ **※ `.dockerignore` 　を作成しファイルパスを記述すれば大丈夫なようです。** ）

ターミナル

```sh
touch Dockerfile

# あとで使用したい copy.txt を作成し編集しておく。
touch copy.txt
```

copy.txt

```sh
# ここではわかりやすいように以下のような文字列を入れておく。
[COPY]でコピーしたファイルです。
```

Dockerfile

```dockerfile
# ubuntu image の最新をベースに
FROM ubuntu:latest

# /var/www/html で命令を実行していく(パスの指定は練習なので適当なもので大丈夫です。)
# コンテナ内に入ったときのパスにもなる
WORKDIR /var/www/html

# echo の内容を sample.txt に格納
RUN echo 'sampleです！' > sample.txt

# 前のステップで作成したテキストファイル
#     ホスト   コンテナ
COPY copy.txt copy.txt

# ubuntu の デフォルトコマンド はもとから bash だけど明示的に記述
CMD ["/bin/bash"]
```

### dokcer build.

`Dockerfile` の記述が終わったら  
`Dockerfile` があるディレクトリ内で次のコマンドを叩きます。

ターミナル

```terminal
docker build .
```

すると `Dockerfile` で記述した命令がステップ毎に実行されていって  
image　が作成されます。

作成した `image` を確認します。

ターミナル

```sh
# 確認方法①
## イメージ一覧の一番上に作成したイメージがあると思います。
docker images

# 確認方法②
## docker build .　したときの最後のあたりにshaからはじまる英数字の羅列の : のあと
## 少々長いですが、docker images　で出るものと同じです。
```

`build` は構築、  
`.` はビルドコンテキストと呼ばれるものです。(`.` はカレントディレクトリを示しています。)

`docker daemon` にこのビルドコンテキスト内を渡しています。  
要するにイメージを作成・構築するために `Dockerfile` を書きましたが  
この `Dockerfile` を `docker daemon` に渡している、ということです。  
(ややこしくなってしまいますが、渡すのは `Dockerfile` だけではなくビルドコンテキスト内の全てのファイルになることに注意してください。)

### dokcer build -f \[PATH/Dockerfile\].

`Dockerfile` をカレントディレクトリに置かなくても `build` することができます。  
それは　 `-f` フラグを使用して `Dockerfile` のあるパスを指定するだけです。

### docker run

作成したイメージからコンテナを立ち上げてみます。  
(**※ 補足ですが、デフォルトコマンドを決めたので bash がなくとも docker run コマンドをしたときに自動的にシェルに入ることはできます。**)

```text
docker run -it [image Id] bash
```

入ったら `/var/www/html` にいることがわかります。

また `ls` コマンドで確認したときに `COPY` 命令でコピーした `copy.txt` と `RUN` 命令で作成した `sample.txt` 作成されていることがわかります。

`cat` など使用して自分が編集した内容と一緒なのか確認もできます。

## Docker instruction

instructionとは命令のことです。  
`Dockerfile` に命令を記述し  
イメージを構築していきます。

| instruction | 意味 | 使用例 |
| --- | --- | --- |
| FROM | 基本的には Dockerfile の書き始めに使用する。用途はイメージの取得で構築するイメージのベースを指定する。 | FROM <イメージ名> |
| RUN | イメージレイヤの階層化。Linuxコマンドを実行。 | RUN <コマンド> |
| CMD | コンテナ実行時のデフォルトコマンドを指定。 | CMD \["/bin/bash"\] |
| ENV | 環境変数を示す | ENV <キー>=<値> |
| ADD | 追加元ファイル・ディレクトリを追加先に追加。追加元がローカルにあり圧縮ファイルの場合展開し追加される。 | ADD <追加元(src)> <追加先(container)> |
| COPY | コピー元ファイル・ディレクトリをコピー先に追加 | COPY <コピー元> <コピー先> |
| ENTRYPOINT | `docker run` 時に実行される。 | ENTRYPOINT \["実行ファイル", "パラメータ1", "パラメータ2"\] |
| VOLUME | ホストのファイルやフォルダのマウントポイント | VOLUME \["/data"\] |
| WORKDIRE | RUNやADDなどDocker instructionが実行されるディレクトリの変更 | WORKDIR /path/to/workdir |

### CMD と ENTRYPOINT の違い

この2つは命令はどちらものコンテナ起動時になんのコマンドを実行するのかを決めるコマンド。

- `ENTRYPOINT` は `docker run` 時にデフォルトコマンドの上書きができない。
- 上書きできるのは `CMD` で書いた部分のみ

**(※ `ENTORYPOINT` でコマンドを指定した場合は `CMD ["パラメータ1", "パラメータ2"]` と指定することができます。)**

#### 参考文献

### ADD と COPY の違い

どちらもファイルやフォルダのコピーができる。

違いとしては下記がある。

- `ADD` はコピー時に圧縮ファイルを解凍する
- `COPY` はしない

#### 参考文献

## 後片付け

使用していないイメージコンテナを削除していきます。

```text
docker image prune
```

```text
docker system prune
```

## おわりに

`Dockerfile` の理解をすることによって自分で `Dockerfile` を書くことができますし、  
プロジェクトで使用している内容を理解することもできるようになります。

とはいえ、馴染みのない `instruction` がでてきたり、応用的な使用をしていたりするとその都度調べることにはなります。ただ、基礎的なところを抑えておけば全く読めないということはなくなりますし、メンタル的にも良いかなとも思っています。

## 参考文献

- [Dockerfile リファレンス](https://docs.docker.jp/engine/reference/builder.html)
- [カレントディレクトリとは](https://wa3.i-3-i.info/word1537.html)
- [\[docker\] CMD とENTRYPOINT の違いを試してみた](https://qiita.com/hihihiroro/items/d7ceaadc9340a4dbeb8f)
- [DockerfileのCMDとENTRYPOINTを改めて解説する](https://qiita.com/uehaj/items/e6dd013e28593c26372d)
- [Dockerfile の ADD と COPY の違いを結論から書く](https://qiita.com/YumaInaura/items/1647e509f83462a37494)
- [DockerfileにてなぜADDよりCOPYが望ましいのか](https://qiita.com/momotaro98/items/bf34eef176cc2bdb6892)
- [ゼロから始めるDocker入門](https://qiita.com/suzu12/items/315fba934de11af826bd)

[0](https://qiita.com/suzu12/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fsuzu12%2Fitems%2Fc4bc47c0df6ec9b9290b&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fsuzu12%2Fitems%2Fc4bc47c0df6ec9b9290b&realm=qiita)

[19](https://qiita.com/suzu12/items/c4bc47c0df6ec9b9290b/likers)

いいねしたユーザー一覧へ移動

13

https://zenn.dev/reijilog/articles/33d3f6b2d1856e