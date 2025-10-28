---
title: "【更新記事】WSL2でWebカメラを使う（usbipd-win v4.0.0）"
source: "https://touch-sp.hatenablog.com/entry/2024/01/07/155445"
author:
  - "[[touch-sp]]"
published: 2024-01-07
created: 2025-08-26
description: "はじめに過去にもWebカメラを使用する方法を書いています。2年ぶりに更新記事を書きました。 touch-sp.hatenablog.com 環境Windows 11です。使用したカメラはロジクール C922 Pro Stream Webcamです。 PS C:\> wsl -v WSL バージョン: 2.0.9.0 カーネル バージョン: 5.15.133.1-1 WSLg バージョン: 1.0.59 MSRDC バージョン: 1.2.4677 Direct3D バージョン: 1.611.1-81528511 DXCore バージョン: 10.0.25131.1002-220531-1700.…"
tags:
  - "clippings"
---
## はじめに

過去にもWebカメラを使用する方法を書いています。  
  
2年ぶりに更新記事を書きました。  
[touch-sp.hatenablog.com](https://touch-sp.hatenablog.com/entry/2022/01/30/001058)  

## 環境

Windows 11です。  
使用したカメラはロジクール C922 Pro Stream Webcamです。
```
PS C:\> wsl -v
WSL バージョン: 2.0.9.0
カーネル バージョン: 5.15.133.1-1
WSLg バージョン: 1.0.59
MSRDC バージョン: 1.2.4677
Direct3D バージョン: 1.611.1-81528511
DXCore バージョン: 10.0.25131.1002-220531-1700.rs-onecore-base2-hyp
Windows バージョン: 10.0.22631.2861
```
```
hoge@NOVA:/mnt/c$ uname -r
5.15.133.1-microsoft-standard-WSL2
```

## 方法

### カーネルのビルド

#### まずは必要なものをインストールします。

```
sudo apt install build-essential flex bison libssl-dev libelf-dev libncurses-dev autoconf libudev-dev libtool bc
```

  
  

#### ベースとなるカーネルをダウンロードします。

```
git clone --depth 1 -b linux-msft-wsl-5.15.y https://github.com/microsoft/WSL2-Linux-Kernel.git
cd WSL2-Linux-Kernel
```

  
  

#### 「.config」ファイルを作ります。（解凍してリネームするだけ）

```
cp /proc/config.gz config.gz
gunzip config.gz
mv config .config
```

  
  

#### 「.config」の中身を変更します。

```
sudo make menuconfig
```

このコマンドでGUIを使って書き換えができます。  
変更したのは５か所です。

- 「Kernel hacking > Compile-time checks and compiler options > General BTF typeinfo」のチェックをはずす

![](https://cdn-ak.f.st-hatena.com/images/fotolife/t/touch-sp/20220129/20220129232250.png)

- 「Device Drivers > Multimedia support」にチェックを付ける

![](https://cdn-ak.f.st-hatena.com/images/fotolife/t/touch-sp/20220129/20220129232404.png)

- 「Device Drivers > Multimedia support > Video4Linux options」内の３つすべてにチェックを付ける

![](https://cdn-ak.f.st-hatena.com/images/fotolife/t/touch-sp/20220129/20220129232527.png)

- 「Device Drivers > Multimedia support > Media drivers > Media USB Adapters」にチェックを付ける

![](https://cdn-ak.f.st-hatena.com/images/fotolife/t/touch-sp/20220129/20220129232644.png)

- 「Device Drivers > Multimedia support > Media drivers > Media USB Adapters > USB Video Class(UVC)」にチェックを付ける

![](https://cdn-ak.f.st-hatena.com/images/fotolife/t/touch-sp/20220129/20220129232724.png)  
  

#### カーネルをビルドします。

```
sudo make -j$(nproc) && sudo make modules_install -j$(nproc) && sudo make install -j$(nproc)
```

  
  

#### 作成された「vmlinux」をWindwos Cドライブの「C:\\Users\\ユーザー名 」内に移動させます。

ユーザー名(username)は適宜自分の環境に合わせて下さい。
```
cp vmlinux /mnt/c/Users/<username>/vmlinux
```

  
  

#### 「.wslconfig」ファイルを作成します

「C:\\Users\\ユーザー名 」内に「.wslconfig」という名前のテキストファイルを作ります。メモ帳などで作れます。  
中身は以下の２行です。ユーザー名(username)は適宜自分の環境に合わせて下さい。
```
[wsl2]
kernel=C:\\Users\\<username>\\vmlinux
```

  
  
「.wslconfig」ファイルを読み込むにはWSL2の再起動が必要です。

### usbipd-winのインストール

下記サイトから「usbipd-win\_4.0.0.msi」をダウンロードして実行します。  
  

### Webカメラをアタッチ

ここから先はWLS2を立ち上げておく必要があります。  
  
コマンドプロンプトを管理者で実行して以下のように実行します。
```
usbipd list
```

このような結果が返ってきます。

```
C:\Windows\System32>usbipd list
Connected:
BUSID  VID:PID    DEVICE                                                        STATE
1-7    26ce:0a08  Realtek USB Audio, USB 入力デバイス                           Not shared
1-13   26ce:01a2  USB 入力デバイス                                              Not shared
1-14   8087:0036  Unknown device                                                Not shared
5-1    0411:0374  Realtek Bluetooth 5.0 Adapter                                 Not shared
5-2    046d:085c  c922 Pro Stream Webcam, C922 Pro Stream Webcam                Not shared
6-1    0853:013f  USB 入力デバイス                                              Not shared
6-2    046d:c547  USB 入力デバイス                                              Not shared
6-3    2341:8036  Arduino Leonardo (COM3), USB 入力デバイス                     Not shared
6-4    2341:006d  USB シリアル デバイス (COM4), DFU-RT Port, USB 入力デバイス   Not shared
```

BUSID 5-2がWebカメラだとわかります。  
  
続いて以下を実行します。

```
usbipd bind -b 5.2
usbipd attach -b 5.2 --wsl
```

これで接続完了です。  
  

### Webカメラをデタッチ

```
usbipd detach -b 5-2
usbipd unbind -b 5-2
```

  
  

[![](https://cdn.image.st-hatena.com/image/square/adad63b72f1d6545b2ba2538c3fc2923b2fd5989/backend=imagemagick;height=80;version=1;width=80/https%3A%2F%2Fcdn.blog.st-hatena.com%2Fimages%2Fcircle%2Fofficial-circle-icon%2Fcomputers.gif)](https://blog.hatena.ne.jp/-/group/11696248318754550880/redirect?blog_id=8454420450087430928)

[ランキング参加中

プログラミング

](https://blog.hatena.ne.jp/-/group/11696248318754550880/redirect?blog_id=8454420450087430928)

[« 【C#】usbipd-winを使ってWSL2にカメラを…](https://touch-sp.hatenablog.com/entry/2024/01/08/093036) [【PyGithub】複数のGitHubリポジトリに対… »](https://touch-sp.hatenablog.com/entry/2024/01/06/143853)