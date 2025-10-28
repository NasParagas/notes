---
title: "[Git]コミットの取り消し、打ち消し、上書き"
source: "https://qiita.com/shuntaro_tamura/items/06281261d893acf049ed"
author:
  - "[[shuntaro_tamura]]"
published: 2014-09-12
created: 2025-07-07
description: "コミットの取り消し 直前のコミットをなかった事にするには git reset --soft HEAD^ を叩けばOK。 --softオプション：ワークディレクトリの内容はそのままでコミットだけを取り消したい場合に使用。 --hardオプション：コミット取り消した..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## コミットの取り消し

直前のコミットをなかった事にするには

```text
git reset --soft HEAD^
```

を叩けばOK。

- `--soft` オプション：ワークディレクトリの内容はそのままでコミットだけを取り消したい場合に使用。
- `--hard` オプション：コミット取り消した上でワークディレクトリの内容も書き換えたい場合に使用。
- `HEAD^` ：直前のコミットを意味する。
- `HEAD~{n}` ：n個前のコミットを意味する。
	- `HEAD^` や `HEAD~{n}` の代わりにコミットのハッシュ値を書いても良い。
	- gitのv1.8.5からは、「HEAD」のエイリアスとして「＠」が用意されている。
	- `HEAD~` と `HEAD^` と `@^` は同じ意味。
	- `HEAD^^^` と `HEAD~3` と `HEAD~~~` と `HEAD~{3}` と `@^^^` は同じ意味。
- ただしWindowsの場合は `git reset --soft "HEAD^"` と、 `HEAD^` を `"` で囲んでください。

`git reset` の詳細は、下記記事に詳しく書いているので、ぜひ参考にされてください。

▼\[git reset (--hard/--soft)\]ワーキングツリー、インデックス、HEADを使いこなす方法  
[https://qiita.com/shuntaro\_tamura/items/db1aef9cf9d78db50ffe](https://qiita.com/shuntaro_tamura/items/db1aef9cf9d78db50ffe)

## コミットの打ち消し

作業ツリーを指定したコミット時点の状態にまで戻し、コミットを行う（コミットをなかったことにはせず、逆向きのコミットをすることで履歴を残す）には、

```text
git revert コミットのハッシュ値
```

を叩けばOK。指定したコミットを打ち消すコミットを作成することができます。

## コミットの上書き

直前のコミットに上書きするには、

```text
git commit --amend
```

を叩けばOK。

- コミットメッセージを変更したい時よく使う。
- 「 `git rebase` 失敗した時、コンフリクトを避けるためにコミットを上書きする」という使い方もよくする。

[8](https://qiita.com/shuntaro_tamura/items/#comments)

コメント一覧へ移動

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fshuntaro_tamura%2Fitems%2F06281261d893acf049ed&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fshuntaro_tamura%2Fitems%2F06281261d893acf049ed&realm=qiita)

[1826](https://qiita.com/shuntaro_tamura/items/06281261d893acf049ed/likers)

いいねしたユーザー一覧へ移動

1422