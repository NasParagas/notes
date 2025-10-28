---
title: "UbuntuでのWebcam確認"
source: "https://qiita.com/motoJinC25/items/c6ef9a36e1d64db1e0f2"
author:
  - "[[motoJinC25]]"
published: 2021-01-29
created: 2025-08-21
description: "背景 Azure IoT EdgeでLive Video Analyticsというモジュールがあり、カメラソース情報を設定しなければいけません。もし、Webcamなど複数カメラを接続している状態ならどのカメラがどちらにつながっているか動作確認と共に事前確認すべきなので困ら..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から3年以上が経過しています。

## 背景

[Azure IoT Edge](https://azure.microsoft.com/en-us/services/iot-edge/) で [Live Video Analytics](https://azure.microsoft.com/en-us/services/media-services/live-video-analytics/) というモジュールがあり、カメラソース情報を設定しなければいけません。もし、Webcamなど複数カメラを接続している状態ならどのカメラがどちらにつながっているか動作確認と共に事前確認すべきなので困らないようにarm64またはx86-64アーキテクチャのUbuntuで確認する方法についてまとめておきます。  
Webcamは [UVC](https://help.ubuntu.com/community/UVC) タイプならLinuxで問題なく使えます。

## 確認方法

## 1\. CLIコマンド

```bash
$ ls /dev/video*
$ lsusb -v
```

## 2\. Cheese Webcam Application

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/287622/35b02ead-0d52-35e0-21eb-b47f6d504de0.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F287622%2F35b02ead-0d52-35e0-21eb-b47f6d504de0.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=f044101c587934a8c4e2ece48cd086a0)

```bash
$ sudo apt-get install cheese
$ cheese
```

### カメラ切り替え

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/287622/382c07b0-4ac6-aa9f-5aad-216e67ff594e.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F287622%2F382c07b0-4ac6-aa9f-5aad-216e67ff594e.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=69b2072ba53530e81df185b682351cdd)  
[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/287622/6806ca74-d84c-9f4b-411a-ff2da9457331.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F287622%2F6806ca74-d84c-9f4b-411a-ff2da9457331.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=4eea995e86a316b38815558b7d4538fb)

## 3\. VLC media player

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/287622/f2c7ca45-df9b-84aa-daad-87ba69ded258.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F287622%2Ff2c7ca45-df9b-84aa-daad-87ba69ded258.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=6319667bf0f21c52563ff984a2dbf255)

```bash
$ sudo apt-get install vlc
$ ls /dev/video*
$ vlc v4l2:///dev/video0
```

[0](https://qiita.com/motoJinC25/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2FmotoJinC25%2Fitems%2Fc6ef9a36e1d64db1e0f2&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2FmotoJinC25%2Fitems%2Fc6ef9a36e1d64db1e0f2&realm=qiita)

[7](https://qiita.com/motoJinC25/items/c6ef9a36e1d64db1e0f2/likers)

いいねしたユーザー一覧へ移動

5