---
title: "Ubuntuでcoreファイルを見つける"
source: "https://qiita.com/k0kubun/items/2f5a1699b8a118088152"
author:
  - "[[k0kubun]]"
published: 2022-06-16
created: 2025-08-21
description: "前提 ulimit -c unlimited とかはやっておく。 /proc/sys/kernel/core_pattern はapportに書き込むようになっているので、apport.serviceがstartしているか確認する。されてなければenable/start..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から3年以上が経過しています。

## 前提

```text
ulimit -c unlimited
```

とかはやっておく。

`/proc/sys/kernel/core_pattern` はapportに書き込むようになっているので、apport.serviceがstartしているか確認する。されてなければenable/start。

## パスの候補1

上記が正しく設定されていれば、通常は以下のパスに入るらしい。

```text
/var/crash/
```

## パスの候補2

自分の場合はそうならないことが多く、 `/var/log/apport.log` を見ると "executable does not belong to a package, ignoring" とかになっていて、その場合以下のパスで見つかる。

```text
/var/lib/apport/coredump/
```

## 参考

- [https://stackoverflow.com/questions/69951510/can-not-find-core-dump-file-in-ubuntu-18-04-and-ubuntu-20-04](https://stackoverflow.com/questions/69951510/can-not-find-core-dump-file-in-ubuntu-18-04-and-ubuntu-20-04)
- [https://askubuntu.com/questions/966407/where-do-i-find-the-core-dump-in-ubuntu-16-04lts](https://askubuntu.com/questions/966407/where-do-i-find-the-core-dump-in-ubuntu-16-04lts)

[0](https://qiita.com/k0kubun/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fk0kubun%2Fitems%2F2f5a1699b8a118088152&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fk0kubun%2Fitems%2F2f5a1699b8a118088152&realm=qiita)

[1](https://qiita.com/k0kubun/items/2f5a1699b8a118088152/likers)

いいねしたユーザー一覧へ移動

1