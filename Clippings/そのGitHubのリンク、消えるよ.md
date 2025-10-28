---
title: "そのGitHubのリンク、消えるよ"
source: "https://qiita.com/185shingeki/items/6e7748b6d67fabe649f0"
author:
  - "[[185shingeki]]"
published: 2025-07-15
created: 2025-08-26
description: "はじめに そのGitHubのリンク、消えるよ とある天才も開発現場でこう言ってるかもしれないです。 あなたがドキュメントや、Pull Requestのコメント、Slackに貼り付けたGitHubのコードへのリンク。 数ヶ月後、あるいは数年後にそのリンクを開いたとき、「..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## はじめに

> そのGitHubのリンク、消えるよ

とある [天才](https://ja.wikipedia.org/wiki/%E4%B8%8D%E4%BA%8C%E5%91%A8%E5%8A%A9) も開発現場でこう言ってるかもしれないです。

あなたがドキュメントや、Pull Requestのコメント、Slackに貼り付けたGitHubのコードへのリンク。  
数ヶ月後、あるいは数年後にそのリンクを開いたとき、「404 Not Found」になっていたり、全く違う内容を指し示していたりした経験はありませんか？

それは、あなたが「消える」リンクを使っているからです。

## なぜリンクは「消える」のか

普段、GitHubのファイルへのリンクをコピーすると、URLにはブランチ名（例: `main` ）が含まれています。

```text
https://github.com/your-org/your-repo/blob/main/src/important-file.ts#L10-L20
```

この形式のリンクは、以下の理由で「消える」可能性があります。

### ファイルがリネーム・移動された

`important-file.ts` がリファクタリングによって別のディレクトリに移動したり、名前が変わったりするとリンクは切れます。

### ファイルが削除された

言うまでもありません。

### ブランチが削除された

`main` ブランチは通常消えませんが、 `feature` ブランチなどへのリンクは、マージ後にブランチが削除されるとリンク切れになります。

### コードが変更された

これが最も厄介かもしれません。  
ファイル自体は存在していても、リンク先の10行から20行目のコードが全く別の内容に書き換えられている可能性があります。  
これではリンク先の文脈が失われてしまいます。

## 「消えない」リンクを作るには

パーマリンク（Permalink）を使いましょう。

パーマリンクは、ブランチ名ではなく、特定のコミットIDを指し示すURLです。コードは特定のコミット時点の状態に固定されるため、その内容が変化することはありません。

### GitHubでパーマリンクを取得する方法

方法はとても簡単で、

1. GitHubでリンクしたいファイルを開き、該当する行番号（または複数行）をクリックしてハイライトする
2. その状態で、キーボードの `y` キーを押す

これだけです。

`y` キーを押すと、URLが自動的にブランチ名から現在の最新コミットのハッシュ（一意のID）に書き換わります。

**変更前（消えるリンク）:**

```text
https://github.com/your-org/your-repo/blob/main/src/important-file.ts#L10-L20
```

**変更後（消えないリンク）:**

```text
https://github.com/your-org/your-repo/blob/a1b2c3d4e5f6.../src/important-file.ts#L10-L20
```

`a1b2c3d4e5f6...` の部分がコミットIDになります。

このURLを共有すれば、たとえ `main` ブランチにどれだけ修正が加えられようとも、あなたのリンクは永遠に「その時点のコード」を指し示し続けます。

## まとめ

- **ブランチ名を指すリンクは「消える」**
- **パーマリンク（コミットIDを指すリンク）は「消えない」**
- **GitHubでファイルやコード行を選択した状態で `y` キーを押せば、一瞬でパーマリンクが手に入る**

これで、あなたの残すドキュメントやコメントも、未来の(天才)同僚から「リンクが消えてるよ…」と悲しまれることはなくなるはずです。

> 君のリンク、もう消えないよ

[2](https://qiita.com/185shingeki/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2F185shingeki%2Fitems%2F6e7748b6d67fabe649f0&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2F185shingeki%2Fitems%2F6e7748b6d67fabe649f0&realm=qiita)

[333](https://qiita.com/185shingeki/items/6e7748b6d67fabe649f0/likers)

いいねしたユーザー一覧へ移動

176