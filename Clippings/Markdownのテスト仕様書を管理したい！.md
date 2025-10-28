---
title: "Markdownのテスト仕様書を管理したい！"
source: "https://qiita.com/__xxWaKoUxx__/items/5909704f0e80b1e25d51"
author:
  - "[[__xxWaKoUxx__]]"
published: 2020-12-08
created: 2025-09-02
description: "忙しい方へ おそらく、大抵のソフトウェアの仕様書はExcelで管理することが多いと思います。しかし、バージョン管理や差分がGitHubと比べてイマイチだと思っていました。 そのような問題を解決するために、まずは（個人的に）取り組みやすいテスト仕様書をMarkdown+Gi..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から3年以上が経過しています。

[@\_\_xxWaKoUxx\_\_ (WaKoU)](https://qiita.com/__xxWaKoUxx__)

投稿日

## 忙しい方へ

おそらく、大抵のソフトウェアの仕様書はExcelで管理することが多いと思います。しかし、バージョン管理や差分がGitHubと比べてイマイチだと思っていました。

そのような問題を解決するために、まずは（個人的に）取り組みやすいテスト仕様書をMarkdown+GitHubで管理するという試みをしてみました。バージョン管理や差分管理は確かにやりやすくなりました。ついでに、一覧で眺めたり集計したいときに変換するpythonスクリプトも作成しました。

変換ソースコードは [GitHub](https://github.com/k-watanb/md_test_case_to_excel) で公開しています。

[![image-20201130175253467.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/285702/94df0c02-61b0-5996-e3a8-271cde3ec54f.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F285702%2F94df0c02-61b0-5996-e3a8-271cde3ec54f.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=b12b10f5e5370980276720d57c2394d1)

## 不要なExcelを撲滅したい

未だにソフトウェアの仕様書にExcelを管理する場合が多いと思います。私はこのExcel管理に嫌気がさしていました。

- **バイナリデータのため、GitHubなどのバージョン管理ツールとの相性が悪い。** "〇〇\_最終版.xlsx", "〇〇\_最終版\_new.xlsx", "〇〇\_最終版（これを使う）.xlsx"のようなファイルが同一ディレクトリ内にあると禿げそうになります。
- 誰がいつどのように編集したのか、という差分管理もイマイチ。
- ファイルが大きくなると、オープンや保存にも異様に時間がかかる。
- そもそも仕様書がどこにあるかが、誰かに聞かないとわからない

等、挙げればキリがありません。ソースコードはGitHubで管理しているのだから、同じ場所・同じツールで仕様書を統一管理したいという思いが強くなっていきました。

とはいってもいきなり全部を置き換えるのは難しいため、まずは個人的に取り組みやすかったテスト仕様書Excelの撲滅に取り組みました。

## Markdown+GitHubによるテスト仕様書管理

Markdown+GitHubならば上記の悩みをほぼ解決してくれましたが、Markdownはテキストベースのため、一覧表示ができなかったり、集計作業はできませんでした。そこで、Markdown型テスト仕様書をExcelに変換するpythonスクリプトを作成しました。ソースコードは [GitHub](https://github.com/k-watanb/md_test_case_to_excel) で公開しています。

## 実際に使ってみて

実際のプロジェクトで運用してみて、良い点もあれば課題も見えてきました。

### 良かった点

- ソースコードと同じディレクトリ階層で仕様書を管理できるため、仕様書を探す手間が省けた。
- 仕様書内まで一括検索・置換ができるようになった（Excelは開くまで中身がわからなかった）。
- バージョン管理や差分管理がものすごく快適になった。
- オープンにもほとんど時間がかからず、生産性が向上した。
- IDEだけで作業が完結するようになった。IDE/エディタとExcelのにらめっこ作業がなくなった。

### 課題

- 図表現が弱い。テスト結果のスクリーンショットの貼り付けができない。
- 集計作業ができない。ここはさすがにExcelに軍配。

## さいごに

Markdownによる仕様書管理はまだまだ発展途上ではありますが、正直従来のExcel管理よりかは恩恵が大きいと感じました。旧式の方法を一新するのは中々骨が折れますが、できる範囲で続けていこうと思いました。次はもっと別の仕様書で。

## 参考文献

- [Excelな設計書をMarkdownにしてみた](https://qiita.com/fuga___/items/e77e5f3acd536536613d)
- [torisawa/convert.py](https://gist.github.com/toriwasa/37c690862ddf67d43cfd3e1af4e40649)

[0](https://qiita.com/__xxWaKoUxx__/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2F__xxWaKoUxx__%2Fitems%2F5909704f0e80b1e25d51&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2F__xxWaKoUxx__%2Fitems%2F5909704f0e80b1e25d51&realm=qiita)

[9](https://qiita.com/__xxWaKoUxx__/items/5909704f0e80b1e25d51/likers)

いいねしたユーザー一覧へ移動

7