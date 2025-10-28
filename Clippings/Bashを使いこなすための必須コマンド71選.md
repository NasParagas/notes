---
title: "Bashを使いこなすための必須コマンド71選"
source: "https://qiita.com/kjm_nuco/items/6a4faf4d026d130e7db1"
author:
  - "[[kjm_nuco]]"
published: 2023-06-12
created: 2025-09-14
description: "はじめに mkdir, ls, cp, touch, mv, rm以上のコマンドにあんまり触れていない。。。という方、もっとコマンドを使いこなしたい！という方。。。必見。 基本的だが全部網羅するには大変な基本コマンド及びショートカットを溢れんばかり71選お届けします。たく..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiitaにログインして、便利な機能を使ってみませんか？

あなたにマッチした記事をお届けします

便利な情報をあとから読み返せます

[ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fkjm_nuco%2Fitems%2F6a4faf4d026d130e7db1&realm=qiita) [新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fkjm_nuco%2Fitems%2F6a4faf4d026d130e7db1&realm=qiita)

この記事は最終更新日から1年以上が経過しています。

## はじめに

`mkdir`, `ls`, `cp`, `touch`, `mv`, `rm` 以上のコマンドにあんまり触れていない。。。という方、もっとコマンドを使いこなしたい！という方。。。必見。  
基本的だが全部網羅するには大変な基本コマンド及びショートカットを溢れんばかり71選お届けします。たくさんありますので、表題に何のコマンドか書いてあるのをご活用ください。  
一部、macユーザー向けとなっていることをご了承ください。

弊社Nucoでは、他にも様々なお役立ち記事を公開しています。よかったら、 [Organization](https://qiita.com/organizations/nuco-inc/profile) のページも覗いてみてください。  
また、Nucoでは一緒に働く仲間も募集しています！興味をお持ちいただける方は、 [こちら](https://www.recruit.nuco.co.jp/?qiita_item_id=6a4faf4d026d130e7db1) まで。

## Ctrl を使ったショートカット

最初に、 `Ctrl` を使ったショートカットを紹介します。

### Ctrl-A　行頭移動

[![スクリーンショット 2023-05-17 15.11.21.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3004778/3e597bb1-9eb5-e7d9-3e7d-22f4ce336526.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3004778%2F3e597bb1-9eb5-e7d9-3e7d-22f4ce336526.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a1e6060446a7c6791681097ba1a83167)  
↓  
[![スクリーンショット 2023-05-17 15.37.11.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3004778/5eb08b46-2c7b-ce2f-65f3-ec45930be08f.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3004778%2F5eb08b46-2c7b-ce2f-65f3-ec45930be08f.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=ebb6f0b66ae0ae7c63633e3871627b66)

### Ctrl-E　行末移動

[![スクリーンショット 2023-05-17 15.11.21.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3004778/3e597bb1-9eb5-e7d9-3e7d-22f4ce336526.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3004778%2F3e597bb1-9eb5-e7d9-3e7d-22f4ce336526.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a1e6060446a7c6791681097ba1a83167)  
↓  
[![スクリーンショット 2023-05-17 15.37.41.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3004778/2c405c02-1299-eb13-7121-560b82623b47.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3004778%2F2c405c02-1299-eb13-7121-560b82623b47.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=26372544efd243a19afecac6a772b381)

### Ctrl-W　直前一単語を削除

  
↓  
[![スクリーンショット 2023-05-17 16.04.34.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3004778/beb6e403-ec39-fe6b-c816-45ed4a77fd82.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3004778%2Fbeb6e403-ec39-fe6b-c816-45ed4a77fd82.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=4419f9b38bafc5c836cd046665fb7533)

### Ctrl-U　カーソルの左から行頭まで削除

[![スクリーンショット 2023-05-17 15.11.21.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3004778/3e597bb1-9eb5-e7d9-3e7d-22f4ce336526.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3004778%2F3e597bb1-9eb5-e7d9-3e7d-22f4ce336526.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a1e6060446a7c6791681097ba1a83167)  
↓  
[![スクリーンショット 2023-05-17 15.33.54.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3004778/b67260a1-6f06-60ec-f9c5-fcb5b40e1eeb.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3004778%2Fb67260a1-6f06-60ec-f9c5-fcb5b40e1eeb.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=b0a12bfb8baef0f9842212de25d868f3)

### Ctrl-K　カーソル位置から末尾までの行を削除

[![スクリーンショット 2023-05-17 15.11.21.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3004778/3e597bb1-9eb5-e7d9-3e7d-22f4ce336526.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3004778%2F3e597bb1-9eb5-e7d9-3e7d-22f4ce336526.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a1e6060446a7c6791681097ba1a83167)  
↓  
[![スクリーンショット 2023-05-17 15.42.14.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3004778/eca6b4e7-18e4-62c6-b17d-b662a26765aa.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3004778%2Feca6b4e7-18e4-62c6-b17d-b662a26765aa.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=bfd9e031942777e345166640c6f166e1)

### Ctrl-Y　直前に削除した中身をペースト

`Ctrl-K` や `Ctrl-U` で削除した中身をペーストします。クリップボードとは別扱いになります。

例) `Ctrl-K` と `Ctrl-Y` を使用  
[![スクリーンショット 2023-05-17 15.11.21.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3004778/3e597bb1-9eb5-e7d9-3e7d-22f4ce336526.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3004778%2F3e597bb1-9eb5-e7d9-3e7d-22f4ce336526.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a1e6060446a7c6791681097ba1a83167)  
↓ `Ctrl-K`  
[![スクリーンショット 2023-05-17 15.42.14.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3004778/eca6b4e7-18e4-62c6-b17d-b662a26765aa.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3004778%2Feca6b4e7-18e4-62c6-b17d-b662a26765aa.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=bfd9e031942777e345166640c6f166e1)  
↓改行  
[![スクリーンショット 2023-05-17 15.44.24.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3004778/d9cf6762-0872-c9ad-923c-b238557cc323.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3004778%2Fd9cf6762-0872-c9ad-923c-b238557cc323.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=94c023b4c1e1af2e99c0c4a981aa9601)  
↓ `Ctrl-Y`  
[![スクリーンショット 2023-05-17 15.42.31.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3004778/1b4281c8-896c-7965-4770-6ee02ec83cd0.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3004778%2F1b4281c8-896c-7965-4770-6ee02ec83cd0.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=01a483838af108433f8aca01b6402215)

### Ctrl-Q　出力一時停止を解除

出力一時停止（ `Ctrl-s` ）を解除する。  
うっかり `Ctrl-s` キーを押してしまい、コンソールやターミナルがキー入力を受け付けなくなったときは、 `Ctrl-Q` で元の状態に戻る。

## TABで補完

Tabキーでファイル・フォルダのパスを補完できる他、予測されるコマンドも補完することができます。予測候補が複数ある場合は、下記のように表示されます。

```bash
$ d
dappprof                       dirname
dapptrace                      dirs
dash                           disklabel
date                           diskutil
db_archive                     disown
db_checkpoint                  display
db_codegen                     dispqlen.d
db_deadlock                    distnoted
db_dump                        ditto
db_hotbackup                   djpeg
db_load                        djxl
db_printlog                    dmc
db_recover                     dmesg
db_stat                        dnctl
db_upgrade                     dns-sd
db_verify                      do
--More--
```

ちなみに `zsh` では、tabキーを2回押すと候補がアルファベット順にコロコロ変わる仕様があります。

## & / && /;　複数コマンドを続けて実行

### $ command1 & command2

この書き方で `command1` が終わるのを待たずに `command2` を実行することができます。

例:

```bash
$ python3 foo/hoge.py & python3 foo/hogehoge.py
[1] 55312
hogehoge.py is executed
hoge.py is executed
[1]  + done       python3 foo/hoge.py
```

[![スクリーンショット 2023-04-25 14.27.44.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3004778/7cbaa537-da84-606b-8b71-543084b49024.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3004778%2F7cbaa537-da84-606b-8b71-543084b49024.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=bffd4989ddc4d6e54f20a6210e237120)

例では、 `command1` にあたる `python3 foo/hoge.py` より先に、 `command2` にあたる `python3 foo/hogehoge.py` が完了しています。 `&` を使用すると複数のコマンドを並列に処理できます。最後に書いたコマンドのみフォアグラウンドで実行し、残り（今回の例だと `command1` ）はバックグラウンドで実行されます。 `command1` と `command2` の実行が完了する順序は保証されません。  
また、\[1\] 55312 は、バックグラウンドで実行されたコマンドのジョブ番号とプロセスIDと呼ばれるものです。ジョブ番号が1で、プロセスIDが55312でした。 `+ done` は、ジョブが完了したことを表します。  
ちなみに、

```bash
$ command1 & command2 & command3
```

のように3個以上並べて書くこともできます。

さらに、

```bash
python3 bar.py &
```

のように書くと、処理をバックグラウンドで実行させ、実行している間（完了していなくても）、プロンプトは自由に使えます。

### $ command1 && command2

```bash
$ ls foooo && python3 bar.py
ls: foooo: No such file or directory
```

`&&` でも複数コマンドを同時実行できますが、これは `&` とは異なり順次実行です。つまり、フォアとかバックとかないやり方で、 `command1` の処理が終わらないと、 `command2` が実行されません。  
また、例を見るとわかるように `command1` が失敗すると `command2` は実行されません。例では、 `foooo` というディレクトリがないのでエラーとなり、続く `python3 bar.py` は実行されませんでした。

### $ command1; command2

`;`は `&&` とほぼ同じ使い方ができますが、`;`は前のコマンドの成功・失敗に関係なく続くコマンドを実行できます。

## Ctrl + z, fg　コマンドの一時停止と再開

```bash
$ python3 bar.py
^Z
[1]+  Stopped                 python3 bar.py
$ fg
python3 bar.py
bar.py is executed
```

`Ctrl + C` でコマンドを強制停止でき、 `Ctrl + D` で、特別な状態が抜け出せることを知っている方は多いでしょう。  
`Ctrl + Z` は実行中のコマンドを一時停止することができるショートカットです。  
一時停止したコマンドは `fg` コマンドで再開できます。  
また、 `jobs` コマンドでバックグラウンドの様子を確認することができます。

```bash
$ jobs
[1]-  Running                 python3 bar.py &
[2]+  Stopped                 python3 foo/hoge.py
```

## {}　複数ディレクトリ、ファイルを一度に作成

### $ touch {}

`{}` を使うと似た名前のファイル・ディレクトリを同時に作るのに便利です。

```bash
touch sample{1,2,3}.txt
```

[![スクリーンショット 2023-04-25 16.01.42.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3004778/519f2152-8e20-40b1-e7d7-8f44eca228c9.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3004778%2F519f2152-8e20-40b1-e7d7-8f44eca228c9.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=6c1b5a5897839024d331b4f030e82906)  
{}の中で、変えたい文字の後に`,`を打ちます。`,`の後にスペースが開かないように注意してください。  
このコマンドはこのように書くこともできます。

```text
touch sample1.txt sample2.txt sample3.txt
```

任意の名前ファイルを一気に作ることができますね。

### $ mkdir {}

```bash
$ mkdir -p fruits/{apple,orange,banana}_imgs
```

[![スクリーンショット 2023-04-25 15.54.48.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3004778/4234980c-747a-09ad-61aa-c94e54af2fd0.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3004778%2F4234980c-747a-09ad-61aa-c94e54af2fd0.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=369996d75d058bc1209310d5c339af37)

ディレクトリの場合も同様です。例では、 `fruits` という元々存在しないディレクトリを `-p` オプションで一気に作っています。 `-p` オプションは複数階層のディレクトリを作成する際に便利なオプションです。

### cp foo{,\_copy}.py

`cp foo{,_copy}.py`  
は、  
`cp foo.py foo_copy.py` と同じです。

`cp foo{.py,.bak}`  
は、  
`cp foo.py foo.bak` と同じです。  
バックアップを取りたい時に使えますね。

### echo {1..10}.txt　数字の範囲を展開

```bash
$ echo {1..10..2}.txt
1.txt 3.txt 5.txt 7.txt 9.txt
```

この `{1..10..2}` は1から10までの数字を2ずつ増やしながら展開するという意味です。 `{1..10}.txt` なら1から10まで順番に展開されます。  
一部の `bash` ではサポートされていない場合もあります。

## echo　文字列を表示する

文字列を表示するだけのシンプルなコマンドです。

```bash
$ echo Hello!
Hello!
```

`$` を使うと環境変数を指定できます。

```bash
$ a=everyone
$ echo Hello, $a.
Hello, everyone.
```

### $ echo 文字列 > ファイル名　文字列をファイルに書き込み

`>` と併用することで、ファイルに書き込みできます。ちなみにこの書き方は新規ファイルの作成も同時にやってくれます。

```bash
$ echo apple > sample1.txt
$ cat sample1.txt
apple
```

## ls -l　ファイルの属性の一覧

ファイルとファイルの属性の一覧を取得します。  
左から順に、

- パーミッション
- ハードリンク数
- 所有者
- グループ
- ファイルサイズ
- 最終更新日時
- ファイル（ディレクトリ）名

を表示します。  
パーミッションについて詳しく知りたい方は以下をご参考ください。

```bash
$ ls -l 
total 16
-rw-r--r--  1 ishiishuto  staff   80  4 25 15:32 20seconds.py
-rw-r--r--  1 ishiishuto  staff   69  5 17 15:56 bar.py
drwxr-xr-x  4 ishiishuto  staff  128  4 22 15:31 foo
drwxr-xr-x  5 ishiishuto  staff  160  4 25 15:54 fruits
drwxr-xr-x  5 ishiishuto  staff  160  5  5 16:58 sample
drwxr-xr-x  6 ishiishuto  staff  192  4 25 16:18 venv
```

## pwd　カレント・ディレクトリを取得

「print working directory」の略です。現在の自分の位置（カレント・ディレクトリ）を表示します。

```bash
$ pwd
/Users/ishiishuto/work/sample
```

## top　実行中のプロセスをリアルタイム表示

実行中のプロセスをリアルタイムで、CPU利用率が高い順に表示します。

```bash
Processes: 623 total, 2 running, 621 sleeping, 3793 threads                                                                    16:32:08
Load Avg: 1.85, 1.65, 1.60  CPU usage: 6.71% user, 6.13% sys, 87.15% idle  SharedLibs: 483M resident, 86M data, 134M linkedit.
MemRegions: 910308 total, 5452M resident, 234M private, 1782M shared. PhysMem: 15G used (1660M wired, 4480M compressor), 240M unused.
VM: 323T vsize, 4142M framework vsize, 0(0) swapins, 0(0) swapouts. Networks: packets: 2823931/3335M in, 933755/226M out.
Disks: 4904757/66G read, 7729950/66G written.

PID    COMMAND      %CPU  TIME     #TH    #WQ  #PORT MEM    PURG   CMPRS  PGRP  PPID  STATE    BOOSTS            %CPU_ME %CPU_OTHRS UID
159    WindowServer 29.7  33:46.86 21     6    2153  500M   14M    114M-  159   1     sleeping *0[1]             2.44052 1.14758    88
2286   Code Helper  14.2  02:15.11 24     1    239+  191M+  0B     71M-   2278  2278  sleeping *0[1]             0.00000 0.00000    501
462    Google Chrom 13.3  23:01.08 48     2    1624  715M   0B     193M-  462   1     sleeping *2307[212]        0.83111 1.80302    501
0      kernel_task  13.3  37:52.94 477/8  0    0     2448K+ 0B     0B     0     0     running   0[0]             0.00000 0.00000    0
19880  top          7.1   00:03.44 1/1    0    38    6481K  0B     0B     19880 2370  running  *0[1]             0.00000 0.00000    0
2281   Code Helper  5.1   00:56.61 12     3    229+  331M+  768K   48M-   2278  2278  sleeping *1[11]            0.08256 0.00000    501
2278   Electron     3.9   01:23.95 31     2    516   106M   16K    49M    2278  1     sleeping *0[5887]          0.25412 0.56849    501
488    Simulator    1.1   01:10.24 4      2    273   43M    0B     32M    488   1     sleeping *0[11548]         0.00000 0.00000    501
259    TouchBarServ 0.9   02:33.08 4      2    284   19M    2880K  8528K  259   1     sleeping *0[1]             0.00000 0.14946    0
530    Google Chrom 0.9   08:49.14 25     5    948   565M   62M    95M    462   462   sleeping *1[5]             0.00000 0.00000    501
```

## cat　ファイルの中身を確認

`cat` はファイルの中身を確認するコマンドです。

```bash
$ cat sample2.txt
APPLE
BANANA
$ cat sample3.txt 
orange
```

### ファイルの結合

複数のファイルを結合して1つのファイルにすることもできます。

```bash
$ cat sample1.txt sample3.txt > marged.txt
$ cat marged.txt                          
apple
orange
```

### ファイル内容の追記

```bash
cat new_content.txt >> existing_file.txt
```

catコマンドには `>>` 演算子を使用して、既存のファイルに新しい内容を追記することもできます。上の例では、 `new_content.txt` の内容が `existing_file.txt` に追加されます。

## head　先頭のn行を抜き出す

```bash
$ cat sample1.txt 
Apple
Banana
Orange
Strawberry
Grape
Pineapple
Watermelon
Mango
Lemon
Peach
Pear
Cherry
Kiwi
Blueberry
Raspberry
Avocado
Papaya
Plum
Fig
Coconut
```

に対して、デフォルトは先頭10行を抜き出します。

```bash
$ head sample1.txt 
Apple
Banana
Orange
Strawberry
Grape
Pineapple
Watermelon
Mango
Lemon
Peach
```

`$ head -15 sample1.txt` とすれば、15行抜き出せます。  
パイプ `|` を使って、 `ls . -l | head -20` などとすれば、カレントディレクトリ下の20個のファイルの情報が得られます。

## tail　末尾のn行を抜き出す

`head` に対して、 `tail` コマンドはファイルの末尾から指定された行数の内容を表示します。  
`head` と同じようにパイプ `|` と併用して使うことができます。

```bash
$ tail sample1.txt 
Pear
Cherry
Kiwi
Blueberry
Raspberry
Avocado
Papaya
Plum
Fig
Coconut
```

## kill　プロセスIDを指定し強制終了

```bash
$ kill <PID>
```

`<PID>` にはプロセスIDを指定します。プロセスIDは `ps` コマンドで確認できます。アプリケーションが応答しなくなったときなどに使用します。

## killall　プロセスの名前を指定し強制終了

指定されたプロセス名に一致するすべてのプロセスを終了させます。 `killall` コマンドは `kill` と違い、プロセス名を指定するだけで、プロセスID（PID）を調べる必要がありません。

```bash
$ killall <process_name>
```

`<process_name>` には終了させたいプロセスの名前を指定します。

## w　ユーザーのログイン情報

システムに現在ログインしているユーザーのリストや、各ユーザーの活動情報を表示するために使用されます。

- `USER`: ユーザー名
- `TTY`: ログインに使用されている端末名
- `FROM`: ログイン元のホスト名またはIPアドレス
- `LOGIN@`: ログイン日時
- `IDLE`: 最後の操作からの経過時間
- `JCPU`: ユーザーが実行中のジョブのCPU時間
- `PCPU`: ユーザーが実行中のプロセスのCPU時間
- `WHAT`: ユーザーの現在のコマンドまたはプログラム

## cd $\_, cd -

ディレクトリを切り替えるコマンド `cd` の便利な使い方を紹介します。

### cd $\_

`mkdir` したコマンドに即座に移動できます。

```bash
sample user_name $ mkdir new_dir
sample user_name $ cd $_
new_dir user_name $
```

### cd -

移動する前のディレクトリに移動します。

```bash
sample $ mkdir -p new_dir/bar/foo
sample $ cd $_
foo $ cd -
sample $
```

## mv　ファイル移動とリネーム

`mv` はファイルの移動に使うコマンドです。

```bash
$ mv ファイル名 目的のディレクトリ
```

### ファイルをリネームする

`mv` はファイルの名前を変更するのにも使えます。 `mv 古いファイル名 新しいファイル名` で使います。

```bash
$ ls
sample1.txt
$ mv sample1.txt sample2.txt
$ ls
sample2.txt
```

### ワイルドカードを利用して一気に移動

```bash
$ ls
sample1.txt     sample2.txt     sample3.txt
$ mv sample?.txt ../bar
$ ls bar
$ ls ../bar
sample1.txt     sample2.txt     sample3.txt
```

`?`は任意の一文字とマッチ、 `*` は0個以上の任意の文字列にマッチします。以上の操作は、

```bash
$ mv sample[123].txt ../bar
```

とかでもできます。 `[]` は指定した文字のいずれか１文字にマッチします。

## cal　カレンダーの表示

カレンダー表示のコマンドです。

```bash
$ cal
      5月 2023         
日 月 火 水 木 金 土  
    1  2  3  4  5  6  
 7  8  9 10 11 12 13  
14 15 16 17 18 19 20  
21 22 23 24 25 26 27  
28 29 30 31
```

### cal -y　年間のカレンダー

`-y` オプションで年間のカレンダーを出せます。 `-y 2000` とすれば、2000年のカレンダーが得られます。

```bash
$ cal -y
                            2023
         1月                    2月                    3月           
日 月 火 水 木 金 土  日 月 火 水 木 金 土  日 月 火 水 木 金 土  
 1  2  3  4  5  6  7            1  2  3  4            1  2  3  4  
 8  9 10 11 12 13 14   5  6  7  8  9 10 11   5  6  7  8  9 10 11  
15 16 17 18 19 20 21  12 13 14 15 16 17 18  12 13 14 15 16 17 18  
22 23 24 25 26 27 28  19 20 21 22 23 24 25  19 20 21 22 23 24 25  
29 30 31              26 27 28              26 27 28 29 30 31     
                                                                  

         4月                    5月                    6月           
日 月 火 水 木 金 土  日 月 火 水 木 金 土  日 月 火 水 木 金 土  
                   1      1  2  3  4  5  6               1  2  3  
 2  3  4  5  6  7  8   7  8  9 10 11 12 13   4  5  6  7  8  9 10  
 9 10 11 12 13 14 15  14 15 16 17 18 19 20  11 12 13 14 15 16 17  
16 17 18 19 20 21 22  21 22 23 24 25 26 27  18 19 20 21 22 23 24  
23 24 25 26 27 28 29  28 29 30 31           25 26 27 28 29 30     
30                                                                

         7月                    8月                    9月           
日 月 火 水 木 金 土  日 月 火 水 木 金 土  日 月 火 水 木 金 土  
                   1         1  2  3  4  5                  1  2  
 2  3  4  5  6  7  8   6  7  8  9 10 11 12   3  4  5  6  7  8  9  
 9 10 11 12 13 14 15  13 14 15 16 17 18 19  10 11 12 13 14 15 16  
16 17 18 19 20 21 22  20 21 22 23 24 25 26  17 18 19 20 21 22 23  
23 24 25 26 27 28 29  27 28 29 30 31        24 25 26 27 28 29 30  
30 31                                                             

        10月                   11月                   12月           
日 月 火 水 木 金 土  日 月 火 水 木 金 土  日 月 火 水 木 金 土  
 1  2  3  4  5  6  7            1  2  3  4                  1  2  
 8  9 10 11 12 13 14   5  6  7  8  9 10 11   3  4  5  6  7  8  9  
15 16 17 18 19 20 21  12 13 14 15 16 17 18  10 11 12 13 14 15 16  
22 23 24 25 26 27 28  19 20 21 22 23 24 25  17 18 19 20 21 22 23  
29 30 31              26 27 28 29 30        24 25 26 27 28 29 30  
                                            31
```

### cal -m　指定した月のカレンダー

```bash
$ cal -m 3
      3月 2023         
日 月 火 水 木 金 土  
          1  2  3  4  
 5  6  7  8  9 10 11  
12 13 14 15 16 17 18  
19 20 21 22 23 24 25  
26 27 28 29 30 31
```

## chmod　アクセス権限（パーミッション）の変更

ファイルやフォルダのアクセス権限（パーミッション）を変更します。 `change mode` の略称です。パーミッションは `ls -l` コマンドで確認できます。

```bash
$ ls -l
total 8
drwxr-xr-x  2 user  group  4096 Jan 12 10:32 directory1
-rw-r--r--  1 user  group    56 Jan 12 10:32 file1.txt
-rw-r--r--  1 user  group   128 Jan 12 10:32 file2.txt
```

最初の10文字の `drwxr-xr-x` などが、パーミッションです。  
例えば、drwxr-xr-xという文字列なら、

- 1文字目: ファイルタイプ
	`\-`: 通常のファイルを表します。  
	`d`: ディレクトリを表します。  
	`l`: シンボリックリンクを表します。
- 2-4文字目: 所有者のパーミッション
	`r`: 読み取り権限を表します。  
	`w`: 書き込み権限を表します。  
	`x`: 実行権限を表します。  
	`\-`: 対応する権限がないことを表します。
- 5-7文字目: 所有者のグループのパーミッション  
	同様にr、w、x、-のいずれかを持つ場合があります。
- 8-10文字目: 他のユーザーのパーミッション  
	同様に `r` 、 `w` 、 `x` 、 `-` のいずれかを持つ場合があります。

`chmod` コマンドは `chmod mode file` のように使います。 `mode` には変更したいパーミッションを表す数値または記号を指定します。

```bash
chmod 755 file.txt
```

パーミッションの情報は、

- 読み取り権限 `r`: 4
- 書き込み権限 `w`: 2
- 実行権限 `x`: 1  
	として、足し算し、 `mode` を決めます。この場合は `mode` は、所有者（user）が、 `1 + 2 + 4 = 7` で、所有者のグループ（group）は、 `1 + 4 = 5` 、他のユーザー（others）は `1 + 4 = 5` という具合で、 `755` です。

## chown　所有者や所有者のグループを変更

`chown` は「change owner」の略称です。ファイルやフォルダのオーナーやグループを変更します。

```bash
$ chown [options] owner[:group] file
```

例えば、

```bash
$ chown user file.txt
```

で、 `file.txt` のオーナーを `user` に変えることができます。

### chown user:group　所有者と所有者のグループを同時

```bash
$ chown user:group file.txt
```

上記の例では、 `file.txt` の所有者を `user` に、所有者のグループを `group` に変更します。

### chown -R　再帰的に所有者を変更する

```bash
$ chown -R user directory
```

`directory` 以下のすべてのファイルとディレクトリの所有者を `user` に変更します。 `-R` オプションを使用することで、ディレクトリ内のすべてのファイルやサブディレクトリも再帰的に所有者が変更します。

## ln　ハードリンクを作る

シンボリックリンクやハードリンクを作成できます。  
シンボリックリンクとハードリンクについては解説記事は以下などを参照してください！

### ハードリンクの作成

```bash
$ ln /path/to/target link_name
```

でハードリンクを作成できます。この場合、 `/path/to/target` へのハードリンクを `link_name` という名前で作成します。

### ln -s　シンボリックリンクの作成

```bash
$ ln -s /path/to/target /path/to/directory/link_name
```

`-s` オプションでハードリンクを作成できます。この場合、 `/path/to/target` へのシンボリックリンクを、 `/path/to/directory` ディレクトリ内に `link_name` という名前で作成します。

## man　マニュアル表示

コマンドのマニュアルページを表示させるのが `man` コマンドです。

```bash
$ man [section] keyword
```

のように使います。

### コマンドのマニュアルページを表示する

```bash
$ man ls

LS(1)                       General Commands Manual                      LS(1)

NAME
     ls – list directory contents

SYNOPSIS
     ls [-@ABCFGHILOPRSTUWabcdefghiklmnopqrstuvwxy1%,] [--color=when]
        [-D format] [file ...]

DESCRIPTION
     For each operand that names a file of a type other than directory, ls
     displays its name as well as any requested, associated information.  For
     each operand that names a file of type directory, ls displays the names
     of files contained within that directory, as well as any requested,
```

この例では `ls` コマンドのマニュアルが `vim` 表示されます。  
マニュアルページを閲覧した後は `q` キーを押すことで終了できます。

### キーワードを検索してマニュアルページを表示する：

```bash
$ man -k network
```

`-k` オプションを使用することで、キーワード「network」に関連するマニュアルページの概要を表示します。

## less more vi　テキストファイル表示

表示だけなら `less` を、編集したい場合は `vi` コマンドを使います。マウスやトラックパッドに触れなくて良い点で、エディタで編集するよりこれらのコマンドが好む方が一定数います。

### less　テキストファイルの表示

lessコマンドは、テキストファイルの内容をページ単位で表示します。  
スクロールや検索ができます。  
ファイルの先頭から表示し、ページ単位で下にスクロールします。  
`f` で1ページ下に、 `b` で1ページ上に送ります。  
動画のように複数ファイル表示する場合は`:n` で次のファイルを表示できます。  
終了したい時は`:q` と入力します。

エディタを使うから `less` コマンドなんていらないでしょ？と思ったあなた。パイプ `|` と併用すれば、出力を快適に読むことができます。

```bash
file imgs/* | less
```

```bash
imgs/1.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, progressive, precision 8, 
1080x1350, components 3
imgs/2.jpg:  JPEG image data, JFIF standard 1.01, resolution (DPI), density 300x300, segment length 16, progressive, preci
sion 8, 735x1102, components 3
imgs/3.jpg: JPEG image data, JFIF standard 1.01, resolution (DPI), density 72x72, segment length 16, progressive, precisi
on 8, 736x919, components 3
```

### more　テキストファイルの表示

`less` は `more` に比べ多機能を備えており、 `more` の完全上位互換ですので、古い `more` コマンドは紹介するに留めます。  
`less` が`:q` を入力しないと終了しないのに対し、 `more` はファイルの終端に到達すると、終了します。

### vi　テキストファイルの閲覧と編集

`vi` コマンドは、ファイルの表示と編集ができます。  
テキストファイルを行単位で表示します。  
カーソル移動で閲覧の「NORMAL」モードと、編集の「INSERT」モード、`:`で、コマンドを入力する「COMMAND」モードがあります。「COMMAND」モードでは、「終了」の`:q` や「INSERT」モードに切り替える`:i` 、保存する`:w` などがあります。  
`vim` の解説は語ると長いので、こちらの記事をご参考ください。

「 **Vimmer** 」  
`Vim` を使いこなす人は「Vimmer」と呼ばれます。 `Vim` を愛用し、その独特の操作スタイルやカスタマイズ能力に熱中する人々を指します。 `Vim` の操作方法は初学者にはやや学習コストがかかりますが、熟練すると、IDEや特別なエディタなしに、より高速かつ効率的なテキスト編集ができるようになります。

## make よく使う複数のコマンドを1行で実行

よく使う複数のコマンドをまとめておいて、１行で実行するのに便利なコマンドです。  
主にソースファイルのコンパイルで用いられますが、それ以外の用途でももちろん使えます。  
実行するには `makefile` という名前のファイルが必要で、言語はシェルスクリプトで記述します。  
`make` とか `make ターゲット名` のような形で実行します。  
ターゲットとは下の例で言うところの、 `install` と `run` と `start` のことです。  
`make run` なら `run` に書いたコマンドが、 `make` のようにターゲットを省略すると一番上の `install` が実行されます。  
`.ONESHELL` は、 `makefile` 内のすべてのコマンドが単一のシェルスクリプトで実行されるようになるおまじないです。

makefile

```makefile
.ONESHELL:

# Command to install packages using pip
INSTALL_PACKAGES = pip install -r requirements.txt

install: 
    $(INSTALL_PACKAGES)
main: 
    python main.py
start: install main
```

c言語のプログラムのビルドに `make` を使う方法は、以下などを参照してください。

## wc　行数、単語数、バイト数、（文字数）を数える

ファイルの行数、単語数、バイト数を数えるコマンドです。

```text
$ wc sample?.txt
      20      20     145 sample1.txt
       2       2      13 sample2.txt
       1       1       7 sample3.txt
      23      23     165 total
```

`sample1.txt` に注目すると、 `20`,`20`,`145` がそれぞれ、行数、単語数、バイト数です。  
オプションを用いると希望する情報だけ表示できます。

| オプション | 説明 |
| --- | --- |
| `-l` | ファイルの行数を表示 |
| `-w` | ファイルの単語数を表示 |
| `-c` | ファイルのバイト数を表示 |
| `-m` | ファイルの文字数を表示 |
| `-L` | ファイル内の最長行の長さを表示 |

```bash
$ wc -m sample1.txt 
     151 sample1.txt
```

パイプ `|` と併用して使われることが多いです。

```bash
$ ls -l | wc -l
    3
```

## pbcopy clip　クリップボードにコピー

```bash
pbcopy < sample1.txt
```

で、クリップボードにコピーできます。  
macは `pbcopy` 、Windowsでは `clip` というコマンドが用意されています。  
パイプ `|` との相性がいいです。

```bash
ls -l | pbcopy
```

また、最初の例は

```bash
echo sample1.txt | pbcopy
```

でも同じことです。

## alias　コマンドに独自の別名を定義

長くて毎度打つのが面倒なコマンドは別名を登録しましょう。

```bash
$ alias エイリアス名="登録したいコマンド"
```

でエイリアス（＝別名）を登録できます。  
筆者の愛用エイリアスは、

```bash
$ alias pyvenv='python3 -m venv venv && . venv/bin/activate'
```

です。Python仮想環境を作って `activate` まで一気に行います。

また、登録したエイリアスは `alias` コマンドで確認できます。

```bash
$ alias
alias g='git'
alias ga='git add'
alias gd='git diff'
alias gs='git status'
alias gp='git push'
alias gb='git branch'
alias gst='git status'
alias gco='git checkout'
alias gf='git fetch'
alias gc='git commit'
```

エイリアスの削除は `unalias` です。

**.bashrcと.bash\_profileについて**  
環境変数 `ailas` の設定には`.bashrc` と`.bash_profile` の理解がある方が望ましいです。  
`.bash_profile` はログイン時のみの実行で`.bashrc` は対話モードの `bash` 起動時に毎回実行されます。  
詳しく書くと長くなるので、以下の記事をご参考ください。

## curl wget　URLを使用してファイルをダウンロード

URlを用いてネット上のファイルをダウンロードできます。  
以下は、Googleのホームページの `HTML` ファイルを取得しています。

```bash
$ curl google.com
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.com/">here</A>.
</BODY></HTML>
```

`curl` は標準出力に結果を出力し、 `wget` はファイルを直接保存します。

## open　Finderで開く

macユーザーであれば、

```bash
$ open ディレクトリorファイル名
```

で、Finderを表示させることができます。  
[![](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3004778/6d25547b-ca8e-41cd-ae0c-3239fd59181d.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3004778%2F6d25547b-ca8e-41cd-ae0c-3239fd59181d.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=3a3df8a40061873f6adf429115fad848)

### Finder上のファイルパスを取得

Finderで選択したファイルに対して、 `option + command + C` で、ファイルのフルパスを取得できます。

## history　コマンド履歴

`Ctrl + R` で入力した履歴を検索できます。  
`-c` ：コマンド履歴をクリアします。履歴を削除した後、現在のセッションで実行されたコマンドのみが表示されます。  
`-w` ：現在のコマンド履歴をファイルに保存します。デフォルトでは、コマンド履歴はセッションの終了時に自動的に保存されますが、このオプションを使用すると手動で保存できます。

```bash
$ history
  1  ls
  2  cd Documents
  3  cat file.txt
  4  cp file.txt backup.txt
  5  rm file.txt
  6  history
```

## find　ファイルやディレクトリ検索

ファイルやディレクトリ検索のためのコマンドです。条件に応じた検索ができます。

### $ find 検索先ディレクトリ -name "ファイル名"

```text
sample/
　├ foo/
    ├ hoge.py
    └ hogehoge.py
　└ bar.py
```

このような構造のディレクトリに対して、

```bash
sample $ find . -name "*.py"
./foo/hogehoge.py
./foo/hoge.py
./bar.py

sample $ find foo -name "*.py"
foo/hogehoge.py
foo/hoge.py
```

のような使い方ができます。ここで、 `*` はワイルドカードの意味です。

### $ find 検索先 -size ±ファイル容量

「検索先」で指定したファイルサイズ以上（以下）のファイル、ディレクトリを取得できます。  
`-size +400k` なら400kB以上の、 `-size -10c` なら、10バイト以下のファイル、ディレクトリです。数字の前の `+` で以上を、 `-` で以下を、 `c, k, M, G` でそれぞれ `B, kB, MB, GB` を表します。

```bash
$ find ./ -size +400k 
./imgs
./imgs/352320.jpg
./imgs/353060.jpg
./imgs/344737.jpg
./imgs/349435.jpg
./imgs/350313.jpg
./imgs/349965.jpg
./imgs/345525.jpg
./imgs/345927.jpg
```

### $ find 検索先 -exec 実行したいコマンド　{} \\;

検索したファイルやディレクトリに対して、 `-exec` オプションで `cp` や `ls` などの任意のコマンドを実行することができます。

```bash
$ find ./imgs/* -type f -size -9k -exec file {} +;
./imgs/pic1.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 225x225, components 3
./imgs/pic3.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 231x500, components 3
```

**詳しく**

`{}` には、findで探してきたファイルやディレクトリが入るイメージ。  
`;`は、 `-exec` の実行するコマンドの終わりはセミコロンで区切るというルールに基づきます。  
`\` でエスケープしているのは、`;`は通常、「複数コマンドを続けて実行」する意味があるからです。  
上記の例では9kB以下のファイルを検索し、ファイル形式や縦横サイズを取得する、 `file` コマンドを実行しています。

## locate　高速なファイルやディレクトリ検索

`locate` は `find` より高速ですが、事前に作成されたデータベースからファイル名やパターンにマッチするファイルを検索するので、最新の情報とは異なる場合があります。大量のファイルを検索したいだけの時に使えます。 `-exec` のようなオプションはありません。  
`$ locate [オプション] [検索パターン]` と使用します。  
`locate` コマンドを初めて使うと、

```bash
$ locate -l 5 'bashrc'

WARNING: The locate database (/var/db/locate.database) does not exist.
To create the database, run the following command:

  sudo launchctl load -w /System/Library/LaunchDaemons/com.apple.locate.plist

Please be aware that the database can take some time to generate; once
the database has been created, this message will no longer appear.
```

のような

```bash
$ locate -l 5 'bashrc'
/System/Library/Templates/Data/private/etc/bashrc
/System/Library/Templates/Data/private/etc/bashrc_Apple_Terminal
/private/etc/bashrc
/private/etc/bashrc_Apple_Terminal
```

## grep ： ファイル内の文字列を検索

```text
$ grep [オプション] '検索文字列' 対象ファイル
```

の使い方で、ファイル内の文字列を検索します。

```text
$ grep -n "AP" sample/sample2.txt
1:APPLE
```

便利なオプションの一部を紹介すると、

| オプション | 説明 |
| --- | --- |
| **\-n** | マッチした行の行番号を表示 |
| **\-i** | 大文字小文字を区別せずに検索 |
| **\-r** | ディレクトリ内の全てのファイルを再帰的に検索 |
| **\-l** | ファイル名だけを表示 |

などがあります。

以下で使用例を紹介します。

```text
$ ls sample
sample1.txt     sample2.txt     sample3.txt
$ cat sample/sample1.txt
apple
$ cat sample/sample2.txt
APPLE
BANANA
$ cat sample/sample3.txt
orange
```

このような中身の `sample` ディレクトリに対して、

```text
$ grep -rl "ap" sample
sample/sample1.txt
$ grep -irl "ap" sample
sample/sample1.txt
sample/sample2.txt
```

のように該当文字列のあるファイル名を取得することができます。

その他オプションについては以下をご参考ください。

## df du ps　ディスク・システムの容量の使用状況

ディスクおよびシステムの容量がどれだけ使用されているかを確認できます。

### df　ファイルシステムの使用状況

```bash
$ df
Filesystem     1K-blocks    Used Available Use% Mounted on
/dev/sda1       10485760 3776328   6169432  38% /
tmpfs             524288       4    524284   1% /tmp
/dev/sdb1        2097152  524288   1572864  25% /home
```

以下は、各カラムが、ファイルシステム・ディスクパーティションにとって持つ意味の説明です。

| カラム名 | 説明 |
| --- | --- |
| 1K-blocks（1キロバイトブロック数） | 総容量をキロバイト単位で表示します。この値は、ファイルシステムが利用可能な総容量です。 |
| Used（使用済み） | 使用されている容量をキロバイト単位で表示します。現在のディスク使用量です。 |
| Available（利用可能） | まだ利用可能な容量をキロバイト単位で表示します。現在のディスクの空き容量です。 |
| Use%（使用率） | 使用率のパーセンテージです。 |
| Mounted on（マウントポイント） | ファイルシステムまたはディスクパーティションがマウントされている場所を示します。ディレクトリのパスとして表示されます。 |

また、 `-h` オプションで、 `G` や `K` 表記でサイズを見やすく整形してます。

```bash
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1       9.5G  3.6G  5.9G  38% /
tmpfs           512M  4.0K  512M   1% /tmp
/dev/sdb1       2.0G  512M  1.5G  25% /home
```

**ファイルシステム**  
ファイルシステムは、OSが提供するデータ管理のためのデータ構造やアルゴリズムのセットのことです。一般的なファイルシステムの例としては、WindowsでのNTFSやFAT、Linuxでのext4やXFS、macOSでのHFS+やAPFSなどがあります。詳しくは以下の記事などをご参考ください。

### du　ディスク使用量

ディスク使用量をディレクトリごとに集計して表示するコマンドです。

```bash
$ du
8       ./.vscode
24      .
```

最初の数字が、ファイルまたはディレクトリのパスのディスク使用量です。  
`du directory_name` の形でディレクトリを指定できます。その場合、そのディレクトリおよび全てのサブディレクトリの使用量を集計します。なお、ディレクトリを省略すると、カレントディレクトリが指定されます。  
ファイル名を指定することもでき、その場合該当のファイルのディスク使用量が表示されます。

### ps　実行中のプロセス情報

```bash
$ ps
  PID TTY          TIME CMD
 1234 pts/0    00:00:01 bash
 5678 pts/0    00:00:00 ps
```

以下は各カラムの説明です。

| カラム名 | 説明 |
| --- | --- |
| PID（Process ID） | プロセスの一意の識別子。 |
| TTY（Terminal） | プロセスが実行されている端末デバイス（ターミナル）の情報。 |
| TIME | プロセスが実行されている総CPU時間。 |
| CMD（Command） | プロセスを起動したコマンドまたはプログラムの名前。 |

以下はオプションの説明です。

| オプション | 説明 |
| --- | --- |
| `-e` | 全てのプロセスの表示。 |
| `-l` | 詳細な情報を表示。 |
| `-u` | 指定したユーザに関連するプロセスのみを表示。 |
| `-p` | 指定したプロセスIDに関連するプロセスのみを表示。 |
| `-r` | 実行中のプロセスのみ表示。 |

## ncdu

インストールが必要な `du` の上位コマンドを紹介します。ファイルサイズやファイル数が見やすく、vimみたいな感じで、サイズの大きいファイルを十字キー使いながら探すことができます。

インストール方法

```bash
$ sudo apt-get install ncdu # Debian系
$ sudo yum install ncdu  # RedHat系
$ brew install ncdu # macOS/Homebrew
$ sudo port install ncdu # macOS/MacPorts
```

[![スクリーンショット 2023-04-21 11.00.57.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3004778/1913538a-e656-96e0-732e-5e6aed366f0b.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3004778%2F1913538a-e656-96e0-732e-5e6aed366f0b.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=78f3410604682d7e52faafca7a1f039c)

## uniq　重複行の削除

```text
$ uniq file.txt
```

で `file.txt` の隣り合った重複する行を削除できます。ただし、保存先を指定しなければ標準出力に結果を書き出します。  
変更内容を保存するには、

```bash
$ uniq file.txt > file_modified.txt
```

などとしましょう。  
なお、重複した行数を表示する `uniq -c file.txt` や、重複した行だけ出力する `uniq -d file.txt` も存在します。  
もちろん、コマンドの出力を引数にパイプ `|` を用いることもできます。

## sort　テキストファイルを行単位で並び替え

```text
$ sort file.txt
```

アルファベット順に従って行を並び替え標準出力に表示します。 `-r` オプションで逆順に、 `-f` オプションで大文字小文字区別なしのソートができます。  
`du` コマンドなどとパイプ `|` で組み合わせることでサイズ順に表示することもできます。

## diff　二つのファイルの差分を表示

二つのファイルの差分を表示します。 `-q` オプションを使えば簡潔に二つのファイルが全く同一かどうかだけを確認できます。

```text
$ cat sample1.txt
orange
orange
tomato
apple
$ cat sample2.txt
orange
tamago
tomato
applo
$ diff sample1.txt  sample3.txt 
2c2
< orange
---
> tamago
4c4
< apple
---
> applo
```

## cmp　バイトレベルの差異

```text
$ cmp file1.txt file2.txt
file1.txt file2.txt differ: byte 13, line 1
```

`file1.txt` と `file2.txt` の内容をバイトレベルで比較し、差異を出力します。差異がなければ出力はされません。

オプションは、以下のものがあります。

| オプション | 説明 |
| --- | --- |
| `-s` | 差異がある場合1を、ない場合0を出力、エラーコードは2を出力。 |
| `-b` | 行の終わりや空白の扱いを無視。 |
| `-l` | 差異のある位置とそのバイト値を表示。 |

## cut　テキストファイルを横方向で分割。

`cut` コマンドのできることを実例で確認しましょう。  
以下のようなファイルを考えます。

sample1.txt

```bash
01,John,200,California
02,Michel,800,Texas
03,Maria,100,New York
04,John,600,New York
05,David,300,Texas
```

### \-b　バイト数で取り出す位置を指定

各行の文字を取り出す位置を `-b` オプションで指定します。

2文字目を取り出す

```bash
$ cut -b 2 sample1.txt
1
2
3
4
5
```

`-` を使えば連続複数文字取り出せます。

2~4文字目を取り出す

```bash
$ cut -b 2-5 sample1.txt
1,Jo
2,Mi
3,Ma
4,Jo
5,Da
```

`,`で区切ればは習えた範囲を指定できます。

2~4文字目と7~9文字目を取り出す

```bash
$ cut -b 2-5,7-10 sample1.txt
01,Jo,200
1,Jon,20
2,Mihel,
3,Maia,1
4,Jon,60
5,Daid,3
```

終端を指定しなければ、最後尾まで取り出します。

5文字目以降を取り出す

```bash
$ cut -b 5- sample1.txt
ohn,200,California
ichel,800,Texas
aria,100,New York
ohn,600,New York
avid,300,Texas
```

### \-d　区切り文字を指定

区切り文字を指定できます。他のオプションと組み合わせて使います。

### \-f　取り出す項目（カラム）の数を指定

`-b` と同じような仕様で項目数を指定します。

```bash
$ cut -d "," -f 3 sample1.txt 
200
800
100
600
300
```

```bash
$ cut -d "," -f 2-5 sample1.txt 
John,200,California
Michel,800,Texas
Maria,100,New York
John,600,New York
David,300,Texas
```

### 出力を保存

出力は標準出力に表示されるだけなので、保存したいときは `>` を使います。

```text
$ cut -d "," -f 2-5 sample1.txt > sample2.txt
```

## sed　文字列置換、抽出、削除

### \-e "s/置換前文字列/置換後文字列/"

下記のようなファイルを考える。

sample1.txt

```
01,John,2000,America/California
02,Michel,8000,America/Texas
03,Maria,1000,America/New York
04,John,6000,America/New York
05,David,3000,America/Texas
```

`-e` オプションに `"s/置換前文字列/置換後文字列/"` で指定します。

```bash
$ sed -e "s/000/k/" sample1.txt 
01,John,2k,America/California
02,Michel,8k,America/Texas
03,Maria,1k,America/New York
04,John,6k,America/New York
```

`"s/置換前文字列/置換後文字列/"` の `/` はなんでも構いません。置換対象に含まれていない特殊文字を指定するのがこつです。 `/` が含まれている `"America/"` を `He is in "` に置換したい場合を考えましょう。 `"s/America//He is in /"` だとうまくいきません。 `"s/America\//He is in /"` とエスケープしても良いですが、

```bash
$ sed -e "s@America/@He is in @" sample1.txt 
01,John,200,He is in California
02,Michel,800,He is in Texas
03,Maria,100,He is in New York
04,John,600,He is in New York
05,David,300,He is in Texas
```

のように `@` 区切りにすることで見やすくなります。

`g` をつけることで、行に複数出てくる文字を全て指定できます。 `"s/置換前文字列/置換後文字列/g"` としないと各行の最初に出てくる置換対象文字列だけ置換されます。

```bash
#gを指定する場合
$ sed -e "s@,@\t@g" sample1.txt
01      John    2000    America/California
02      Michel  8000    America/Texas
03      Maria   1000    America/New York
04      John    6000    America/New York
05      David   3000    America/Texas
# gを指定しない場合
$ sed -e "s@,@\t@" sample1.txt
01      John,2000,America/California
02      Michel,8000,America/Texas
03      Maria,1000,America/New York
04      John,6000,America/New York
05      David,3000,America/Texas
```

### \-e "3d"　3行目を削除

`"3d"` で3行目を削除できます。 `"1,4d"` で1~4行目を削除、 `"/$d"` で最終行を削除できます。

```text
$ sed -e "3d" sample1.txt 
01,John,2000,America/California
02,Michel,8000,America/Texas
04,John,6000,America/New York
05,David,3000,America/Texas
```

### \-i　直接編集

`-e` オプションは標準出力に表示するのに対し、 `-i` にすると直接ファイルを編集できます。

### その他

`sed` コマンドには他にもできることがたくさんあります。以下は参考記事からの引用です。

| コマンド | 説明 |
| --- | --- |
| `=` | 現在の行番号を出力します。 |
| `#` | コメント |
| `a 文字列` | 指定した位置の後ろに文字列を追加します。（改行する場合は改行前の文字列に `/` を付ける。） |
| `i 文字列` | 指定した位置の後ろにテキストを挿入します。（改行する場合は改行前の文字列に `/` を付ける。） |
| `c 文字列` | 選択した行をテキストに置き換えます。（改行する場合は改行前の文字列に `/` を付ける。 |
| `q` | 入力を中断し、それ以上処理を行わず終了します。（※未出力分があれば出力してから終了します。） |
| `d` | 指定した行を削除します。 |
| `p` | 処理した内容を出力します。 `-n` オプションと組み合わせて使うことがあります。 |
| `P` | 処理した内容を出力します。 |
| `r ファイル名` | ファイルを先頭に追加します。 |
| `s/置換前文字列/置換後文字列/` | （置換前文字列）で指定した文字列にマッチした部分を置換後文字列へ置き換えます。また複数マッチした場合は先頭のみ置換を行い、対象全て置換を行う場合は `"s/置換前/置換後/g"` のように `-g` オプションを併用します。 |
| `y/元の文字列/対象文字列/` | （元の文字列）のものを（対象文字列）の同じ位置に存在する文字列に置換します。（※ `tr` コマンドのように使用可能です。） |

以下の記事をご参考ください。

## ssh scp　リモートマシンとやりとり

### ssh　リモートマシンに接続

`$ ssh [オプション] [ログイン名@]接続先 [接続先で実行したいコマンド]` の書式でリモートマシンに接続します。DeepLearningの文脈ならGPUに接続する際に登場します。  
`~/.ssh` 配下に `config` ファイルを作成すると簡単にリモートマシンと `ssh` 接続できます。

config

```bash
Host example
    HostName example.com #IPアドレスでも可
    User myusername
    Port 22
    IdentityFile ~/.ssh/id_rsa
```

各項目の説明です。

| 項目 | 説明 |
| --- | --- |
| Host | ホストのエイリアスを指定。 |
| HostName | ホスト名またはIPアドレスを指定。 |
| User | 接続する接続先のユーザ名を指定。 |
| Port | 接続するポート番号を指定。 |
| IdentityFile | 認証に使用するローカルの秘密鍵ファイルのパスを指定。 |

`IdentityFile` は

```text
ls ~/.ssh
```

で見つけることができます。

```text
ssh host_name
```

接続にはローカルPCの公開鍵が必要です。

```text
$ ls ~/.ssh/
```

## scp　ローカル・リモート間でファイルのコピー

ローカルのファイルをリモートにコピーしたりその逆ができます。

### ローカルからリモートサーバーにファイルをコピー

ローカルからリモートにコピー

```bash
$ scp /path/to/local/file.txt username@hostname:/path/to/remote/
```

### リモートサーバーからローカルにファイルをコピー

リモートからローカルにコピー

```bash
$ scp username@hostname:/path/to/remote/file.txt /path/to/local/
```

### \-r ディレクトリごとコピー

```bash
$ scp -r /path/to/local/directory username@hostname:/path/to/remote/
```

## rsync

ファイルやディレクトリをローカル間またはローカルとリモート間で同期します。

### ローカルのディレクトリを別のローカルのディレクトリに同期する

```bash
$ rsync -av /path/to/source/ /path/to/destination/
```

`-a`, `--archive` オプションは、ファイルの属性、パーミッション、タイムスタンプなどが保持されるアーカイブモードで同期します。  
`-v`, `--verbose` オプションは 詳細な出力を表示します。

### ローカルのディレクトリをリモートサーバーに同期する

```bash
$ rsync -av /path/to/source/ user@remote:/path/to/destination/
```

### リモートサーバーからローカルのディレクトリに同期する

```bash
$ rsync -av user@remote:/path/to/source/ /path/to/destination/
```

## awk　条件満たす行を表示

`$ awk 'パターン { アクション }' ファイルパス` の書式で実行します。  
パターンには指定する行の条件を、アクションには `{}` で囲む表示する `{print}` などのコマンドを、ファイルパスには対象テキストファイルのパスを指定します。  
以下の `sample4.txt` を使います。

sample4.txt

```
01    John    2000    America/California
02    Michel    8000    America/Texas
03    Maria    1000    America/New York
04    John    6000    America/New York
05    David    3000    America/Texas
```

```bash
$ awk '$2 == "John"' sample4.txt 
01      John    2000    America/California
04      John    6000    America/New York
```

`$2` は2行目という意味です。2行目が `John` の行をそのまま表示しています。「アクション」は省略されでファルとの `{print}` が呼び出されています。  
アクションの種類には分割の `split` や置換の `sub` などがありますが全部紹介すると多いので、以下の記事等をご参考ください。

## ifconfig　ネットワークインターフェースの設定

ネットワークインターフェースの設定情報を表示するため `$ ifconfig インターフェース名` で使用します。

```text
$ ifconfig eth0
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.0.100  netmask 255.255.255.0  broadcast 192.168.0.255
        inet6 fe80::cdef:1234:abcd:ef01  prefixlen 64  scopeid 0x20<link>
        ether 00:11:22:33:44:55  txqueuelen 1000  (Ethernet)
        RX packets 12345  bytes 6789012 (6.4 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 23456  bytes 7890123 (7.5 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

| 項目名 | 説明 |
| --- | --- |
| inet | IPv4アドレス |
| inet6 | IPv6アドレス |
| netmask | ネットマスク |
| ether | MACアドレス |
| RX packets | 受信パケット数 |
| TX packets | 送信パケット数 |
| RX errors | 受信エラー数 |
| TX errors | 送信エラー数 |

## おわりに

弊社Nucoでは、他にも様々なお役立ち記事を公開しています。よかったら、 [Organization](https://qiita.com/organizations/nuco-inc/profile) のページも覗いてみてください。  
また、Nucoでは一緒に働く仲間も募集しています！興味をお持ちいただける方は、 [こちら](https://www.recruit.nuco.co.jp/?qiita_item_id=6a4faf4d026d130e7db1) まで。

## 参考

[https://qiita.com/reireias/items/d906ab086c3bc4c22147](https://qiita.com/reireias/items/d906ab086c3bc4c22147)  
[https://qiita.com/magicant/items/d3bb7ea1192e63fba850](https://qiita.com/magicant/items/d3bb7ea1192e63fba850)  
[https://qiita.com/Hige-Moja/items/44bbfb14868816169978](https://qiita.com/Hige-Moja/items/44bbfb14868816169978)  
[https://qiita.com/marrontan619/items/95e954972706f32be255](https://qiita.com/marrontan619/items/95e954972706f32be255)  
[https://atmarkit.itmedia.co.jp/ait/series/3065/](https://atmarkit.itmedia.co.jp/ait/series/3065/)  
  
[https://qiita.com/takech9203/items/b96eff5773ce9d9cc9b3](https://qiita.com/takech9203/items/b96eff5773ce9d9cc9b3)

[3](https://qiita.com/kjm_nuco/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fkjm_nuco%2Fitems%2F6a4faf4d026d130e7db1&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fkjm_nuco%2Fitems%2F6a4faf4d026d130e7db1&realm=qiita)

[850](https://qiita.com/kjm_nuco/items/6a4faf4d026d130e7db1/likers)

いいねしたユーザー一覧へ移動

963