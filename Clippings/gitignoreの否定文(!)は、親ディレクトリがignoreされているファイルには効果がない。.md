---
title: ".gitignoreの否定文(!)は、親ディレクトリがignoreされているファイルには効果がない。"
source: "https://qiita.com/NumAniCloud/items/fd452828f634b577ae28"
author:
  - "[[NumAniCloud]]"
published: 2014-12-23
created: 2025-08-26
description: ".gitignore ファイルにファイル名のパターンをリストにして書いておくと、それらをgitによる管理から無視するようにできるわけですが、先頭に!を付けた行は「ただしこのファイルは無視しない」という意味になります。この!についてハマったところを解説します。 理想と現実 ..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から1年以上が経過しています。

`.gitignore` ファイルにファイル名のパターンをリストにして書いておくと、それらをgitによる管理から無視するようにできるわけですが、先頭に`!`を付けた行は「ただしこのファイルは無視しない」という意味になります。この`!`についてハマったところを解説します。

## 理想と現実

ディレクトリを無視した時に、その中の１ファイルだけ無視しないようにしたいことがあります。次のようなディレクトリ階層を考えます：

```text
└─.gitignore
└─bin/ #これ以下を全部無視したい
  └─hoge 
  └─fuga
  └─Resource #でも、これは無視したくない
```

試しに次のように書いてみます。

.gitignore

```txt
bin/
!Resource
```

しかし、これではうまく行きません。これだと `bin` 以下は `Resource` も含めてすべて無視されてしまいます。 `.gitignore` では、親ディレクトリが無視されているファイルは`!`を使っても無視しないようにはできないのです。

`Resource` ファイルは親ディレクトリである `bin` が無視されていたので、`!`による否定が効きません。

## どうするか

`bin` 以下を無視したいけど無視したくないファイルも含んでいる、そんなときは次のように書くのが正しいです。

.gitignore

```txt
bin/*
!Resource
```

bin/の後に\*を追加しました。あくまでディレクトリを無視したのではなく、ディレクトリ内のファイルを無視しましたので、Resourceの否定が効くようになります。

## 参考

[gitignoreまとめ - maeharinの日記](http://d.hatena.ne.jp/maeharin/20130206/gitignore)

[0](https://qiita.com/NumAniCloud/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2FNumAniCloud%2Fitems%2Ffd452828f634b577ae28&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2FNumAniCloud%2Fitems%2Ffd452828f634b577ae28&realm=qiita)

[253](https://qiita.com/NumAniCloud/items/fd452828f634b577ae28/likers)

いいねしたユーザー一覧へ移動

146