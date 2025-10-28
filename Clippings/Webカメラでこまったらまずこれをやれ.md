---
title: "Webカメラでこまったらまずこれをやれ"
source: "https://qiita.com/naoppy/items/74bdfa8216c7223f584b"
author:
  - "[[naoppy]]"
published: 2019-12-12
created: 2025-08-26
description: "概要 謎のエラーでwebカメラから画像がとれないorおかしい場合のトラブルシューティングを自分なりにまとめました。 きっかけ なんじゃこりゃああああああああああああああああああああああああああ！！！！！！！！！！！！ OpenCVでいつものようにwebカメラから撮って..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

## 概要

謎のエラーでwebカメラから画像がとれないorおかしい場合のトラブルシューティングを自分なりにまとめました。

## きっかけ

なんじゃこりゃああああああああああああああああああああああああああ！！！！！！！！！！！！  
[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/212534/c3a69958-c0d5-d152-f70e-3a92e5fe5fc5.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F212534%2Fc3a69958-c0d5-d152-f70e-3a92e5fe5fc5.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e54b9244b8d91b498ca7f576e070ce71)

OpenCVでいつものようにwebカメラから撮ってきたところ、画像の半分ほどが緑で埋まる事態に。  
解決に5時間ほど費やしてしまったので、今後のためにまとめた。

## その1. カメラがデバイスとして認識されているか

## USBにwebカメラを刺した後、あるいは撮影しようとした後にdmesgになんか出力されてないか見る。

```bash
$ dmesg
[13759.750846] input: UVC Camera (046d:0825) as /devices/platform/scb/fd500000.pcie/pci0000:00/0000:00:00.0/0000:01:00.0/usb1/1-1/1-1.3/1-1.3:1.0/input/input10
[13761.092510] usb 1-1.3: set resolution quirk: cval->res = 384
```

## lsusbで認識されているか確認

```bash
$ lsusb
Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 003: ID 18d1:9302 Google Inc.
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 009: ID 046d:0825 Logitech, Inc. Webcam C270
Bus 001 Device 002: ID 2109:3431 VIA Labs, Inc. Hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```

私の環境では、上から4つ目に `Bus 001 Device 009: ID 046d:0825 Logitech, Inc. Webcam C270` とあります。  
Logicool C270を使っていることがわかるのでググるときに助かります。

## デバイスファイルが生成されているか確認

/dev/video0とかがwebカメラのデバイスファイルとなるはずです。

```bash
$ ls /dev/video*
/dev/video0  /dev/video1  /dev/video10  /dev/video11  /dev/video12
```

私のラズパイ4では、1つのwebカメラに対して2つデバイスファイル(video0とvideo1)が作られる仕様になっているようです。  
しかし、プログラムで使うときはどちらか片方は使えますが、もう片方は失敗するようです。  
また、グラフィックチップも `video10, video11, video12` として認識されているようです。こういうのは無視して大丈夫です。

またデバイスファイルが権限の問題で開けないなどの原因もあるようです。権限が大丈夫かも確かめましょう。

## その2. エラーがでる方法以外で画像をキャプチャしてみる。

参考： [https://linux.keicode.com/tools/how-to-capture-image-from-terminal-fswebcam.php](https://linux.keicode.com/tools/how-to-capture-image-from-terminal-fswebcam.php)

## fswebcamをいれる

```bash
$ sudo apt-get install fswebcam
```

## 画像を撮ってみる

こんな感じで使う。

```bash
$ fswebcam -d /dev/video0 -r 640x480 test.jpg
```

## 表示

```bash
$ eog test.jpg
```

これで上手く撮れるのなら、とりあえずwebカメラが壊れてることは無さそう。  
ただfswebcamはかなり賢いので、これが上手くいっても、自分の書いたプログラム上で上手く動かない時もある。

## その3. 使っているWebカメラの情報を詳しく見る

参考： [https://leico.github.io/TechnicalNote/Linux/webcam-usage](https://leico.github.io/TechnicalNote/Linux/webcam-usage)

## v4l-utilsをいれる

```bash
$ sudo apt-get install v4l-utils
```

## 利用可能なカメラを列挙

```bash
$ v4l2-ctl --list-devices
bcm2835-codec-decode (platform:bcm2835-codec):
        /dev/video10
        /dev/video11
        /dev/video12

UVC Camera (046d:0825) (usb-0000:01:00.0-1.3):
        /dev/video0
        /dev/video1
```

## デバイスがサポートするフォーマットと フレームサイズとフレームレートの組み合わせを表示する。

これが一番重要かもしれないです。

```bash
$ v4l2-ctl -d /dev/video0 --list-formats-ext
ioctl: VIDIOC_ENUM_FMT
        Type: Video Capture

        [0]: 'YUYV' (YUYV 4:2:2)
                Size: Discrete 640x480
                        Interval: Discrete 0.033s (30.000 fps)
 
...

                        Interval: Discrete 0.200s (5.000 fps)
                Size: Discrete 160x120
                        Interval: Discrete 0.033s (30.000 fps)

....

               Size: Discrete 1280x960
                        Interval: Discrete 0.033s (30.000 fps)
                        Interval: Discrete 0.040s (25.000 fps)
                        Interval: Discrete 0.050s (20.000 fps)
                        Interval: Discrete 0.067s (15.000 fps)
                        Interval: Discrete 0.100s (10.000 fps)
                        Interval: Discrete 0.200s (5.000 fps)
```

割とある話ですが、 `HDで60fps` を謳っている格安webカメラは、クソ低画質なら60fpsでるけど、HD画質でだと30fpsでないくらい。みたいな感じになってるときがあります。

疑ってください。

あとは、カメラサイズを何も設定しない場合、デフォルトで決められるカメラサイズに、webカメラが対応していなかった。というエラーを非常に良く見ます。  
実はこの記事の冒頭の緑の画像もこれが原因でした。OpenCVはデフォルトで `640x480` で開くのですが、私のカメラは `640x360` でした。

## その4. ソフトウェア的な原因

## RGB BGR RGBAなどのフォーマット問題

色みのついた画像が手に入る場合、ピクセルフォーマットが違う可能性が高いです。

## webカメラの余計な機能が動いている

自動で露光時間を調整してくれる機能などがあるwebカメラなどの場合、その機能を使うと30fps以上はだせない、のようなボトルネックになり得るものがあります。だた、その分綺麗な画像が得られるので難しいところではあります。  
また、それらの設定を無効にしたり、変更するようなAPIは提供されていないことも多いです。

## その5. 帯域について考える

あなたがUSB2.0で接続している場合、規格上の最大伝送速度はたったの `60MB/s` です。

画像一枚のサイズは無圧縮の場合、単純に計算するとカメラサイズ *チャンネル数 バイトとなります。  
例えば 640x480でRGB画像を撮影する場合、640* 480\*3 B = 300 KB程度です。  
これにフレームレートを乗じた数が、伝送速度に近い場合は伝送速度の問題のように思われます。

フレームレートを下げる、あるいはカメラサイズを変更する処理をしましょう。

あるいは、前節にて確認した、フォーマットを変更することで伝送効率を上げるという手段もあります。  
デフォルトでは割と単純な形式なのでCPUに負荷をかけない `YUYV` という形式でカメラからデータを取ってきますが、これを `MPEG` や `H264` にかえることでCPU負荷は高くなりますが、高速に通信することができるようになるかもしれません。

繰り返しますが、フォーマット、カメラサイズ、フレームレートの組み合わせがサポートされていることを前節をみて確認してからにしましょう。

## その6. めっちゃググる

エラーがでるフレームワーク名、カーネル名、デバイス名などでググりましょう。(もうやってるわ！じゃないんですよ)  
私の場合、ラズパイ4、OpenCV4、LogicoolC270で悩んでいたのでひたすらググったら、こんなページを見つけました。  
[https://elinux.org/RPi\_USB\_Webcams](https://elinux.org/RPi_USB_Webcams)  
多くの有名なwebカメラとRaspbianの相性表がまとめられていました。

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/212534/483aa6c6-a5d5-4e76-9c45-bba707a2b539.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F212534%2F483aa6c6-a5d5-4e76-9c45-bba707a2b539.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=26e72e244b34e4f32da53613525dae43)

さらっと有益な情報が書いてありますね。

## 終わりに

ここまで読んでくださってありがとうございました。  
webカメラで悩んでいる方の助けになれば幸いです。

間違いや誤解していると思われる記述があればコメントで積極的にご指摘お願いします。

[1](https://qiita.com/naoppy/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fnaoppy%2Fitems%2F74bdfa8216c7223f584b&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fnaoppy%2Fitems%2F74bdfa8216c7223f584b&realm=qiita)

[40](https://qiita.com/naoppy/items/74bdfa8216c7223f584b/likers)

いいねしたユーザー一覧へ移動

38