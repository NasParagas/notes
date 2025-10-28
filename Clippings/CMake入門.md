---
title: "CMake入門"
source: "https://qiita.com/yoshiyasu1111/items/02bd5ee1b07eaa45b335"
author:
  - "[[yoshiyasu1111]]"
published: 2022-12-08
created: 2025-10-19
description: "CMakeLists.txt CMakeプロジェクトはCMakeLists.txtにより動作が設定されます。ソースツリーのルートに少なくとも1つ用意する必要があります。必須の設定項目はcmake_minimum_requiredとprojectの2つです。 cmake_..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiita Conference 2025 Autumn

プロダクトづくりから、システムづくりへ

[特設サイトで詳細をチェック](https://qiita.com/official-campaigns/conference/2025-autumn)

この記事は最終更新日から1年以上が経過しています。

## CMakeLists.txt

CMakeプロジェクトはCMakeLists.txtにより動作が設定されます。ソースツリーのルートに少なくとも1つ用意する必要があります。必須の設定項目は `cmake_minimum_required` と `project` の2つです。

## cmake\_minimum\_required()

CMakeの最小バージョンを指定します。システムが正しいCMakeのバージョンを持っているかどうかをチェックします

もし指定したバージョンより低いバージョンしかインストールされていなければエラーが出ます。

```text
$ cmake -S . -B build
CMake Error at CMakeLists.txt:1 (cmake_minimum_required):
  CMake 3.26 or higher is required.  You are running version 3.25.1

-- Configuring incomplete, errors occurred!
See also "/work/work/build/CMakeFiles/CMakeOutput.log".
```

## project()

言語とメタデータの設定をします。は必須項目で、その他は任意です。2つの形式のうちどちらかを使用します。

```text
project(<PROJECT_NAME> [<language-name>])
```

VERSIONを設定すると以下の変数が利用できるようになります。

- PROJECT\_VERSION
- PROJECT\_VERSION\_MAJOR
- PROJECT\_VERSION\_MINOR
- PROJECT\_VERSION\_PATCH
- PROJECT\_VERSION\_TWEAK

## message()

指定されたメッセージの文字列をログに記録します。

## 変数

変数はパッケージの設定や、ソースファイルに渡して最終的な実行ファイルで利用することができます。

## 変数の利用

`${<変数>}` のように変数は’${'と'}'で囲えば利用することができます。

たとえば以下のようなCMakeLists.txtを用意して `cmake -S . -B build` コマンドを実行すると変数の値を表示することができます。

```bash
$ cmake -S . -B build
-- The CXX compiler identification is GNU 11.3.0
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
プロジェクトの名前： hello
プロジェクトの名前： hello
プロジェクトのソースディレクトリ： /work/work
プロジェクトのビルドディレクトリ： /work/work/build
プロジェクトのバージョン： 0.1.0.0
プロジェクトのバージョン： 0.1.0.0
プロジェクトのメジャーバージョン： 0
プロジェクトのマイナーバージョン： 1
プロジェクトのパッチーバージョン： 0
プロジェクトのツイークバージョン： 0
プロジェクトの説明： sample hello project
-- Configuring done
-- Generating done
-- Build files have been written to: /work/work/build
```

## file()

ファイルシステムへのアクセスを必要とするファイル操作やパス操作を実行します。

ファイル名: <filename>に内容:<content>を書き込みます。

```text
file({WRITE | APPEND} <filename> <content>)
```

ファイル名:<filename>内容を文字列として変数:<variable>に読み込みます。

```text
file(STRINGS <filename> <variable>)
```

```bash
$ cmake -S . -B build
I write a contents.
$ cat hello.txt 
I write a contents.
```

## add\_executable()

指定されたソースファイルを使用して、プロジェクトに実行ファイルを追加します。

```text
add_executable(<name>
    [EXCLUDE_FROM_ALL]
    [source1] [source2 ...]
)
```

\[source\]で指定したソースファイルからビルドする実行形式ターゲット <name> を追加します。<name>はプロジェクト内で一意でなければならない。

`EXCLUE_FROM_ALL` にtrue値を設定すると、そのターゲットが含まれるディレクトリと、祖先のすべてのターゲットから除外されます。

### 実行例

次のようなCMakeLists.txtとhello.cppを用意します。

```bash
$ tree
.
├── CMakeLists.txt
└── hello.cpp
```

hello.cpp

```cpp
#include <iostream>

int main(void)
{
    std::cout << "Hello World!" << std::endl;

    return 0;
}
```

プロジェクトのビルドシステムの生成とビルドを実行します。

```bash
$ cmake -S . -B build
$ cmake --build build
```

./buildディレクトリに `hello` が生成されています。

```bash
$ ./build/hello 
Hello World!
```

## add\_library()

指定されたソースファイルを使用して、プロジェクトにライブラリを追加します。

### 静的ライブラリ

```text
add_library(<name> STATIC [source ...])
```

静的ライブラリ<name>をソースコード\[source...\]から生成します。

### 実行例〜静的ライブラリ〜

次のようなCMakeLists.txtとhello.cppを用意します。

```bash
$ tree
.
├── CMakeLists.txt
└── hello.cpp
```

hello.cpp

```cpp
#include <iostream>

int main(void)
{
    std::cout << "Hello World!" << std::endl;

    return 0;
}
```

プロジェクトのビルドシステムの生成とビルドを実行します。

```bash
$ cmake -S . -B build
-- The CXX compiler identification is GNU 11.3.0
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /work/build
```

```bash
$ cmake --build build
[ 50%] Building CXX object CMakeFiles/hello.dir/hello.cpp.o
[100%] Linking CXX static library libhello.a
[100%] Built target hello
```

./buildディレクトリにライブラリlibhello.aが生成されています。

```bash
$ ls -lh ./build/libhello.a 
-rw-r--r-- 1 gccuser gccuser 2.9K Dec  8 08:30 ./build/libhello.a
```

### 共有ライブラリ

共有ライブラリ<name>をソースコード\[source...\]から生成します。

### 実行例〜共有ライブラリ〜

次のようなCMakeLists.txtとhello.cppを用意します。

```bash
$ tree
.
├── CMakeLists.txt
└── hello.cpp
```

hello.cpp

```cpp
#include <iostream>

int main(void)
{
    std::cout << "Hello World!" << std::endl;

    return 0;
}
```

プロジェクトのビルドシステムの生成とビルドを実行します。

```bash
$ cmake -S . -B build
-- The CXX compiler identification is GNU 11.3.0
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /work/build
```

./buildディレクトリにライブラリlibhello.soが生成されています。

```bash
$ ls -lh ./build/libhello.so 
-rwxr-xr-x 1 gccuser gccuser 16K Dec  8 08:54 ./build/libhello.so
```

[0](https://qiita.com/yoshiyasu1111/items/#comments)

コメント一覧へ移動

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fyoshiyasu1111%2Fitems%2F02bd5ee1b07eaa45b335&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fyoshiyasu1111%2Fitems%2F02bd5ee1b07eaa45b335&realm=qiita)

## Qiita Conference 2025 Autumn 11月5日(水)~7日(金)開催！

![](https://cdn.qiita.com/assets/public/official_campaigns/qiita_conference_2025_autumn/image-conference_2025_autumn_ogp-d35d2500cd0420d93f2ed69a8d162d74.png)

Qiita Conferenceは、AI時代のエンジニアに贈るQiita最大規模のテックカンファレンスです！

基調講演ゲスト(敬称略)

piacere、牛尾 剛、Esteban Suarez、和田 卓人、seya、ミノ駆動、市谷 聡啓、からあげ、岩瀬 義昌、まつもとゆきひろ、みのるん and more…

[イベント詳細を見る](https://qiita.com/official-campaigns/conference/2025-autumn)

[2](https://qiita.com/yoshiyasu1111/items/02bd5ee1b07eaa45b335/likers)

いいねしたユーザー一覧へ移動

2