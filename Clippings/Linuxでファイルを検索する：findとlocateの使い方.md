---
title: "Linuxでファイルを検索する：findとlocateの使い方"
source: "https://www.digitalocean.com/community/tutorials/how-to-use-find-and-locate-to-search-for-files-on-linux-ja"
author:
  - "[[Justin Ellingwood]]"
published: 2020-12-02
created: 2025-08-21
description: "ファイルの検索は、オペレーティングシステムを問わずごく一般的なタスクです。Linuxの「find」と「locate」コマンドは、どちらもファイルシステムのファイルの検索に使用します。このガイドでは、これら2つのツールを使用して、個別にファイルのクエリを作成する方法を説明します。"
tags:
  - "clippings"
---
![Linuxでファイルを検索する：findとlocateの使い方](https://www.digitalocean.com/api/static-content/v1/images?src=%2F_next%2Fstatic%2Fmedia%2Fintro-to-cloud.d49bc5f7.jpeg&width=1920 "Linuxでファイルを検索する：findとlocateの使い方")

### はじめに

Linuxを始めて最初に突き当たる問題は、ファイルの検索方法です。

このガイドは、 `find` というコマンドの適切な使い方について説明します。これにより、さまざまなフィルターやパラメータを使用してVPS上のファイルを検索できます。また、別の方法でファイルを検索できる `locate` コマンドについても簡単に触れます。

## 名前で検索する

ファイル検索の最も一般的な手段はファイル名の検索です。

名前からファイルを探すには、次のように入力します。

```bash
find -name "query"
```

大文字小文字を区別します。つまり、 `file` の検索は `File` の検索とは異なります。

大文字小文字を無視して名前検索するには、次のように入力します。

```bash
find -iname "query"
```

特定の文字列を除外してファイルを検索するには、 `-not` または`!`で検索を反転させます。`!`を使用する際は、findが実行される前にbashが解釈しないよう、この文字をエスケープしておく必要があります。

```bash
find -not -name "query_to_avoid"
```

または次を入力します。

```bash
find \! -name "query_to_avoid"
```

## ファイルの種類で検索する

検索するファイルの種類を `-type` パラメータで指定できます。次のようになります。

```bash
find -type type_descriptor query
```

ファイルの種類を指定するのに最もよく使用される記述子にはこのようなものがあります。

- **f**:通常のファイル
- **d**:ディレクトリ
- **l**:シンボルリンク
- **c**:キャラクタデバイス
- **b**:ブロックデバイス

たとえば、システム上のキャラクタデバイスをすべて表示するには、このコマンドを発行します。

```bash
find / -type c
```

```
Output/dev/parport0
/dev/snd/seq
/dev/snd/timer
/dev/autofs
/dev/cpu/microcode
/dev/vcsa7
/dev/vcs7
/dev/vcsa6
/dev/vcs6
/dev/vcsa5
/dev/vcs5
/dev/vcsa4
. . .
```

`.conf` で終わるファイルをすべて表示するには次のように入力します。

```bash
find / -type f -name "*.conf"
```

```
Output/var/lib/ucf/cache/:etc:rsyslog.d:50-default.conf
/usr/share/base-files/nsswitch.conf
/usr/share/initramfs-tools/event-driven/upstart-jobs/mountall.conf
/usr/share/rsyslog/50-default.conf
/usr/share/adduser/adduser.conf
/usr/share/davfs2/davfs2.conf
/usr/share/debconf/debconf.conf
/usr/share/doc/apt-utils/examples/apt-ftparchive.conf
. . .
```

## 時刻とサイズでフィルタリングする

Findは、結果をサイズと時刻でフィルタリングするさまざまな方法を提供します。

### サイズ

サイズでフィルターするには `-size` パラメータを使用します。

値の後ろにカウント方法を示す接尾辞を付けて単位を指定します。一般的なオプションは次のとおりです。

- **c**: Byte
- **k**: KByte
- **M**: MByte
- **G**: GByte
- **b**: 512 Byteブロック

50Byteちょうどのサイズのファイルをすべて検索するには、次を入力します。

```bash
find / -size 50c
```

50Byte未満のファイルをすべて検索するには、このフォームを使用します。

```bash
find / -size -50c
```

700MByte以上のファイルをすべて検索するには、このコマンドを使用します。

```bash
find / -size +700M
```

### 時刻

Linuxは、Access Time、Modification Time、Change Timeに関する時刻データを保存します。

- **Access Time**: ファイルが最後に読み込み、書き込みされた時刻。
- **Modification Time**: ファイルの内容が最後に変更された時刻。
- **Change Time**: ファイルのinodeメタデータが最後に変更された時刻。

これらの検索には `-atime` 、 `-mtime` 、 `-ctime` パラメータを使用します。サイズの場合と同様に、+／-記号を使用して、以前／以内を指定します。

このパラメータの値は、何日前に保存されたファイルを検索するかを指定します。

1日前に内容が変更されたファイルを検索するには、次のように入力します。

```bash
find / -mtime 1
```

1日以内にアクセスしたファイルを検索するには、次のように入力します。

```bash
find / -atime -1
```

メタ情報の最終変更日から3日以上経過したファイルを検索するには、次のように入力します。

```bash
find / -ctime +3
```

類似のパラメータを使用して、日数ではなく分数を指定することもできます。

```bash
find / -mmin -1
```

これにより、1分以内に内容が変更されたファイルがわかります。

また、Findを使用して参照ファイルとの比較を行い、新しい方のファイルを返すこともできます。

```bash
find / -newer myfile
```

## 所有者やパーミッションで検索する

ファイルの所有者やグループ所有者でファイルを検索することもできます。

これを行うには、それぞれ `-user` パラメータと `-group` パラメータを使用します。「syslog」ユーザーが所有するファイルを検索するには次を入力します。

```bash
find / -user syslog
```

同様に、「shadow」グループが所有するファイルを指定するには次のように入力します。

```bash
find / -group shadow
```

また、特定のパーミッションでファイルを検索することもできます。

あるパーミッションに完全一致するファイルをマッチさせる場合は、次の体裁で入力します。

```bash
find / -perm 644
```

この場合、指定されたパーミッションと完全に一致するファイルがマッチします。

これらのパーミッション *以上* のファイルを指定するには、次の体裁で入力します。

```bash
find / -perm -644
```

この場合、指定より上のパーミッションを持つファイルがマッチします。このインスタンスでは、パーミッション「744」のファイルがマッチします。

## 階層でフィルタリングする

このセクションでは、一時ディレクトリにディレクトリ構造を作成します。このディレクトリ構造に、3階層のディレクトリと、1階層目に10個のディレクトリを配置します。各ディレクトリ（一時ディレクトリを含む）には10個のファイルと10個のサブディレクトリを配置します。

次のコマンドでこの構造を作成します。

```bash
cd
mkdir -p ~/test/level1dir{1..10}/level2dir{1..10}/level3dir{1..10}
touch ~/test/{file{1..10},level1dir{1..10}/{file{1..10},level2dir{1..10}/{file{1..10},level3dir{1..10}/file{1..10}}}}
cd ~/test
```

`ls` と `cd` で各ディレクトリを自由に確認して回り、その構造を把握してください。終わったら、testディレクトリに戻ります。

```bash
cd ~/test
```

この構造から特定のファイルを返す方法について見ていきます。比較のためにまず、通常の名前検索で例を試してみましょう。

```bash
find -name file1
```

```bash
Output./level1dir7/level2dir8/level3dir9/file1
./level1dir7/level2dir8/level3dir3/file1
./level1dir7/level2dir8/level3dir4/file1
./level1dir7/level2dir8/level3dir1/file1
./level1dir7/level2dir8/level3dir8/file1
./level1dir7/level2dir8/level3dir7/file1
./level1dir7/level2dir8/level3dir2/file1
./level1dir7/level2dir8/level3dir6/file1
./level1dir7/level2dir8/level3dir5/file1
./level1dir7/level2dir8/file1
. . .
```

結果がたくさん表示されます。出力をカウンタにパイプすると、計1111個の結果があるのがわかります。

```bash
find -name file1 | wc -l
```

```bash
Output1111
```

これでは結果が多すぎて、ほとんどの場合役に立たないでしょう。絞り込み検索をしてみましょう。

検索するディレクトリの最大の深さを指定できます。

```bash
find -maxdepth num -name query
```

「レベル1」ディレクトリかそれ以上の階層でのみ「file1」を検索するには、最大深さ2（トップレベルディレクトリ1、レベル1ディレクトリ1）を指定します。

```bash
find -maxdepth 2 -name file1
```

```bash
Output./level1dir7/file1
./level1dir1/file1
./level1dir3/file1
./level1dir8/file1
./level1dir6/file1
./file1
./level1dir2/file1
./level1dir9/file1
./level1dir4/file1
./level1dir5/file1
./level1dir10/file1
```

これでとても管理しやすい一覧になりました。

現在のディレクトリ配下の、一定の階層から先にすべてのファイルが存在するとわかっていれば、最小ディレクトリを指定することもできます。

```bash
find -mindepth num -name query
```

これにより末端ディレクトリのファイルだけを検索できます。

```bash
find -mindepth 4 -name file
```

```bash
Output./level1dir7/level2dir8/level3dir9/file1
./level1dir7/level2dir8/level3dir3/file1
./level1dir7/level2dir8/level3dir4/file1
./level1dir7/level2dir8/level3dir1/file1
./level1dir7/level2dir8/level3dir8/file1
./level1dir7/level2dir8/level3dir7/file1
./level1dir7/level2dir8/level3dir2/file1
. . .
```

ここでも、分岐ディレクトリ構造の為、多数の結果(1000)が返されます。

最小・最大階層パラメータを組み合わせれば、焦点を狭い範囲に絞り込めます。

```bash
find -mindepth 2 -maxdepth 3 -name file
```

```bash
Output./level1dir7/level2dir8/file1
./level1dir7/level2dir5/file1
./level1dir7/level2dir7/file1
./level1dir7/level2dir2/file1
./level1dir7/level2dir10/file1
./level1dir7/level2dir6/file1
./level1dir7/level2dir3/file1
./level1dir7/level2dir4/file1
./level1dir7/file1
. . .
```

## Findコマンドの実行と組み合わせ

`-exec` パラメータを使用すると、すべての検索コマンド上で任意のヘルパーコマンドが実行できます。このように呼び出します。

```bash
find find_parameters -exec command_and_params {} \;
```

`{}` は、findで合致したファイルのプレースホルダーです。 `\;`は、findにコマンドの終わりを示します。

たとえば、前のセクションで見たファイルのパーミッション `644` を `664` に変更します。

```bash
cd ~/test
find . -type f -perm 644 -exec chmod 664 {} \;
```

また、ディレクトリのパーミッションも次のように変更できます。

```bash
find . -type d -perm 755 -exec chmod 700 {} \;
```

異なる結果を連結させるには、 `-and` または `-or` コマンドを使用します。省略されていれば `-and` と想定します。

```bash
find . -name file1 -or -name file9
```

## Locateでファイルを検索する

`find` の代替コマンドとして、 `locate` コマンドがあります。このコマンドの方が早いことが多く、ファイルシステム全体の検索が容易になります。

このコマンドは、DebianまたはUbuntuに `apt` でインストールできます。

```bash
sudo apt install mlocate
```

CentOSでは、 `dnf` を使用します。

```bash
sudo dnf install mlocate
```

`locate` が `find` より早いのは、それがファイルシステムのファイルのデータベースに依存しているためです。

データベースは通常、cronスクリプトで1日に一度更新されますが、次を入力して手動でも更新できます。

```bash
sudo updatedb
```

このコマンドを今すぐ実行します。最近取得したファイルや作成されたばかりのファイルを検索するなら、データベースを常に最新にしておく必要があります。

locateでファイルの検索に使用する構文はこれだけです。

```bash
locate query
```

出力をフィルターする方法がいくつかあります。

たとえば、クエリ自体に含まれるファイルのみを返すなら、上位ディレクトリ付きでファイルを一つ一つ返すのではなく、 `-b` を使用して「basename」だけを検索できます。

```bash
locate -b query
```

locateが、ファイルシステムにまだ存在する（直近の `updatedb` コールと現在の `locate` コール間で削除されなかった）結果のみを返すようにするには、 `-e` フラグを使用します。

```bash
locate -e query
```

locateでカタログ化された情報を統計表示するには、 `-S` オプションを選択します。

```bash
locate -S
```

```
OutputDatabase /var/lib/mlocate/mlocate.db:
3,315 directories
37,228 files
1,504,439 bytes in file names
594,851 bytes used to store database
```

## まとめ

find、locateの両コマンドとも、システム上のファイルの検索手段としてお勧めします。状況に応じてどちらのツールが適当かご自身で判断してください。

find、locateの両コマンドとも、他のユーティリティとパイプラインでつないで増強できる、パワフルなコマンドです。 `wc` 、 `sort` 、 `grep` などのコマンドを使用してフィルタリングを試しましょう。

Thanks for learning with the DigitalOcean Community. Check out our offerings for compute, storage, networking, and managed databases.

[Learn more about our products](https://www.digitalocean.com/products "Learn more about our products")