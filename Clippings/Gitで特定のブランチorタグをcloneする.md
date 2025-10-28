---
title: "Gitで特定のブランチorタグをcloneする"
source: "https://qiita.com/iaoiui/items/fc318fa75cce3227b638"
author:
  - "[[iaoiui]]"
published: 2018-12-08
created: 2025-08-26
description: "GitHubにあるリポジトリから特定のブランチをcloneしたい git clone リポジトリ名 -b ブランチorタグ名 具体例 例. 特定のブランチのapache tomcatをcloneする ブランチ名を調べる cloneする ここでdepth 1を..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から3年以上が経過しています。

## GitHubにあるリポジトリから特定のブランチをcloneしたい

```text
git clone リポジトリ名 -b ブランチorタグ名
```

## 具体例

例. 特定のブランチのapache tomcatをcloneする

1. ブランチ名を調べる  
	[![tomcat.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/94861/305b52dd-205b-6583-3e07-569fc31a3c18.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F94861%2F305b52dd-205b-6583-3e07-569fc31a3c18.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=48620aabc1025711a03d3d81785401f7)
2. cloneする  
	ここでdepth 1を指定することで、取得するコミットの履歴数を抑え軽量にcloneできる

```text
git clone https://github.com/apache/tomcat.git -b 9.0.13 --depth 1
```

**2020/10/07追記**  
Github向けのCLIツールとしてghがリリースされたので、ghを使う場合の方法も追記します  
ghでは--のあとに、gitコマンドのフラグを使えるので、下記のように読み替えます。

```text
gh repo clone apache/tomcat -- -b 9.0.13 --depth 1
```

[4](https://qiita.com/iaoiui/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fiaoiui%2Fitems%2Ffc318fa75cce3227b638&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fiaoiui%2Fitems%2Ffc318fa75cce3227b638&realm=qiita)

[84](https://qiita.com/iaoiui/items/fc318fa75cce3227b638/likers)

いいねしたユーザー一覧へ移動

59