---
title: "git を https 経由で使うときのパスワードを保存する"
source: "https://qiita.com/usamik26/items/c655abcaeee02ea59695"
author:
  - "[[usamik26]]"
published: 2012-09-24
created: 2025-07-04
description: "git を https 経由で使う場合、pull や push のたびに毎回パスワードを聞かれてしまいます。 これを改善するには git-credential を使うと良いです。 git-credential は git 1.7.9 以降で使用可能です。 なお、古いやり方と..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)


## git を https 経由で使うときのパスワードを保存する

最終更新日 投稿日 2012年12月26日

git を https 経由で使う場合、pull や push のたびに毎回パスワードを聞かれてしまいます。  
これを改善するには [git-credential](http://git-scm.com/docs/gitcredentials) を使うと良いです。

git-credential は git 1.7.9 以降で使用可能です。  
なお、古いやり方としては.netrc を使う方法もありますが、パスワードを平文でファイルに保存するので、やらないほうがいいと思います。

## 使用可能な管理方式

git-credential では、以下のような方法でユーザ名とパスワードを管理できます。

- `git-credential-store`: ファイルに保存します。ただし、パスワードが平文が保存されます。
- `git-credential-cache`: 常駐プロセスに記憶させます。
- `git-credential-osxkeychain`: Mac OS X のパスワード管理を使います。
- `Git-Credential-Manager-for-Windows`: Windows のパスワード管理を使います。
- `git-credential-gnomekering`: GNOME のパスワード管理を使います。

なお、git 本家の contrib に、Windows 用の wincred というものが入っているようです。試していませんが・・・。

## cache

cache 方式は標準で付属しています。  
次のコマンドで cache 方式が使用できます。

```sh
git config --global credential.helper cache
```

初回パスワード入力時に git-credential-cache--daemon プロセスが常駐して、タイムアウト時間が過ぎるまでパスワードを記憶します。

タイムアウトは、デフォルトでは 900 秒（15分）です。変更するには、次のようにタイムアウト時間（例では 3600 秒）を指定します。

```sh
git config --global credential.helper 'cache --timeout=3600'
```

## osxkeychain

osxkeychain は git の contrib として付属しています。MacPorts や Homebrew でインストールすると有効になっています。  
次のコマンドで osxkeychain が使用できます。

```sh
git config --global credential.helper osxkeychain
```

初回パスワード入力時に OS X キーチェーンに登録され、以後はパスワード入力が不要になります。

## windows

別途インストールが必要です。

手順通りインストールを行えば良いです。

初回パスワード入力時に Internet Explorer などと同じ場所にアクセストークンが保存され、以後はパスワード入力が不要になります。

## gnomekeyring

gnomekeyring は別途インストールが必要です。

手順通りビルド・インストールを行えば良いです。

初回パスワード入力時に GNOME キーリングに登録され、以後はパスワード入力が不要になります。

## 最後に

実は同様のことが github のヘルプにほとんど書いてありました。

[0](https://qiita.com/usamik26/items/#comments)

コメント一覧へ移動

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fusamik26%2Fitems%2Fc655abcaeee02ea59695&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fusamik26%2Fitems%2Fc655abcaeee02ea59695&realm=qiita)

[525](https://qiita.com/usamik26/items/c655abcaeee02ea59695/likers)

いいねしたユーザー一覧へ移動

502