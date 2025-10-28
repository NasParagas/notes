---
title: "Gitのユーザ情報設定メモ"
source: "https://qiita.com/myy/items/af37a794a12b08a3ee77"
author:
  - "[[myy]]"
published: 2020-03-02
created: 2025-09-09
description: "git commitする時に必要な、ユーザ名とメールアドレス情報設定。 commitしようとした時に空だと設定してねと怒られる。PCセットアップ時とかにいつも忘れる。 (エラーメッセージ中でも以下のコマンドを実行せよと言われている…) globalで設定する場合 (その端..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiitaにログインして、便利な機能を使ってみませんか？

あなたにマッチした記事をお届けします

便利な情報をあとから読み返せます

[ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fmyy%2Fitems%2Faf37a794a12b08a3ee77&realm=qiita) [新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fmyy%2Fitems%2Faf37a794a12b08a3ee77&realm=qiita)

この記事は最終更新日から5年以上が経過しています。

git commitする時に必要な、ユーザ名とメールアドレス情報設定。  
commitしようとした時に空だと設定してねと怒られる。PCセットアップ時とかにいつも忘れる。  
(エラーメッセージ中でも以下のコマンドを実行せよと言われている…)

## globalで設定する場合

(その端末の)ユーザ全体で適用される。

```bash
$ git config --global user.email "you@example.com"
$ git config --global user.name "Your Name"
```

確認

```bash
$ git config --global -l
```

以下の行が入力した通りに表示されていればOK。

```text
user.name=Your Name
user.email=you@example.com
```

## localで設定する場合

複数アカウントを使っている場合などに、特定ポジトリだけの設定をしたい場合は、そのリポジトリ内で、 `--global` を外して実行すればOK。

```bash
$ git config user.email "you@example.com"
$ git config user.name "Your Name"
```

確認

```bash
$ git config --local -l
```

## 参考

もっとしっかり書いてあった…  
[gitconfig の基本を理解する - Qiita](https://qiita.com/shionit/items/fb4a1a30538f8d335b35)

[0](https://qiita.com/myy/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fmyy%2Fitems%2Faf37a794a12b08a3ee77&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fmyy%2Fitems%2Faf37a794a12b08a3ee77&realm=qiita)

[7](https://qiita.com/myy/items/af37a794a12b08a3ee77/likers)

いいねしたユーザー一覧へ移動

5