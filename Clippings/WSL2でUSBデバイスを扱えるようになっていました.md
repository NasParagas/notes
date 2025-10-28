---
title: "WSL2でUSBデバイスを扱えるようになっていました"
source: "https://qiita.com/ryoma-jp/items/9db6cca5ed10f1aed7ff#2%E6%89%8B%E9%A0%86"
author:
  - "[[ryoma-jp]]"
published: 2023-02-26
created: 2025-08-26
description: "1.この記事の内容 昔投稿した「WSLがUSBデバイスサポートしていなかった件について」の続報です． 2022年4月に，USBデバイスを接続できるようになっていました． この記事ではUSBカメラの映像を取り込んでウィンドウで表示するところまでの手順を記載します． もし，ウ..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から1年以上が経過しています。

## 1.この記事の内容

昔投稿した「 [WSLがUSBデバイスサポートしていなかった件について](https://qiita.com/ryoma-jp/items/799f3811fc02150094a4) 」の続報です．

2022年4月に，USBデバイスを接続できるようになっていました．  
この記事ではUSBカメラの映像を取り込んでウィンドウで表示するところまでの手順を記載します．

もし，ウィンドウ表示などのGUI関係でトラブルが生じた方は， [WSLでGUIアプリを実行する](https://qiita.com/ryoma-jp/items/bff539d8b060a0ff84cc) の記事も参照してみてください．

基本的な手順は「 [WSL2 USBカメラ+他のUSB機器 2022年09月06日版](https://zenn.dev/pinto0309/articles/e1432253d29e30) 」の通りですが，バージョンの影響なのか，一部異なる点がありましたので，その内容を補足する形で記事に残します．

なお，本記事ではWindows側のTerminalは `Windows Terminal` ではなく `PowerShell` を使用しました．

## 1-1.手順まとめ

[2.手順](https://qiita.com/ryoma-jp/items/#2%E6%89%8B%E9%A0%86) に記載の環境構築を行い，トラブルがすべて解消した状態では，下記の操作でWSLにUSBデバイスを接続できます．

1. Windows側の設定
	- `usbipd wsl list` で接続したいUSBのBUSIDを確認後， `usbipd wsl attach` を実行します
	```shell
	> usbipd wsl list
	BUSID  VID:PID    DEVICE                                                        STATE
	2-2    8087:0032  インテル(R) ワイヤレス Bluetooth(R)                           Not attached
	2-4    0b05:18f3  AURA LED Controller, USB 入力デバイス                         Not attached
	2-14   0b05:1996  Realtek USB2.0 Audio, USB 入力デバイス                        Not attached
	5-1    046d:c53f  USB 入力デバイス                                              Not attached
	5-3    0458:7081  PC Camera                                                    Not attached
	5-4    04bb:101a  USB 大容量記憶装置                                            Not attached
	6-3    056e:00f9  USB 入力デバイス                                              Not attached
	6-4    026d:0002  USB 入力デバイス                                              Not attached
	> usbipd wsl attach --busid  5-3
	usbipd: info: Using default WSL distribution 'Ubuntu-22.04_E_USBIP'; specify the '--distribution' option to select a different one.
	>
	```
2. WSL側の設定（必要に応じて）
	- USBカメラを使うには `/dev/video*` のモード変更が必要です
	USBカメラの例
	```shell
	$ lsusb
	Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
	Bus 001 Device 002: ID 0458:7081 KYE Systems Corp. (Mouse Systems) USB 2.0 PC Camera
	Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
	$ sudo chmod 777 /dev/video*
	```

## 2.手順

## 2-1.バージョンの確認

`wsl --status` ではカーネルバージョンの確認ができないときは， `wsl --version` で確認できます．

```shell
> wsl --status
既定のディストリビューション: Ubuntu-22.04_E
既定のバージョン: 2
> wsl --version
WSL バージョン: 1.1.3.0
カーネル バージョン: 5.15.90.1
WSLg バージョン: 1.0.49
MSRDC バージョン: 1.2.3770
Direct3D バージョン: 1.608.2-61064218
DXCore バージョン: 10.0.25131.1002-220531-1700.rs-onecore-base2-hyp
Windowsバージョン: 10.0.22623.1325
>
```

## 2-2.追加パッケージのインストール

`linux-tools-5.4.0-77-generic` はパッケージが見つからない問題が生じました．

```shell
$ sudo apt install -y linux-tools-5.4.0-77-generic hwdata
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
E: Unable to locate package linux-tools-5.4.0-77-generic
E: Couldn't find any package by glob 'linux-tools-5.4.0-77-generic'
```

[USB/IP client tools](https://github.com/dorssel/usbipd-win/wiki/WSL-support#usbip-client-tools) によると， `linux-tools-virtual` をインストールする手順になっていました．

```shell
$ sudo apt install linux-tools-virtual hwdata
$ sudo update-alternatives --install /usr/local/bin/usbip usbip \`ls /usr/lib/linux-tools/*/usbip | tail -n1\` 20
```

## 2-3.USBカメラの認識確認

PowerShellで `usbipd` のコマンドを実行しようとすると，認識できないコマンドだと怒られてしまいました．

```shell
> usbipd
usbipd: The term 'usbipd' is not recognized as a name of a cmdlet, function, script file, or executable program.
Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
```

PowerShellを起動しなおすと，認識できました．

```text
> usbipd wsl list
BUSID  VID:PID    DEVICE                                                        STATE
2-2    8087:0032  インテル(R) ワイヤレス Bluetooth(R)                           Not attached
2-4    0b05:18f3  AURA LED Controller, USB 入力デバイス                         Not attached
2-9    328f:2005  eMeet M0, USB 入力デバイス                                    Not attached
2-11   0458:7081  PC Camera                                                     Not attached
2-14   0b05:1996  Realtek USB2.0 Audio, USB 入力デバイス                        Not attached
5-1    046d:c53f  USB 入力デバイス                                              Not attached
5-4    04bb:101a  USB 大容量記憶装置                                            Not attached
6-3    056e:00f9  USB 入力デバイス                                              Not attached
6-4    026d:0002  USB 入力デバイス                                              Not attached
```

また，この状態で `wsl attach` の正常に実行できました．

```shell
> usbipd wsl attach --busid 2-11
usbipd: info: Using default WSL distribution 'Ubuntu-22.04_E'; specify the '--distribution' option to select a different one.
> usbipd wsl list
BUSID  VID:PID    DEVICE                                                        STATE
2-2    8087:0032  インテル(R) ワイヤレス Bluetooth(R)                           Not attached
2-4    0b05:18f3  AURA LED Controller, USB 入力デバイス                         Not attached
2-9    328f:2005  eMeet M0, USB 入力デバイス                                    Not attached
2-11   0458:7081  PC Camera                                                     Attached - Ubuntu-22.04_E
2-14   0b05:1996  Realtek USB2.0 Audio, USB 入力デバイス                        Not attached
5-1    046d:c53f  USB 入力デバイス                                              Not attached
5-4    04bb:101a  USB 大容量記憶装置                                            Not attached
6-3    056e:00f9  USB 入力デバイス                                              Not attached
6-4    026d:0002  USB 入力デバイス                                              Not attached
>
```

そして，Ubuntu上でも `lsusb` では認識されるものの， `/dev/video*` に出てこない現象となり，PINTOさんの記事と同じ状態となりました．

```shell
$ lsusb
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 002: ID 0458:7081 KYE Systems Corp. (Mouse Systems) USB 2.0 PC Camera
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
$ ls -l /dev/video*
ls: cannot access '/dev/video*': No such file or directory
$
```

## 2-4.Linuxカーネルのカスタムビルド

[wsl2\_linux\_kernel\_usbcam\_enable\_conf](https://github.com/PINTO0309/wsl2_linux_kernel_usbcam_enable_conf) では，要件が `[Mandatory] WSL2 Ubuntu 20.04` なのですが，筆者の環境は `Ubuntu 22.04` であった為， [Building your own USB/IP enabled WSL 2 kernel](https://github.com/dorssel/usbipd-win/wiki/WSL-support#building-your-own-usbip-enabled-wsl-2-kernel) の手順に従い，対応することとしました．

※普段使用しているディストリビューションのサイズが176GBもあった為，エクスポートには10時間以上かかりました．

```shell
> wsl --system -d Ubuntu-22.04_E df -h /mnt/wslg/distro
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdb       1007G  176G  781G  19% /mnt/wslg/distro
```

※長時間待ってエクスポートしたtarをインポートしようとすると，特定できないエラーが発生したので，あきらめてUSBIP用のディストリビューションを新たに作ることにしました．

```shell
> wsl --import Ubuntu-22.04_E-usbip E:\work\wsl\dist\Ubuntu-22.04_E-usbip .\wsl2-usbip.tar
インポート中です。この処理には数分かかることがあります。
エラーを特定できません
Error code: Wsl/Service/E_FAIL
```

上手くインポートまでできた人は， [Building your own USB/IP enabled WSL 2 kernel](https://github.com/dorssel/usbipd-win/wiki/WSL-support#building-your-own-usbip-enabled-wsl-2-kernel) の手順に沿ってカーネルをリビルドします．

筆者が新しく作り直したディストリビューションのカーネルバージョンは `5.15.90.1-microsoft-standard-WSL2` でしたので，これを `git checkout` しました．

```shell
$ uname -r
5.15.90.1-microsoft-standard-WSL2
```

`make menuconfig` では下記の項目を変更しました．バージョン等により異なると思いますので，環境に合わせて設定してください．

make menuconfigでの設定更新箇所

```text
[*] Network device support  --->
    <*>   USB Network Adapters  --->
        <*>   Multi-purpose USB Networking Framework
            <*>     Host for RNDIS and ActiveSync devices

<M> Multimedia support  --->
    [*]   Autoselect ancillary drivers (tuners, sensors, i2c, spi, frontends)
        Media core support  --->
            <*> Video4Linux core
            [*] Media Controller API
        Video4Linux options  --->
            [*] V4L2 sub-device userspace API
            [*] Enable advanced debug functionality on V4L2 drivers
            [*] Enable old-style fixed minor ranges on drivers/video devices
        Media drivers  --->
            [*] Media USB Adapters  --->
                <*>   USB Video Class (UVC)

[*] USB support  --->
    <*>   USB Modem (CDC ACM) support
    <*>   USB Mass Storage support
    <*>   USB/IP support
        <*>     VHCI hcd
    <*>   USB Serial Converter support  --->
        <*>   USB FTDI Single Port Serial Driver
    USB Physical Layer drivers  --->
        <*> NOP USB Transceiver Driver
```

カーネルをビルド(`sudo make -j 8 && sudo make modules_install -j 8 && sudo make install -j 8`)する際に， `bc` コマンドが見つからないエラーが発生しましたので， `sudo apt install bc` でインストールしました．

```shell
/bin/sh: 1: bc: not found
```

ビルドは開始しましたが， `Failed to generate BTF for vmlinux` というエラーで停止しました．

```shell
MODPOST vmlinux.symvers
  MODINFO modules.builtin.modinfo
  GEN     modules.builtin
BTF: .tmp_vmlinux.btf: pahole (pahole) is not available
Failed to generate BTF for vmlinux
Try to disable CONFIG_DEBUG_INFO_BTF
make: *** [Makefile:1218: vmlinux] Error 1
```

メッセージ通りに，`.config` から `CONFIG_DEBUG_INFO_BTF` を無効となるように変更して(該当行をコメントアウトして)，リビルドしました．ビルド再開にあたりいくつか聞かれましたが， `1. Rely on the toolchain's implicit default DWARF version (DEBUG_INFO_DWARF_TOOLCHAIN_DEFAULT)` を選択して，残りはデフォルトを指定して，ビルドが通りました．

```shell
$ sudo make -j 8 && sudo make modules_install -j 8 && sudo make install -j 8
  SYNC    include/config/auto.conf.cmd
*
* Restart config...
*
*
* Compile-time checks and compiler options
*
Compile the kernel with debug info (DEBUG_INFO) [Y/n/?] y
  Reduce debugging information (DEBUG_INFO_REDUCED) [N/y/?] n
  Compressed debugging information (DEBUG_INFO_COMPRESSED) [N/y/?] n
  Produce split debuginfo in .dwo files (DEBUG_INFO_SPLIT) [N/y/?] n
  DWARF version
  > 1. Rely on the toolchain's implicit default DWARF version (DEBUG_INFO_DWARF_TOOLCHAIN_DEFAULT)
    2. Generate DWARF Version 4 debuginfo (DEBUG_INFO_DWARF4)
    3. Generate DWARF Version 5 debuginfo (DEBUG_INFO_DWARF5) (NEW)
  choice[1-3?]: 1
  Generate BTF typeinfo (DEBUG_INFO_BTF) [N/y/?] (NEW)
  Provide GDB scripts for kernel debugging (GDB_SCRIPTS) [N/y/?] n
Warn for stack frames larger than (FRAME_WARN) [1024] 1024
Strip assembler-generated symbols during link (STRIP_ASM_SYMS) [N/y/?] n
Generate readable assembler code (READABLE_ASM) [N/y/?] n
Install uapi headers to usr/include (HEADERS_INSTALL) [N/y/?] n
Enable full Section mismatch analysis (DEBUG_SECTION_MISMATCH) [N/y/?] n
Make section mismatch errors non-fatal (SECTION_MISMATCH_WARN_ONLY) [N/y/?] n
Force all function address 64B aligned (DEBUG_FORCE_FUNCTION_ALIGN_64B) [N/y/?] n
Compile-time stack metadata validation (STACK_VALIDATION) [Y/?] y
Generate vmlinux.map file when linking (VMLINUX_MAP) [N/y/?] n
Force weak per-cpu definitions (DEBUG_FORCE_WEAK_PER_CPU) [N/y/?] n
  DESCEND objtool
  CALL    scripts/atomic/check-atomics.sh
  CC      arch/x86/kernel/asm-offsets.s
```

## 2-5.USBカメラの動作確認

[WSL2 USBカメラ+他のUSB機器 2022年09月06日版](https://zenn.dev/pinto0309/articles/e1432253d29e30) に記載の動作確認用プログラムを実行すると，Qt関連のエラーが発生しました．

```shell
$ python3 usbcam_test.py
qt.qpa.plugin: Could not load the Qt platform plugin "xcb" in "/home/ryoichi/.local/lib/python3.10/site-packages/cv2/qt/plugins" even though it was found.
This application failed to start because no Qt platform plugin could be initialized. Reinstalling the application may fix this problem.

Available platform plugins are: xcb.

Aborted
```

`QT_DEBUG_PLUGINS=1` をつけて実行すると， `libSM.so.6` が見つからないとのことでした．

追加でパッケージをインストールして，USBカメラの映像の取得と表示をすることができるようになりました．

```shell
$ sudo apt install libsm6 libxrender1 libxext-dev
```

映像のカクつきとノイズが発生しましたが，画像の取得サイズをVGA(640x480)からQVGA(320x240)へ下げることで解消しました．

### VGA

VGAで画像を取り込むとノイズがひどいです．

[![vga.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F983907%2Faee7fc71-3ada-72f7-e8b2-726daec70b08.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=84193e0e4ff822274d2cfb4d93fce187)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F983907%2Faee7fc71-3ada-72f7-e8b2-726daec70b08.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=84193e0e4ff822274d2cfb4d93fce187)

### QVGA

QVGAで取り込むようにするとノイズが解消しました．

[![qvga.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F983907%2Fedafefc8-2f53-07bd-dd82-b7cd70d600b3.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=4252bfbc56c8ce2aaa048bcf1c9c3b92)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F983907%2Fedafefc8-2f53-07bd-dd82-b7cd70d600b3.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=4252bfbc56c8ce2aaa048bcf1c9c3b92)

## 3.さいごに

いろいろトラブルもありましたが，WSLでUSBカメラを扱えるようになりました．  
画像のノイズはUSBカメラの性能のようなので，新調しようかなと思いました．

## 4.関連リンク

- [WSL2 USBカメラ+他のUSB機器 2022年09月06日版](https://zenn.dev/pinto0309/articles/e1432253d29e30)
- [Connect USB devices](https://learn.microsoft.com/en-us/windows/wsl/connect-usb)
- [USB/IP client tools](https://github.com/dorssel/usbipd-win/wiki/WSL-support#usbip-client-tools)
- [Building your own USB/IP enabled WSL 2 kernel](https://github.com/dorssel/usbipd-win/wiki/WSL-support#building-your-own-usbip-enabled-wsl-2-kernel)
- [Python3 OpenCVで ImportError: libSM.so.6: cannot open shared object file: No such file or directoryが出たとき](https://omohikane.com/python3_open_cv_libsm/)

[0](https://qiita.com/ryoma-jp/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fryoma-jp%2Fitems%2F9db6cca5ed10f1aed7ff&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fryoma-jp%2Fitems%2F9db6cca5ed10f1aed7ff&realm=qiita)

[16](https://qiita.com/ryoma-jp/items/9db6cca5ed10f1aed7ff/likers)

いいねしたユーザー一覧へ移動

15