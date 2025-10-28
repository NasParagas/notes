---
title: "Homebrewでpython3系の最新をインストールする方法"
source: "https://qiita.com/toranoko92114/items/08b287e54bdc36943375"
author:
  - "[[toranoko92114]]"
published: 2022-08-02
created: 2025-09-22
description: "Homebrewでpython3系の最新版をインストールする方法を説明します。 5分もあれば終わる作業でした👍 Homebrewがインストールされていることが前提となります そもそもHomebrewとは・・・ Homebrew（ホームブルー）は、macOS（または..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiitaにログインして、便利な機能を使ってみませんか？

あなたにマッチした記事をお届けします

便利な情報をあとから読み返せます

[ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Ftoranoko92114%2Fitems%2F08b287e54bdc36943375&realm=qiita) [新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Ftoranoko92114%2Fitems%2F08b287e54bdc36943375&realm=qiita)

この記事は最終更新日から3年以上が経過しています。

[@toranoko92114 (とらのこ)](https://qiita.com/toranoko92114)

## Homebrewでpython3系の最新をインストールする方法

投稿日

Homebrewでpython3系の最新版をインストールする方法を説明します。

5分もあれば終わる作業でした👍

Homebrewがインストールされていることが前提となります

## そもそもHomebrewとは・・・

Homebrew（ホームブルー）は、macOS（またはLinux）用パッケージマネージャーにです。

インストール方法や、基本的な使い方はこちらに記載していますので、まだインストールしていないという方やどう使うか分からないという方は是非一度ご覧ください！

## Python3系のインストール

当記事の執筆時点で、Python3系の最新版はPython@3.9になりますので、Python@3.9をインストールしていきます。

```bash
% brew search python@3
==> Formulae
boost-python3   python@3.7      python@3.9      bpython         cython
python@3.8      ipython         jython
```

HomebrewでPython@3.9をインストールします。

```bash
% brew install python@3.9
```

これでインストールされたはずです！

## バージョンを確認する

それでは、バージョンを確認してみましょう。

```bash
% python3 -V                
Python 3.9.10
```

Python3.9系が表示されるので、大丈夫そうですね。

上記のバージョン確認したときに、自分が入れたバージョンでない（たとえばPython 3.8系）が表示されたときは、インストールしたバージョンでPath（パス）を通してあげる必要があります！

`export PATH="/usr/local/opt/python@3.9/libexec/bin:$PATH"`

インストールは以上になります！

## ちなみに・・・pipも確認しておこう

pipとは、Pythonのパッケージを管理するためのツールを指します。

これがあることで、コマンド一つで、seleniumだったり、webdriver\_managerだったりをインストールできる優れものです。

インストール方法としては、基本的にPythonをインストールした時点で自動的にインストールされます。

では、インストールされているかを確認しましょう！

```bash
% pip3 -V
pip 21.3.1 from /opt/homebrew/lib/python3.9/site-packages/pip (python 3.9)
```

全段でインストールしてPython3.9の配下にpipがインストールされていますね！

[0](https://qiita.com/toranoko92114/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Ftoranoko92114%2Fitems%2F08b287e54bdc36943375&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Ftoranoko92114%2Fitems%2F08b287e54bdc36943375&realm=qiita)

[5](https://qiita.com/toranoko92114/items/08b287e54bdc36943375/likers)

いいねしたユーザー一覧へ移動

1