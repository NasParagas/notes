---
title: "【git stash】コミットはせずに変更を退避したいとき"
source: "https://qiita.com/chihiro/items/f373873d5c2dfbd03250"
author:
  - "[[chihiro]]"
published: 2017-04-28
created: 2025-07-07
description: "「とあるブランチで作業中だけど、いますぐやりたいことができた。作業がすごく中途半端だからコミットはしたくない。」 というときに、stashが使えます。 stashを使用すると、コミットしていない変更を退避することができます。 stashで現在の変更を退避して、今すぐやりたい..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## エンジニアとしての市場価値を測りませんか？PR

企業からあなたに合ったオリジナルのスカウトを受け取って、市場価値を測りましょう

[無料でForkwellに登録する](https://lp.recruiting.forkwell.com/scout?argument=249xHStF&dmai=a67f4ef09e582b)

「とあるブランチで作業中だけど、いますぐやりたいことができた。作業がすごく中途半端だからコミットはしたくない。」  
というときに、 `stash` が使えます。  
`stash` を使用すると、コミットしていない変更を退避することができます。  
`stash` で現在の変更を退避して、今すぐやりたい作業をして、退避させていた変更を戻して作業を再開することができます。

## 変更を退避する

```bash
$ git stash -u
```

コミットしていない変更がある状態で上記のコマンドを実行すると、変更した部分が退避されます。  
ワーキングディレクトリ上は差分がない状態になります。  
「コミットしていない変更」とは、 `add` したものも `add` していないものもどちらも含まれます。  
`-u` は　 `--include-untracked` の略です。新規作成ファイル(追跡対象に含まれていないファイル)も退避することができます。

## 退避した作業の一覧を見る

以下のコマンドで退避した作業の一覧を見ることができます。

```bash
$ git stash list
stash@{0}: WIP on test: xxxx
stash@{1}: WIP on commit-sample: xxxx
```

`stash@{n}` から始まる1行が1回分のstashになります。  
`WIP on` のあとはブランチ名です。  
`xxxx` はstashをしたときのHEADのコミットハッシュとコミットメッセージになります。

## 退避した作業を戻す

```bash
$ git stash apply stash@{0}
```

`stash@{0}` の作業をもとに戻します。

`git stash apply stash名` にて退避した作業を元に戻すことができます。  
このとき、現在チェックアウトしているブランチへ退避した変更が書かれます。  
変更を退避したときのブランチにも、それ以外のブランチにも戻すことができます。  
また、stash名を指定しない場合は、直近に退避された作業を戻します。

`stash@{0}` のstash名は省略することもできます。省略した場合は、直近にstashした情報が戻されます。

上記のコマンドだと、addしていた変更もaddされていない状態で戻ります(`staged` されていた変更も `not staged` ととして戻る)。  
addした状態そのままにもどしたいときは、上記のコマンドに `--index` オプションを付けて実行します。

```bash
$ git stash apply stash@{0} --index
```

## 退避した作業を消す

`stash` を使用して退避した作業を元に戻しても、退避した情報は残ったままです。  
`git stash list` を実行すると、退避した情報はそのまま残っているはずです。  
退避した作業を消すには、以下のコマンドを使用します。

```bash
$ git stash drop stash@{0}
```

`stash@{0}` をstashのリストから削除します

`drop` と同様、 stash名（ `stash@{0}` ）を省略すると、直近にstashした情報を消すことができます。

## 退避した作業を元に戻すと同時に、stashのリストから消す

退避した作業を消すには `drop` を使用する必要がありましたが、作業をもとに戻すと同時に消すこともできます。

```bash
$ git stash pop stash@{0}
```

`stash@{0}` の作業をもとに戻すと同時に、退避作業の中から削除します

こちらもstash名（ `stash@{0}` ）を省略すると、直近にstashした情報を消すことができます。  
退避した変更をもとに戻す際にコンフリクトが発生する場合もあるので注意してください。

## そのほか

### 作業を退避するときにメッセージを付ける

```bash
$ git stash save "stash message"

$ git stash list
stash@{0}: On test: stash message
```

コミットハッシュ+コミットメッセージが表示されていた部分が、指定したメッセージになっています。

### 退避した変更の詳細をみる

```bash
$ git stash show stash@{0}
```

変更ファイルの一覧を見ることができます。  
また、 `-p` オプションをつけることで、変更内容の詳細をみることもできます。

```bash
$ git stash show stash@{0} -p
```

### addした変更以外を退避させる

オプションを何もつけない場合、addした変更もaddしていない変更も退避されます(新規追加ファイルは例外)。  
`-k` (もしくは--keep-index)をつけて実行すると、addした変更は退避されません。

```bash
$ git stash -k
```

### 退避した作業をすべて消す

```bash
$ git stash clear
```

`git stash list` を実行すると、退避した情報がすべて消えていることが確認できます。

[1](https://qiita.com/chihiro/items/#comments)

コメント一覧へ移動

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fchihiro%2Fitems%2Ff373873d5c2dfbd03250&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fchihiro%2Fitems%2Ff373873d5c2dfbd03250&realm=qiita)

[1903](https://qiita.com/chihiro/items/f373873d5c2dfbd03250/likers)

いいねしたユーザー一覧へ移動

1503