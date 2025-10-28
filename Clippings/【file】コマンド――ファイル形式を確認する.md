---
title: "【file】コマンド――ファイル形式を確認する"
source: "https://atmarkit.itmedia.co.jp/ait/articles/1605/10/news018.html"
author:
  - "[[西村めぐみ]]"
published: 2016-05-10
created: 2025-09-01
description: "本連載は、Linuxのコマンドについて、基本書式からオプション、具体的な実行例までを紹介していきます。今回は、「file」コマンドです。"
tags:
  - "clippings"
---
## 【file】コマンド――ファイル形式を確認する：Linux基本コマンドTips（12）

本連載は、Linuxのコマンドについて、基本書式からオプション、具体的な実行例までを紹介していきます。今回は、「file」コマンドです。

2016年05月10日 05時00分 公開

[**Linux基本コマンドTips一覧**](https://atmarkit.itmedia.co.jp/ait/series/3065/)

　本連載では、Linuxの基本的なコマンドについて、基本的な書式からオプション、具体的な実行例までを分かりやすく紹介していきます。今回は「 **file** 」コマンドでファイル形式を確認する方法を紹介します。

## fileコマンドとは？

　「file」は、ファイル形式を調べるためのコマンドです。「 **file ファイル名** 」で、ファイルの形式が表示されます。テキストファイルの場合は、文字コードが表示されます。

[**目次に戻る**](https://atmarkit.itmedia.co.jp/ait/articles/1605/10/#mokuji)

  

## fileコマンド書式

file \[[オプション](https://atmarkit.itmedia.co.jp/ait/articles/1605/10/#opt)\] ファイル1 ファイル2 ファイル3……

※\[ \]は省略可能な引数を示しています

  

## fileコマンドの主なオプション

　fileコマンドの主なオプションは次の通りです。

<table><thead><tr><th>短いオプション</th><th>長いオプション</th><th>意味</th></tr></thead><tbody><tr><th>-f</th><td>--files-from リスト</td><td>検査するファイルの名前をリストファイルから読み込む（ <a href="https://atmarkit.itmedia.co.jp/ait/articles/1605/10/#sample2">実行例2</a> を参照）</td></tr><tr><th>-L</th><td>--dereference</td><td>シンボリックリンクの参照先を調べる（ <a href="https://atmarkit.itmedia.co.jp/ait/articles/1605/10/#sample3">実行例3</a> を参照）</td></tr><tr><th>-z</th><td>--uncompress</td><td>圧縮ファイルの中も検査する（ <a href="https://atmarkit.itmedia.co.jp/ait/articles/1605/10/#sample4">実行例4</a> を参照）</td></tr><tr><th>-b</th><td>--brief</td><td>出力行の最初にファイル名を表示しない（簡易モード）</td></tr><tr><th>-N</th><td>--no-pad</td><td>出力を整列するためのファイル名への空白の追加を行わない</td></tr></tbody><tfoot><tr><td colspan="3"></td></tr></tfoot></table>

  

[**目次に戻る**](https://atmarkit.itmedia.co.jp/ait/articles/1605/10/#mokuji)

  

## ファイルの形式を表示する

　「 **file ファイル名** 」でファイル形式が表示されます（ **画面1** ）。

## コマンド実行例

**file データファイル**

[![画面1](https://image.itmedia.co.jp/ait/articles/1605/10/command_12-01.png)](https://image.itmedia.co.jp/l/im/ait/articles/1605/10/l_command_12-01.png) **画面1** 　「file /usr/bin/grep」と「file /usr/bin/egrep」でそれぞれのファイル形式を調べる（※）

※「egrep」は「grep -E」と同じ意味のコマンド（「-E」は検索文字列に [拡張正規表現](https://atmarkit.itmedia.co.jp/ait/articles/1604/13/news022.html#sample4) を使うオプション）で、CentOS環境の場合はシェルスクリプトで実現しています。「/usr/bin/egrep」の内容を見ると、最終的に「grep -E」を実行していることが分かります。

  

　なお、データファイルの場合は「.jpg」や「.mp3」といった拡張子で判断してしまうのが一般的ですが、fileコマンドの場合は拡張子ではなく、あくまでも“ファイルの内容”から判断します（ **画面2** ）。

## コマンド実行例

**file データファイル**

[![画面2](https://image.itmedia.co.jp/ait/articles/1605/10/command_12-02.png)](https://image.itmedia.co.jp/l/im/ait/articles/1605/10/l_command_12-02.png) **画面2** 　「file screenshot001.png」と「file screenshot001.jpg」でそれぞれのファイル形式を調べる（※）

※筆者がテスト用に作成したファイルで、両者の内容は同じです。

  
  

[**目次に戻る**](https://atmarkit.itmedia.co.jp/ait/articles/1605/10/#mokuji)

  

## ファイルのリストから調べる

　「 **\-f** 」オプションで、ファイルの一覧を指定することができます。例えば、Linuxの設定ファイル「/etc/shells」には [インストールされているシェルがリストアップされています](https://atmarkit.itmedia.co.jp/ait/articles/1603/02/news016.html) が、「 **file -f /etc/shells** 」で、それぞれのシェルのファイル形式を調べることができます（ **画面3** ）。

## コマンド実行例

**file -f リストファイル**

[![画面3](https://image.itmedia.co.jp/ait/articles/1605/10/command_12-03.png)](https://image.itmedia.co.jp/l/im/ait/articles/1605/10/l_command_12-03.png) **画面3** 　「file -f /etc/shells」で設定ファイル「/etc/shells」にリストアップされているファイルの形式を調べる

  

[**目次に戻る**](https://atmarkit.itmedia.co.jp/ait/articles/1605/10/#mokuji)

  

## シンボリックリンクの参照先を調べる

　「 **\-L** 」オプションで、シンボリックリンクのリンク先を調べることができます。例えば、先ほどの実行結果から「/bin/csh」は、tcshへのシンボリックリンクであることが分かりました。「 **file -L /bin/csh** 」で、リンク先であるtcshのファイル形式が表示されます（ **画面4** ）。

## コマンド実行例

**file -L ファイル**

[![画面4](https://image.itmedia.co.jp/ait/articles/1605/10/command_12-04.png)](https://image.itmedia.co.jp/l/im/ait/articles/1605/10/l_command_12-04.png) **画面4** 　「file -L /bin/csh」で「/bin/csh」のリンク先（tcsh）のファイル形式を調べる

  

[**目次に戻る**](https://atmarkit.itmedia.co.jp/ait/articles/1605/10/#mokuji)

  

## 圧縮されているファイルを調べる

　「 **\-z** 」オプションで、圧縮されているファイルの元の形式を調べることができます。

　「 **man コマンド** 」ではコマンドの詳しい使い方（マニュアル）を参照することができますが、このmanコマンドが参照しているファイルはgzip形式で圧縮されたテキストファイルです。「 **file -z ファイル名** 」で、圧縮元のファイル形式が表示されます（ **画面5** ）。

## コマンド実行例

**file データファイル**

  

[**目次に戻る**](https://atmarkit.itmedia.co.jp/ait/articles/1605/10/#mokuji)

  

## grepコマンドと組み合わせてスクリプトファイルを一覧表示する

　fileコマンドでシェルスクリプトをはじめとするスクリプトファイルを調べると、「 **shell script** 」のように表示されます。

　「 **file /bin/\* | grep script** 」で「/bin」にあるファイルを全て調べ、grepコマンドで「script」と出力されたものを絞り込むことができます。先頭が大文字で「Script」になっている可能性も考えて、grepには大小文字を区別しない「-i」オプションを付けておくとより安心です。

　なお、該当するものが多いため、 **画面6** の実行例ではさらに「more」コマンドも組み合わせています。

## コマンド実行例

**file /bin/\* | grep -i script**

[![画面6](https://image.itmedia.co.jp/ait/articles/1605/10/command_12-06.png)](https://image.itmedia.co.jp/l/im/ait/articles/1605/10/l_command_12-06.png) **画面6** 　「file /bin/\* | grep script | more」で「/bin」下にあるスクリプトを一覧表示する

  

[**目次に戻る**](https://atmarkit.itmedia.co.jp/ait/articles/1605/10/#mokuji)

  

## whichコマンドと組み合わせてコマンドのファイル形式を調べる

　「type」コマンドや「which」コマンドを使うと、コマンドがどこにあるかを調べることができます（ [コマンドがどこにあるか探してみよう（連載：Linux再入門 第8回）](https://atmarkit.itmedia.co.jp/ait/articles/1605/09/news013_3.html) ）。特に、whichコマンドはファイル名だけが表示されるので、バッククォート（\`）で「whichコマンドの実行結果を、fileコマンドで調べる」という使い方ができます。

　「 **\`** 」（バッククォート）で囲まれた部分は、実行時にコマンドの結果で置き換えられます。従って、「 **file \`which reboot\`** 」を実行すると、「\`which reboot\`」部分は「which reboot」の実行結果である「/usr/sbin/reboot」に置き換わるので、「 **file /usr/sbin/reboot** 」が実行される、という意味になります（ **画面7** ）。

## コマンド実行例

**file \`which コマンド名\`**

[![画面7](https://image.itmedia.co.jp/ait/articles/1605/10/command_12-07.png)](https://image.itmedia.co.jp/l/im/ait/articles/1605/10/l_command_12-07.png) **画面7** 　「file \`which reboot\`」でrebootの場所を探し、そのファイル形式を調べる（「/usr/sbin/reboot」はシンボリックリンクだと分かったので、さらに「file -L \`which reboot\`」でリンク先も調べている）

## ワンポイント

　CentOSでは、whichコマンドでエイリアス定義とコマンド本体の両方が表示されるように設定されています。

　例えば「 **which ls** 」では、lsのエイリアス定義とコマンド本体の場所である「/usr/bin/ls」が表示されます。whichコマンドを今回のような用途で使用したい場合は、「 **file \`/bin/which ls\`** 」のように指定するか、「 **unalias which** 」でwhichコマンドのエイリアス定義を一時的に削除します。

　エイリアス定義はbashの設定ファイル（具体的には「/etc/profile」から呼び出される「/etc/profile.d/」下のファイル）で設定されているので、ログインし直すとwhichのエイリアス設定は元に戻ります。

　whichコマンドを常に“オプションなし”で使いたい場合は、ユーザー用の設定ファイル（今回の場合、具体的にはホームディレクトリの「.bash\_profile」）に「 **unalias which** 」と書いた行を追加するとよいでしょう。

  

[**目次に戻る**](https://atmarkit.itmedia.co.jp/ait/articles/1605/10/#mokuji)

  

[この連載を「連載記事アラート」に登録する](https://atmarkit.itmedia.co.jp/ait/articles/1605/10/) New

## 筆者紹介

PC-9801N／PC-386MからのDOSユーザー。1992年より生産管理のパッケージソフトウェアの開発およびサポート業務を担当。のち退社し、専業ライターとして活動を開始。著書に『図解でわかるLinux』『らぶらぶLinuxシリーズ』『はじめてでもわかるSQLとデータ設計』『シェルの基本テクニック』など。2011年より、地方自治体の在宅就業支援事業にてPC基礎およびMicrosoft Office関連の教材作成およびeラーニング指導を担当。

- [連載：“応用力”をつけるためのLinux再入門](https://atmarkit.itmedia.co.jp/ait/kw/ait_linux_ouyoy.html)
- [連載：ネットワーク管理の基本Tips](https://atmarkit.itmedia.co.jp/ait/kw/ait_basic_networkcommands.html)
- [連載：Windowsユーザーに教えるLinuxの常識（全12回）](https://www.atmarkit.co.jp/flinux/index/indexfiles/rootindex.html)

Special PR

この記事に関連する製品／サービスを比較（キーマンズネット）

- [ディストリビューションの違いは要確認！『Linux』製品比較](http://www.keyman.or.jp/server/linux/product?cx_source=kn-pdb201704)
- [用途にあったOSやエディションを選択できていますか？『Windows Server』関連製品情報](http://www.keyman.or.jp/server/windows/product?cx_source=kn-pdb201704)

[印刷／保存](https://id.itmedia.co.jp/isentry/contents?sc=0c1c43111448b131d65b3b380041de26f2edd6264ee1c371184f54d26ab53365&lc=7d7179c146d0d6af4ebd304ab799a718fe949a8dcd660cd6d12fb97915f9ab0a&return_url=https://ids.itmedia.co.jp/print/ait/articles/1605/10/news018.html&encoding=shift_jis&ac=e8cb9106baa7e37eb9feb877b9f0a27ddaf48b95ba02da49cbb3a8247ee7fec4&cr=e9fd42802bc22856808963077023568339063544b05e5a8646e62c02a898e0fd "この記事を印刷する")

[連載通知](https://id.itmedia.co.jp/isentry/contents?sc=0c1c43111448b131d65b3b380041de26f2edd6264ee1c371184f54d26ab53365&lc=7d7179c146d0d6af4ebd304ab799a718fe949a8dcd660cd6d12fb97915f9ab0a&return_url=https%3A%2F%2Fid.itmedia.co.jp%2Fapp%2Falert%2Fregist_setting%3Furl%3Dhttps%3A%2F%2Fatmarkit.itmedia.co.jp%2Fait%2Farticles%2F1605%2F10%2Fnews018.html%26type%3D2&encoding=shift_jis&ac=8b70865e13a2d61cf96c34172b9312018dffa6d8d496609bf9fd8314fd091e1a&cr=4634196a317325dfc00aa8fe3ea82f36ad62e80b103fcbdaf13b4d39fa261577 "Linux基本コマンドTips")

## アイティメディアからのお知らせ

- [キャリア採用の応募を受け付けています](https://hrmos.co/pages/itmedia/jobs?jobType=FULL)

スポンサーからのお知らせ PR

Special PR

**本日** **月間**

編集部からのお知らせ

[【Amazonギフトカード プレゼント】9月3～4日開催【無料オンラインセミナー】『＠IT Operator Live 2025 夏　目まぐるしく変わる「不安の時代」に、ビジネスを安定稼働させるには』GMOペパボ SUZURI事業部 事業部CTO 黒瀧悠太氏による【基調講演　GMOペパボのSUZURIを支えるAIOpsの実践】、運用設計ラボ シニアアーキテクト 波田野裕一氏による【基調講演　レジリエントな運用を実現する「運用エンジニアリング」】などを配信](https://rd.itmedia.co.jp/8Cfq#utm_source=ait&utm_content=rightcolumn_info)

あなたにおすすめの記事 PR