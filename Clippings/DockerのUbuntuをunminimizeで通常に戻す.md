---
title: "DockerのUbuntuをunminimizeで通常に戻す"
source: "https://qiita.com/ssc-ynakamura/items/d69307a3d94bf81c363d"
author:
  - "[[ssc-ynakamura]]"
published: 2023-06-23
created: 2025-09-01
description: "Linux上での操作手順の確認や「Ubuntuでアレどうやるんだっけ？」みたいなときに、いちいち仮想マシンを用意するのは手間なので、手軽に扱えるDocker上で試してみようとすると、思うように動かないことがあります。 そんな時の解決方法を紹介します。 なお、本記事ではUbu..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiitaにログインして、便利な機能を使ってみませんか？

あなたにマッチした記事をお届けします

便利な情報をあとから読み返せます

[ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fssc-ynakamura%2Fitems%2Fd69307a3d94bf81c363d&realm=qiita) [新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fssc-ynakamura%2Fitems%2Fd69307a3d94bf81c363d&realm=qiita)

この記事は最終更新日から1年以上が経過しています。

Linux上での操作手順の確認や「Ubuntuでアレどうやるんだっけ？」みたいなときに、いちいち仮想マシンを用意するのは手間なので、手軽に扱えるDocker上で試してみようとすると、思うように動かないことがあります。

そんな時の解決方法を紹介します。

なお、本記事ではUbuntuイメージを対象としています。CentOS等の他のディストリビューションについては事情も異なるでしょうし扱いません。

## DockerのUbuntuは特殊版

Dockerイメージというのは基本的に余計なものを極限まで削ぎ落として軽量化されているものが多く、一般的に中に入ってインタラクティブにあれこれ操作するのには向いていません。

普通のLinuxのような感覚で操作しようと思ったら、下記のような謎のメッセージが表示されて動かない…… `man` コマンドも使えない、みたいな経験はありませんか？

```text
This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, including manpages, you can run the 'unminimize'
command. You will still need to ensure the 'man-db' package is installed.
```

これはDockerコンテナとしてアプリを実行する目的には不要である「インタラクティブな操作に関する機能」が削ぎ落とされている状態なのです。

Ubuntuの場合、Minimal Ubuntu（あるいはUbuntu Minimal Cloud Images）という、人が直接ログインして操作することを想定しない、クラウドやコンテナ向けに軽量化したものが使われているそうです。  
（参考: [「Minimal Ubuntu」とは？Google Compute Engineで早速試してみた！ | LFI](https://linuxfan.info/minimal-ubuntu-released) ）

では普通のUbuntuのように使うことはできないのでしょうか？  
いいえ、そんなことはありません。

## unminimizeで通常のUbuntuに戻す

メッセージをよく読んでみると、文中に `unminimize` というコマンドが出てきますね？

はい、UbuntuのDockerイメージの場合 [^1] は、 `unminimize` というコマンドを実行すると、削ぎ落とされていたコンテンツが復元されて、通常のUbuntu Serverとだいたい同じような状態になります。

```console
root@486452421de5:/# unminimize
This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

This script restores content and packages that are found on a default
Ubuntu server system in order to make this system more suitable for
interactive use.

Reinstallation of packages may fail due to changes to the system
configuration, the presence of third-party packages, or for other
reasons.

This operation may take some time.

Would you like to continue? [y/N]
```

実行するとこのようなメッセージが表示されるので、 `y` を入力すると削ぎ落とされていたパッケージのインストールが始まります。  
パッケージのインストール中にも yes/no を聞かれたりするので、 `yes | unminimize` というように実行するとよいでしょう。

## 追加のパッケージをインストールする

`unminimize` しただけだと、 `man` やその他のよく使うユーティリティは入っていないので、これらは別途インストールする必要があります。

```sh
apt install man-db vim git curl wget # などなど
```

Dockerなのでsystemdを使うといったことはできませんが、これでだいたい普通のUbuntuと同じように操作できるようになります。

[0](https://qiita.com/ssc-ynakamura/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fssc-ynakamura%2Fitems%2Fd69307a3d94bf81c363d&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fssc-ynakamura%2Fitems%2Fd69307a3d94bf81c363d&realm=qiita)

[14](https://qiita.com/ssc-ynakamura/items/d69307a3d94bf81c363d/likers)

いいねしたユーザー一覧へ移動

5

[^1]: 他のディストリビューションの場合はどのような軽量化がされているのか、また、元に戻す方法は存在するのかなどは、調べていないのでわかりません。