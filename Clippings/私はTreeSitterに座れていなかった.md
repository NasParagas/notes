---
title: "私はTreeSitterに座れていなかった"
source: "https://zenn.dev/atoyr/articles/8802733f238e6d"
author:
  - "[[Zenn]]"
published: 2024-04-09
created: 2025-11-10
description:
tags:
  - "clippings"
---
16[tech](https://zenn.dev/tech-or-idea)

## はじめに

私はVimからNeoVimに乗り換えた時に噂のTreeSitterを使いたいと思い設定しました。  
しばらくTreeSitter を使っていたと思っていたのですが、とあるときに使えていないことに気づきました。  
そうです、題名にある通り私はTreeSitterに座れていなかったのです。  
本稿では私がTreeSitterに座れていなかった理由とその解決方法を紹介します。

## 環境

NeoVim 0.10.0-dev  
Pluginマネージャー: Packer → lazy.nvim

## どうして座れていなかったのか

特に経緯が必要ない人はこの結論だけで十分かと思います。

- `lazy.nvim` で `nvim-treesitter` を読み込みするときに `opts` を指定する場合は、メインモジュールの指定が必要だった
	- `nvim-treesitter.configs` をメインモジュールに指定する
- ハイライトが動いているイコールTreeSitterがハイライトしているではなかった
	- `nvim-treesitter` がインストールされていてもハイライドが有効になっていない場合がある
	- NeoVimに標準でついているハイライト機能が動いているだけかもしれない

## いつからTreeSitterを使えていると錯覚していた

ことの発端は `Svelte` で簡単なコードを書いてみようと思った時でした。  
`Svelte` は `html` と `TypeScript` (`javascript`)を一つのファイルに記載します。  
はじめて `Svelte` のファイルを開いたときに、 `TypeScript` のシンタックスハイライトが効いていないことに気づきました。

このとき、ちょうど数週間前に `Packer` から `lazy.nvim` に移行したばっかだったため、移行ミスが原因かと思いました。  
移行ミスを疑いつつ、Chat-GPTやTreeSitterのIssue、Web上の記事なんかを参考にしながら調査しはじめました。

## 確認したこと

上に述べた通り `lazy.nvim` に移行したばかりだったため、移行ミスの疑いから調査をはじめました。

- `lazy.nvim` へのマイグレーションガイドを確認
- `lazy.nvim` のアップデート
- `TSInstall all` ですべての言語のTreeSitterをインストール
- `TSUpdate` でTreeSitterをアップデート
- `checkhealth nvim-treesitter` でTreeSitterの状態を確認
- `TSInstallInfo` でインストールされているTreeSitterの情報を確認

ここまでで、 `nvim-treesitter` は動作していることが確認できました。  
しかし、 `Svelte` のファイルを開いても `TypeScript` のシンタックスハイライトが効いていませんでした。

`Svelte` 自体が1ファイル内に `html` と `TypeScript` を記載するため、何か特殊な設定が必要なのかと思いました。  
調べるとGrammarあたりの設定が必要だという記事も見つけたため、 `TSInstallFromGrammar` で `Svelte` に関連するGrammarをインストールしました。 しかし、状況は変わりませんでした。

追加で以下を調べたところ、そもそもTreeSitterがハイライトしていないことに気づきました。

- `TSConfigInfo` でTreeSitterに何が設定されているか確認
	- ここで `highlight` が `false` になっていることに気づく
- `TSBufEnable` でTreeSitterを有効にする
	- ここで `Svelte` ハイライトが効くようになった

つまり、 `lazy.nvim` のoptsを記載していますが、 `nvim-treesitter` はそれを適用できていなかったのです。  
普段からハイライトが効いていたのでTreeSitterに座れていると思っていましたが、実はnvimのハイライトの機能でハイライトされているだけでした。  
NeoVimに移行したタイミングに同時にTreeSitterを導入しているため、素のハイライトとTreeSitterのハイライトを見分けることができなかったのです。

## 設定したこと

冒頭で述べた通り、 `lazy.nvim` で `opts` を指定する方法自体はあっていました。  
じゃあ何か特殊な設定が必要なのかと調べたところ、 `nvim-treesitter.configs` をメインモジュールに指定する必要があることがわかりました。  
最終的に以下のように設定したところ、TreeSitterがハイライトするようになりました。

```lua
return {
  -- Treesitter
  { 
    "nvim-treesitter/nvim-treesitter", 
    build = ":TSUpdate", 
    dependencies = {
      "nvim-treesitter/nvim-treesitter-textobjects",
    },
    main = 'nvim-treesitter.configs', 
    opts = {
      highlight = { enable = true },
    }, 
  }, 
  {
    "nvim-treesitter/nvim-treesitter-textobjects",
    event = "CursorMoved",
  },
}
```

ハイライトが有効になっているかは、 `TSConfigInfo` で確認しました。また、 `Svelte` のファイルを開いて `html` と `TypeScript` のシンタックスハイライトが効いているか確認しました。

## さいごに

今回は私がTreeSitterに座れていなかった理由とその解決方法を紹介しました。  
ハイライトは素のNeoVimでも機能としてあるため、NeoVimに移行したタイミングでTreeSitterを導入していると、TreeSitterが有効になっているかどうか見分けることが難しいかもしれません。  
もし私と同じようにNeoVimに移行したタイミングでTreeSitterを導入している方がいれば、一度TreeSitterが有効になっているか確認してみてください。

[GitHubで編集を提案](https://github.com/Atoyr/Zenn/blob/main/articles/8802733f238e6d.md)

16

### Discussion

![](https://static.zenn.studio/images/drawing/discussion.png)

記事についてコメントする  

[コミュニティガイドライン](https://zenn.dev/guideline) に則った投稿をしましょう。

16