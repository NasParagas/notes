---
title: "Vim初心者から中級者の入り口くらいまで"
source: "https://qiita.com/miya-masa/items/a30c252490136579186d"
author:
  - "[[miya-masa]]"
published: 2018-12-17
created: 2025-09-26
description: "はじめに アドベントカレンダー18日目、aptpodのしがないサーバーサイドエンジニアがお届けします。 前回はGoのトランザクションの話を書きました（こちら）。今回は筆者が大好きなエディタ 「Vim」 について書こうと思います。と、いっても宗教戦争するつもりもありませんし..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

## はじめに

アドベントカレンダー18日目、aptpodのしがないサーバーサイドエンジニアがお届けします。

前回はGoのトランザクションの話を書きました（ [こちら](https://qiita.com/miya-masa/items/316256924a1f0d7374bb) ）。今回は筆者が大好きなエディタ 「Vim」 について書こうと思います。と、いっても宗教戦争するつもりもありませんし、マニアックなことを語るつもりもありません。そもそも語れないですし。表題の通り初心者向けの記事にしようと思います。

## 筆者のVimスキル

「Vim好き」と言っても、VimScriptはよくわかりませんし、素のVimもそこまで使いこなしていないような気がします。ですが、そんな筆者でもプラグインを活用しながら、とても便利にVimを使うことができています。そんな感じのVimスキルです。

ちなみに、Vimは2013年ごろから使っています。今は基本的にターミナルとVimとブラウザで生きています。

## 対象読者

- Vim初めて触る人
- Vimを使い始めてからそんなに経っていない人
- いまいち便利に使う糸口が見えない人

## チュートリアル

まずはvimの操作に慣れましょう。vimには標準でチュートリアルがついています。これがなかなか良くできていて、数回繰り返すだけで正しいVimの操作が身についてきます。vimのチュートリアルを起動するにはこのようなコマンドを叩きます。

```text
vimtutor ja
```

vimtutorはvimのruntimeパッケージに含まれています。  
もし、vimtutorコマンドが見つからなかったら、vimをインストールしましょう。

一回30分くらいのボリュームです。慣れるまでは一日一回のペースで行うのが良いでしょう。仕事前とか退社前とかね。筆者もそうやって必要最低限の使い方は覚えました。一週間ほどでかなり慣れるはずです。

ちなみに、いきなり普段使っているエディタをVimに変える必要はないと思います。チュートリアルをやりながら、慣れてきたら、普段使っているエディタのキーマップをVimに変えるというやりかたで全く問題ありません。各種エディタのVimプラグインをまとめておきます。

- vscode -> [vscodevim](https://marketplace.visualstudio.com/items?itemName=vscodevim.vim)
- atom -> [atom-vim-mode](https://atom.io/packages/vim-mode)
- idea製品 -> [ideavim](https://github.com/JetBrains/ideavim)
- eclipse -> [vrapper](http://vrapper.sourceforge.net/home/)

普段使っているエディタのvimキーマップにも我慢できる程度に使えるようになってきたら、チュートリアルは卒業です。チュートリアルを卒業するだけでも（vimをエディタとして使わなくても）効果は十分と筆者は思っています。各種エディタのvimキーバインドで操れるだけで満足という方はこれ以降読まなくても問題ありません。

## neovimのインストール

vimの操作にも慣れてきてもっとVimのことを知りたいという方は初期設定ををします。  
Vimと言っていますが最近は、neovim がナウいので neovimをインストールします。  
以降MacOSを前提に話を進めさせていただきます。インストールの方法は [ここ](https://github.com/neovim/neovim/wiki/Installing-Neovim) みれば大体わかります。

```bash
brew install neovim
```

neovimはプラグインでpython3を使うことが多いので、python3も有効化します。

```bash
pip3 install pynvim
```

neovimを起動し、python3を有効化しましょう。neovimの起動コマンドは `nvim` なのでご注意ください。

```text
nvim

# 起動後のpython3の有効化
:UpdateRemotePlugin
# nvimの状態チェック
:checkhealth
```

`:checkhealth` 少し待つとneovimの状態が出力されます。python3が有効化されていることが確認できればOKです。

おめでとうございます。一旦ここでneovimを閉じましょう。

```text
:q
```

## プラグイン設定をしてみる

Vimといえば、プラグインです。ものは試しでヘルプの日本語化をプラグインマネージャを使ってインストールしてみましょう。

## プラグインマネージャのインストール

今回はプラグインマネージャを使ってプラグインを入れようと思いますので、プラグインマネージャをインストールするところから。  
vimのプラグインマネージャはいくつかありますが、筆者が [vim-plug](https://github.com/junegunn/vim-plug) を使っているのでvim-plugで説明します。  
どんなものがあるかは後述します。

vim-plugは `curl` 一発でインストール可能です。

## プラグインのインストール

プラグインの設定は `~/.config/nvim/init.vim` に記述します。  
ヘルプ日本語化 `vim-jp/vimdoc-ja` を `vim-plug` 使ってインストールします。

```vimscript
" ~/.config/nvim/init.vim
call plug#begin('~/.vim/plugged')
  Plug 'vim-jp/vimdoc-ja'
call plug#end()
set helplang=ja,en
```

これで、日本語化プラグインの設定が完了しました。

次はneovimを開きプラグインをインストールするコマンドを実行し、`:h` で本当に日本語化されているか確認しましょう。

```text
:PlugInstall
:h
```

この時のヘルプが日本語化されていればOKです。  
ね、簡単でしょ？

## プラグイン紹介

Vimは本当にプラグインが豊富です。その中からとりあえず入れておいたほうがいいと、筆者が思っているプラグインのカテゴリと、そのカテゴリのプラグインを紹介します。それぞれのカテゴリでどれを選ぶか好みかと思いますが、その内のいずれかを入れておくと良いと思います。中にはvim標準でまかなえるものありますが、標準については割愛します。が、それも含めて検討すべきと思います。どれが合うかは人それぞれであって、使ってみないとわからないというのは筆者の持論なので、ころころ変えてみて試してみるのがおすすめです。

## プラグインまとめ

vimのプラグインを探したいときは [vimawesome](https://vimawesome.com/) を見ると良いです。筆者も暇さえあればのぞいてます。

## プラグイン管理

- [dein.vim](https://github.com/Shougo/dein.vim)
- [vim-plug](https://github.com/junegunn/vim-plug)

## コード補完

- [deoplete](https://github.com/Shougo/deoplete.nvim)
- [YouCompleteMe(YCM)](https://github.com/Valloric/YouCompleteMe)
- [ncm2](https://github.com/ncm2/ncm2)

## コードスニペット

- [UltiSnips](https://github.com/SirVer/ultisnips)
- [deoppet](https://github.com/Shougo/deoppet.nvim)

## ファイラ

自分自身迷っています。いまは `dirvish` ですが、他に乗り換えるかもです。

- [NERDTree](https://github.com/scrooloose/nerdtree)
- [Vaffie](https://github.com/cocopon/vaffle.vim)
- [dirvish](https://github.com/justinmk/vim-dirvish)
- [defx](https://github.com/Shougo/defx.nvim)

## ファインダー(選択系の統一インターフェース)

- [ctrlp](https://github.com/kien/ctrlp.vim)
- [denite](https://github.com/Shougo/denite.nvim)
- [fzf.vim](https://github.com/junegunn/fzf.vim)

## 番外編

## もっと楽なVim

設定とかめんどくさい、ターミナルじゃなくってもうちょっとモダンな感じのないの？  
という方向けに最初から諸々設定されているものであったり、neovimベースのGUIもあったりします。正直あまり詳しくないですが、いくつかそれっぽいのを載せておきます。設定周りの敷居が高いと感じる方はこういったところからの方が入りやすいのではないでしょうか。

- [SpaceVim](https://github.com/SpaceVim/SpaceVim)
- [Oni](https://github.com/onivim/oni)
- [NyaoVim](https://github.com/rhysd/NyaoVim)

## 筆者のよく使う便利なVimプラグイン達

Vimを使うとこんなこともできるんだよということを紹介しておきます。

### RestClient

筆者はRestClientもVim上で行っています。アプリケーション開発時とかにWebAPIを叩くことがままあるのですが、そういう時にすごく重宝します。

- [vim-rest-console](https://github.com/diepm/vim-rest-console)

[![rest-console.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F46644%2F821ecf5a-bca1-09c8-94e3-7c4af1fad6b2.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=0603d5c39e81ef2c3b363b788ed358c3)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F46644%2F821ecf5a-bca1-09c8-94e3-7c4af1fad6b2.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=0603d5c39e81ef2c3b363b788ed358c3)

裏では `curl` が動いています。左ペインにパスとHTTPメソッド、右ペインに結果が表示されています。

### 翻訳

翻訳もVimで行っています。 日本語と英語の双方向可能なので、英語の文章をちょろっと訳したいときとか、逆に日本語を英語にしたい時とか頻繁に利用します。

- [vim-translate](https://github.com/VincentCordobes/vim-translate)

[![translate.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F46644%2Fa00684b8-954c-fc69-6256-10da9d596121.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=52d424f8578b8b406704c70430a4c53e)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F46644%2Fa00684b8-954c-fc69-6256-10da9d596121.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=52d424f8578b8b406704c70430a4c53e)

### スプラッシュ

最初に開いた時のスプラッシュ画面が自分用だとテンション上がりますよね？ スプラッシュをカスタマイズできるプラグインで変更しています。

- [vim-startify](https://github.com/mhinz/vim-startify)

[![startify.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F46644%2F6f858a61-40aa-c3df-c843-0a8ed517af11.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e5d2634a3f722137b846f347003b959e)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F46644%2F6f858a61-40aa-c3df-c843-0a8ed517af11.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e5d2634a3f722137b846f347003b959e)

はい。テンション上がりますね。

## まとめ

最後の方はちょっと投げやりになってしまいましたが、少しでもVimに興味を持っていただけたら幸いに思います。

[0](https://qiita.com/miya-masa/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fmiya-masa%2Fitems%2Fa30c252490136579186d&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fmiya-masa%2Fitems%2Fa30c252490136579186d&realm=qiita)

## Qiita Conference 2025 Autumn 11月5日(水)~7日(金)開催！

![](https://cdn.qiita.com/assets/public/official_campaigns/qiita_conference_2025_autumn/image-conference_2025_autumn_ogp-7cf3021de31b9ab76a7b3bbaf2909bb5.png)

Qiita Conferenceは、AI時代のエンジニアに贈るQiita最大規模のテックカンファレンスです！

基調講演ゲスト(敬称略)

piacere、牛尾 剛、Esteban Suarez、和田 卓人、seya、ミノ駆動、市谷 聡啓、からあげ、まつもとゆきひろ、みのるん

[イベント詳細を見る](https://qiita.com/official-campaigns/conference/2025-autumn)

[22](https://qiita.com/miya-masa/items/a30c252490136579186d/likers)

いいねしたユーザー一覧へ移動

10