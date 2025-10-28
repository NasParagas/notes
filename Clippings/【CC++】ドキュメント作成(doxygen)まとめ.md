---
title: "【C/C++】ドキュメント作成(doxygen)まとめ"
source: "https://www.mathkuro.com/c-cpp/doxygen/#toc16"
author:
  - "[[マスクロ(mathkuro)]]"
published: 2020-04-13
created: 2025-08-26
description: "C/C++のドキュメント作成でdoxygenを使ってみたまとめです。doxygenスタイルのコメントの記述方法、doxygenのインストール方法、HTML形式のドキュメント出力方法についてまとめています。"
tags:
  - "clippings"
---
C/C++のドキュメント作成でdoxygenを使ってみたまとめです。

## １．doxygenとは

> *Doxygen* は、C++、C、Java、Objective-C、Python、IDL (Corba、Microsoft 風) 向けのドキュメンテーションシステムです。 PHP、C#、Dにもある程度対応しています。
> 
> [http://www.doxygen.jp/](http://www.doxygen.jp/)

と、公式サイトで説明されている通り、ドキュメント生成を行うためのシステムです。  
※ライセンスはGPLで、無料で使うことができます。  
※OSはLinux, Windows, Macで使うことができます。  
  
下図はドキュメント生成のざっくりとした流れです。

![](https://www.mathkuro.com/wp-content/uploads/2020/04/%E3%83%88%E3%82%99%E3%82%AD%E3%83%A5%E3%83%A1%E3%83%B3%E3%83%88%E7%94%9F%E6%88%90%E3%81%AE%E6%B5%81%E3%82%8C-1024x423.png)

  
まずは、doxygenスタイルのコメントの記述方法からまとめていきたいと思います。

## ２．doxygenスタイルのコメント記述方法(とサンプルコード)

### 2-1 基本

doxygenはすべてのコメントをドキュメント化するのではなく、特定の形式で記述されたコメントのみをドキュメント化します。  
※全部ドキュメント化されると逆に困りますよね…  
  
doxygen形式のコメントの基本形がこれです。

/\*\* doxygenスタイルのコメント　その１ \*/  
/\*! doxygenスタイルのコメント　その２ \*/  
//! doxygenスタイルのコメント　その３

これらの形式で記述されたコメントのみがドキュメント化の対象となります。  
  
注意点は、コメント開始の `/**` やコメント終了の `*/` と、コメント文との間は半角スペースが必須なところです。  
  
また、複数行のコメントの場合は、以下のように記述します。

/\*\*  
\* コメント一行目  
\* コメント二行目  
\*/  
  
※その２、その３も複数行で書けますがここでは省略します。

複数行の場合も、コメントの本文と `*` との間には半角スペースが必須です。  
  
このページでは、 `/** 〜 */` を以降使用していきます。

doxygenコメントにはもう一つ覚えることがあります。  
それが、@detailのような特殊コマンドです。  
  
これは、doxygenがドキュメントを生成する際のヒントを与えるようなもので、例えば、以下のように@detailと記載した場合、  
`* @detail 詳細説明`  
ここに書かれたコメントは出力するドキュメントの詳細説明の欄に出力されます。  
  
細かい意味は以降の章で触れますので、ここでは、 **コマンドの前後は半角スペースが必須** である点だけ抑えておいてください。

[Doxygen](Doxygen.md)
[Doxygen](Doxygen.md)
[Doxygen](Doxygen.md)

[Doxygen](Doxygen.md)
### 2-6 名前空間のコメント

namespaceの上にdoxygenスタイルのコメントを記載すると自動的に名前空間のドキュメントとして扱われます。

/\*\* NameSpaceAの概要説明 \*/  
namespace NameSpaceA {  
}

ファイルのコメントと異なり、名前空間のコメントは記載しなくても特に問題は起こりません。

## ３．doxygenインストール

### 3-1 ダウンロード

公式ページからバイナリをダウンロードします。  
[http://www.doxygen.nl/download.html](http://www.doxygen.nl/download.html)  
  
少し分かりにくいのですが、↑のサイトの中盤に **Sources and Binaries** という項目があり、その項にダウンロード用のソースとバイナリが用意されています。  
  
Mac(dmg形式)、Windows(exe形式)、Linux(バイナリのtarball)が用意されていたので、通常はコンパイル等は不要なはずです。  
  
今回はmac(Catalina 10.15.4)で試したのでmacOS向けのバイナリをダウンロードしました。  
(Doxygen-1.8.17.dmg)

### 3-2 インストール

ダウンロードしたdmgファイルを開くと以下のような画面になります。  
  
Doxygen.appをアプリケーションにドラッグして追加します。

![](https://www.mathkuro.com/wp-content/uploads/2020/04/%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB%E3%81%97%E3%81%9F%E6%99%82%E3%81%AE%E7%94%BB%E9%9D%A2.png)

そして、追加したアプリケーションを開こうとすると、以下のエラーが表示されて開けないかと思います。  
  
`"Doxygen.app"は、開発元を検証できないため開けません。`  
  
その場合は、アイコンを右クリックし、\[**開く**\]を選択します。  
すると、以下の画面が表示されるので、もう一度\[**開く**\]を選択します。

![Doxygen.appを右クリックで開いた際に表示される画面](https://www.mathkuro.com/wp-content/uploads/2020/04/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88-2020-04-13-16.18.24.png)

もちろん自己責任ですが

これで、以下のようなdoxygenのGUIのトップ画面が開くはずです。

![](https://www.mathkuro.com/wp-content/uploads/2020/04/GUI%E3%83%88%E3%83%83%E3%83%95%E3%82%9A%E7%94%BB%E9%9D%A2.png)

doxygenのインストールはこれで完了です。  
  
次回以降は普通にクリックして開くことができます。

## ４．ドキュメント出力方法(HTML形式)

次は実際にドキュメントを出力していきます。  
  
説明は、doxygenのウィザードに沿って進めていきます。

### 4-1 プロジェクト設定

doxygenを立ち上げると以下の画面になると思うので、まずは、プロジェクトの設定を行います。  
  
★の項目は必須項目なので全て入力してください。

![](https://www.mathkuro.com/wp-content/uploads/2020/04/%E3%83%88%E3%82%99%E3%82%AD%E3%83%A5%E3%83%A1%E3%83%B3%E3%83%88%E7%94%9F%E6%88%90%E7%94%BB%E9%9D%A2%EF%BC%91.png)

/\*\* doxygenスタイルのコメント　その１ \*/ /\*! doxygenスタイルのコメント　その２ \*/ //! doxygenスタイルのコメント　その３

「★doxygenの作業ディレクトリ」にはソースコードのルートディレクトリを指定します。  
大抵の場合は「★生成対象のコードのディレクトリ」と同じになるかと思います。  
  
「★ドキュメント出力先ディレクトリ」に設定するパスは権限さえあればどこでも良いです。  
指定したディレクトリの下に”html”や”tex”のディレクトリが作成されます。  
  
必要箇所を入力したら\[**next**\]をクリックします。

### 4-2 モードの設定

次にモードの設定を行います。

![](https://www.mathkuro.com/wp-content/uploads/2020/04/%E3%83%A2%E3%83%BC%E3%83%88%E3%82%99%E3%81%AE%E8%A8%AD%E5%AE%9A-1024x706.png)

基本的にはデフォルトでOKかと思いますが、一応触れておきます。  
  
“ **All Entities** “を選択すると、ドキュメントを作成していないファイル・関数等についても自動的にドキュメントが出力されます。  
(一覧に表示されるレベルですが)  
  
“ **Include cross-referenced source code in the output** “を有効化すると、参照されているコードのリンクが貼られるようになります。  
(例えば、hoge.hxxで宣言してhoge.cxxで定義した関数など)  
  
その下の” **Select programming〜** “は言語の選択です。  
今回はC++なのでデフォルトのままでOKです。  
  
そして、\[**next**\]をクリック。

### 4-3 出力の設定(HTML)

次は出力の設定を行います。  
※ここの設定は見た目だけ問題ですので、趣味の範囲でどうぞ。

![](https://www.mathkuro.com/wp-content/uploads/2020/04/%E5%87%BA%E5%8A%9B%E6%96%B9%E6%B3%95%E3%81%AE%E8%A8%AD%E5%AE%9A-1024x706.png)

今回はHTML形式のドキュメントを出力したいのでHTMLにチェックを入れます。  
  
HTMLの出力形式がいくつかありますが、ドキュメントの左側に目次が表示される” **with navigation panel** “が個人的には見易かったです。  
  
また、 **Change color…** でドキュメントの色を変更できます。

![](https://www.mathkuro.com/wp-content/uploads/2020/04/HTML%E3%81%AE%E8%89%B2%E3%81%AE%E8%A8%AD%E5%AE%9A-1024x702.png)

デフォルトは綺麗な青色です。こんな禍々しくないです。

ドキュメントの出力形式を決めたら\[**next**\]を選択します。

### 4-4 ダイアグラム(クラス図)の出力設定

最後にダイアグラムの設定を行います。

![](https://www.mathkuro.com/wp-content/uploads/2020/04/%E3%82%BF%E3%82%99%E3%82%A4%E3%82%A2%E3%82%AF%E3%82%99%E3%83%A9%E3%83%A0%E3%81%AE%E8%A8%AD%E5%AE%9A-1024x706.png)

- **No diagrams**
- **Use built-in class diagram generator**
- **Use dot tool from the GraphViz package**

の三択です。  
  
**No diagrams** はグラフを出力しません。  
  
**Use built-in class diagram generator** はplant-umlの図を出力してるっぽいです。  
関連するクラスが２つ以上ないと図は出力されません。  
  
**Use dot tool from the GraphViz package** は公式マニュアルでは *“if you use the binary packages for MacOSX this tool is already included”* となっていたのですが、私の環境では動作しませんでした。  
  
グラフを出力するかどうかが決まれば、\[**next**\]をクリックします。

### 4-5 HTML出力実行

下図の\[**Run doxygen**\]をクリックするとドキュメント出力が実行されます。

![](https://www.mathkuro.com/wp-content/uploads/2020/04/RunDoxygen-1024x706.png)

  
出力されたHTMLは以下の感じになります。  
  
トップページ

![](https://www.mathkuro.com/wp-content/uploads/2020/04/HTML_top.png)

  
クラス図

![](https://www.mathkuro.com/wp-content/uploads/2020/04/HTML_class_diagram.png)

  
HTML出力は以上になります。

## ５．その他

個人的なメモなど。

### 5-1 コメントをヘッダに記載するか否か

C++の場合、宣言箇所と定義箇所が分かれて、どちらにdoxygen形式のコメントを記載するべきか迷うかもしれません。  
  
doxygenの動作としては、両方に記載した場合は、両方の内容が出力されてしまいます。  
特別な理由がない場合はどちらかに記載するべきです。  
  
doxygenの動作上、どちらに書いても動作するので、悩みどころではありますが、個人的には.cxxではなく.hxxのファイルに書くべきだと思います。  
  
基本的にドキュメントを後から見るのはコードを知らない人間(もしくは未来の自分)になると思います。  
  
コードを知らない時はヘッダファイルから読んでいくことが多いと思うんですよね。  
  
なので、後からの見やすさとして、ヘッダーに書いておくべきではないかと思います。

### 5-2 VS Codeでの見え方

余談ですが、個人的には、doxygen形式のコメントを書いておくと、VS Codeで関数にカーソルを合わせた際に説明が表示されるのが地味に気に入っています。

![](https://www.mathkuro.com/wp-content/uploads/2020/04/VSCode.png)

せめて人に見せる可能性のあるソースコードには関数・クラスにコメントつけて欲しいところ。

### 5-3 privateなメンバでも出力したい！

doxygenではデフォルトだとprivateなメンバはドキュメント化されません。  
  
privateなメンバもドキュメント化したい露出狂な方におかれましては、以下を有効化してください。

![](https://www.mathkuro.com/wp-content/uploads/2020/04/extract_private-1024x692.png)

**EXTRACT\_PRIVATE** 、まさにと言ったネーミングですね。  

ところで、５章に突入してから気付いたんですが、この記事長すぎやしませんかね。  
自分でも確認のスクロールが面倒なんですよね。  
  
…なんと6187文字もあるみたいです。  
  
長文お付き合いいただきありがとうございました。

## 参考

↓メインに参考にさせてもらったサイト  
[https://qiita.com/wakaba130/items/faa6671bd5c954cb2d02](https://qiita.com/wakaba130/items/faa6671bd5c954cb2d02)  
  
↓各種コマンドの参考  
[https://cercopes-z.com/Doxygen/list-command-dxy.html](https://cercopes-z.com/Doxygen/list-command-dxy.html)

タイトルとURLをコピーしました