---
title: "WSL2でRvizやGazeboなどを動かす"
source: "https://qiita.com/comocc/items/68d4ca02277abd2c457c"
author:
  - "[[comocc]]"
published: 2021-02-16
created: 2025-08-26
description: "はじめに WSL2上のUbuntuからWindows上のXサーバーを利用してRvizやGazeboなどを表示させようとすると、Segmentation Faultなどが発生する場合がある。 この記事はその対策についてのメモである。 前提 WSL2、Ubuntu、ROS、..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から3年以上が経過しています。

## はじめに

WSL2上のUbuntuからWindows上のXサーバーを利用してRvizやGazeboなどを表示させようとすると、Segmentation Faultなどが発生する場合がある。  
この記事はその対策についてのメモである。

## 前提

WSL2、Ubuntu、ROS、Rviz、Gazebo等のインストールは済んでいるものとする。

## 検証環境

### ソフトウェア

Windows 10 Pro バージョン 20H2 (OS ビルド 19032.804)  
WSL2  
Ubuntu 18.04.5 LTS (Bionic Beaver)  
ROS Melodic (aptでインストールしたもの)  
[VcXsrv](https://sourceforge.net/projects/vcxsrv/) Version 1.20.8.1

### ハードウェア

ALIENWARE m15  
Core i7  
NVIDIA RTX 2080

## 設定方法

### WSL2上のUbuntu側

`~/.bashrc` に以下を追記して `source ~/.bashrc` する。

```bash
export DISPLAY="\`grep nameserver /etc/resolv.conf | sed 's/nameserver //'\`:0"
export LIBGL_ALWAYS_INDIRECT=0
```

ここで `export LIBGL_ALWAYS_INDIRECT=0` は `export LIBGL_ALWAYS_INDIRECT=` でも良いようだ。(動作することは確認したが正確な仕様は未確認)

### Windows上のVcXsrvのXLaunch側

XLaunch起動後の画面遷移を順番に貼っていく。

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/39561/4bc9765e-0a65-af4b-249f-dcf01e887f53.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F39561%2F4bc9765e-0a65-af4b-249f-dcf01e887f53.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a77aac6c311b797c2064c26c64e126a1)

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/39561/1e0116a0-6e42-7365-b41c-64a43ee40a2b.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F39561%2F1e0116a0-6e42-7365-b41c-64a43ee40a2b.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=7dde7957ad9c8bcb4538b47e3644c377)

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/39561/781e7e99-fe4e-7ff1-e51e-2458e1d9d8d1.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F39561%2F781e7e99-fe4e-7ff1-e51e-2458e1d9d8d1.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=deaffa42b3371640497df73eb8bfa4ed)  
`Native OpenGL` のチェックを外す。  
`Disable access control` にチェックを入れる。

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/39561/33d16c1f-63c1-9980-e3aa-75c3183811c9.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F39561%2F33d16c1f-63c1-9980-e3aa-75c3183811c9.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=12d56e5c6a87fce12ae30de4ede3eae7)

### VcXsrvのWindows Defenderファイアウォールの設定

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/39561/750083a9-9a78-bb20-4cb5-3131a64a1e1c.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F39561%2F750083a9-9a78-bb20-4cb5-3131a64a1e1c.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=ed48b95b5b2afc10a994013633fa30a9)  
最低限 `パブリック` にチェックを入れること。  
恐らくWSL2内とWindowsとの間にはデフォルトゲートウェイが無いので、パブリックネットネットワーク扱いになるのではないかと。

## 動作確認

```bash
$ rviz
```

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/39561/350c2134-5b97-cb59-9c7e-c9d9a069a7f0.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F39561%2F350c2134-5b97-cb59-9c7e-c9d9a069a7f0.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=baac75495c6a6b9e0e93e6aa1ad3c0d6)

## おまけ。起動に失敗する場合の症状

試しに `export LIBGL_ALWAYS_INDIRECT=1` としてみる。  
[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/39561/a85ebd8a-77c8-774d-c447-fbdba93cc023.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F39561%2Fa85ebd8a-77c8-774d-c447-fbdba93cc023.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=37136337863f7c549bd4e5df22ff31cb)

試しに `export DISPLAY=` としてみる。  
[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/39561/414be5c2-6b9f-3a25-05cf-d10d9f549095.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F39561%2F414be5c2-6b9f-3a25-05cf-d10d9f549095.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a0800b3653b928e5874b7e0a7134083e)

## 参考

[WSL1からWSL2への移行](http://www.aise.ics.saitama-u.ac.jp/~gotoh/WSL1ToWSL2.html)

[0](https://qiita.com/comocc/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fcomocc%2Fitems%2F68d4ca02277abd2c457c&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fcomocc%2Fitems%2F68d4ca02277abd2c457c&realm=qiita)

[20](https://qiita.com/comocc/items/68d4ca02277abd2c457c/likers)

いいねしたユーザー一覧へ移動

22