---
title: "git管理下のファイルのみをtree表示する"
source: "https://masawada.hatenablog.jp/entry/2021/02/24/123000"
author:
  - "[[新米 (id:masawada)]]"
published: 2021-02-24
created: 2025-07-04
description: "2023-06-28 追記: tree が --gitignore オプションに対応して tree --gitignore で同様のことができるようになりました。 mama.indstate.edu treeはみなさんご存知便利なコマンドで、cwd以下のファイルをツリー表示するもの。だいたいの環境にはデフォルトで入っていないので、 brew などで入れる必要がある。 $ tree . ├── hello.txt ├── test │ └── hoge.txt └── world.txt 1 directory, 3 files 大抵のソフトウェアのプロジェクトでは node_modules の…"
tags:
  - "clippings"
---
【注意】この記事は1年以上前に書かれた記事です。最新の情報ではない可能性があります。

2023-06-28 追記: tree が `--gitignore` オプションに対応して `tree --gitignore` で同様のことができるようになりました。

[mama.indstate.edu](http://mama.indstate.edu/users/ice/tree/changes.html)

---

[tree](http://mama.indstate.edu/users/ice/tree/) はみなさんご存知便利なコマンドで、cwd以下のファイルをツリー表示するもの。だいたいの環境にはデフォルトで入っていないので、 `brew` などで入れる必要がある。

```
$ tree
.
├── hello.txt
├── test
│   └── hoge.txt
└── world.txt

1 directory, 3 files
```

大抵のソフトウェアのプロジェクトでは `node_modules` のような依存ライブラリ群をプロジェクトのディレクトリに内包しているのが常で、gitで管理している場合は `.gitignore` で無視することがほとんどだと思う。このような環境下でtreeを使うとこれらのファイルが一気に出力されてノイズとなることがしばしば。初めて参加するプロジェクトのコードを読む際はどこから読むかあたりをつけるのにtreeコマンドが便利そうだが、正直このままでは使いづらい。

treeには `--fromfile` オプションがあり、パスのリストをファイルから読み取ってツリー表示することができる。更に `--fromfile=.` とすると、標準入力からパスのリストを受け取ることができる。 `git ls-files | tree --fromfile=.` とすることで、git管理しているファイルのみ、つまり依存などが無視されて純粋にプロジェクトそのもののファイルリストのみがツリー表示されるようになる。例えば以下は以前書いたbt2usbhidのプロジェクトルートでの出力。`.gitignore` で指定されている `bin/bt2usbhid` はファイルとしては存在するが、ツリー表示には出力されていないことが分かる。

```
$ ls bin
bt2usbhid  init_bt2usbhid
$ git ls-files | tree --fromfile=.
.
├── .gitignore
├── LICENSE
├── Makefile
├── README.md
├── bin
│   └── init_bt2usbhid
├── config
│   ├── 99-bluetooth.rules.example
│   ├── bt2usbhid_keyboard.service
│   ├── bt2usbhid_mouse.service
│   └── init_bt2usbhid.service
└── src
    ├── keyboard.c
    ├── keyboard.h
    ├── main.c
    ├── mouse.c
    └── mouse.h

3 directories, 14 files
```

[github.com](https://github.com/masawada/bt2usbhid)

以下のシェルスクリプトを `$PATH` のいずれかのディレクトリの中に `git-tree` という名前で実行権限をつけて入れておくと、 `git tree` サブコマンドでこれを呼び出すことができる。

```sh
#!/bin/bash

set -e

if ! type tree > /dev/null 2>&1; then
  echo 'command not found: tree'
  exit 1;
fi

git ls-files | tree --fromfile=. "$@"
```

インターネットで探したところ、これと同等の機能をGoやPythonで実装したツールが出てきたが、tree自体に実現する機能があるのでtreeを使う方が柔軟性があって良い。treeにはまだ面白いオプションがいくつかある。例えば `-J` オプションでツリーをJSONに変換することができる。上記のスクリプトはそのままオプションを受け取れるようにしているので、以下のようにJSONを出力することができる。

```
$ git tree -J
[{"type":"directory","name": ".","contents":[
    {"type":"file","name":".gitignore"},
    {"type":"file","name":"LICENSE"},
    {"type":"file","name":"Makefile"},
    {"type":"file","name":"README.md"},
    {"type":"directory","name":"bin","contents":[
      {"type":"file","name":"init_bt2usbhid"}
    ]},
    {"type":"directory","name":"config","contents":[
      {"type":"file","name":"99-bluetooth.rules.example"},
      {"type":"file","name":"bt2usbhid_keyboard.service"},
      {"type":"file","name":"bt2usbhid_mouse.service"},
      {"type":"file","name":"init_bt2usbhid.service"}
    ]},
    {"type":"directory","name":"src","contents":[
      {"type":"file","name":"keyboard.c"},
      {"type":"file","name":"keyboard.h"},
      {"type":"file","name":"main.c"},
      {"type":"file","name":"mouse.c"},
      {"type":"file","name":"mouse.h"}
    ]}
  ]},
  {"type":"report","directories":3,"files":14}
]
```

これでファイルリストをプログラムやjqなどのコマンドに食わせやすくなる。同様に `-X` オプションでXMLを出力することもできるし、 `-H .` とすればHTMLを出力することもできる。もっともこれらが役立つ場面があるかというと滅多になさそうではあるが、覚えておくと役に立つことがあるかもしれない。

---

追記(2021-02-24): 指摘いただいて、 `git-tree` のスクリプトの `$@` を `"$@"` に変更しました。引数にスペースが含まれている場合に分割されてしまうためです

[« gitでコミットする前のファイルを選択して…](https://masawada.hatenablog.jp/entry/2021/03/03/123000) [自作ラップトップ: ThinkPad X270を組み立… »](https://masawada.hatenablog.jp/entry/2021/02/17/093000)