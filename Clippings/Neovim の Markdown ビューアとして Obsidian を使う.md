---
title: "Neovim の Markdown ビューアとして Obsidian を使う"
source: "https://qiita.com/delphinus/items/c591ca78a4c44eaad9ed"
author:
  - "[[delphinus]]"
published: 2024-05-20
created: 2025-12-04
description: "この記事は Vim 駅伝 2024/5/20 の記事です。 前回は staticWagomU さんの Masonから脱却できなかった - 輪ごむの空き箱という記事でした。 次回は kuuote さんの HとLをいい感じにするマッピングをvimrc読書会で見つけたので魔改..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiitaにログインして、便利な機能を使ってみませんか？

あなたにマッチした記事をお届けします

便利な情報をあとから読み返せます

[ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fdelphinus%2Fitems%2Fc591ca78a4c44eaad9ed&realm=qiita) [新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fdelphinus%2Fitems%2Fc591ca78a4c44eaad9ed&realm=qiita)

この記事は最終更新日から1年以上が経過しています。

この記事は [Vim 駅伝](https://vim-jp.org/ekiden/) 2024/5/20 の記事です。

前回は staticWagomU さんの [Masonから脱却できなかった - 輪ごむの空き箱](https://wagomu.me/blog/2025-05-17-vim-ekiden/) という記事でした。

次回は kuuote さんの [HとLをいい感じにするマッピングをvimrc読書会で見つけたので魔改造してみた](https://zenn.dev/vim_jp/articles/20240522_ekiden_better_hl) という記事です。

## これは何？

Markdown は実質、単なるテキストファイルです。そのため Neovim で書くこと自体は簡単なんですが、ビューアが無いのはターミナルで動くものである以上仕方ありません。今までもこの問題を解決するために様々なプラグインが作られてきました。

- [previm/previm](https://github.com/previm/previm)
- [iamcco/markdown-preview.nvim](https://github.com/iamcco/markdown-preview.nvim)
- [euclio/vim-markdown-composer](https://github.com/euclio/vim-markdown-composer)

検索するとまだまだ出て来ます。大抵のものが、裏で HTTP サーバを立てておいて、ターミナル（Neovim）の横にブラウザを開いて使う、という形式です。今回紹介するのはこれらとはちょっと違い、 [Obsidian](https://obsidian.md/) をビューアとして使うという提案です。

こんな感じです。プレビューできてますね。

Obsidian とは……というのはここには書かないことにします。検索すればいくらでも出て来ますからね。最近流行のメモアプリという認識で OK です。

## Obsidian を Neovim で書く

Obsidian の一番の特長とは、管理する文書自体はフォルダに入れられた Markdown ファイルでしかない、ということです。このために、公式の Obsidian アプリでなくても好きなエディタで文書を開いて編集できるのです。

Neovim の場合も同じ。例えば以下のようにしてファイルを開けます。

```bash
$ nvim ~/Library/Mobile\ Documents/iCloud~md~obsidian/Documents/Foo/2024-05-13.md
```

これで `Foo` という保管庫に入った 2024/5/13 の日記を開けます。

ファイルパスは macOS での例です。以下でも同じようなパスが出て来ますので、その他の OS では読み替えてください。

しかし Neovim ですからね。当然テキスト表示です。 [Tree-sitter](https://tree-sitter.github.io/tree-sitter/) などで見た目はある程度リッチにできますが、画像は表示できませんしドラッグ & ドロップで追加することもできません。

## obsidian-bridge.nvim

これを解決するために [oflisback/obsidian-bridge.nvim](https://github.com/oflisback/obsidian-bridge.nvim) を使います。このプラグインにより Neovim を Obsidian の公式アプリと協調して動かすことができます。

- Neovim で開いた文書を自動的に Obsidian アプリで開く。
- Neovim で編集中にスクロールすると該当の場所に自動的に追随する。

プラグインが提供する機能は主に上記の 2 つです。これにより、Obsidian アプリと Neovim を行ったり来たりして編集することが可能になります。普段は Neovim で内容を編集しながら、画像を挿入したいと思ったら Obsidian アプリにドラッグ & ドロップすればいいのです。Neovim に戻った時にそれも反映されています。

## プラグインのインストール

### Obsidian アプリの制限モードをオフにする

すでにコミュニティプラグインを使っている人はここをスキップしてください。

obsidian-bridge.nvim を使うためには、Obsidian アプリにもプラグインを入れる必要があります。インストールした状態では、Obsidian アプリには任意のコミュニティプラグインをインストールすることができません。設定→コミュニティプラグイン→制限モードからオフにしましょう。

[![スクリーンショット 2024-05-11 17.50.54.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/8880/e08ee791-ff0b-39a4-8e5c-b73bbe86626b.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F8880%2Fe08ee791-ff0b-39a4-8e5c-b73bbe86626b.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a600b57d555f2b3a6509b8f98ba9e0a4)

### Local REST API for Obsidian のインストール

このプラグインは名前の通り、Obsidian アプリを操作できる REST API を提供します。obsidian-bridge.nvim は内部で `curl` を使い、この REST API を叩いて Obsidian アプリを操作しているのです。

通常なら Obsidian アプリの設定 UI からプラグインをインストールする所なのですが、2024/5 現在、obsidian-bridge.nvim に必要な機能を利用するには開発中の [fork](https://github.com/oflisback/obsidian-local-rest-api) を使う必要があります。これは Beta 版という扱いですので簡単にインストールできません。ここで利用するのが BRAT という別のプラグインです。

この記事の初版では fork したレポジトリーからコンパイルしてプラグインをインストールしていました。その後 obsidian-bridge.nvim の README が下記の BRAT を使う方法に書き換えられ、併せて本記事も修正しています。

#### BRAT のインストール

これも、すでに [BRAT](https://tfthacker.com/BRAT) をインストール済みの人はスキップしてください。

設定→コミュニティプラグイン→閲覧から BRAT をインストールしましょう。

[![スクリーンショット 2024-06-03 15.05.54.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/8880/47b9a793-28ac-adeb-0208-0dfa33e0f1dc.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F8880%2F47b9a793-28ac-adeb-0208-0dfa33e0f1dc.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=634c133fdc34c524331a167d3086cf59)

“インストール” をクリックすると完了です。

[![スクリーンショット 2024-06-03 15.05.59.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/8880/352b5502-acf8-1ee7-b2de-0219a8e5b7b1.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F8880%2F352b5502-acf8-1ee7-b2de-0219a8e5b7b1.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=ffd521561e8e92c1ee1df595c61d508c)

設定→コミュニティプラグインから、“BRAT” を有効にしておきます。

[![スクリーンショット 2024-06-03 17.05.12.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/8880/43226d8c-00a2-ea3a-bfa9-e88ff668dde7.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F8880%2F43226d8c-00a2-ea3a-bfa9-e88ff668dde7.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=9b54cc463fd82e187a4c76f3e43eed39)

#### BRAT によるインストール

設定画面左メニューの下部から BRAT の設定画面が開けるようになります。ここで、“Add Beta plugin with frozen version” を選択し、

[![スクリーンショット 2024-06-03 15.06.19.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/8880/b8ddef67-0297-e5b9-08e4-456b805f2732.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F8880%2Fb8ddef67-0297-e5b9-08e4-456b805f2732.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=8eb6db3ca63679f070958c330e517973)

以下のように入力して “Add Plugin” をクリックしてください。

- URL → [https://github.com/oflisback/obsidian-local-rest-api](https://github.com/oflisback/obsidian-local-rest-api)
- リリースタグ → v1.0.0

[![スクリーンショット 2024-06-03 15.07.16.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/8880/65d7a096-a30b-3cce-a4c8-e43fc05d1756.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F8880%2F65d7a096-a30b-3cce-a4c8-e43fc05d1756.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=6c01999579af498754cead446b077e70)

暫く待つとインストール完了です。設定画面で BRAT と同じ並びに “Local REST API with Scroll” が現れているはずです。

#### アプリで有効にする

設定→コミュニティプラグインから、“Local REST API with Scroll” を有効にします。

[![スクリーンショット 2024-05-11 18.19.29.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/8880/f7188d44-713a-ee68-483c-94460735be10.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F8880%2Ff7188d44-713a-ee68-483c-94460735be10.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=0a2e618eaf1403371d922c53ef1511a5)

歯車アイコンを押してプラグインの設定を開き、“Your API Key” をメモっておきます。更に、“Enable Non-encrypted (HTTP) Server” を ON にします。

Local REST API プラグイン自体は HTTPS でのアクセスにも対応しているのですが、2024/5 現在、obsidian-bridge.nvim は HTTP でのアクセスにしか対応していません。

[![スクリーンショット 2024-05-11 19.36.11.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/8880/4ebb11fd-71f8-e79c-6eb6-b088021a79c6.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F8880%2F4ebb11fd-71f8-e79c-6eb6-b088021a79c6.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=d90e95af9141a3b2bffd91be1f8a3e73)

### 環境変数に API Key をセットする

先ほどメモっておいた文字列を環境変数 `OBSIDIAN_REST_API_KEY` にセットします。Neovim を起動する時は必ずこの環境変数が有効になるようにしておきましょう。

```bash
export OBSIDIAN_REST_API_KEY=hogehoge
```

誤った設定で REST API サーバを公開した場合、この API Key で全ての文書にアクセスできてしまいます。この文字列は注意して扱いましょう。

### obsidian-bridge.nvim のインストール

次に、Neovim に obsidian-bridge.nvim をインストールします。以下ではプラグインマネージャーとして [lazy.nvim](https://github.com/folke/lazy.nvim) を使っているものとして進めます。

```lua
{ "oflisback/obsidian-bridge.nvim", opts = { scroll_sync = true } }
```

`obsidian_server_address` というオプションを指定すれば、別のマシンで起動している Obsidian アプリに接続することもできます。ですが HTTP でアクセスするのは危険ですので、標準の `http://localhost:27123` から変更しない方が良いでしょう（ポート番号は変更してもいいかもですね）。

これで終わりです。後は、Neovim を再起動した上で Obsidian 上の文書を開いてみましょう。Obsidian アプリでも同じ文書が開くはずです。Neovim をスクロールすると Obsidian アプリもスクロールします。Neovim、Obsidian アプリ、双方で編集したものが、もう片方のアプリにフォーカスが移ると互いに同期されるはずです。

ただし他のソリューションと違い、Neovim 上の修正は `:w` で保存しないと反映されません。これは善し悪しですね。

## 更に便利に使うには

Markdown を、あるいは、Obsidian の文書を Neovim で書くにはまだまだ様々なテクニックがあります。この記事はここまでとしますが、他にも便利になるプラグインは色々ありますのでそれらを簡単に紹介して終わりましょう。

### Markdown を更に見易くする

#### nvim-treesitter/nvim-treesitter

これはもう入れてますよね。

#### MeanderingProgrammer/markdown.nvim

通称 “render-markdown”。Tree-sitter を組み合わせると見た目がかなりリッチになります。

| TS のみ | TS + `se cole=2` | render-markdown |
| --- | --- | --- |
| [![スクリーンショット 2024-05-16 19.39.13.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/8880/e1c68669-ab44-ab44-93ee-0161f52dbba0.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F8880%2Fe1c68669-ab44-ab44-93ee-0161f52dbba0.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=31459430f9918763927e10f4c0873452) | [![スクリーンショット 2024-05-16 19.39.29.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/8880/251716b6-2552-6351-bfdb-08422097fede.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F8880%2F251716b6-2552-6351-bfdb-08422097fede.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=299110a4b960fa7521051a71b6433c37) | [![スクリーンショット 2024-05-16 19.39.44.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/8880/c2d7cc2a-3fcf-e847-e17b-070a1d26e855.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F8880%2Fc2d7cc2a-3fcf-e847-e17b-070a1d26e855.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=b301455c6aa767d7cc349386fbd20d90) |

TS のみ

Tree-sitter を入れて `set conceallevel=0` だとこの表示です。色がリッチになります。

TS + `se cole=2`

レベル 1 に加えて、 `set conceallevel=2` するとこの表示です。チェックボックスが見易くなったかな？

render-markdown

更に render-markdown を入れました。表や引用マークが変わってますね。

### Obsidian を便利に使う

#### epwalsh/obsidian.nvim

Obsidian 特有の記法（文書間のリンクなど）がきちんとハイライトされるようになったり、 [hrsh7th/nvim-cmp](https://github.com/hrsh7th/nvim-cmp) （自動補完）や [nvim-telescope/telescope.nvim](https://github.com/nvim-telescope/telescope.nvim) （ファジーファインダー）を使ってより素早く文書を読んだり書いたりできます。

[![スクリーンショット 2024-05-18 16.41.44.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/8880/bfe549ba-b5b1-8096-7b44-580b64028443.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F8880%2Fbfe549ba-b5b1-8096-7b44-580b64028443.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=15d67ed6939fe40f41278a7925aa01b9)

このプラグインにより、リンクを書く時補完も効きます。

#### delphinus/obsidian-kensaku.nvim

Obsidian 文書の全文検索をローマ字で行うプラグインです。 [lambdalisue/vim-kensaku](https://github.com/lambdalisue/vim-kensaku) を利用しています。

[![demo-egrepify.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/8880/773cba8d-5685-be64-cb32-aca3bfed8538.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F8880%2F773cba8d-5685-be64-cb32-aca3bfed8538.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=cdf107409ea8f2060ce6bee2f73adcad)

#### ツリー形式で開けるファイラー

Obsidian アプリの左側にあるファイラーが、Neovim にもあると便利ですね。これも星の数ほどあるので好きなやつを選びましょう。

- [nvim-neo-tree/neo-tree.nvim](https://github.com/nvim-neo-tree/neo-tree.nvim)
- [lambdalisue/vim-fern](https://github.com/lambdalisue/vim-fern)
- [obaland/vfiler.vim](https://github.com/obaland/vfiler.vim)

僕は 2 画面ファイラーとして使える、vfiler が好きですね。

## 終わりに

以上です。いいですよ。Neovim と Obsidian。

[3](https://qiita.com/delphinus/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fdelphinus%2Fitems%2Fc591ca78a4c44eaad9ed&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fdelphinus%2Fitems%2Fc591ca78a4c44eaad9ed&realm=qiita)

## Qiita Advent Calendar 開催！

[55](https://qiita.com/delphinus/items/c591ca78a4c44eaad9ed/likers)

いいねしたユーザー一覧へ移動

38