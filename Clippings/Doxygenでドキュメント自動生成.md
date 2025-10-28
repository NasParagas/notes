---
title: "Doxygenでドキュメント自動生成"
source: "https://zenn.dev/karaage0703/articles/50c7445bc65350"
author:
  - "[[Zenn]]"
published: 2023-10-22
created: 2025-08-26
description:
tags:
  - "clippings"
---
4[idea](https://zenn.dev/tech-or-idea)

## 仕様書を明日までに100ページかけるかな？

「出来らあっ！」

といってしまったときに使うのがDoxygenです（嘘です）。

ソースコードから自動でドキュメントを生成してくれます。たまに色々な事情で使いたくなるのですが、使い方いつも忘れてしまうのでメモしておきます。

## セットアップ

### Mac

[Homebrew](https://brew.sh/) をインストールした前提です。以下コマンドでOKです。

```shell
$ brew install doxygen
$ brew install graphviz
```

### Linux/Windows(WSL2)

Linux（というかUbuntu）だと以下です。

```shell
$ sudo apt update
$ sudo apt install -y graphviz
$ sudo apt install -y doxygen
```

## 使い方

ドキュメントを生成したいソースコードに移動して、Doxygenのセットアップファイルを新規作成します。

```shell
$ doxygen -g Doxyfile
```

参考リンクのサイトとかを参考に、 [いい感じに修正したDoxyfileをGistにアップロードしました](https://gist.github.com/karaage0703/31d7be40796ad58f205120d707daacaf) 。そちらを使う場合は、上記コマンドの代わりに以下実行してください。

```shell
$ wget https://gist.githubusercontent.com/karaage0703/31d7be40796ad58f205120d707daacaf/raw/e31dffbbf326790e47b95e64aa340371dc240734/Doxyfile
```

設定ファイルを作成したら、ドキュメントを自動生成しましょう。Doxyfileがあるディレクトリで以下実行してください。

```shell
$ doxygen Doxyfile
```

## Doxygen使用例

[Open Interpreter](https://github.com/KillianLucas/open-interpreter) で試してみた例です。それっぽいドキュメントが生成されます。

![](https://storage.googleapis.com/zenn-user-upload/555db75c317a-20231020.png)

![](https://storage.googleapis.com/zenn-user-upload/0ee914e96d9d-20231020.png)

![](https://storage.googleapis.com/zenn-user-upload/f14c72a086bb-20231020.png)

![](https://storage.googleapis.com/zenn-user-upload/a60c3552e824-20231020.png)

## まとめ

Doxygenの使い方です。ちなみに、Doxygenで作ったドキュメント、あんまり活用できた記憶はないです。ソースコード読めると、あんまりソースコードみるのと変わらない気がしますし、逆にソースコード読めない人は、Doxygenで自動生成したドキュメントを読んでも「いや、これがあっても」みたいになる気がします。

一番の活用例は、ドキュメント作りの時間稼ぎだったり？するかもしれません。

いい感じのドキュメントの自動生成は、そのうちAIがいい感じにやってくれるようになるんじゃないかなと思います。

## 参考リンク

4

### Discussion

![](https://static.zenn.studio/images/drawing/discussion.png)

ログインするとコメントできます

4