---
title: "git branch コマンド"
source: "https://qiita.com/chihiro/items/e178e45a7fd5a2fb4599"
author:
  - "[[chihiro]]"
published: 2017-06-10
created: 2025-07-03
description: "20190505追記 わかりにくい表現を修正しました。 git branch -vコマンドの説明を追加しました。 git branch \コマンドの説明に追記しました。 git branchコマンドについて、主にオプションをまとめました。 ..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiitaにログインして、便利な機能を使ってみませんか？

あなたにマッチした記事をお届けします

便利な情報をあとから読み返せます

[ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fchihiro%2Fitems%2Fe178e45a7fd5a2fb4599&realm=qiita) [新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fchihiro%2Fitems%2Fe178e45a7fd5a2fb4599&realm=qiita)

この記事は最終更新日から5年以上が経過しています。

**20190505追記**

- わかりにくい表現を修正しました。
- `git branch -v` コマンドの説明を追加しました。
- `git branch \<branchname>` コマンドの説明に追記しました。

---

`git branch` コマンドについて、主にオプションをまとめました。

## 表示

## git branch

ローカルブランチの一覧を表示する。

## git branch -r

`-r` 、もしくは、 `--remotes` オプション。  
リモートブランチの一覧を表示する。

## git branch -a

`-a` 、もしくは `--all` オプション。  
リモートブランチを含んだブランチの一覧を表示する。

## git branch --merged

`--merged` オプション。  
HEADにマージ済みのブランチの一覧を表示する。

## git branch --no-merged

`--no-merged` オプション。  
HEADにマージされていないブランチの一覧を表示する。

## git branch --color

`--color` オプション。  
ブランチの一覧の表示をカラフルにする(デフォルト)。

## git branch --no-color

`--no-color` オプション。  
ブランチの一覧を色付きで表示しない。

## git branch -v

`-v` 、もしくは `--verbose` オプション。  
詳細情報を付与した状態で、ブランチの一覧を表示する。  
各ブランチの先頭のコミットのIDとメッセージを表示する。  
`-vv` オプションを使用することで、追跡しているリモートブランチの名前を表示する。

## 操作

## git branch <branchname>

現在のHEADから、指定したbranchnameを名前として、新しいブランチを作成する。  
新しく作成したブランチへの切り替えは行わない。  
切り替えを行う場合は、 `git checkout <branchname>` を使用する。  
新しいブランチの作成と切り替えを同時に行う場合は、 `git checkout -b <branchname>` を使用する。

## git branch -d <branchname>

`-d` 、もしくは、 `--delete` オプション。  
指定したブランチを削除する。  
`-r` オプションを付けた場合は、リモートブランチを削除する。  
指定したブランチの内容が、追跡しているリモートブランチ(設定されていない場合はHEAD)にマージされていないと削除できない。

## git branch -D <branchname>

`-D` オプション。  
マージの状態に関わらず、指定したブランチを削除する。

## git branch -m <new branchname>

`-m` 、もしくは、 `--move` オプション。  
現在チェックアウトしているブランチ名を<new branchname>に変更する。  
`git branch -m <old branchname> <new branchname>` で変更元を指定することもできる。

## git branch -u <upstream>

`-u` 、もしくは、 `--set-upstream-to=<upstream>` オプション。  
現在チェックアウトしているブランチが、<upstream>で指定したリモートブランチを追跡するようにする。  
`git branch -u <upstream> <branchname>` で対象ブランチを指定することもできる。

## git branch --unset-upstream

`--unset-upstream` オプション。  
現在チェックアウトしているブランチが追跡しているリモートブランチがあった場合、そのひもづきを解除する。  
`git branch --unset-upstream <branchname>` で対象ブランチを指定することもできる。

[1](https://qiita.com/chihiro/items/#comments)

コメント一覧へ移動

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fchihiro%2Fitems%2Fe178e45a7fd5a2fb4599&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fchihiro%2Fitems%2Fe178e45a7fd5a2fb4599&realm=qiita)

## @chihiroのピックアップ記事

### 【git stash】コミットはせずに変更を退避したいとき

1901いいね

投稿日2017年04月28日

[![chihiro](https://qiita-user-profile-images.imgix.net/https%3A%2F%2Fs3-ap-northeast-1.amazonaws.com%2Fqiita-image-store%2F0%2F45528%2Fd654151fa7f0771948992652feb6eb858d47d642%2Fx_large.png%3F1663908115?ixlib=rb-4.0.0&auto=compress%2Cformat&lossless=0&w=128&s=e2d545628538a19df03dcb1cf40cce8d)](https://qiita.com/chihiro)

[

## @chihiro

](https://qiita.com/chihiro)

[RSS](https://qiita.com/chihiro/feed)## [リコーITソリューションズ株式会社](https://qiita.com/organizations/jrits)

信頼と魅力のある先進のITをもとに、お客様のワークスタイル・イノベーションの実現を目指します。

[http://www.jrits.co.jp/](http://www.jrits.co.jp/)

[210](https://qiita.com/chihiro/items/e178e45a7fd5a2fb4599/likers)

いいねしたユーザー一覧へ移動

220