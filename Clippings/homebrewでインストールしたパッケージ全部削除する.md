---
title: "homebrewでインストールしたパッケージ全部削除する"
source: "https://qiita.com/anon5r/items/d262c80b718739caf076"
author:
  - "[[anon5r]]"
published: 2020-11-21
created: 2025-08-29
description: "使用していたMacBook Proを他の人に譲渡する必要があり、その際なるべくクリーンアップしてから譲渡したいものですが、アプリ関連/Applications以下の不要なアプリをアンインストールしても、まだ使用済み領域がだいぶ残っていたので、思い当たる領域をいくつか探してい..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiitaにログインして、便利な機能を使ってみませんか？

[ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fanon5r%2Fitems%2Fd262c80b718739caf076&realm=qiita) [新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fanon5r%2Fitems%2Fd262c80b718739caf076&realm=qiita)

この記事は最終更新日から3年以上が経過しています。

使用していたMacBook Proを他の人に譲渡する必要があり、その際なるべくクリーンアップしてから譲渡したいものですが、アプリ関連 `/Applications` 以下の不要なアプリをアンインストールしても、まだ使用済み領域がだいぶ残っていたので、思い当たる領域をいくつか探していました。

その一つとして、Macを開発環境としても利用していたため、アプリ一覧には出てこないhomebrewでインストールしたパッケージ(Formula)などが残っているのを思い出しました。

そこで、思い切ってここも全部削除することにしました。

## インストール済みパッケージの削除

`brew install` でインストールしたパッケージは `uninstall` 、 `remove` または `rm` のサブコマンドで削除できます。よく使われる系の単語でエイリアスされてて便利。

```shell
brew uninstall  <パッケージ名>
```

または

```shell
brew remove <パッケージ名>
```

`<パッケージ名>` は任意のパッケージ名を入れますが、ここは複数一括で指定可能です。

## インストール済みパッケージを取得

上記で削除方法は分かりました。では何のパッケージがインストールされていたかを取得するには `list` サブコマンドを利用します。

```shell
brew list
```

これでインストール済みパッケージが一覧で表示されます。  
`list` の結果は、単純にインストール済みパッケージ名のみが羅列されるのでこの結果をそのまま使用することができます。

```text
adns            gh            libass            lzlib            python@2
aom            giflib            libassuan        lzo            python@3.7
apr            git            libbluray        mas            python@3.8
apr-util        git-flow        libev            mcrypt            python@3.9
argon2            git-lfs            libevent        mhash            rav1e
aspell            glib            libffi            mosh            readline
augeas            gmp            libgcrypt        mysql            rtmpdump
autoconf        gnupg            libgpg-error        ncurses            rubberband
aws-sam-cli        gnutls            libidn            nettle            sdl2
bdw-gc            go            libidn2            nghttp2            snappy
brotli            gobject-introspection    libksba            nginx            speex
...
```

## ワンライナーで削除する

上記を組み合わせて、下記のコマンドで全パッケージを一括削除できます。

```shell
brew uninstall --force $(brew list)
```

`--force` オプションを指定しているのは、1つのパッケージで複数バージョンを含む際に、パッケージに含まれる全バージョンを削除するためです。 `--force` または `-f` を付けず実行し、上記のケースに該当する場合はその旨の警告が出ます。

## 削除実行時の注意点

## 必ずインストール時のユーザーで

`brew` コマンドでインストールしたユーザーの権限で実行する必要があります。  
他のユーザーで実行すると、権限の関係でエラーとなり、削除に失敗します。

## homebrew含め全部削除する

実はこんなことしなくても [公式アンインストーラーが用意されている](https://github.com/homebrew/install#uninstall-homebrew) ので、それを利用するだけでも良かったりします。

```shell
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall.sh)"
```

一部パーミッションの関係や他のアプリケーションからのインストールなどで、素直に削除できないこともありますが、それらもすべてリストアップして教えてくれます。  
それをみて一つ一つ確認して削除すれば綺麗にクリーンアップできます。

[0](https://qiita.com/anon5r/items/#comments)

コメント一覧へ移動

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fanon5r%2Fitems%2Fd262c80b718739caf076&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fanon5r%2Fitems%2Fd262c80b718739caf076&realm=qiita)

[23](https://qiita.com/anon5r/items/d262c80b718739caf076/likers)

いいねしたユーザー一覧へ移動

26