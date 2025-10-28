---
title: "Ubuntu版VSCodeでPlantuml"
source: "https://qiita.com/heat_exchange/items/e8b31c921d316d388aee"
author:
  - "[[heat_exchange]]"
published: 2018-11-30
created: 2025-08-27
description: "使っていなかったノートPCにUbuntuを入れてみた。Windowsと異なる部分が多いため、これからなれて行きたいと考えている。 ノートPCの用途としては、軽いプログラミングの他に、ドキュメントの作成・編集(Qiita等を含む）を想定している。 これらの用途に便利な環境とし..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

[@heat\_exchange](https://qiita.com/heat_exchange)

投稿日

使っていなかったノートPCにUbuntuを入れてみた。Windowsと異なる部分が多いため、これからなれて行きたいと考えている。  
ノートPCの用途としては、軽いプログラミングの他に、ドキュメントの作成・編集(Qiita等を含む）を想定している。  
これらの用途に便利な環境として、VSCodeが挙げられる。VSCodeならば、プログラミングのほか、plantumlを使ったドキュメントの作成もできる。そこで、

## VSCodeのインストール

ターミナルで以下のコマンドを打つ。

```text
sudo apt -y install code
```

これで、VSCodeがインストールできる。

## javaのインストール

PlantUMLはJavaベースで動いているようだ。そこで、Javaもインストールする必要がある。

```text
sudo apt -y install default-jre
```

## Graphvizのインストール

次に、グラフ構造を描画する [Graphviz](https://ja.wikipedia.org/wiki/Graphviz) をインストールする。VSCode上でもインストールできるが、以下のようなエラーが発生し、plantumlが動作しない。

```text
Dot Executable: /opt/local/bin/dot
File does not exist
Cannnot find Graphviz. You should try

@startuml
testdot
@enduml

or

java -jar plantuml.jar -testdot
```

そのため、きちんとaptからインストールしてあげる必要がある。

```text
sudo apt -y install graphviz
```

あとはWindowsと同様にVSCode上でplantumlとgraphvizをインストールすれば良い。  
具体的な方法は、以下の記事を参照してほしい。

## 参考文献

[Ubuntu / DebianにコマンドでVSCode (Visual Studio Code) をインストールする方法](https://www.karelie.net/ubuntu-debian-command-install-vscode/)  
[apt-getでJavaをインストールしよう(Ubuntu)](https://www.linuxmania.jp/apt-install-java.html)  
[UbuntuのVimでGraphvizをプレビューする](https://qiita.com/mitsugu/items/96d4ce5878bee918102a)

[0](https://qiita.com/heat_exchange/items/#comments)

コメント一覧へ移動

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fheat_exchange%2Fitems%2Fe8b31c921d316d388aee&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fheat_exchange%2Fitems%2Fe8b31c921d316d388aee&realm=qiita)

[3](https://qiita.com/heat_exchange/items/e8b31c921d316d388aee/likers)

いいねしたユーザー一覧へ移動

3