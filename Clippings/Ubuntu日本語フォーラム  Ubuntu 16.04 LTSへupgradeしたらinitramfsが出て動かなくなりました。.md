---
title: "Ubuntu日本語フォーラム / Ubuntu 16.04 LTSへupgradeしたらinitramfsが出て動かなくなりました。"
source: "https://forums.ubuntulinux.jp/viewtopic.php?id=18660"
author:
published:
created: 2025-09-26
description:
tags:
  - "clippings"
---
ページ: **1**

- [インデックス](https://forums.ubuntulinux.jp/index.php)
- » [初心者サポート](https://forums.ubuntulinux.jp/viewforum.php?id=15)
- » Ubuntu 16.04 LTSへupgradeしたらinitramfsが出て動かなくなりました。

## #1

**[Higuchi](https://forums.ubuntulinux.jp/profile.php?id=593062)**

**メンバ**

登録日: 2014-11-08

### Ubuntu 16.04 LTSへupgradeしたらinitramfsが出て動かなくなりました。

樋口と申します。Ubuntu初心者です。今まで14.04LTSを使用していました。今日(2016-08-17)Ubuntu16.04にupgradeしたところ下記コメントが出て全く動かなくなってしまいました。  
対処法をご存じの方がいらっしゃいましたらご指導のほどよろしくお願いいたします。  
  
ex/dev/sdai contains a file systems with errors, check forced.  
Inodes that were part of a corrupted orphan linked list found.  
  
/dev/sdai:UNEXPECTED INCONSISTENCY; RUN fsck MANUALLY.  
(i.e., without -a or -p options)  
fsck exited with status code 4  
The root filesystem on /dev/sdai requires a manual fsck.  
  
BusyBox v1.22.1 (Ubuntu 1:1.22.0-15ubuntu1) built-in shell (ash)  
Enter 'help' for a list of built-in commands.  
  
(initramfs)\_  
  
ちなみに使用しているPCは、  
(1)CPU: Intel Core i5-4590  
(2)メモリ:32 GiB  
(3)ディスク: SSD 500GB  
(3)OS: Ubuntu 64bit  
です。

オフライン

## #2

**[yaneno-suzume](https://forums.ubuntulinux.jp/profile.php?id=263409)**

**メンバ**

From: 名古屋

登録日: 2013-06-30

### Re: Ubuntu 16.04 LTSへupgradeしたらinitramfsが出て動かなくなりました。

…私も詳しくないのですが、他にフォローがないようですので…  
  
ファイルシステムのエラーということで、  
強制的にプログラムを止めたり、  
強制的に電源を切ったりした時に出るようなエラーです。  
  
(initramfs)\_  
  
のプロンプトに対して、  
エラーメッセージにあるように  
  
fsck /dev/sdai  
  
を入力して、Enterを押すと、fsckコマンドの実行が始まり、  
  
\> group descriptor checksums are invalid. Fix<y>?  
\> Pass 1:...  
...  
\> Pass 5:...  
\> Block bitmap differences:...  
\> Fix<y>?  
\> Free blocks count wrong for group #1...  
\> Fix<y>?  
  
というようなメッセージが出て、Fix<y>? のところで  
毎回止まりますので、全部 y を選んで Fix(=修正) します。  
fsck はファイルシステムのチェックをするコマンドです。  
  
ひととおり終わりましたら、確認のため、もう一度  
  
fsck /dev/sdai  
  
を実行し、  
  
\> ubu: clean, 302818/6176768 files. 3913618/24696832 blocks  
  
というようなメッセージが出れば修正できていますので  
  
exit  
  
を入力して、Enterを押すと、  
initramfs (=初期RAMディスクファイルシステム)モードから出て  
自動で再起動したか、忘れましたが、しなかったら  
手動で再起動してください。  
  
私の場合は何度も fsck するハメになったのですが、  
最終的にはリカバリモードで fsck してから正常に復帰しました。  
  
リカバリモードは、起動時の GRUB の選択メニューから  
\[Ubuntu Advanced option\]、  
\[4.x.x.xx(linuxの最新バージョン) リカバリモード\]  
のように選ぶと起動できると思います。  
詳細はどなたかお願いします(私は疲れました)。  
  
※ fsck -y /dev/sdai のように入力すると、  
　毎回 y (またはEnter) を押さずとも、  
　全部 y で答えたことになります。  
  
＃ sdai は sda1 のことでしょうか？？  
  
繰り返しエラーが出る場合は、ハードディスクの損傷かもしれませんね。

オフライン

## #3

**[Higuchi](https://forums.ubuntulinux.jp/profile.php?id=593062)**

**メンバ**

登録日: 2014-11-08

### Re: Ubuntu 16.04 LTSへupgradeしたらinitramfsが出て動かなくなりました。

yamane-suzume様  
  
樋口です。山根様ご指示の通りに実行したら上手く行きました。本当にありがとうございます。  
大切なファイルをバックアップしていなかったので、かなり慌てましたが、本当に助かりました。  
今後ともご指導のほどよろしくお願いいたします。  
  
以下は経緯です。  
  
(initramfs)\_fsck /dev/sda1  
  
Enter  
  
fsckコマンドが始まる。→全部 y と入力する。  
(数えきれないくらいたくさん出てきました。)  
  
最終的に、  
/dev/sda1: \*\*\*\*\*FILE SYSTEM WAS MODIFIED\*\*\*\*\*  
/dev/sda1: 1051101/30007296 files (0.4% non-contiguous), 59220422/120019712 blocks  
  
確認のためもう一度  
fsck /dev/sda1  
を実行したところ、  
  
fsck from util-linux 2.27.1  
e2fsck 1.42.13 (17-May-2015)  
  
/dev/sda1: clean, 1051101/30007296 files, 59220422/120019712 blocks  
  
が出てきたので、  
exit  
したところ、正常なGUI画面が出てきました。  
  
【備考】  
sdaiはsda1の間違いでした。  
  
以上

オフライン

ページ: **1**

- [インデックス](https://forums.ubuntulinux.jp/index.php)
- » [初心者サポート](https://forums.ubuntulinux.jp/viewforum.php?id=15)
- » Ubuntu 16.04 LTSへupgradeしたらinitramfsが出て動かなくなりました。