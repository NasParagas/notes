---
title: "[Git] mergeコマンドの復習"
source: "https://qiita.com/yam_dev/items/47bd70e7582b14154541"
author:
  - "[[yam_dev]]"
published: 2024-12-01
created: 2025-07-04
description: "こんにちは！ 開発で git merge コマンドを利用してますが、よく忘れるのでまとめ。（使いそうなものだけ） ネタ元はこちら。 基本コマンド 1. 通常のマージ git merge <ブランチ名> 指定したブランチの変更を現在のブランチに統合します。 オプション..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## エンジニアとしての市場価値を測りませんか？PR

[無料でForkwellに登録する](https://lp.recruiting.forkwell.com/scout?argument=249xHStF&dmai=a67f4ef09e582b)

こんにちは！

開発で git merge コマンドを利用してますが、よく忘れるのでまとめ。（使いそうなものだけ）

ネタ元は [こちら](https://git-scm.com/docs/git-merge) 。

## 基本コマンド

### 1\. 通常のマージ

```bash
git merge <ブランチ名>
```

指定したブランチの変更を現在のブランチに統合します。

## オプション

### 1\. マージコミットの制御

- `--ff` （デフォルト）
	- 可能な場合はfast-forwardで実行
	- マージコミットを作成しない

※fast-forward は、マージコミットを作成せず、単純にブランチのポインタを最新のコミットまで進める、ということらしい。

- `--no-ff`
	- 必ずマージコミットを作成
	- ブランチの統合履歴を明確に残したい場合に使用
- `--ff-only`
	- fast-forwardできる場合のみマージを実行
	- できない場合は中止

### 2\. コミット制御

- `--no-commit`
	- マージ後のコミットを自動で行わない
	- マージ結果を確認してから手動でコミットしたい場合に使用
- `--squash`
	- ブランチの全変更を1つのコミットにまとめる
	- 履歴をシンプルに保ちたい場合に有効

### 3\. コンフリクト発生時（発生後）のオプション

- `--abort`
	- マージを中止して元の状態に戻る
- `--continue`
	- マージを続行（解決した場合）

※コンフリクト発生に備えたものではなく、コンフリクト発生後、

```text
git merge --abort
```

のように、中止するか、継続するかのオプションです。

### 4\. その他

- `-m "<メッセージ>"`
	- マージコミットのメッセージを指定
- `--stat`
	- マージ時の変更統計を表示
	- どのファイルが変更されたかを確認可能

```text
$ git merge xxx-branch --stat

# 以下のような統計情報が表示されます
src/main.js        | 25 +++++++++++++++----------
src/utils.js       |  8 ++++++++
test/main.test.js  |  5 +++--
3 files changed, 26 insertions(+), 12 deletions(-)
```

## まとめ

`git marge` コマンドは非常に便利ですが、コミットを作るか、履歴をどうするか等、リポジトリの運用に関わる部分があります。

開発体制にもよりますが、リポジトリの運用方針を確認し、適切に使いましょう。

[0](https://qiita.com/yam_dev/items/#comments)

コメント一覧へ移動

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fyam_dev%2Fitems%2F47bd70e7582b14154541&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fyam_dev%2Fitems%2F47bd70e7582b14154541&realm=qiita)

[0](https://qiita.com/yam_dev/items/47bd70e7582b14154541/likers)

いいねしたユーザー一覧へ移動

2