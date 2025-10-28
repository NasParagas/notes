---
title: "超絶初心者むけtcpdumpの使い方"
source: "https://qiita.com/tossh/items/4cd33693965ef231bd2a"
author:
  - "[[tossh]]"
published: 2014-07-26
created: 2025-08-26
description: "Software Design 2014年8月号の後藤大地さんの記事を読んだメモを残しておきます。普通に書くとまんまコピーになるので、自分がよく使うであろうコマンドを主に載せています。詳しくかつちゃんと知りたい人はSoftware Designを読みましょうw 0.tcp..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

Software Design 2014年8月号の後藤大地さんの記事を読んだメモを残しておきます。普通に書くとまんまコピーになるので、自分がよく使うであろうコマンドを主に載せています。詳しくかつちゃんと知りたい人はSoftware Designを読みましょうw

## 0.tcpdumpとは

tcpdumpとはネットワーク通信の生のデータをキャプチャし、その結果を出力してくれるキャプチャツールです。

Linuxだとディストリのパッケージ管理ソフトでわりとさくっと入ると思います（無責任w）。実際には導入していませんが、WindowsでもWinDumpというtcpdumpライクなツールがあります。Mac OS X 10.9.4 では最初から入っているようです。

## 1.tcpdumpコマンドの使い方

## 1.1.とにかく実行してみる

次のコマンドでとりあえずtcpdumpを実行できます。

```bash
sudo tcpdump
```

これで、すべてのインターフェースの通信をキャプチャすることになります。

通信の内容をASCIIで見たい場合には次のようにします。

```bash
sudo tcpdump -A
```

## 1.2.オプションの指定

tcpdumpはオプションで色々動作を変えることができます。  
代表的なオプションは下記の通りです。

| オプション | 意味 |
| --- | --- |
| \-i \[interface\] | インターフェースを指定してキャプチャ |
| \-w \[filename\] | \[filename\]で指定したファイルにキャプチャ結果を書き出す |
| \-r \[filename\] | tcpdumpでとったキャプチャ結果を読み込む |
| \-A | キャプチャデータをASCIIで表示 |
| \-p | 自ホスト宛以外のデータはキャプチャしない（非プロミスキャスモード） |
| よく使うコマンドを下記に記載しておきます。 |  |

- インターフェースを指定して実行

```bash
tcpdump -i [interface name]
```

- ファイルに書き出す

```bash
tcpdump -w dumpfile
```

## 1.3.フィルタ指定

大量に流れているパケットの中で特定ホストの特定ポートだけパケットをキャプチャしたいという要求があると思います。もちろんtcpdumpにはパケットフィルタの機能があります。

条件として指定できるものにtype, dir, protoの3つがあります。詳しくは `man 7 pcap-filter` を見て書きましょう（丸投げ）。

よく使う条件式は次のようなものがあります。

- 送信元ipアドレスを指定

```bash
tcpdump src host [src_ip]
```

- 送信先ipアドレスを指定

```bash
tcpdump dst host [dst_ip]
```

- 送信元もしくは送信先にipアドレスを指定

```bash
tcpdump host [target_ip]
```

- 送信元ipアドレスレンジを指定

```bash
tcpdump src net [src_net] mask [net_mask]
```

- 送信先ipアドレスレンジを指定

```bash
tcpdump dst net [dst_net] mask [net_mask]
```

- 送信元もしくは送信先にipアドレスレンジを指定

```bash
tcpdump net [target_net] mask [net_mask]
```

- 送信元のポート番号を指定

```bash
tcpdump src port [port_num]
```

- 送信先のポート番号を指定

```bash
tcpdump dst port [port_num]
```

- 送信先もしくは送信元のポート番号を指定

```bash
tcpdump port [port_num]
```

条件をandやorで組み合わせることもできます。

- 特定IPアドレスの80番ポートに関するトラフィックを見る

```bash
tcpdump port 80 and host 192.168.0.100
```

## 2.実際にやってみる

ここではwebサーバに存在しないページにアクセスしてみます｡サーバ側で `sudo tcpdump  -A -i eth1 port 80` をしたあと、クライアント側で `wget vagrant_cent/neko` を打鍵します。-Aオプションを指定することでどんなデータが流れているのかうかがい知れます。

---

- **tcpdumpの結果（抜粋）**

〜省略〜  
16:23:41.987654 IP 192.168.33.1.63395 > 192.168.33.10.http: Flags \[P.\], seq 1:118, ack 1, win 8235, options \[nop,nop,TS val 582463335 ecr 17636722\], length 117  
E....y@.@..y..!...!  
...P....$${l.. +.......  
"..g.^M.rGET /neko HTTP/1.1  
User-Agent: Wget/1.14 (darwin13.0.0)  
Accept: */*  
Host: vagrant\_cent  
Connection: Keep-Alive

〜省略〜  
16:23:41.994497 IP 192.168.33.10.http > 192.168.33.1.63395: Flags \[P.\], seq 1:785, ack 118, win 227, options \[nop,nop,TS val 17636729 ecr 582463335\], length 784  
E..Df @.@..8..!  
..!..P..$${l...............  
.y"..gHTTP/1.1 404 Not Found  
Date: Sat, 26 Jul 2014 07:23:41 GMT  
Server: Apache/2.2.15 (CentOS)  
X-Request-Id: 9b38104208ce3ff9e34479de251aff52  
X-Runtime: 0.003236  
X-Rack-Cache: miss  
X-Powered-By: Phusion Passenger 4.0.43  
Content-Length: 457  
Status: 404 Not Found  
Connection: close  
Content-Type: text/html; charset=utf-8

<html>  
<head>  
<meta charset="utf-8" />  
<title>Redmine 404 error  
<style>  
body {font-family: Trebuchet MS, Georgia, "Times New Roman", serif; color: #303030; margin: 10px;}  
h1 {font-size:1.5em;}  
p {font-size:0.8em;}  
</style>  
</head>  
<body>  
<h1>Page not found  
<p>The page you were trying to access doesn't exist or has been removed.</p>  
<p><a href="javascript:history.back()">Back</a></p>  
</body>  
</html>

---

## 3.WireSharkでdump結果を確認する

\-wで取得したキャプチャファイルをWireSharkでオープンすれば、WireSharkで解析することが可能です。

## 4.おまけ

[2.で実施したキャプチャデータをCloud Sharkというキャプチャデータ共有サイトに載せておきます。](https://www.cloudshark.org/captures/af80ce8eaec6 "tcpdumpやってみた")

[0](https://qiita.com/tossh/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Ftossh%2Fitems%2F4cd33693965ef231bd2a&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Ftossh%2Fitems%2F4cd33693965ef231bd2a&realm=qiita)

[604](https://qiita.com/tossh/items/4cd33693965ef231bd2a/likers)

いいねしたユーザー一覧へ移動

601