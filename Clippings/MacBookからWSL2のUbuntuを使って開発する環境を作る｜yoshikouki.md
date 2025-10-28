---
title: "MacBookからWSL2のUbuntuを使って開発する環境を作る｜yoshikouki"
source: "https://note.com/yoshikouki/n/n10015ed1fcb8"
author:
  - "[[yoshikouki]]"
published: 2024-03-31
created: 2025-09-13
description: "#雑記  今年1月にGeForce RTX 4070 Ti SUPERを載せたWindows PCを買ったが、どうしても Windows の操作に馴れることができず、放置されがちだった。4年前までは業務でWindows PCを使っていたはずなのだが・・・。  Karabiner-Elements や Raycast などのツールのお陰で生産性は上がっているが、がっつりロックインされてしまっている。     ただ Windows PC は安い買い物ではなく、グラボを利用した開発をやりたい欲が強くなってきたため、macOS にロックインされているならWSL2にSSHしちゃえば良いじゃないと"
tags:
  - "clippings"
---
![見出し画像](https://assets.st-note.com/production/uploads/images/135687975/rectangle_large_type_2_01568336980fe2245dc3227a69d2d5bf.png?width=1200)

## MacBookからWSL2のUbuntuを使って開発する環境を作る

[yoshikouki](https://note.com/yoshikouki)

[#雑記](https://note.com/hashtag/%E9%9B%91%E8%A8%98)

今年1月にGeForce RTX 4070 Ti SUPERを載せたWindows PCを買ったが、どうしても Windows の操作に馴れることができず、放置されがちだった。4年前までは業務でWindows PCを使っていたはずなのだが・・・。

Karabiner-Elements や Raycast などのツールのお陰で生産性は上がっているが、がっつりロックインされてしまっている。

  

ただ Windows PC は安い買い物ではなく、グラボを利用した開発をやりたい欲が強くなってきたため、macOS にロックインされているならWSL2にSSHしちゃえば良いじゃないということで環境を整えている。

  

## (参考) WSL2のネットワークについて概観する

まず重要な前提知識として、WSL2のネットワークについて大枠理解しておくとよい。でなければ、ネットに転がっている設定が何をしているのか分からなくなり、ファイヤウォール設定を緩くしすぎたり、設定したことを忘れてしまうかも知れない。

[Hyper-V](https://learn.microsoft.com/ja-jp/virtualization/hyper-v-on-windows/about/) ・Windows・WSL2のネットワークの関係性や、3種類の仮想スイッチ (仮想ネットワーク) について理解しておくとよさそうだ

![画像](https://assets.st-note.com/img/1711853267857-PxbKh41RL2.png)

[https://atmarkit.itmedia.co.jp/ait/articles/1909/09/news020.html](https://atmarkit.itmedia.co.jp/ait/articles/1909/09/news020.html)

上画像が掲載されている記事はとても分かりやすかった。私はこの記事を読んでいたことでつまずいたときに助った

[**Linuxがほぼそのまま動くようになった「WSL2」のネットワーク機能** *2020年春に提供予定のWindows 10の機能アップデート「20H1」に実装予定のLinux環境「WSL 2」の開発が* *atmarkit.itmedia.co.jp*](https://atmarkit.itmedia.co.jp/ait/articles/1909/09/news020.html)

その上でこの記事を読んでみると、なお理解が進む

[**WSL2にMacからSSHする - 西尾泰和のScrapbox** *2022/9/22 僕のメインマシンはMacBookだが、Stable Diffusionの環境を構築するにあたってまとも* *scrapbox.io*](https://scrapbox.io/nishio/WSL2%E3%81%ABMac%E3%81%8B%E3%82%89SSH%E3%81%99%E3%82%8B)

公式ドキュメントであればここが該当する

[**Accessing network applications with WSL** *Learn about the considerations for accessing network applicat* *learn.microsoft.com*](https://learn.microsoft.com/en-us/windows/wsl/networking)

  

## SSH環境を整える

WSL2がセットアップされている前提で、LAN内のクライアントからSSHするための工程は以下

1. WSL2 側 (私は Ubuntu) で SSH プロセスを立てる
2. Windows Defender ファイヤウォールの詳細設定でポートを開ける
3. WSL2 設定の **Mirrored mode networking** を有効にする

  

手順 1, 2 はネット記事が多く存在するため割愛する。「Windows Defender ファイヤウォールの詳細設定でポートを開ける」のポートは、一旦SSHだけで良かったため 22 だけだ。今はLANだけで SSH するため標準ポートのままにしたが、今後外部ネットワークから接続するようになったら変更する必要があろう。

  

手順 3 の「Mirrored mode networking」については日本語の記事が少なく、また他のネット記事では異なる方法をとっている場合が多かったので詳述していく

  

## WSL2 設定の Mirrored mode networking を有効にする

### 設定手順

やることは簡単で、Windows 側ホームディレクトリの WSL2 設定ファイル.wslconfig (e.g. C:\\Users\\yoshi\\.wslconfig) に以下を記載するだけだ。

```cs
[wsl2]
networkingMode=mirrored
```

WSL2を再起動 **wsl --shutdown** すると反映される (厳密には [8秒後ルールが適用](https://learn.microsoft.com/en-us/windows/wsl/wsl-config#the-8-second-rule-for-configuration-changes) されてから？)。

  

### 公式ドキュメント

[**Accessing network applications with WSL** *Learn about the considerations for accessing network applicat* *learn.microsoft.com*](https://learn.microsoft.com/en-us/windows/wsl/networking#mirrored-mode-networking)

ネット記事でよく見かける netsh を使った設定は、公式ドキュメント上部に記載されている「Default networking mode: NAT」の場合になる。これにより LAN (外部ネットワーク) から Windows へのリクエストをWSL2 (内部ネットワーク) へフォワードできる。

```java
netsh interface portproxy add v4tov4 listenport=<yourPortToForward> listenaddress=0.0.0.0 connectport=<yourPortToConnectToInWSL> connectaddress=(wsl hostname -I)
```

今回は昨年リリースされた Mirrored mode networking を利用する。これによって、LAN から WSL に直接接続できるようになる

> \- IPv6 support  
> \- Connect to Windows servers from within Linux using the localhost address 127.0.0.1. IPv6 localhost address::1 is not supported  
> \- Improved networking compatibility for VPNs  
> \- Multicast support  
> \- Connect to WSL directly from your local area network (LAN)

https://learn.microsoft.com/en-us/windows/wsl/networking#mirrored-mode-networking

  

Mirrored mode networking が有効になると、手順 2「Windows Defender ファイヤウォールの詳細設定でポートを開ける」の設定がWSL2にも適用されるようだ。

なお、公式ドキュメント Note に記載されている Hyper-V ファイアウォールの設定が必要な場合もあるかもしれない。レイヤー的に Hyper-V ファイアウォールは Windows Defender ファイアウォールよりも前に適用される。私の場合は設定不要だった。

  

以上の設定により、MacBook からは ssh username@192.168.1.234 (Windows のIPアドレス。IPは固定した) などでSSHできるようになり、また Windows 側からも ssh username@localhost で Ubuntu へ SSH できるようになった。

  

もちろん、MacBook の VSCode で Remote-SSH を使って開発できる。

  

## MacBook 上に Ubuntu ホームディレクトリをマウントする

### セットアップ

```cs
brew install --cask macfuse
brew install gromgit/fuse/sshfs-mac
```

macOS で sshfs を利用するためには macFUSE をインストールする必要がある。

[**Home - macFUSE** *osxfuse.github.io*](https://osxfuse.github.io/)

私は brew で管理することでアップデートも自動化したかったため、上記のコマンド brew install --cask macfuse でインストールした。

macFUSE が必要な経緯などの経緯は以下の記事がまとまっている

[**Macでsshfsをインストール** *Mac で sshfs をインストール* *blog.kmdkuk.com*](https://blog.kmdkuk.com/2021/06/05/mac%E3%81%A7sshfs%E3%82%92%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB.html)

  

### マウントコマンド

```javascript
sshfs ubuntu:/home/yoshikouki/ ~/ubuntu/
```

マウントするコマンド。注意点として、各パスの最後ははスラッシュ / を忘れずに入れる。アンマウント時には sudo を入れてルート権限で実行する

```javascript
sudo umount ~/ubuntu/
```

一つ困った点として、Finder と ls コマンドとで表示名が異なるのは微妙である。Finder の見た目上スクショのように表示されるだけであり、 GitHub Desktop などの利用は問題ない。

![画像](https://assets.st-note.com/img/1711895346285-xFXTvpGzQj.png)

```cs
❯ la ~/ | grep ubuntu
drwxr-x---@   1 yoshikouki  staff    4096 Mar 31 11:13 ubuntu/
```

  

## 今後やること

以下を考えている

- ~~sshfs を使って MacBook 上に Ubuntu ホームディレクトリをマウントする ~/ubuntu~~ 項目を追加
- WSL2 で起動したアプリケーションのポートをファイアウォールに自動適用
- LAN外部 (信頼できるネットワーク) からアクセス可能にする
- 今は Windows のスリープを無効にしているため、スリープしていても外部からスリープ解除できるような何かを考えたい (実現可能性を探る)

  

  

MacBookからWSL2のUbuntuを使って開発する環境を作る｜yoshikouki