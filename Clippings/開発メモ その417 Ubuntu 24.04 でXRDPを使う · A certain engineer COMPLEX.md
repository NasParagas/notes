---
title: "開発メモ その417 Ubuntu 24.04 でXRDPを使う · A certain engineer \"COMPLEX\""
source: "https://taktak.jp/2025/02/22/4524/"
author:
  - "[[Core]]"
published:
created: 2025-08-26
description: "Introductionこれまで、Ubuntu で RDP する記事を書いてきた。開発メモ その325 Ubuntu 20.04でXRDPを使う開発メモ その296 Ubuntu 22.04.1でXRDPを使う開発メモ その212 Ubuntu 18.04.5でXRDPを使う開発メモ その"
tags:
  - "clippings"
---
## Introduction

これまで、Ubuntu で RDP する記事を書いてきた。

- [開発メモ その325 Ubuntu 20.04でXRDPを使う](https://taktak.jp/2023/03/05/4483/)
- [開発メモ その296 Ubuntu 22.04.1でXRDPを使う](https://taktak.jp/2022/08/27/4455/)
- [開発メモ その212 Ubuntu 18.04.5でXRDPを使う](https://taktak.jp/2021/01/17/4374/)
- [開発メモ その187 Ubuntu 18.04.3でXRDPを使う](https://taktak.jp/2019/11/10/4203/)

今回も同様。

## How to use?

インストール自体は最近は `apt` で一発。  
だが、少々設定に手を加える必要がある。

```bash
$ sudo apt install xrdp
```

このままだと画面が真っ暗で何も映らないため

```bash
$ sudo vi /etc/gdm3/custom.conf
```

下記のように修正。

```
# GDM configuration storage

  #

  # See /usr/share/gdm/gdm.schemas for a list of available options.

  

  [daemon]

  # Uncomment the line below to force the login screen to use Xorg

- # WaylandEnable=false

+ WaylandEnable=false

  

  # Enabling automatic login

  #  AutomaticLoginEnable = true

  #  AutomaticLogin = user1
```

そして、 `xrdp` を自動起動設定して再起動。

```bash
$ sudo systemctl enable xrdp

$ sudo systemctl start xrdp
```

そして Windows から RDP 接続すると、無事に

[![firewall](https://taktak.jp/public/2025/02/22/4524/xrdp-before.png)](https://taktak.jp/public/2025/02/22/4524/xrdp-before.png)

…誰？

## GUI が全然違う

Ubuntu 24.04 のコードネームは **Noble Numbat (高貴なナンバット \[アリクイ\])** というように、壁紙は王冠がデフォルトのはず。  
なのに何だこの青の無機質な RHEL みたいなデスクトップは。

原因は GNOME が起動していないから、とのこと。  
なので下記で GNOME を有効にする。

```bash
$ sudo vi /etc/xrdp/startwm.sh
```

下記を編集。

```
+ export GNOME_SHELL_SESSION_MODE=ubuntu

+ export XDG_CURRENT_DESKTOP=ubuntu:GNOME

  #!/bin/sh

  # xrdp X session start script (c) 2015, 2017, 2021 mirabilos

  # published under The MirOS Licence
```

そして、 `XRDP` を再起動。

```bash
$ sudo systemctl restart xrdp
```

ただ、私の場合は上手くいかなかったので、OS ごと再起動したら治りました。

[![firewall](https://taktak.jp/public/2025/02/22/4524/xrdp-after.png)](https://taktak.jp/public/2025/02/22/4524/xrdp-after.png)