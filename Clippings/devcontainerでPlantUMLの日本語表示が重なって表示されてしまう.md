---
title: "devcontainerでPlantUMLの日本語表示が重なって表示されてしまう"
source: "https://qiita.com/yomon8/items/a3e016b7ffc3e4fbb7e5"
author:
  - "[[yomon8]]"
published: 2023-05-01
created: 2025-08-26
description: "事象 DevContainerでPlantUMLを表示させたい場合以下のように必要なパッケージ入れてあげるだけだと日本語表示がおかしくなります。 RUN apt update && apt install -y default-jre graphviz @startu..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から1年以上が経過しています。

## 事象

DevContainerでPlantUMLを表示させたい場合以下のように必要なパッケージ入れてあげるだけだと日本語表示がおかしくなります。

```dockerfile
RUN apt update && apt install -y default-jre graphviz
```

```text
@startuml
actor user
actor ユーザー
user -> system : order
ユーザー -> システム : 注文する
@enduml
```

↑の定義の場合、↓のように日本語が重なって表示されてしまいます。

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/116068/87d182f0-6538-0495-9124-b62384b673c8.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F116068%2F87d182f0-6538-0495-9124-b62384b673c8.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=49ede2204b9f22dbc6b0e2f3c0548f67)

## 対応

以下のように日本語フォント入れるだけでOKです。

中途半端に表示されているから少し詰まりました。

```text
RUN apt update && apt install -y default-jre graphviz fonts-ipafont
```

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/116068/cd2057d7-c0bb-b684-5b50-473832d03133.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F116068%2Fcd2057d7-c0bb-b684-5b50-473832d03133.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=b07976d498bb859dab9fbfc0d7da0a16)

[2](https://qiita.com/yomon8/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fyomon8%2Fitems%2Fa3e016b7ffc3e4fbb7e5&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fyomon8%2Fitems%2Fa3e016b7ffc3e4fbb7e5&realm=qiita)

[8](https://qiita.com/yomon8/items/a3e016b7ffc3e4fbb7e5/likers)

いいねしたユーザー一覧へ移動

5