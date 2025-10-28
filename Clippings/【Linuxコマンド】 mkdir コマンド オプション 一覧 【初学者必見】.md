---
title: "【Linuxコマンド】 mkdir コマンド オプション 一覧 【初学者必見】"
source: "https://qiita.com/web-hackers/items/e6cf2b222fc035ee6766"
author:
  - "[[web-hackers]]"
published: 2020-02-24
created: 2025-09-10
description: "【Linuxコマンド】 mkdir コマンド オプション 一覧 【初学者必見】 まとめ Linuxコマンド一覧 mkdir コマンドとは ディレクトリを新規作成するLinuxコマンドです。 \"mkdir\" は \"make directory\"の略です。 コマンドの使い..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiitaにログインして、便利な機能を使ってみませんか？

あなたにマッチした記事をお届けします

便利な情報をあとから読み返せます

[ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fweb-hackers%2Fitems%2Fe6cf2b222fc035ee6766&realm=qiita) [新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fweb-hackers%2Fitems%2Fe6cf2b222fc035ee6766&realm=qiita)

この記事は最終更新日から5年以上が経過しています。

## 【Linuxコマンド】 mkdir コマンド オプション 一覧 【初学者必見】

まとめ  
[Linuxコマンド一覧](https://qiita.com//web-hackers/items/55329223ca39643d950e)

## mkdir コマンドとは

ディレクトリを新規作成するLinuxコマンドです。  
"mkdir" は "make directory"の略です。

### コマンドの使い方

```bash
$ mkdir ディレクトリ名

# hogeディレクトリ作成
$ mkdir hoge
```

## mkdirコマンドのオプション一覧

| オプション | 説明 |
| --- | --- |
| \-m | パーミッションを指定しディレクトリを作成する |
| \-p | エラーを表示せず記述したディレクトリが存在しなければ作成する |
| \-v | ディレクトリを作成した結果のメッセージを表示する |

### \-m オプション

```bash
$ mkdir -m パーミッション指定 ディレクトリ名

# パーミッションが666のhogeディレクトリを作成する。
$ mkdir -m 666 hoge
```

### \-p オプション

mkdirは作成しようとしているディレクトリが存在していると、エラーになりディレクトリが作成されない。ディレクトリがすでにあるにもかかわらず再度作成すると、そのディレクトリ内容も削除されてしまうからです。また、mkdirは作成する親ディレクトリが存在していなくてもエラーになり子ディレクトリが作成されません。  
これらを解決するのが-pオプションです。-pを指定することで、ディレクトリが存在していてもエラーメッセージを表示せず、現在あるディレクトリはそのままにして新規に作成することはなく子ディレクトリを作成することができます。

```bash
$ mkdir -p ディレクトリ名

# 現在hogeディレクトリがある場合は無視し、ない場合はhogeディレクトリを作成する。
$ mkdir -p hoge
```

## その他のLinuxコマンドのオプション一覧

[pwd](https://qiita.com//web-hackers/items/705a296743322ea9561b), [mkdir](https://qiita.com//web-hackers/items/e6cf2b222fc035ee6766), [cd](https://qiita.com/web-hackers/items/88514031ed45a3ec1fd4), [cat](https://qiita.com//web-hackers/items/28bd20992b5fdc57b6a7), [cp](https://qiita.com//web-hackers/items/08420eb57b1765fdc65d), [ls](https://qiita.com/web-hackers/items/0a7c46c2930a831ee759), [touch](https://qiita.com//web-hackers/items/0e44fe3fc5c894790975), [less](https://qiita.com//web-hackers/items/1048bf3f10086f1f1241), [mv](https://qiita.com//web-hackers/items/8ecca14a948d7865ae0f), [rm](https://qiita.com//web-hackers/items/7bb34a0a6228a5c10da2), [ssh](https://qiita.com/web-hackers/items/b4dd0e0da54dbb0ab043), [man](https://qiita.com//web-hackers/items/a0985c6d2a6996089d46), **随時追加中**

### 広告欄

各種開発や構築の請負や初学者向けのメンター業務してます。  
ご興味お持ちいただけた方は\*\* [こちら](https://web-hackers.com/) \*\*へどうぞ

[0](https://qiita.com/web-hackers/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fweb-hackers%2Fitems%2Fe6cf2b222fc035ee6766&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fweb-hackers%2Fitems%2Fe6cf2b222fc035ee6766&realm=qiita)

[4](https://qiita.com/web-hackers/items/e6cf2b222fc035ee6766/likers)

いいねしたユーザー一覧へ移動

4