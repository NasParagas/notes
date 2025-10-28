---
title: "[備忘録] wslからusbカメラにアクセス"
source: "https://qiita.com/yarakigit/items/783a1623d7a09ef60736"
author:
  - "[[yarakigit]]"
published: 2022-02-26
created: 2025-08-26
description: "環境構築 wslカーネルのビルド 下記の通り GitHub, PINTO0309, wsl2_linux_kernel_usbcam_enable_conf, https://github.com/PINTO0309/wsl2_linux_kernel_usbcam..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から3年以上が経過しています。

## 環境構築

### wslカーネルのビルド

- 下記の通り
	- [GitHub, PINTO0309, wsl2\_linux\_kernel\_usbcam\_enable\_conf, https://github.com/PINTO0309/wsl2\_linux\_kernel\_usbcam\_enable\_conf#2-usage](https://github.com/PINTO0309/wsl2_linux_kernel_usbcam_enable_conf#2-usage)

## 実行（Powershell ⇒ wsl）

### PowerShell(管理者)

- 下記のコマンドで使用したいカメラの番号を取得（例：1-4）

```bash
$ usbipd wsl list
```

- 有効化

```bash
$ usbipd wsl attach --busid 1-4
```

- 無効化

```bash
$ usbipd wsl detach --busid 1-4
```

### wsl

- usbカメラのデバイスファイルのパーミッションを変更

```bash
$ sudo chmod 777 /dev/video*
```

- `fswebcam` による動作確認

```bash
$ fswebcam -d /dev/video0 -r 640×480 test.jpg
```

[![test.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2525418/2d75ce4a-21a2-818b-0133-c31eb44542f4.jpeg)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2525418%2F2d75ce4a-21a2-818b-0133-c31eb44542f4.jpeg?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=ce634bc4cb912d75caa8dea4a67c4289)

- `v4l2-ctl` を使ったデバイスの確認
	- [qiita: @naoppy Webカメラでこまったらまずこれをやれ](https://qiita.com/naoppy/items/74bdfa8216c7223f584b)
- 利用可能なカメラを列挙

```bash
$ v4l2-ctl --list-devices
```

- デバイスがサポートするフォーマットと フレームサイズとフレームレートの組み合わせを表示する

```bash
$ v4l2-ctl -d /dev/video0 --list-formats-ext
```

## openCVで画像取得

- [qiita: @kakinaguru\_zo（ラズパイで）USBカメラの映像をPythonのOpenCVで高速に表示する](https://qiita.com/kakinaguru_zo/items/eda129635816ad871e9d)
- usbカメラの場合、コーデックをMJPGにしないと動かなかった

```python
import cv2
capture = cv2.VideoCapture(0)
capture.set(cv2.CAP_PROP_FOURCC,cv2.VideoWriter_fourcc('M','J','P','G'))

if capture.isOpened() is False:
  raise IOError

while(True):
  try:
    ret, frame = capture.read()
    if ret is False:
      raise IOError
    cv2.imshow('frame',frame)
    cv2.waitKey(1)
  except KeyboardInterrupt:
    # 終わるときは CTRL + C を押す
    break

capture.release()
cv2.destroyAllWindows()
```

- キャプチャーボードがあればゲーム画面も取得できます  
	[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2525418/6a62131b-3ea3-87b2-fb1e-7d16c5c30741.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2525418%2F6a62131b-3ea3-87b2-fb1e-7d16c5c30741.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e564d61054389a266800d6a03f083c19)

## 使用した機器

- [ロジクール Webカメラ C270n](https://www.amazon.co.jp/%E3%83%AD%E3%82%B8%E3%82%AF%E3%83%BC%E3%83%AB-%E3%82%A6%E3%82%A7%E3%83%96%E3%82%AB%E3%83%A1%E3%83%A9-C270n-%E3%82%B9%E3%83%88%E3%83%AA%E3%83%BC%E3%83%9F%E3%83%B3%E3%82%B0-2%E5%B9%B4%E9%96%93%E3%83%A1%E3%83%BC%E3%82%AB%E3%83%BC%E4%BF%9D%E8%A8%BC/dp/B07QMKND9M/ref=sr_1_3?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&crid=1WZU8XUL79FRB&keywords=c270n&qid=1645875388&s=electronics&sprefix=c270%2Celectronics%2C180&sr=1-3)
- [KINGONE HDMI キャプチャーボード ゲームキャプチャー](https://www.amazon.co.jp/gp/product/B08L3F2818/ref=ppx_yo_dt_b_asin_title_o00_s00?ie=UTF8&psc=1)

## 参考文献

- [Zenn: PINTO WSL2 USBカメラ+他のUSB機器 2022年01月17日版](https://zenn.dev/pinto0309/articles/7c7ce81bea8b6c)
- [DevelopersIO: さかじ Raspberry Piに接続されているwebカメラの情報をv4l2-ctlで簡単に取得したい](https://dev.classmethod.jp/articles/raspberry_pi_v4l2_ctl_get_resorution_from_webcam/)

[1](https://qiita.com/yarakigit/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fyarakigit%2Fitems%2F783a1623d7a09ef60736&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fyarakigit%2Fitems%2F783a1623d7a09ef60736&realm=qiita)

[4](https://qiita.com/yarakigit/items/783a1623d7a09ef60736/likers)

いいねしたユーザー一覧へ移動

2