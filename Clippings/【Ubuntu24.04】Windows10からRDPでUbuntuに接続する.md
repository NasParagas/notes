---
title: "【Ubuntu24.04】Windows10からRDPでUbuntuに接続する"
source: "https://www.diy-gedankenexperiment.jp/entry/2024/06/16/130755"
author:
  - "[[gedankenexperiment (id:blank16ban_40yrs)]]"
published: 2024-06-16
created: 2025-08-26
description: "Ubuntu24.04LTSに日本語環境を入れたいところですが、効率的に作業するために、まずは、Winパソコンからリモートデスクトップ接続ができるようにします。 1.Ubuntuの設定 (1)xrdpのインストールと起動 (2)ファイアウォールの設定 (3)/etc/groupの修正 (4)Ubuntuの再起動 2.Windows10からUbuntuにRDP接続する (1)リモートデスクトップアプリの起動 (2)リモートデスクトップ接続の設定 3.xrdpの設定変更 (1)RDP接続時のGUI変更(2024/8/6追記) (2)デスクトップに接続すると黒い画面が表示される事への対応(2024/…"
tags:
  - "clippings"
---
Ubuntu24.04LTSに日本語環境を入れたいところですが、効率的に作業するために、まずは、Winパソコンから [リモートデスクトップ](https://d.hatena.ne.jp/keyword/%A5%EA%A5%E2%A1%BC%A5%C8%A5%C7%A5%B9%A5%AF%A5%C8%A5%C3%A5%D7) 接続ができるようにします。

|  |
| --- |

#### 1.Ubuntuの設定

Windows10からRDPを使って、 [Ubuntu](https://d.hatena.ne.jp/keyword/Ubuntu) に [リモートデスクトップ](https://d.hatena.ne.jp/keyword/%A5%EA%A5%E2%A1%BC%A5%C8%A5%C7%A5%B9%A5%AF%A5%C8%A5%C3%A5%D7) (RDP)接続を行うために、xrdpを [Ubuntu](https://d.hatena.ne.jp/keyword/Ubuntu) にインストールします。

##### (1)xrdpのインストールと起動

| $sudo apt-get update | ：おまじない [\*1](https://www.diy-gedankenexperiment.jp/entry/2024/06/16/#f-ec85a1fd "何かをインストールする際、依存性チェックはしてくれますが、念のため、update/upgradeをしておいた方が安心なため、小生はおまじないを唱えることにしています。") |
| --- | --- |
| $sudo apt-get upgrade |  |
|  |  |
| $sudo apt install xrdp | ：xrdpのインストール |

![](https://cdn-ak.f.st-hatena.com/images/fotolife/b/blank16ban_40yrs/20240618/20240618131343.png)

| $sudo systemctl enable xrdp | ：xrdpの [自動起動](https://d.hatena.ne.jp/keyword/%BC%AB%C6%B0%B5%AF%C6%B0) 設定 |
| --- | --- |
| $ sudo systemctl start xrdp | ：xrdpの起動 |

##### (2)ファイアウォールの設定

Windows10が、RDP接続の際に利用するポート3389からの通信を、 [Ubuntu](https://d.hatena.ne.jp/keyword/Ubuntu) に許可します。

| $sudo [ufw](https://d.hatena.ne.jp/keyword/ufw) allow 3389 | ：ポート3389へのアクセスを有効化 |
| --- | --- |

##### (3)/etc/groupの修正

ユーザーがxrdpを使えるように、RDP接続ができるユーザーを/etc/groupファイルのxrdpグループに追加します。

| $sudo [vim](https://d.hatena.ne.jp/keyword/vim) /etc/group | ：/etc/groupファイルを編集 |
| --- | --- |
|  |  |
| lxd:x:135:hogehoge   hogehoge:x:1000:   sambashare:x:136:hogehoge   xrdp:x:137:hogehoge | ：黄字の部分を追記 |

##### (4)Ubuntuの再起動

[自動起動](https://d.hatena.ne.jp/keyword/%BC%AB%C6%B0%B5%AF%C6%B0) の確認も踏まえて、 [Ubuntu](https://d.hatena.ne.jp/keyword/Ubuntu) マシンを再起動します。

#### 2.Windows10からUbuntuにRDP接続する

##### (1)リモートデスクトップアプリの起動

検索で「 [リモートデスクトップ](https://d.hatena.ne.jp/keyword/%A5%EA%A5%E2%A1%BC%A5%C8%A5%C7%A5%B9%A5%AF%A5%C8%A5%C3%A5%D7) 」と入力し、 [リモートデスクトップ](https://d.hatena.ne.jp/keyword/%A5%EA%A5%E2%A1%BC%A5%C8%A5%C7%A5%B9%A5%AF%A5%C8%A5%C3%A5%D7) アプリを見つける。

[リモートデスクトップ](https://d.hatena.ne.jp/keyword/%A5%EA%A5%E2%A1%BC%A5%C8%A5%C7%A5%B9%A5%AF%A5%C8%A5%C3%A5%D7) アプリを起動する。

※アプリを右クリックで、「スタートにピン留めする」または、「タスクバーにピン止留めする」をクリックしておくと便利です。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/b/blank16ban_40yrs/20240606/20240606054803.png)

##### (2)リモートデスクトップ接続の設定

コンピュータ名に [Ubuntu](https://d.hatena.ne.jp/keyword/Ubuntu) マシンの「 [IPアドレス](https://d.hatena.ne.jp/keyword/IP%A5%A2%A5%C9%A5%EC%A5%B9) 」を入力します。

または、 [イントラネット](https://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%C8%A5%E9%A5%CD%A5%C3%A5%C8) のローカル [DNS](https://d.hatena.ne.jp/keyword/DNS) サーバーに [Ubuntu](https://d.hatena.ne.jp/keyword/Ubuntu) マシンを登録してある場合には、 [DNS](https://d.hatena.ne.jp/keyword/DNS) サーバーに登録してあるホスト名を入力し、接続ボタンを押します。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/b/blank16ban_40yrs/20240618/20240618132550.png)

xrdpのログイン画面が表示されるので、ユーザー名とパスワードを入力します。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/b/blank16ban_40yrs/20240618/20240618132312.png)

[Ubuntu](https://d.hatena.ne.jp/keyword/Ubuntu) マシンに接続されますが、 [Ubuntu](https://d.hatena.ne.jp/keyword/Ubuntu) の性能や [イントラネット](https://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%C8%A5%E9%A5%CD%A5%C3%A5%C8) の状況によっては、画面が表示されるまで、しばらく真っ黒なままな場合があります。

[Ubuntu](https://d.hatena.ne.jp/keyword/Ubuntu) へのログインが完了すると、 [リモートデスクトップ](https://d.hatena.ne.jp/keyword/%A5%EA%A5%E2%A1%BC%A5%C8%A5%C7%A5%B9%A5%AF%A5%C8%A5%C3%A5%D7) 経由で、 [Ubuntu](https://d.hatena.ne.jp/keyword/Ubuntu) の操作ができるようになります。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/b/blank16ban_40yrs/20240618/20240618132958.png)

#### 3.xrdpの設定変更

##### (1)RDP接続時のGUI変更(2024/8/6追記)

上記の [リモートデスクトップ](https://d.hatena.ne.jp/keyword/%A5%EA%A5%E2%A1%BC%A5%C8%A5%C7%A5%B9%A5%AF%A5%C8%A5%C3%A5%D7) 経由で接続すると、物理コンソールと [ユーザーインターフェース](https://d.hatena.ne.jp/keyword/%A5%E6%A1%BC%A5%B6%A1%BC%A5%A4%A5%F3%A5%BF%A1%BC%A5%D5%A5%A7%A1%BC%A5%B9) (UI)が異なります。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/b/blank16ban_40yrs/20240806/20240806094247.jpg)

実用上、特に困らないのですが、せっかくですから、物理コンソールとRDPでUIが同じになる様にxrdpの設定を変更します。

| $ sudo [vim](https://d.hatena.ne.jp/keyword/vim) /etc/xrdp/startwm.sh |  |  |
| --- | --- | --- |

下記の赤字部分の2行を追加します。

| export [GNOME](https://d.hatena.ne.jp/keyword/GNOME) \_SHELL\_SESSION\_MODE= [ubuntu](https://d.hatena.ne.jp/keyword/ubuntu)   export XDG\_CURRENT\_DESKTOP= [ubuntu](https://d.hatena.ne.jp/keyword/ubuntu):[GNOME](https://d.hatena.ne.jp/keyword/GNOME)   #![/bin/sh](https://d.hatena.ne.jp/keyword//bin/sh)   \# xrdp X session start script (c) 2015, 2017, 2021 mirabilos   \# published under The MirOS Licence  \[..\] |  |
| --- | --- |

xrdpを再起動します。

| $ sudo systemctl restart xrdp |  |  |
| --- | --- | --- |

物理コンソールとRDPでUIが同じになりました。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/b/blank16ban_40yrs/20240806/20240806100249.jpg)

##### (2)デスクトップに接続すると黒い画面が表示される事への対応(2024/8/12追記)

これは、Ubuntu22.04からの既知の問題だったようです\[[\*2](https://www.diy-gedankenexperiment.jp/entry/2024/06/16/#f-0394fd2a "https://github.com/Ylianst/MeshCentral/issues/4142")\]\[[\*3](https://www.diy-gedankenexperiment.jp/entry/2024/06/16/#f-6ebe4528 "How to enable/disable wayland on Ubuntu Desktop - Linux Tutorials - Learn Linux Configuration")\]。

理由は、"問題は、 [Ubuntu](https://d.hatena.ne.jp/keyword/Ubuntu) 22がWaylandをデフォルトでtrueに切り替え、 [X11](https://d.hatena.ne.jp/keyword/X11) ではfalseにする必要があることです。"（引用元\[[\*4](https://www.diy-gedankenexperiment.jp/entry/2024/06/16/#f-c8b06d6f "xorg - How do I use the X window manager instead of Wayland on Ubuntu 22.04? - Ask Ubuntu")\]、原文英語、 [Google翻訳](https://d.hatena.ne.jp/keyword/Google%CB%DD%CC%F5) ）

| $ sudo [vim](https://d.hatena.ne.jp/keyword/vim) 　/etc/gdm3/custom.conf |  |  |
| --- | --- | --- |

下記の赤字部分のコメントを外します。

| \# GDM configuration storage  #   \# See /usr/share/gdm/gdm.schemas for a list of available options.  \[[daemon](https://d.hatena.ne.jp/keyword/daemon)\]   \# Uncomment the line below to force the login screen to use [Xorg](https://d.hatena.ne.jp/keyword/Xorg)   WaylandEnable=false  \[..\] |  |
| --- | --- |

xrdpを再起動します。

| $ sudo systemctl restart xrdp |  |  |
| --- | --- | --- |

#### 出典・引用・備考

[\*1](https://www.diy-gedankenexperiment.jp/entry/2024/06/16/#fn-ec85a1fd):何かをインストールする際、依存性チェックはしてくれますが、念のため、update/upgradeをしておいた方が安心なため、小生はおまじないを唱えることにしています。

[\*2](https://www.diy-gedankenexperiment.jp/entry/2024/06/16/#fn-0394fd2a):[https://github.com/Ylianst/MeshCentral/issues/4142](https://github.com/Ylianst/MeshCentral/issues/4142)

[\*3](https://www.diy-gedankenexperiment.jp/entry/2024/06/16/#fn-6ebe4528):[How to enable/disable wayland on Ubuntu Desktop - Linux Tutorials - Learn Linux Configuration](https://linuxconfig.org/how-to-enable-disable-wayland-on-ubuntu-22-04-desktop)

[\*4](https://www.diy-gedankenexperiment.jp/entry/2024/06/16/#fn-c8b06d6f):[xorg - How do I use the X window manager instead of Wayland on Ubuntu 22.04? - Ask Ubuntu](https://askubuntu.com/questions/1410256/how-do-i-use-the-x-window-manager-instead-of-wayland-on-ubuntu-22-04/1413490#1413490)

[« 【Ubuntu24.04】インストール(2)：日本語…](https://www.diy-gedankenexperiment.jp/entry/2024/06/17/183825) [【Ubuntu24.04】インストール(1)：インス… »](https://www.diy-gedankenexperiment.jp/entry/2024/06/15/160115)