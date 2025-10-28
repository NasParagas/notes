---
title: "標準出力、標準エラー出力のリダイレクションとteeコマンドのまとめ"
source: "https://qiita.com/yousan/items/3999717cbba79fafb13a"
author:
  - "[[yousan]]"
published: 2023-10-17
created: 2025-08-27
description: "はじめに コマンドを実行した際に標準出力、標準エラー出力に出力されるものがありますが、それらをうまく制御したいです。 よく忘れたりして調べることが多いのでリダイレクトとマージ方法、teeコマンドをつかって標準出力とファイルに同時に書き出す方法などをまとめました。 準備 ..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiitaにログインして、便利な機能を使ってみませんか？

[ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fyousan%2Fitems%2F3999717cbba79fafb13a&realm=qiita) [新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fyousan%2Fitems%2F3999717cbba79fafb13a&realm=qiita)

この記事は最終更新日から1年以上が経過しています。

## はじめに

コマンドを実行した際に標準出力、標準エラー出力に出力されるものがありますが、それらをうまく制御したいです。  
よく忘れたりして調べることが多いのでリダイレクトとマージ方法、teeコマンドをつかって標準出力とファイルに同時に書き出す方法などをまとめました。

## 準備

標準出力、エラー出力のそれぞれに出力してくれるスクリプトを書いておきます。

test.sh

```shell
#!/usr/bin/env bash

echo "This is stdout"
echo "This is stderr" 1>&2
```

`echo` コマンドはそのままであれば標準出力にだされます。標準エラー出力に出したい場合には `1>&2` を後ろに書いておきます。

そのまま実行するとどちらもターミナルに出力されます。

```bash
$ chmod +x test.sh

$ ./test.sh

This is stdout
This is stderr
```

両方出てきました。

## 標準出力と標準エラー出力

ファイルディスクリプタ番号

| ファイルディスクリプタ番号 | 名称 | 略称(stdio.h) |
| --- | --- | --- |
| 0 | 標準入力 | stdin |
| 1 | 標準出力 | stdout |
| 2 | 標準エラー出力 | stderr |

参考:

このファイルディスクリプタ番号をリダイレクト記号 （ `>` ）の直前に書くことで出力先を変えることができます。  
例えば `2>` となります。

## リダイレクトを使う

それぞれをリダイレクションを使ってファイルに書き出してみます。  
リダイレクションの場合にはファイルディスクリプタ番号と `>` を使うと出力先を指定することができます。

まずは標準出力のみを書き出してみます。

```bash
$ ./test.sh > stdout.txt
This is stderr                   # 標準エラー出力はリダイレクトされていないためここに出力されています。

$ cat stdout.txt
This is stdout
```

`2>` を使って標準エラー出力をリダイレクトしてみます。　

```text
$ ./test.sh 2> stderr.txt
This is stdout                   # 今度は標準出力がリダイレクトされていないためここに出力されています。

$ cat stderr.txt
This is stderr
```

うまく標準エラー出力がリダイレクトされています。

次は両方ファイルに書き込むようにしてみます。

```bash
$ ./test.sh 1> stdout.txt 2> stderr.txt
#　どちらもファイルにリダイレクトされているため何も出力されなくなりました。

$ cat stdout.txt
This is stdout

$ cat stderr.txt
This is stderr
```

それぞれ分かれて書かれていますね。

`1>` の指定部分 `>` と書いても良いのですが、明示的にするために `1>` と書きました。

## 両方を書き込む

標準出力、標準エラー出力の両方を書き込む方法です。  
リダイレクションで `2>&1` と書くとファイルディスクリプタ番号2を1にマージする、という意味になります。具体的には下記のようになります。

```bash
$ ./test.sh > stdout_stderr.txt 2>&1
# 何も出力されない

$ cat stdout_stderr.txt
This is stdout
This is stderr
```

`2>&1` はすべてをファイルに書き込むので、サービスのログに書き込む際に使われたりします。そのためLinuxのサービス管理などのスクリプトに書かれたりしています。

参考

## teeコマンド

「ファイルに書き込みつつ画面にも出したい」という場合には `tee` コマンドを使うと良いです。具体的には下記のようにします。

```bash
$ ./test.sh | tee stdout.txt
This is stderr
This is stdout # teeコマンドで標準出力が表示される。

$ cat stdout.txt
This is stdout
```

パイプを使ってファイル名を書くことでファイルの書き出しと標準出力への書き出しを行っています。

なお `stderr` の出力と `stdout` の出力のうち、 `stderr` が先に来るのは tee コマンドを使っている際の遅延ではないかと思われます。

また「リダイレクト」という言葉ですが、狭義には `>` や `<` を指し、広義には追加 `>>` やパイプ `|` を指すようです。 [^1]

## 標準出力、標準エラー出力のどちらもそれぞれファイルと画面に出す

画面とファイル、両方に書き出す方法です。書き込み先のファイルは別々にします。  
記述がややこしいのですが、Process Substitution という方法を使い、標準出力、標準エラー出力用にプロセスを２つ立てる方法でできるようです。

```text
$ ./test.sh 1> >(tee stdout.txt >&1 ) 2> >(tee stderr.txt >&2)
This is stderr
This is stdout

$ cat stdout.txt
This is stdout

$ cat stderr.txt
This is stdout
```

## ファイルを追記する

リダイレクトでファイルを追記する方法は `>>` です。

```bash
$ ./test.sh > stdout.txt 2> /dev/null

$ ./test.sh >> stdout.txt 2> /dev/null    # 二回目の実行で >> を使う

$ cat stdout.txt
This is stdout # 一度目の実行で書き出された内容
This is stdout # 追記されて二行目が出力されている。
```

`tee` コマンドで同様に追記する場合には `-a` オプションを使用します。

```bash
$ ./test.sh 1> >(tee stdout.txt >&1 ) 2> >(tee stderr.txt >&2)
This is stderr
This is stdout
$ ./test.sh 1> >(tee -a stdout.txt >&1 ) 2> >(tee -a stderr.txt >&2)
This is stderr
This is stdout

$ cat stdout.txt
This is stdout # 一度目の実行で書き出された内容
This is stdout # 追記されて二行目が出力されている。

$ cat stderr.txt
This is stderr
This is stderr
```

[0](https://qiita.com/yousan/items/#comments)

コメント一覧へ移動

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fyousan%2Fitems%2F3999717cbba79fafb13a&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fyousan%2Fitems%2F3999717cbba79fafb13a&realm=qiita)

[4](https://qiita.com/yousan/items/3999717cbba79fafb13a/likers)

いいねしたユーザー一覧へ移動

4

[^1]: [https://en.wikipedia.org/wiki/Redirection\_(computing)](https://en.wikipedia.org/wiki/Redirection_\(computing\))