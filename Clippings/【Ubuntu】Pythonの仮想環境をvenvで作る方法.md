---
title: "【Ubuntu】Pythonの仮想環境をvenvで作る方法"
source: "https://laplace-daemon.com/create-virtualenv-by-venv/"
author:
  - "[[ラプラス]]"
published: 2019-07-07
created: 2025-09-23
description: "この記事ではvenvを使ってPythonの仮装環境を作る方法、作成した仮想環境を利用する方法を解説しています。 最初に仮想環境自体の解説をしていますが、すでにご存知の方は「仮想環境作成手順」からお読..."
tags:
  - "clippings"
---
![](https://laplace-daemon.com/wp-content/uploads/2020/02/pc.jpg)

この記事ではvenvを使ってPythonの仮装環境を作る方法、作成した仮想環境を利用する方法を解説しています。

最初に仮想環境自体の解説をしていますが、すでにご存知の方は「仮想環境作成手順」からお読みください。

## 仮想環境とは

1つのコンピュータの中には普通一つの環境しかありません。そのため何かしらアプリケーションをインストールしたら、そのコンピュータ内のすべての箇所においてそのアプリケーションは利用可能な状態になります。

![](https://laplace-daemon.com/wp-content/uploads/2023/02/Screen-Shot-2023-02-19-at-19.24.37.png)

しかし仮想的に別の環境を作り上げれば、1つのマシンの中に複数の環境を作り上げることが出来ます。以下の図を例にすると、環境Aでインストールしたアプリケーションは、環境Bではインストールされておらず使えない状態にすることも可能です。

![](https://laplace-daemon.com/wp-content/uploads/2023/02/Screen-Shot-2023-02-19-at-19.27.33.png)

仮想環境のイメージが分かりやすいように環境Aと環境Bのように書きましたが、実際の構成は以下の図ような形になります。

![](https://laplace-daemon.com/wp-content/uploads/2024/06/%E4%BB%AE%E6%83%B3%E7%92%B0%E5%A2%83_image.png)

コンピュータ自体の環境はグローバル環境と呼ばれます。グローバル環境内に仮想環境があるのですが、仮想環境に入った時にはグローバル環境のライブラリを利用することはできません。また仮想環境を抜けてグローバル環境にいる状態では仮想環境内のライブラリを利用することはできません。

なぜならそれぞれの環境で利用できるライブラリは別々の場所に保存されていて、どの環境が有効になっているかでライブラリを探しに行く場所が変わるからです。

![](https://laplace-daemon.com/wp-content/uploads/2024/06/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88-2024-06-15-21.58.34.png)

Pythonの仮想環境の場合はpythonライブラリをそれぞれの環境で独立して持つことになります。あくまでpython関係のものを別環境として扱えるようにするだけなので、aptで入れたOSのパッケージなどPythonに関係ないものであればどの環境でも変わりなく利用できます。

仮想環境は開発を進める上でとても便利なもので、venvはpython仮想環境の操作を容易にしてくれる優れたツールです。ぜひこれからする説明を参考にしていただき使い方をマスターしてください。

## venvとは

「仮想環境とは」の章でも少し説明しましたが、改めてvenvとは何かについて説明します。

venvはpython用の仮想環境操作をサポートしてくれるツール です。

venvを使うことによって、python用仮想環境を作成することが可能になります。これにより特定のpython用ライブラリが入った環境と入っていない環境、バージョンが異なるPythonがインストールされた環境など、複数のPython実行環境を一つのコンピュータの中に構築することが出来ます。

仮想環境作成機能を持ったモジュールはvenv以外だとpyenvが有名ですが、pyenvはPythonバージョン3.6から非推奨になっているので今から仮想環境を使うのならvenvを使う方が良いです。参考サイト： [venv — 仮想環境の作成](https://docs.python.org/ja/3/library/venv.html)

## 仮想環境作成手順

それではvenvを使ってPythonの仮想環境を作成するまでの手順を説明します。複雑なコマンドの場合、コマンドの下に補足説明をしているので参考にしてください。

1\. python3-venvをOSにインストール

```bash
$ sudo apt install python3-venv
```

2\. venvを使って仮想環境を作成

```bash
$ python3 -m venv virtual_environment_name
```

- “-m"はそのあとに記載されたモジュール（今回で言うとvenv）を実行するというオプション
- virtual\_environment\_nameの個所で仮想環境名を設定可能
- ちなみに仮想環境は一つのマシン内に何個でも作れます。

3\. 仮想環境の有効化

```bash
$ source virtual_environment_name/bin/activate
```

- 仮想環境名/bin/activateファイルを実行することにより仮想環境が有効化される
- sourceは対象ファイルに書かれたコマンドを実行するコマンド。詳細は以下の記事をご参照ください。  
	[【 source 】コマンド／【. 】コマンド――シェルの設定を即座に反映させる](https://www.atmarkit.co.jp/ait/articles/1712/21/news015.html)

4\. 仮想環境の無効化

```bash
$ deactivate
```

- このコマンドはカレントディレクトリがどこでも効きます。

仮想環境作成・有効化・無効化手順は以上です。

ではこれからは仮想環境が期待通りに作成されているか確認してみましょう。

### 仮想環境の確認

1\. Pythonで利用するライブラリをインストールするためにはpipというパッケージが必要です。以下のコマンドを実行してインストールします。（venvはpipを使ってインストールしたPython用ライブラリを仮想環境ごとに分けて管理します。）

```bash
$ sudo apt install python3-pip
```

2\. 仮想環境に入らない（無効化した）状態で下記の「pip3 list」コマンドを実行します。実行すると今いる環境で利用できるPython用ライブラリが表示されます。

```bash
$ pip3 list
DEPRECATION: The default format will switch to columns in the future.
You can use --format=(legacy|columns) (or define a format=(legacy|columns) in your pip.conf under the [list] section) to disable this warning.
apturl (0.5.2)
asn1crypto (0.24.0)
Brlapi (0.6.6)
certifi (2018.1.18)
chardet (3.0.4)
command-not-found (0.3)
cryptography (2.1.4)
cupshelpers (1.0)
defer (1.0.6)
distro-info (0.18ubuntu0.18.04.1)
httplib2 (0.9.2)
idna (2.6)
keyring (10.6.0)
keyrings.alt (3.0)
language-selector (0.1)
launchpadlib (1.10.6)
lazr.restfulclient (0.13.5)
lazr.uri (1.0.3)
louis (3.5.0)
macaroonbakery (1.1.3)
Mako (1.0.7)
MarkupSafe (1.0)
netifaces (0.10.4)
oauth (1.0.1)
olefile (0.45.1)
〜〜省略〜〜
xkit (0.0.0)
zope.interface (4.3.2)
```

3\. 今度は先ほど作成した仮想環境を有効化してから利用できるライブラリを確認してみます。  
　(venvで作った仮想環境ならpipだけでpip3を意味するので"pip list"でOKです。)

```bash
# 仮想環境有効化
$ source new_environment_name/bin/activate

# 仮想環境内に存在するPython用パッケージのリストを表示
$ pip list
DEPRECATION: The default format will switch to columns in the future.
You can use --format=(legacy|columns) (or define a format=(legacy|columns) in your pip.conf under the [list] section) to disable this warning.
pip (9.0.1)
pkg-resources (0.0.0)
setuptools (39.0.1)
```

仮想環境外で実行した時よりかなり少ないです。インストールされているPython用ライブラリの数に違いがあることが分かります。

最後に仮想環境でPython用ライブラリをインストールしたら本体の環境はどうなるのか確認してみましょう。

4\. 仮想環境内で下記コマンドを実行してnumpyというpython用ライブラリをインストールします。

```bash
$ pip install numpy
```

5\. 仮想環境内でpip listを実行し、numpyがインストールされていることを確認します。

```bash
$ pip list | grep numpy
numpy (1.16.4)
$
```

numpyが仮想環境にインストールされていることが分かります。

6\. 今度は仮想環境を無効化してから、pip3 listを実行します。

```bash
$ deactivate
$ pip3 list | grep numpy
$
```

こちらにはnumpyが入ってないことが分かります。

## venvのベストプラクティス

venvの使い方はわかったと思いますが、実際にどこにどんな名前で作れば良いのかが気になるかと思います。

一つのファイルの実行のためや検証のためであればどこにどんな名称の仮想環境を作成しても良いのですが、gitなどでpythonプロジェクトを管理している場合にはベストプラクティスが存在するために紹介しておきます。

それはプロジェクトのルートディレクトリに\`.venv\`という名称で作成することです。

```bash
cd Pythonプロジェクト
python3 -m venv --prompt . .venv
```

初めて登場した–promptオプションについて説明します。

–promptは仮想環境を有効化している時にどんな名称を表示させるのかを指定できるコマンドです。–promptを指定せずに単純に.venvという名称で仮想環境を作成すると、仮想環境に入った時の表示が".venv"になってしまいます。そのため複数のpython仮想環境が存在するコンピュータでは、フォルダ移動したときなどにpython仮想環境にいるのかが分かりにくいです。

```bash
root@4ba2c5787088:~# cd python_projectA/
root@4ba2c5787088:~/python_projectA# python -m venv .venv
root@4ba2c5787088:~/python_projectA# source .venv/bin/activate
(.venv) root@4ba2c5787088:~/python_projectA#
```

–promptオプションを利用することで現在有効化されている仮想環境がわかりやすくなります。先ほど消化した例では.(ドット)を指定していましたが、.を指定するとカレントディレクトリの名称が指定されることになります。よって先ほどの例だと以下のように表示が変わります。

```bash
root@4ba2c5787088:~/python_projectA# python -m venv --prompt . .venv
root@4ba2c5787088:~/python_projectA# source .venv/bin/activate
(python_projectA) root@4ba2c5787088:~/python_projectA#
```

これで.venvという仮想環境用のフォルダであることがわかりやすく、そしてどの仮想環境が有効化されているのか分かりやすい状態になりました。

一般的にこの作り方が良いとされているためこの構成にしているpythonプロジェクトは多く、その結果新メンバーでも理解しやすいプロジェクト構成になります。

ここから先は少しgitに関係した発展系なので、gitについての知識がある方は参考にしてください。

仮想環境として作成した.venvフォルダにはサイズが大きいpythonライブラリや一時ファイルが入っており、実装内容自体にも当たらないためgitで管理すべきではありません。そこで.venvをgit管理外にするために.gitignoreファイルに.venv/を追記します。

またどの環境でも同じバージョンのpythonライブラリをインストールできるように仮想環境にインストールしているpythonライブラリをファイルで吐き出しておくと良いです。仮想環境を有効化して以下のコマンドを実行すると現在有効化されている仮想環境にあるpythonパッケージの一覧をバージョン情報込みでファイルに残せます。

```bash
pip freeze > requirements.txt
```

requirements.txtはプロジェクトのルートディレクトリに置かれて管理されることが一般的なため、上記のコマンドはプロジェクトのルートディレクトリで実行しましょう。

別の方がリポジトリをクローンした時には、同様に仮想環境を作成・有効化してもらった上で以下のコマンドを実行することで、ファイル出力した仮想環境と同じpythonライブラリが入った環境を再現することができます。

```bash
pip install -r requirements.txt
```

## まとめ

以上でvenvの説明は終了です。一つのマシンで複数のPython実行環境を構築できるvenvを使い、本体の環境はクリーンな状態を保ちながら開発を行っていきましょう。

## 参考サイト

- [venv — 仮想環境の作成](https://docs.python.org/ja/3/library/venv.html)
- [【 source 】コマンド／【. 】コマンド――シェルの設定を即座に反映させる](https://www.atmarkit.co.jp/ait/articles/1712/21/news015.html)

## ふるさと納税に関するお知らせ

ふるさと納税の指定基準の見直しが行われ、ふるさと納税仲介サイトによるポイント付与が2025年10月から禁止になります。

参考： [総務省 | ふるさと納税の指定基準の見直し等](https://www.soumu.go.jp/menu_news/s-news/01zeimu04_02000144.html)

9月末までの間はふるさと納税によるポイントが付与されるので、今のうちに見ておくと良さそうです。

＞＞ [amazon ふるさと納税](https://amzn.to/47yKDYR) （9/30まで最大20%ポイント還元キャンペーン実施中）

＞＞ [楽天 ふるさと納税](https://a.r10.to/he4O1t)