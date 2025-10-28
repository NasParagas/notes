---
title: "CMAKE_BUILD_TYPEのUPPER CASEにご用心"
source: "https://qiita.com/KRiver1/items/4b7ad90168dfb4aedde6"
author:
  - "[[KRiver1]]"
published: 2018-05-25
created: 2025-08-26
description: "CMAKE_BUILD_TYPE とは cmakeを用いてビルドするとき、-DCMAKE_BUILD_TYPEを指定すると、（きちんとCMakeLists.txtが書かれているプロジェクトなら）適切なオプションを付けてビルドが行われる。 $ cmake -DCMAKE_B..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

## CMAKE\_BUILD\_TYPE とは

`cmake` を用いてビルドするとき、 `-DCMAKE_BUILD_TYPE` を指定すると、（きちんと `CMakeLists.txt` が書かれているプロジェクトなら）適切なオプションを付けてビルドが行われる。

```bash
$ cmake -DCMAKE_BUILD_TYPE=Release  # -O2 とか -DNDEBUG とかが付く
$ cmake -DCMAKE_BUILD_TYPE=Debug  # -O0 とか -g とかが付く
```

ここによると、値としては `empty`, `Debug`, `Release`, `RelWithDebInfo`, `MinSizeRel` などが許されているらしい。

## 事実1: 値はcase insensitive

```bash
$ cmake -DCMAKE_BUILD_TYPE=Debug  # -g が付く
$ cmake -DCMAKE_BUILD_TYPE=debug  # -g が付く
$ cmake -DCMAKE_BUILD_TYPE=DEBUG  # -g が付く
$ cmake -DCMAKE_BUILD_TYPE=DeBuG  # -g が付く
```

大文字小文字は区別されず、 `Debug` でも `DEBUG` でも通ってしまう。

## 事実2: Invalid valueに対して警告が出ない

```bash
$ cmake -DCMAKE_BUILD_TYPE=Dedug  # typoだが、警告は出ない
```

`cmake` の `-DCMAKE_BUILD_TYPE` は、 `<Option>` が指定されると `CMAKE_C((XX)?)_FLAGS_<OPTION>` に含まれるオプションをコンパイルオプションに追加する。

つまり、

CMakeLists.txt

```cmake
set(CMAKE_CXX_FLAGS "-std=c++14 -Wall")
set(CMAKE_CXX_FLAGS_DEBUG "-g -O0")
set(CMAKE_CXX_FLAGS_RELEASE "-O3 -DNDEBUG")
```

となっていた場合、

```bash
$ cmake  # gcc <target> -std=c++14 -Wall
$ cmake -DCMAKE_BUILD_TYPE=Debug  # gcc <target> -std=c++14 -Wall -g -O0
$ cmake -DCMAKE_BUILD_TYPE=Release  # gcc <target> -std=c++14 -Wall -O3 -DNDEBUG
```

のようにオプションが決まる。

ここで、例えば

```bash
$ cmake -DCMAKE_BUILD_TYPE=Dedug  # ...?
```

のように存在しないオプションを指定した場合、 `cmake` はエラーを出してはくれず、単に

```bash
$ cmake -DCMAKE_BUILD_TYPE=Dedug  # gcc <target> -std=c++14 -Wall
```

と、あたかも

```text
set(CMAKE_CXX_FLAGS_DEDUG "")
```

何も指定しない変数が定義されているかのように振る舞ってくれる。

## 事実3: CMakeLists.txt 内で複雑な処理をする奴がいる

`CMakeLists.txt` 内では単にコンパイル対象を指定するだけでなく、 [cmake language](https://cmake.org/cmake/help/v3.0/manual/cmake-language.7.html) に従って様々な操作を行うことができる。

例えば、 [pybind11](https://github.com/pybind/pybind11) の [/tools/pybind11Tools.cmake](https://github.com/pybind/pybind11/blob/master/tools/pybind11Tools.cmake) の中では、

tools/pybind11Tools.cmake

```cmake
...
  if (NOT MSVC AND NOT ${CMAKE_BUILD_TYPE} MATCHES Debug)
    # Strip unnecessary sections of the binary on Linux/Mac OS
...
```

という操作が行われている。  
`${MSVC}` が `FALSE` で、かつ `${CMAKE_BUILD_TYPE}` が正規表現 `Debug` にマッチしないなら、実行ファイルの `strip` を行うセクションだ。

見ると分かる通り、この部分はcase sensitiveである。 `Debug` と正確に一致するかどうかのみを見ており、 `DEBUG` や `debug` では通らない。

## 結果: エラーも出ないのに、-gも付いているのに、なぜかデバッグシンボルが付かない

```bash
$ cmake -DCMAKE_BUILD_TYPE=DEBUG
```

とすると、

- `cmake`
	- case insensitiveなので、 `-DCMAKE_BUILD_TYPE=Debug` と認識し、エラーや警告は出ない。仮にそう認識しなかったとしてもどちらにせよ `cmake` はエラーや警告を出さない。
- `CMakeLists.txt`
	- 各自が勝手に書いているので、case sensitiveかもしれない。その場合 `-DCMAKE_BUILD_TYPE=Debug` とは区別される。もちろんそんなtypoでエラーを出すような機構は基本的にない。

となって、結局想定外の挙動になってしまう。

## まとめ

`-DCMAKE_BUILD_TYPE` の値は `Debug` など、大文字から始まるcamel case。  
間違えても `cmake` は怒ってはくれないし、 `DEBUG` とか `debug` とか書いててもほとんどの環境で通じてしまうので、問題が発生するまで気づけない（そして問題が発生しても表面には出てこない）。  
十分注意して使用してほしい。

[0](https://qiita.com/KRiver1/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2FKRiver1%2Fitems%2F4b7ad90168dfb4aedde6&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2FKRiver1%2Fitems%2F4b7ad90168dfb4aedde6&realm=qiita)

[26](https://qiita.com/KRiver1/items/4b7ad90168dfb4aedde6/likers)

いいねしたユーザー一覧へ移動

14