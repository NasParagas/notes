---
title: "C++のライブラリboostの最新版をLinuxサーバーにインストールする方法"
source: "https://qiita.com/king_dog_fun/items/0dc555e847acba89aae7"
author:
  - "[[king_dog_fun]]"
published: 2024-02-23
created: 2025-10-28
description: "はじめに 本記事はC++のライブラリであるboostの最新版をLinuxサーバーに圧縮ファイルからインストールする方法を解説します。最新版が必要でないのであれば、yumやaptによるインストールが最もスマートです。 環境 ・2024/02/18 ・GitHub Cod..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から1年以上が経過しています。

## はじめに

　本記事はC++のライブラリであるboostの最新版をLinuxサーバーに圧縮ファイルからインストールする方法を解説します。最新版が必要でないのであれば、yumやaptによるインストールが最もスマートです。

## 環境

・2024/02/18  
・GitHub Codespace

## boostとは

　boostとはタイトルにもある通り、C++のライブラリです。オープンライブラリであり、非商用/商用に無料で利用できます。  
　コミュニティとしてのboostは、ライブラリを提供しているだけに留まらず、C++のプログラム言語自体に多大な影響を与えています。boostライブラリはC++の標準機能を試験するための試験場としての役割があります。そのためboostライブラリはC++の標準機能では提供されない先鋭的な技術を提供しており、非常に有用なライブラリとして有名です。  
　ChatGPTにそのライブラリの内容を聞けば、以下の通りに回答してくれます。

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2647158/c146c3b7-25a0-4ab5-6cb2-731469f283f4.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2647158%2Fc146c3b7-25a0-4ab5-6cb2-731469f283f4.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=ea81d29e09da784476a5b74b576a780a)

　他にも非常に有用な機能を提供しているため、詳しくはWikipediaに記される内容を参照してください。

## ダウンロード方法

　boostの公式ページに飛び、URLを取得します

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2647158/539b3643-b044-e198-f786-f994e2422939.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2647158%2F539b3643-b044-e198-f786-f994e2422939.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=92483814b1e20dda38aa562ff70ca44c)

　Downloadsにあるいずれかの青い文字からURLを取得してください。  
　私の場合は以下のURLが取得しました。圧縮ファイルのtar.bz2です。

　Linuxサーバーのターミナルで以下のコマンドを実行して下さい。URLの部分には、取得したURLを代入してください。

```text
wget URL
```

## 解凍方法

## bz2をダウンロードした場合

　取得したURLの末尾にbz2と書かれている場合、以下のオプションを使用して解凍してください。xxxx部分は、ダウンロードした圧縮ファイルの名前を正確に入力して下さい。

```text
tar -jxvf xxxx.tar.bz2
```

## gzをダウンロードした場合

取得したURLの末尾にgzと書かれている場合、以下のオプションを使用して解凍してください。xxxx部分は、ダウンロードした圧縮ファイルの名前を正確に入力して下さい。

```text
tar -zxvf xxxx.tar.gz
```

## bootstrapの実行

　解凍したboostのディレクトにcdコマンドを使用して入り、以下のコマンドを実行します。

```text
./bootstrap.sh
```

## boostのビルド

　bootstrapの実行後にboostを使用するためにビルドが必要ですので、以下のコマンドを実行します。リカレントディレクトリは解凍されたboostのディレクトリのままです。  
　 b2はBoost.Buildで管理されたプロジェクトをビルドする実行ファイルです。b2コマンドはprefixを指定することで、ビルドによる生成物の生成ディレクトリを指定できます。prefixのデフォルトは/usr/localが指定されているそうです。

```text
sudo ./b2 install --prefix=/usr/local
```

　以上のコマンドをそのまま実行した場合、boostライブラリとヘッダーはそれぞれ、/usr/local/libと/usr/local/includeにビルドされる。  
　ビルド時間はライブラリの大きさ相応に長くなる。全てのboostライブラリは要らず、特定のライブラリだけを使用したい場合は、bootstrap実行時に –with-libraries のオプションを使用することで解決できます。

## example

　boostライブラリを使用した簡単なコード例をコンパイルし、実行することでboostのインストールに問題がないことを確認します。今回はexample.cppというファイルに次のコードを保存しました。

example.cpp

```c++
#include <iostream>
#include <boost/uuid/uuid.hpp>
#include <boost/uuid/uuid_generators.hpp>
#include <boost/uuid/uuid_io.hpp>

int main() {
    // UUIDを生成する
    boost::uuids::uuid uuid = boost::uuids::random_generator()();

    // UUIDを文字列に変換して表示する
    std::cout << "Generated UUID: " << uuid << std::endl;

    return 0;
}
```

　上記のコードを保存したファイルが存在するディレクトリで以下のコンパイルを実行します。/usr/localとは異なるディレクトリにboostをビルドした際は、そのディレクトリを指定してください。

```text
g++ -o example example.cpp -I /usr/local/include/boost -L /usr/local/lib
```

　無事に実行できればexample実行ファイルが作成されています。実行ファイルを実行すると適当なUUIDが生成され、出力されます。

```text
$ ./example
Generated UUID: c2253419-a003-4431-95cf-fe8db55938ac
```

## 参考

[0](https://qiita.com/king_dog_fun/items/#comments)

コメント一覧へ移動

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fking_dog_fun%2Fitems%2F0dc555e847acba89aae7&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fking_dog_fun%2Fitems%2F0dc555e847acba89aae7&realm=qiita)

## Qiita Conference 2025 Autumn 11月5日(水)~7日(金)開催！

![](https://cdn.qiita.com/assets/public/official_campaigns/qiita_conference_2025_autumn/image-conference_2025_autumn_ogp-d35d2500cd0420d93f2ed69a8d162d74.png)

Qiita Conferenceは、AI時代のエンジニアに贈るQiita最大規模のテックカンファレンスです！

基調講演ゲスト(敬称略)

piacere、牛尾 剛、Esteban Suarez、和田 卓人、seya、ミノ駆動、市谷 聡啓、からあげ、岩瀬 義昌、まつもとゆきひろ、みのるん and more…

[イベント詳細を見る](https://qiita.com/official-campaigns/conference/2025-autumn)

[5](https://qiita.com/king_dog_fun/items/0dc555e847acba89aae7/likers)

いいねしたユーザー一覧へ移動

3