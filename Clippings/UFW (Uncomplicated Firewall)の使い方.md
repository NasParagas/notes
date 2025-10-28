---
title: "UFW (Uncomplicated Firewall)の使い方"
source: "https://zenn.dev/tomoakinagahara/articles/3ef0f6ab777af5"
author:
  - "[[Zenn]]"
published: 2024-11-10
created: 2025-08-26
description:
tags:
  - "clippings"
---
2[tech](https://zenn.dev/tech-or-idea)

## UFW (Uncomplicated Firewall) とは？

**UFW (Uncomplicated Firewall)** とは、 **Debian** 系（ **Ubuntu** など）で一般的に使用されるファイアウォールソフトウェア。 **iptables** のフロントエンド（インターフェース）で、実際のAllow/Denyは **iptables** が行っている。

## ルールを設定したファイル

ルールを設定したファイルの内容は、iptablesへのコマンドが書かれている。

```
/etc/ufw/user.rules
/etc/ufw/user6.rules
```

## UFWの状況を確認する

UFWが起動しているか確認する。

```shell
sudo systemctl status ufw
```

UFWが起動していなければ起動する。

```shell
sudo systemctl start ufw
```

現在の許可リストを確認する。

```shell
sudo ufw status
```

`(v6)` となっているのは、IPv6のこと。

設定の再読み込み。

```shell
ufw reload
```

UFWを停止する。

```shell
ufw stop
```

## UFWを有効化する

起動していても有効になっていない場合があります。  
`Command may disrupt existing ssh connections. Proceed with operation (y|n)?`  
このメッセージは、SSHが有効になっていないと接続が切れるかもしれないという注意です。  
SSHを有効にする方法を次に紹介するので確認して下さい。  
もしSSH接続が切れてしまった場合は、直接ログインして設定し直します。

```shell
sudo ufw enable
```

## UFWを無効にする。

UFWを起動したまま、FW機能を無効にすることができます。

```shell
sudo ufw disable
```

## ssh接続を許可する。

ssh接続を許可しないまま、UFWを有効にすると、sshが切断されます。  
もしSSH接続が切れてしまった場合は、直接ログインして設定し直します。

```shell
sudo ufw allow ssh
```

上記の `ssh` は、ポート20番のエイリアスである。実際は20番ポートへのallow/denyを行う。sshdサーバーの待ち受けポート番号を変更している場合は、その番号を指定すること。

```shell
sudo ufw allow 60022
```

## HTTP/HTTPSを許可

```shell
sudo ufw allow http
sudo ufw allow https
```

## FTPを許可

```shell
sudo ufw allow ftp
```

パッシブモード用に、ポート番号10000〜10010を通す。

```shell
sudo ufw allow 10000:10010/tcp
```

ポート範囲を指定する場合は、必ず `tcp` か `udp` を指定する必要がある。

## TCPだけ許可する（UDPは許可しない）

```shell
sudo ufw allow [ポート番号]/tcp
```

## IPアドレスを指定して許可する

特定のIPアドレスの接続は全て許可する。

```shell
sudo ufw allow from [IPアドレス]
```

あるポートだけ許可したい（ビットコインなど）。

```shell
sudo ufw allow from [IPアドレス] to any port [ポート番号]
```

プロトコルを指定する方法。

```shell
sudo ufw allow from [IPアドレス] to any port [ポート番号] proto tcp
```

## 登録したルールを削除する

手順

1. 削除したいルールの登録番号を調べる
2. 登録番号を指定して削除する  
	ルールの番号を調べる

```shell
sudo ufw status numbered
```

ルールの番号を指定して削除する

```shell
sudo ufw delete [番号]
```

`(v6)` となっているのは、IPv6のこと。

2

### Discussion

![](https://static.zenn.studio/images/drawing/discussion.png)

ログインするとコメントできます

2