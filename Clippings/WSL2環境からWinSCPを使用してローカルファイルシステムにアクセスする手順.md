---
title: "WSL2環境からWinSCPを使用してローカルファイルシステムにアクセスする手順"
source: "https://qiita.com/Soysoy11110000/items/1b553eed5eebcd6d82bd"
author:
  - "[[Soysoy11110000]]"
published: 2023-04-07
created: 2025-08-26
description: "概要 Windows環境で、WinSCPを利用してWSL2のUbuntu環境へ接続する手順です。 自身のWindows環境から、WSL2のUbuntuへファイルやフォルダを移動させたい場合に活用できます。 手順 1. WSL2環境でSSHサーバーをインストール & 起..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から1年以上が経過しています。

## 概要

Windows環境で、WinSCPを利用してWSL2のUbuntu環境へ接続する手順です。  
自身のWindows環境から、WSL2のUbuntuへファイルやフォルダを移動させたい場合に活用できます。

## 手順

#### 1\. WSL2環境でSSHサーバーをインストール & 起動

```text
sudo apt-get update
sudo apt-get install openssh-server
sudo service ssh start
```

#### 2\. WSL2環境のIPアドレスを確認

```text
ip addr show eth0 | grep -oP '(?<=inet\s)\d+(\.\d+){3}'
# IPアドレスが赤字で表示される
```

コマンドの意味（不必要な方は読み飛ばしてください）

- **ip addr show eth0**: "eth0" という名前のネットワークインターフェースに関する詳細情報を表示
- **grep**: 検索コマンド
- **|(パイプ)**: 直前のコマンドをフィルタリングする
- **\-o**: マッチした部分だけを表示。
- **\-P**: Perlの正規表現構文を使用。
- **(?<=inet\\s)\\d+(.\\d+){3}**: 正規表現
- **eth0**: Linuxシステムで最初に認識されるイーサネットインターフェース
	- **Ethernet** ：ローカルエリアネットワーク（LAN）で最も広く使用されるネットワークプロトコルの1つ

#### 3\. WinSCPを起動 & Sessionの設定

> ファイルプロトコル: SFTP  
> ホスト名: WSL2環境のIPアドレス  
> ポート番号: 22  
> ユーザー名: WSL2環境のユーザー名

#### 4.Session設定で保存した情報を選択し、ログイン

[2](https://qiita.com/Soysoy11110000/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2FSoysoy11110000%2Fitems%2F1b553eed5eebcd6d82bd&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2FSoysoy11110000%2Fitems%2F1b553eed5eebcd6d82bd&realm=qiita)

[2](https://qiita.com/Soysoy11110000/items/1b553eed5eebcd6d82bd/likers)

いいねしたユーザー一覧へ移動

7