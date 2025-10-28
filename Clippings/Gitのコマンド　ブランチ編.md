---
title: "Gitのコマンド　ブランチ編"
source: "https://qiita.com/moriokatakashir/items/18b5dffe235b035e2b36"
author:
  - "[[moriokatakashir]]"
published: 2023-11-02
created: 2025-08-26
description: "Git 今回はGitに不可欠な、ブランチに関するコマンドを7個紹介します！ ・ブランチ:Gitプロジェクト内で異なる作業用のコピーを作成する仕組み 1. git branch ブランチの作成 新しいブランチを作成するときは、git branchを使用します！ ブランチ名..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から1年以上が経過しています。

## Git

今回はGitに不可欠な、 **ブランチに関するコマンド** を7個紹介します！

・ **ブランチ**:Gitプロジェクト内で異なる作業用のコピーを作成する仕組み

## 1\. git branch　ブランチの作成

**新しいブランチを作成するときは** 、 **git branch** を使用します！  
ブランチ名は任意でつけることができます。

**注意：これにより新しいブランチが作成されますが、ブランチの移動は行われません！**

```bash
git branch <新しいブランチ名>
```

## 2\. git checkout　ブランチの切り替え

**既存のブランチへ切り替えるときは** 、 **git checkout** を使用します！  
これにより作成したブランチへ切り替えることが可能です。

```bash
git checkout <既存のブランチ名>
```

## 3\. git checkout -b　ブランチの作成＋切り替え

**新しいブランチ作成とブランチ切り替えを同時に行うときは** 、 **git checkout -b** を使用します！

```bash
git checkout -b <新しいブランチ名>
```

## 4\. git branch -d　ブランチの削除

**不要なブランチを削除するときは** 、 **git checkout -d** を使用します！

```bash
git branch -d <ブランチ名>
```

**注意：削除するブランチがまだマージされていない場合、このコマンドは実行されません！**  
**どうしても削除したい場合は、'-d'フラグを'-D'にすることで強制的に削除できます**

```bash
// マージされていないブランチを強制削除
git branch -D <ブランチ名>
```

## 5\. git branch -a　ローカルブランチの一覧表示

**ローカルリポジトリに存在するブランチを確認するときは** 、 **git branch** を使用します！  
**すべてのブランチを確認するときは** 、 **git branch -a** を使用します！

```bash
git branch
// すべてのブランチを表示する
git branch -a
```

## 6\. git branch -r　リモートブランチの一覧表示

**リモートリポジトリ(GitHub)に存在するブランチを確認するときは** 、 **git branch -r** を使用します！  
これにより、リモートブランチの一覧が表示されます

```bash
git branch -r
```

## 7\. git branch -m　ブランチ名の変更

**ファイルの変更履歴** を確認できる。  
**ブランチ名を変更するときは** 、 **git branch -m** を使用します！  
これにより、現在のブランチ名を変更できます

```bash
git branch -m <新しいブランチ名>
```

## まとめ

**次回は作成、変更したブランチのマージ方法、またコンフリクト解消方法を紹介します！**

[0](https://qiita.com/moriokatakashir/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fmoriokatakashir%2Fitems%2F18b5dffe235b035e2b36&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fmoriokatakashir%2Fitems%2F18b5dffe235b035e2b36&realm=qiita)

[2](https://qiita.com/moriokatakashir/items/18b5dffe235b035e2b36/likers)

いいねしたユーザー一覧へ移動

1