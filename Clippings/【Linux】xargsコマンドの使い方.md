---
title: "【Linux】xargsコマンドの使い方"
source: "https://qiita.com/P-man_Brown/items/c3f2634b7b5e08306c8f"
author:
  - "[[P-man_Brown]]"
published: 2022-10-10
created: 2025-08-26
description: "基本的な使い方 xargsは以下のように記述することで前のコマンドの実行結果を標準入力から受け取って、次のコマンドの引き数に渡して実行できます。 find . -name \"*.log\" | xargs rm -fv 上記の場合にはfindコマンドで取得したすべてのファ..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## 基本的な使い方

`xargs` は以下のように記述することで前のコマンドの実行結果を標準入力から受け取って、次のコマンドの引き数に渡して実行できます。

```bash
find . -name "*.log" | xargs rm -fv
```

上記の場合には `find` コマンドで取得したすべてのファイルパスに対して `rm` コマンドが実行されます。

以下のようなコマンドが実行されます。

```bash
rm -fv ./path/to/sample1.log ./path/to/sample2.log ./path/to/sample3.log
```

## コマンドライラン

`-p` をつけると実際にはコマンドは実行されず実行される予定のコマンドの内容が出力されます。

```bash
$ find . -name "*.log" | xargs -p rm -fv
rm -fv ./path/to/sample1.log ./path/to/sample2.log ./path/to/sample3.log?...
```

## 引数の位置を指定

以下のように記述することで引数の位置を指定できます。

```bash
find . -name "*.log" | xargs -i cp {} /exampledir/
```

以下のようなコマンドが実行されます。

```bash
cp ./path/to/sample.log /exampledir/
```

また以下のように記述しても上記と同様のことが実現できます。

```bash
find . -name "*.yml" | xargs -IXXX cp -f XXX ./exampledir/
```

## 引数の最大数を指定

`-L 数値` を追記するとコマンドに一度に渡す引数の最大数を指定できます。

```bash
find . -name "*.log" | xargs -L 1 rm -fv
```

上記の場合に `find` コマンドで3つのパスを取得したとすると以下のように実行されます。

```bash
$ rm -fv ./path/to/sample1.log
$ rm -fv ./path/to/sample2.log
$ rm -fv ./path/to/sample3.log
```

なお `-L 数値` は改行ごとに1つの引数として扱います。

`-n 数値` でも引数の数を指定できますがこちらは `xargs` がコマンドに渡す引数の最大数を指定します。

```bash
$ seq -s ' ' 10 | xargs -L 2 echo T
T 1 2 3 4 5 6 7 8 9 10

$ seq -s ' ' 10 | xargs -n 2 echo T
T 1 2
T 3 4
T 5 6
T 7 8
T 9 10
```

## 同時実行の最大数を指定

`-P 数値` を追記すると同時実行の最大数を指定できます。

```bash
find . -name "*.log" | xargs -L 1 -P 2 rm -fv
```

上記の場合に `find` コマンドで3つのパスを取得したとすると1つ目と2つ目が同時実行されその後に3つ目が実行されます。

[0](https://qiita.com/P-man_Brown/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2FP-man_Brown%2Fitems%2Fc3f2634b7b5e08306c8f&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2FP-man_Brown%2Fitems%2Fc3f2634b7b5e08306c8f&realm=qiita)

[47](https://qiita.com/P-man_Brown/items/c3f2634b7b5e08306c8f/likers)

いいねしたユーザー一覧へ移動

32