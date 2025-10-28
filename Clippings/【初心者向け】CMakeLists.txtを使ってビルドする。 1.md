---
title: "【初心者向け】CMakeLists.txtを使ってビルドする。"
source: "https://qiita.com/hi_to_san/items/490f8320900617db9230"
author:
  - "[[hi_to_san]]"
published: 2023-05-24
created: 2025-10-19
description: "はじめに 仕事でCMakeLists.txtと出会い、画期的で驚きました。 ということで、共有です。 Linux, WSLで簡単にビルドできます。 ただ、CMakeLists.txtの内容を書くのも難しいと思います。 まずはCMakeLists.txtとは・・・、なんての..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiitaにログインして、便利な機能を使ってみませんか？

[ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fhi_to_san%2Fitems%2F490f8320900617db9230&realm=qiita) [新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fhi_to_san%2Fitems%2F490f8320900617db9230&realm=qiita)

この記事は最終更新日から1年以上が経過しています。

## はじめに

仕事でCMakeLists.txtと出会い、画期的で驚きました。  
ということで、共有です。

Linux, WSLで簡単にビルドできます。

ただ、CMakeLists.txtの内容を書くのも難しいと思います。  
まずはCMakeLists.txtとは・・・、なんてのも面倒臭い。

CMakeLists.txtを理解するには、簡潔に2言で  
　・CMakeLists.txtは、CMakeの設定ファイル  
　・CMakeはコンパイラに依存せずビルドを自動化するためのツール  
ぐらいでいいです。

気になる方は私が学習に使った参考サイトへ飛んでください。

## 参考サイト

[かみのメモ](https://kamino.hatenablog.com/entry/cmake_tutorial1)  
　※このサイトを一番読みました。  
[CMakeの使い方](https://qiita.com/shohirose/items/45fb49c6b429e8b204ac)  
[実践C++ 応用講座CMake編](https://theolizer.com/cpp-school3/cpp-school3-1/)

## 目次

使いこなすために簡単な流れを目次として書き出します。

- [まずはビルド](https://qiita.com/hi_to_san/items/%E3%81%BE%E3%81%9A%E3%81%AF%E3%83%93%E3%83%AB%E3%83%89)
- [記述内容について説明](https://qiita.com/hi_to_san/items/%E8%A8%98%E8%BF%B0%E5%86%85%E5%AE%B9%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6%E8%AA%AC%E6%98%8E)
- [ConfigureとGenerate](https://qiita.com/hi_to_san/items/Configure%E3%81%A8Generate)

## まずはビルド

### ファイルの準備

まずは以下のファイルを作ります。

```text
.
├── CMakeLists.txt
└── sampleApp.cpp
```

sampleApp.cppには以下の内容を書きます。

sampleApp.cpp

```c++
#include <iostream>

int main(void)
{
    std::cout << "Hello,World" << std::endl;
}
```

いよいよ、CMakeLists.txtの内容を書きます。

CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.1)
project(sampleApp CXX)
add_executable(sampleApp sampleApp.cpp)
```

これで準備完了です。

### ビルド

実際にビルドしてみます。

```shell-session
mkdir build
cd build
cmake ..
cmake --build .
```

これにて、buildフォルダに実行ファイル(sampleApp)が作られます。

### ファイル実行

```shell-session
$ ./sampleApp 
Hello,World
```

coutで記載したメッセージがコンソールに出力されます。

## 記述内容について説明

CMakeLists.txtに記述した内容について説明していきます。

CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.1)
project(sampleApp CXX)
add_executable(sampleApp sampleApp.cpp)
```

1. cmake\_minimum\_required
	- CMakeのバージョンを指定。
	- 今回は `VERSION 3.1` を指定しています。
2. project
	- プロジェクト名とプロジェクトで使用するプログラム言語を指定。
	- プロジェクト名 `sampleApp` とし、C++を指定しています。
3. add\_executable
	- ビルドする実行ファイル名とそれを構成するソースファイルを指定。
	- 実行ファイル名 `sampleApp` としてます。
	- 構成するソースファイルは `sampleApp.cpp` 一つになります。
	- 複数のソースファイルからビルドする場合は、 `add_executable` の内容を以下にします。
		CMakeLists.txt
		```cmake
		add_executable(sampleApp sampleApp1.cpp sampleApp2.cpp)
		```

## ConfigureとGenerate

この項目ではビルドする際に打ったコマンドについて説明したいと思います。

```shell-session
mkdir build
cd build
cmake ..
cmake --build .
```

CMakeのビルドでは、 **Configure** と **Generate** という2つの作業をします。  
コマンドで説明すると3行目の `cmake ..`になります。

**Configure** は、CMakeLists.txtをCMakeに実行させて **ビルドに必要な情報を収集する作業** をします。  
**Generate** は、Configureで集めた情報を基に **自分の開発環境に合わせたプロジェクトファイルの生成** をします。

3行目の `cmake ..`では、CMakeLists.txtを置いたディレクトリを`..`で指定し、 **Configure** を実施します。  
CMakeLists.txtの記載に問題があれば、ここでエラーが出ます。  
Configure実施後、収集した情報から **Generate** し、プロジェクトファイルを生成します。

このConfigureとGenerateで、いくつかの中間ファイルとプロジェクトファイルが生成されるので、ディレクトリ内が汚れてしまいます。  
そのために1, 2行目で作業ディレクトリを作っています。

4行目の `cmake --build .`について、`.`は作業ディレクトリを指定します。  
作業ディレクトリは、ConfigureとGenerateで生成した中間ファイルとプロジェクトファイルのあるディレクトリになります。  
ここでは `build` ディレクトリになります。

また、裏で `make` コマンドを起動しているので、 `make` でもビルドできます。

## 最後に

以上で終了です。  
ご参考になれば幸いです。

## 関連記事

- 【初心者向け】CMakeLists.txtを使ってビルドする。
- [【初心者向け】CMakeLists.txtを使ってincludeのpathを省略する。](https://qiita.com/hi_to_san/items/00a5e9a75a8876b39492)
- [【初心者向け】CMakeLists.txtを使ってlibraryをリンクする。](https://qiita.com/hi_to_san/items/2b44dd44d3e152594c53)

[0](https://qiita.com/hi_to_san/items/#comments)

コメント一覧へ移動

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fhi_to_san%2Fitems%2F490f8320900617db9230&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fhi_to_san%2Fitems%2F490f8320900617db9230&realm=qiita)

## Qiita Conference 2025 Autumn 11月5日(水)~7日(金)開催！

![](https://cdn.qiita.com/assets/public/official_campaigns/qiita_conference_2025_autumn/image-conference_2025_autumn_ogp-d35d2500cd0420d93f2ed69a8d162d74.png)

Qiita Conferenceは、AI時代のエンジニアに贈るQiita最大規模のテックカンファレンスです！

基調講演ゲスト(敬称略)

piacere、牛尾 剛、Esteban Suarez、和田 卓人、seya、ミノ駆動、市谷 聡啓、からあげ、岩瀬 義昌、まつもとゆきひろ、みのるん and more…

[イベント詳細を見る](https://qiita.com/official-campaigns/conference/2025-autumn)

[7](https://qiita.com/hi_to_san/items/490f8320900617db9230/likers)

いいねしたユーザー一覧へ移動

10