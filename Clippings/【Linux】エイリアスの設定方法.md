---
title: "【Linux】エイリアスの設定方法"
source: "https://qiita.com/ydah/items/18d242b632e5486fe912"
author:
  - "[[ydah]]"
published: 2019-05-16
created: 2025-08-26
description: "Linuxでエイリアスを設定する方法について、 備忘録も兼ねて書いていきます。 エイリアスってなんぞや？ エイリアス 【alias】 エイリアスとは、偽名、別名、通称などの意味を持つ英単語。 引用元： エイリアスとは : IT用語辞典 e-Words つまり、コマンド..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から3年以上が経過しています。

Linuxでエイリアスを設定する方法について、  
備忘録も兼ねて書いていきます。

## エイリアスってなんぞや？

> エイリアス 【alias】  
> エイリアスとは、偽名、別名、通称などの意味を持つ英単語。  
> 引用元： [エイリアスとは: IT用語辞典 e-Words](http://e-words.jp/w/%E3%82%A8%E3%82%A4%E3%83%AA%E3%82%A2%E3%82%B9.html)

つまり、コマンドの別名を設定します。  
例えばいくつかのコマンドをまとめて別の名前に設定することが出来ます。  
例えば、 `ls -al ` と打っていたのを `ll` と打つだけで実行出来るようにする事が出来ます。

## 設定方法

以下のように、エイリアスを指定してコマンドを実行すれば一時的にエイリアスは有効になります。  
しかし、これだけだとマシンをシャットダウンすると、エイリアスの情報が消えてしまいます。

```bash
alias ll='ls -al'
```

そこで、`.bashrc` というファイルを編集していきます。

## .bashrcってなんぞや？

> （ログインした後に画面上から）bashを起動したときに読み込まれる設定ファイル  
> 引用元: [.bashrcとは｜「分かりそう」で「分からない」でも「分かった」気になれるIT用語辞典](https://wa3.i-3-i.info/word13649.html)

主な用途としては以下のようなものが挙げられます。

- エイリアスを定義する
- シェルの関数を定義する

## 改めて設定方法

### 1..bashrcにエイリアスを定義する

ターミナルを開いて、お好きなテキストエディタで`.bashrc` を開きます。  
例：viの場合

```bash
vi ~/.bashrc
```

.bashrcを開いたら、エイリアスを記述します。

```bash
alias ll='ls -al'
```

### 2..bash\_profileに.bashrcを読み込ませる設定

もし`.bashrc` に書いても、設定が反映されない場合は`.bash_profile` というファイルに`.bashrc` を読み込ませてあげる設定が必要です。

## .bash\_profileってなんぞや？

> ログインシェルがbashの状態でログインしたときに読み込まれる設定ファイル  
> 引用元: [.bash\_profileとは｜「分かりそう」で「分からない」でも「分かった」気になれるIT用語辞典](https://wa3.i-3-i.info/word13650.html)

ターミナルを開いて、お好きなテキストエディタで`.bash_profile` を開きます。  
例：viの場合

```bash
vi ~/.bash_profile
```

.bash\_profileを開いたらファイルの一番下に以下のコマンドを記述します。

```bash
source ~/.bashrc
```

そしてこの変更自体も反映させなければいけないので、以下のどちらかを実施して下さい。

- `source ~/.bash_profile` の実行
- ターミナルの再起動

以上で設定は完了です。

## エイリアスの確認方法

エイリアスが登録出来ているかを確認するには、aliasコマンドを実行するだけでOKです。  
設定されているエイリアスの一覧が出力されます。

```bash
alias
```

## 参考資料

[http://blog.maplesystems.co.jp/programming/18533.html](http://blog.maplesystems.co.jp/programming/18533.html)  
[http://qiita.com/yutat93/items/b5bb9c0366f21bcbea62](http://qiita.com/yutat93/items/b5bb9c0366f21bcbea62)

[0](https://qiita.com/ydah/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fydah%2Fitems%2F18d242b632e5486fe912&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fydah%2Fitems%2F18d242b632e5486fe912&realm=qiita)

[124](https://qiita.com/ydah/items/18d242b632e5486fe912/likers)

いいねしたユーザー一覧へ移動

111