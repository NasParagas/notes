---
title: "SSH接続のための設定まとめ"
source: "https://qiita.com/010Ri/items/0a09356633655b5613ee"
author:
  - "[[010Ri]]"
published: 2022-07-19
created: 2025-10-29
description: "はじめに 備忘録 SSHでのサーバー接続のために必要な設定をまとめました。 OS SHELL REMARKS CLIENT macOS Monterey zsh %で表記 SERVER Ubuntu 20.04 bash $で表記 どなたかのお..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiita Conference 2025 Autumn

![](https://cdn.qiita.com/assets/public/client-resources/image-papanda-e489702ad88d7aa9-e489702ad88d7aa9.png)

市谷 聡啓

プロダクトづくりから、システムづくりへ

[特設サイトで詳細をチェック](https://qiita.com/official-campaigns/conference/2025-autumn)

## はじめに

備忘録  
SSHでのサーバー接続のために必要な設定をまとめました。

|  | OS | SHELL | REMARKS |
| --- | --- | --- | --- |
| **CLIENT** | macOS Monterey | zsh | `%` で表記 |
| **SERVER** | Ubuntu 20.04 | bash | `$` で表記 |
| どなたかのお役に立てれば幸いです。 |  |  |  |

## サーバーでの事前準備

SSHのためにはサーバーをSSH接続に対応させる必要があります。  
以下では、そのための事前準備の方法を紹介します。

## openssh-serverのインストール

サーバー側（今回はUbuntu）に `openssh-server` をインストールします。

```bash
$ sudo apt install openssh-server
```

`openssh-server` が入っていないとSSHできないので、初めに必ずインストールしましょう。  
インストールすることで `ssh` のデーモンさん（ `sshd` ）が働き始めます。

## SSHのステータスを確認

以下のコマンドで確認できます。

```bash
$ sudo systemctl status ssh
```

サービスが `active` になっていればOKです。

```bash
● ssh.service - OpenBSD Secure Shell server
   Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
   Active: active (running)　・・・ ＜＝　　ここです
　　　　　　　　　　　　　　              :
```

## サービスが実行されていない場合

以下のコマンドで実行できます。

```bash
$ sudo systemctl enable ssh
$ sudo systemctl start ssh
```

## その他

SSHを停止する

```bash
$ sudo systemctl stop ssh
```

SSHを無効にする

```bash
$ sudo systemctl disable ssh
```

## 鍵の生成〜登録〜接続

これまでの設定でSSHのための事前準備が整いました。  
以下では

- [クライアント側での鍵ペア（公開鍵＆秘密鍵）の生成方法](https://qiita.com/010Ri/items/#%E9%8D%B5%E3%82%92%E7%94%9F%E6%88%90%E3%81%99%E3%82%8B)
- [サーバーへの公開鍵の登録方法](https://qiita.com/010Ri/items/#%E3%82%B5%E3%83%BC%E3%83%90%E3%83%BC%E3%81%AB%E9%8D%B5%E3%82%92%E3%82%B3%E3%83%94%E3%83%BC)
- [SSHでの接続方法](https://qiita.com/010Ri/items/#%E3%82%B5%E3%83%BC%E3%83%90%E3%83%BC%E3%81%ABSSH%E6%8E%A5%E7%B6%9A%E3%81%99%E3%82%8B)  
	を紹介します。

## 鍵を生成する

SSHに必要な鍵ペア（秘密鍵と公開鍵）は以下のコマンドで生成できます。

```bash
% ssh-keygen
```

`-t` を利用することで暗号の種類を指定することができます。

```bash
% ssh-keygen -t rsa
% ssh-keygen -t ecdsa
% ssh-keygen -t ed25519
```

`-b` を利用することで鍵長を指定することができます。

```bash
% ssh-keygen -t rsa -b 4096
% ssh-keygen -t ecdsa -b 384
```

`rsa` と `ecdsa` は鍵長を指定できます。  
`ed25519` は `256bit` 固定のようです。

`-C` を利用することで公開鍵の後ろにコメントをすることができます。

```bash
% ssh-keygen -t ed25519 -C ""
```

コメントの有無は認証には関係ありません。  
`-C` なしで鍵を生成すると `username@hostname` がコメントに設定されるようです。  
プライバシーが気になる人は `""` でコメントをブランクにしても良いかもしれません。  
逆に大人数で管理する等の都合でたくさんの公開鍵を管理するサーバーであればコメント有りの方が誰の鍵かわかって良いかも？

`ssh-keygen` を実行すると以下の文章が表示されます。

```bash
Enter file in which to save the key (/username/.ssh/id_ed25519):
Created directory '/username/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```

`1行目` ：鍵ファイルの名前や作成場所を変える場合は入力します  
`2行目` ：ディレクトリがなければ自動的に作られます  
`3, 4行目` ：パスフレーズを設定する場合は入力します  
:::note  
パスフレーズを設定することで安全性を高めることができますが、SSHするたびにパスフレーズを入力しなければいけません。  
秘密鍵が安全に管理されており、パスフレーズの入力が面倒であれば、パスフレーズの設定は見送ってもいいかもしれません。  
:::  
パスフレーズは後から変更することもできます。

```bash
% ssh-keygen -p -f ~/.ssh/id_ed25519
```

## サーバーに鍵をコピー

`ssh-copy-id` コマンドは以下のように書くことができます。

```bash
% ssh-copy-id ユーザー名@サーバーのipアドレス
```

## サーバー側で鍵が書き込まれていることを確認

`less` コマンドをつかってファイルの中身を見ます。

```bash
$ less ~/.ssh/authorized_keys
```

閲覧を終了するには、 `esc` を押してから`:q` と入力して `Enter` を押します。

## サーバーにSSH接続する

`ssh` コマンドは以下のように書くことができます。

```bash
% ssh -i 秘密鍵へのパス ユーザー名@サーバーのipアドレス
```

`id_ed25519` を使う場合は以下のようになります。

```bash
% ssh -i ~/.ssh/id_ed25519 user_name@xxx.xxx.xxx.xxx
```

パスフレーズを設定した場合は、 `ssh` コマンドを実行すると秘密鍵のパスワード入力が求められます。

## SSH接続を切断する

`exit` コマンドで切断できます。

```bash
ユーザー名@ホスト名:~$　exit
```

## SSHできないとき（UFWの設定方法）

ファイアウォールによってSSH接続が拒否されているかもしれません。  
Ubuntuでは、UFWを設定することでアクセスを制限することができます。  
SSHは `22` 番ポートに設定されていることが多いので、ファイアウォールの設定を確かめてみてください。  
また、適切に設定することでサーバーの安全性を高めることができるので、余裕がある人は試してみてください。  
[https://qiita.com/010Ri/items/f37f7f5fb35265823c7c](https://qiita.com/010Ri/items/f37f7f5fb35265823c7c)

[3](https://qiita.com/010Ri/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2F010Ri%2Fitems%2F0a09356633655b5613ee&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2F010Ri%2Fitems%2F0a09356633655b5613ee&realm=qiita)

## @010Riのピックアップ記事

### SSH接続のための設定まとめ

24いいね

投稿日2022年07月19日

### UFWの設定方法

24いいね

投稿日2022年08月25日

[![010Ri](https://qiita-user-profile-images.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2515057%2Fprofile-images%2F1642906611?ixlib=rb-4.0.0&auto=compress%2Cformat&lossless=0&w=128&s=c6fadefa5b6599d0870e894c9e26a82a)](https://qiita.com/010Ri)

[

## @010Ri(010Ri)

](https://qiita.com/010Ri)

備忘録を投稿します。 もし間違っているところや、もっと良い書き方があるところ等あれば、コメントいただけるととても嬉しいです。

[RSS](https://qiita.com/010Ri/feed)

この記事は以下の記事からリンクされています

- [nvidia gpuをつんだPCにインストールしたubuntuの初期設定](https://qiita.com/shimajiroxyz/items/834b28e2f150d724aa35)

## Qiita Conference 2025 Autumn 11月5日(水)~7日(金)開催！

![](https://cdn.qiita.com/assets/public/official_campaigns/qiita_conference_2025_autumn/image-conference_2025_autumn_ogp-d35d2500cd0420d93f2ed69a8d162d74.png)

Qiita Conferenceは、AI時代のエンジニアに贈るQiita最大規模のテックカンファレンスです！

基調講演ゲスト(敬称略)

piacere、牛尾 剛、Esteban Suarez、和田 卓人、seya、ミノ駆動、市谷 聡啓、からあげ、岩瀬 義昌、まつもとゆきひろ、みのるん and more…

[イベント詳細を見る](https://qiita.com/official-campaigns/conference/2025-autumn)

[24](https://qiita.com/010Ri/items/0a09356633655b5613ee/likers)

いいねしたユーザー一覧へ移動

22