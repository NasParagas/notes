---
title: "Neovimプラグイン2023決定版"
source: "https://qiita.com/ysmb-wtsg/items/79be0d97711eb49f5e82"
author:
  - "[[ysmb-wtsg]]"
published: 2023-12-31
created: 2025-09-26
description: "はじめに 2023年もいよいよ終わりということで、neovimのプラグインを棚卸ししました。 そのついでにお気に入りのプラグインを紹介します。 ↓設定ファイルのリポジトリです 目次 1.PluginManager wbthomason/packer.nvim..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から1年以上が経過しています。

## はじめに

2023年もいよいよ終わりということで、neovimのプラグインを棚卸ししました。  
そのついでにお気に入りのプラグインを紹介します。

↓設定ファイルのリポジトリです

## 目次

- [wbthomason/packer.nvim](https://github.com/wbthomason/packer.nvim)
- [Shatur/neovim-ayu](https://github.com/Shatur/neovim-ayu)
- [catppuccin/nvim](https://github.com/catppuccin/nvim)
- [rebelot/kanagawa.nvim](https://github.com/rebelot/kanagawa.nvim)
- [folke/tokyonight.nvim](https://github.com/folke/tokyonight.nvim)
- [williamboman/mason.nvim](https://github.com/williamboman/mason.nvim)
- [williamboman/mason-lspconfig.nvim](https://github.com/williamboman/mason-lspconfig.nvim)
- [jayp0521/mason-null-ls.nvim](https://github.com/nvimtools/none-ls.nvim)
- [nvimtools/none-ls.nvim](https://github.com/nvimtools/none-ls.nvim)
- [nvimdev/lspsaga.nvim](https://github.com/nvimdev/lspsaga.nvim)
- [folke/trouble.nvim](https://github.com/folke/trouble.nvim)
- [hrsh7th/nvim-cmp](https://github.com/hrsh7th/nvim-cmp)
- [hrsh7th/cmp-nvim-lsp](https://github.com/hrsh7th/cmp-nvim-lsp)
	- [onsails/lspkind.nvim](https://github.com/onsails/lspkind.nvim)
- [hrsh7th/cmp-buffer](https://github.com/hrsh7th/cmp-buffer)
- [hrsh7th/cmp-path](https://github.com/hrsh7th/cmp-path)
- [L3Mon4D3/LuaSnip](https://github.com/L3MON4D3/LuaSnip)
- [rafamadriz/friendly-snippets](https://github.com/rafamadriz/friendly-snippets)
- [saadparwaiz1/cmp\_luasnip](https://github.com/saadparwaiz1/cmp_luasnip)
- [nvim-tree/nvim-tree.lua](https://github.com/nvim-tree/nvim-tree.lua)
	- [kyazdani42/nvim-web-devicons](https://github.com/nvim-tree/nvim-tree.lua)
- [nvim-treesitter/nvim-treesitter](https://github.com/nvim-treesitter/nvim-treesitter)
- [akinsho/nvim-bufferline.lua](https://github.com/akinsho/bufferline.nvim)
- [nvim-lualine/lualine.nvim](https://github.com/nvim-lualine/lualine.nvim)
- [karb94/neoscroll.nvim](https://github.com/karb94/neoscroll.nvim)
- [petertriho/nvim-scrollbar](https://github.com/petertriho/nvim-scrollbar)
- [folke/noice.nvim](https://github.com/folke/noice.nvim)
- [kevinhwang91/nvim-ufo](https://github.com/kevinhwang91/nvim-ufo)
- [nvim-telescope/telescope.nvim](https://github.com/nvim-telescope/telescope.nvim)
	- [nvim-telescope/telescope-project.nvim](https://github.com/nvim-telescope/telescope-project.nvim)
- [nvim-telescope/telescope-fzf-native.nvim](https://github.com/nvim-telescope/telescope-fzf-native.nvim)
- [lewis6991/gitsigns.nvim](https://github.com/lewis6991/gitsigns.nvim)
- [FotiadisM/tabset.nvim](https://github.com/FotiadisM/tabset.nvim)
- [christoomey/vim-tmux-navigator](https://github.com/christoomey/vim-tmux-navigator)
- [akinsho/toggleterm.nvim](https://github.com/akinsho/toggleterm.nvim)
- [kylechui/nvim-surround](https://github.com/kylechui/nvim-surround)
- [inkarkat/vim-ReplaceWithRegister](https://github.com/vim-scripts/ReplaceWithRegister)
- [numToStr/Comment.nvim](https://github.com/numToStr/Comment.nvim)
- [windwp/nvim-autopairs](https://github.com/windwp/nvim-autopairs)
- [iamcco/markdown-preview.nvim](https://github.com/iamcco/markdown-preview.nvim)
- [phaazon/hop.nvim](https://github.com/phaazon/hop.nvim)
- [folke/zen-mode.nvim](https://github.com/folke/zen-mode.nvim)
- [Weissle/easy-action](https://github.com/Weissle/easy-action)
- [magatti/auto-session](https://github.com/rmagatti/auto-session)
- [jackMort/ChatGPT.nvim](https://github.com/jackMort/ChatGPT.nvim)
- [gcmt/wildfire.vim](https://github.com/gcmt/wildfire.vim)
- [gsuuon/note.nvim](https://github.com/gsuuon/note.nvim)

## 1\. PluginManager

neovimと言えばプラグイン。プラグインと言えばプラグインマネージャですね。  
neovimユーザにとっては空気のような存在ですが改めて紹介します。

- [wbthomason/packer.nvim](https://github.com/wbthomason/packer.nvim)

私は比較的モダン [^1] なpackerを使っています。

他のプラグインマネージャとの比較についても軽く触れたかったのですが、私はpackerしか使ったことがないので割愛します。  
とりあえずモダンで流行ってるやつ使っとけって感じでノリで使ってます。

## 2\. ColorScheme

neovimのcolorschemeはvscと遜色ないくらい色々あって楽しいです。  
を眺めているだけでも楽しいと思います。

- [Shatur/neovim-ayu](https://github.com/Shatur/neovim-ayu)  
	メインで使っているcolorschemeです。  
	全体的にwarmな感じが好きで使ってます。  
	[![スクリーンショット 2023-12-31 15.13.45.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/435ae1d5-5285-b913-124d-73727d505098.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F435ae1d5-5285-b913-124d-73727d505098.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=b40cf9e51721ffd6c05561cf7e4c550a)
- [catppuccin/nvim](https://github.com/catppuccin/nvim)
- [rebelot/kanagawa.nvim](https://github.com/rebelot/kanagawa.nvim)
- [folke/tokyonight.nvim](https://github.com/folke/tokyonight.nvim)  
	これらはたまに気分で切り替えたり切り替えなかったりします。

## 3\. LSP

LSPというのはLanguage Server Protocolの略です。  
neovimには、Language Serverに対してLSPで通信するためのクライアントが標準で備わっています(このクライアントのことをbuilt-in LSPと呼んだりします)。  
LS/LSPをinstallしたり設定するために以下のプラグインを使います。

- [williamboman/mason.nvim](https://github.com/williamboman/mason.nvim)  
	LSのinstallを簡単にしてくれるパッケージです。  
	`:Mason` と実行すると専用のUIが出現し、そこからinstall/uninstall/updateを簡単に行えます。  
	[![mason.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/0ec984fd-ce33-71a8-4518-ddd1471704d6.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F0ec984fd-ce33-71a8-4518-ddd1471704d6.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=b774009a0d905dc9ae7f38e45ace26ba)
- [williamboman/mason-lspconfig.nvim](https://github.com/williamboman/mason-lspconfig.nvim)  
	masonでinstallしたLSの設定を自動化するためのプラグインです。  
	mason-lspconfigを使わない場合、masonでLSをinstallしてもsetupを済ませないとLSが有効になりません。  
	mason-lspconfigはそのsetupをinstallと同時に済ませてくれるプラグインです。
- [jayp0521/mason-null-ls.nvim](https://github.com/nvimtools/none-ls.nvim)  
	masonでDAP(Debug Adapter Protocol)、Linter、Formatterをインストールできるようにするプラグインです。
- [nvimtools/none-ls.nvim](https://github.com/nvimtools/none-ls.nvim)  
	masonでinstallしたlinterやfomatterの設定をしそれらを動かすためのプラグインです。  
	ファイルを上書き保存したときに勝手にformatしてくれるように設定もできます。
- [nvimdev/lspsaga.nvim](https://github.com/nvimdev/lspsaga.nvim)  
	built-in LSPに備わった機能に対して、高速でいい感じのUIを提供してくれるプラグインです。  
	変数の定義元へのjumpや、docのhover、変数・関数のrenameなど、built-in LSPが本来もつ機能をより手軽に使えるようにしてくれます。
	- 定義/参照の表示  
		[![lspsaga-gf.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/0f5dfacf-6a40-4ef9-d22b-d6ce8b2cc272.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F0f5dfacf-6a40-4ef9-d22b-d6ce8b2cc272.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=9403e2993b992bb0b18f5e4cbeff5ca7)
	- outlineの表示  
		[![lspsaga-go.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/5342e545-d419-2378-6d79-4e3181df79c3.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F5342e545-d419-2378-6d79-4e3181df79c3.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=da2a2994c3cf8a404685613601353118)
	- docのhover  
		[![lspsaga-gd.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/fcb6e7a3-f447-98c3-ed7b-1cc14f3d5e05.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2Ffcb6e7a3-f447-98c3-ed7b-1cc14f3d5e05.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=4e0dae8f852612d09808f504255628f9)
- [folke/trouble.nvim](https://github.com/folke/trouble.nvim)  
	LSPが検出したエラーやワーニングを一覧表示してくれるプラグインです。  
	[![trouble.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/1994e0d0-e056-d083-cfa9-bbd57b836f2d.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F1994e0d0-e056-d083-cfa9-bbd57b836f2d.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=2f8a85b31c1d55972be768278e4bb902)

## 4\. Completion

built-in LSPには補完の機能がありません。  
なので補完を表示したい場合はプラグインを入れる必要があります。

- [hrsh7th/nvim-cmp](https://github.com/hrsh7th/nvim-cmp)  
	補完エンジンの本体です。  
	これだけでは補完は表示されるようにはならず、以降紹介するcmp-シリーズと併用することで補完が表示されるようになります。
- [hrsh7th/cmp-nvim-lsp](https://github.com/hrsh7th/cmp-nvim-lsp)  
	built-in LSPに基づいた補完を表示してくれます。  
	変数とか関数とかその辺の補完ですね。
	- [onsails/lspkind.nvim](https://github.com/onsails/lspkind.nvim)  
		補完のアイコンをvscライクなiconにしてくれるプラグインです。
- [hrsh7th/cmp-buffer](https://github.com/hrsh7th/cmp-buffer)  
	bufferに出てくる単語を補完で表示してくれます。
- [hrsh7th/cmp-path](https://github.com/hrsh7th/cmp-path)  
	ファイルやディレクトリのpathを保管してくれます。  
	[![cmp.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/a0a8ec11-139e-1850-188a-ec2a4d390bb8.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2Fa0a8ec11-139e-1850-188a-ec2a4d390bb8.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=8969020fb5ed9b27f8845ec7790fec6f)

## 5\. Snippet

スニペット管理のためのプラグインです。  
基本的にはスニペットは補完に表示されてほしいので、補完に表示させるために複数プラグインを入れています。

- [L3Mon4D3/LuaSnip](https://github.com/L3MON4D3/LuaSnip)  
	スニペット管理のためのプラグイン本体です。
- [rafamadriz/friendly-snippets](https://github.com/rafamadriz/friendly-snippets)  
	様々な言語でよく使うスニペット集です。
- [saadparwaiz1/cmp\_luasnip](https://github.com/saadparwaiz1/cmp_luasnip)  
	completionで紹介したcmp-シリーズのスニペット版です。  
	これを入れることで補完にスニペットが表示されます。  
	[![snip1.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/39995592-e4fd-befb-1a68-b370ad0f4b9b.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F39995592-e4fd-befb-1a68-b370ad0f4b9b.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=63239e37ff2d4111e362f0e6e10be488)

## 6\. UI

neovimのUIカスタマイズはかなり自由度が高く、故に人によって全く違ったUIに仕上がるのが面白いところだと思います。

- [nvim-tree/nvim-tree.lua](https://github.com/nvim-tree/nvim-tree.lua)  
	ファイラです。  
	とりあえずgithubでstart数多いやつ使っとけって感じで使ってます。
	- [kyazdani42/nvim-web-devicons](https://github.com/nvim-tree/nvim-tree.lua)  
		ファイラで表示するアイコン集です。  
		vscにもあるやつのneovim移植版です。  
		[![nvimtree.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/421a5b54-5aa0-33b5-a0be-c907928cffab.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F421a5b54-5aa0-33b5-a0be-c907928cffab.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=da0254fdde8bda6e42d0399344091cf9)
- [nvim-treesitter/nvim-treesitter](https://github.com/nvim-treesitter/nvim-treesitter)  
	コードハイライトのためのプラグインです。
- [akinsho/nvim-bufferline.lua](https://github.com/akinsho/bufferline.nvim)  
	タブ表示をカスタムするためのプラグインです。  
	[![bufline.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/e6f02c1c-0635-e255-5854-bd2869f4f063.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2Fe6f02c1c-0635-e255-5854-bd2869f4f063.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=22eb91def27f91d5e99f7e3fc4e34435)
- [nvim-lualine/lualine.nvim](https://github.com/nvim-lualine/lualine.nvim)  
	neovimのステータスラインをカスタムするためのプラグインです。  
	[![lualine.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/a5632fdd-8bcb-156b-f6cf-2871ff92cdab.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2Fa5632fdd-8bcb-156b-f6cf-2871ff92cdab.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=7d72356ea23146ae7e8b3a92de0287f5)
- [karb94/neoscroll.nvim](https://github.com/karb94/neoscroll.nvim)  
	`<C-u>` や `<C-d>` などのスクロール系の操作をした時の画面の動きがスムーズになります。  
	[![scroll.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F41ac0268-0f26-db0b-9196-711f1f5ee838.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=9fd78b13912d07b770209f4db058150e)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F41ac0268-0f26-db0b-9196-711f1f5ee838.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=9fd78b13912d07b770209f4db058150e)  
	(追記)↑サンプル動画撮ったけどgifになるから結局カクカクに見えちゃってて草🌱
- [petertriho/nvim-scrollbar](https://github.com/petertriho/nvim-scrollbar)  
	スクロールバーを表示してくれます。
- [folke/noice.nvim](https://github.com/folke/noice.nvim)  
	コマンド実行時のメッセージや、コードのエラー・ワーニングなど、neovim内の通知をモダンなUIにしてくれます。  
	環境によっては重くなるかもしれないので、その場合は必要な通知だけに適用するのが良いかと思います。  
	[![noice1.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/ecab432c-8d41-8dd4-cb52-e24e28b6592a.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2Fecab432c-8d41-8dd4-cb52-e24e28b6592a.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=f3b357952141b8764f34565a81164b34)  
	[![noice2.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/205d1df9-a9c4-efb1-35ef-601e9052319f.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F205d1df9-a9c4-efb1-35ef-601e9052319f.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=7b52445e59a92af279b5ed4055e38216)
- [kevinhwang91/nvim-ufo](https://github.com/kevinhwang91/nvim-ufo)  
	fold(関数などのブロック折りたたみ)の見た目をカスタムするためのプラグインです。  
	[![ufo1.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/6905b61e-1159-a1b7-f86f-b293ef48cd33.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F6905b61e-1159-a1b7-f86f-b293ef48cd33.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=96c69be1322c669bbf56be93260c9892)  
	[![ufo2.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/ee00b516-1daa-ceef-e723-9c056045f592.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2Fee00b516-1daa-ceef-e723-9c056045f592.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=4dcea484be6e06d4ec15a3ef14551a3a)

## 7\. Search

neovimではファイル検索や字句検索のための非常に有名で強力なプラグインがあります。

- [nvim-telescope/telescope.nvim](https://github.com/nvim-telescope/telescope.nvim)  
	telescopeはneovim版のfzfを提供してくれるプラグインです。  
	非常に有名で非常に強力なプラグインです。
	- [nvim-telescope/telescope-project.nvim](https://github.com/nvim-telescope/telescope-project.nvim)  
		vscのprojectManagerのように、ローカルgitリポジトリをリストしてくれます。  
		telescopeのextensionの1つです。(telescopeには多くのextensionがあります)
- [nvim-telescope/telescope-fzf-native.nvim](https://github.com/nvim-telescope/telescope-fzf-native.nvim)  
	telescopeの検索時に正規表現での検索をサポートしてくれるプラグインです
	- ファイル検索  
		[![telescope-ff.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/879669a9-467a-2786-d36b-5f796ba939a3.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F879669a9-467a-2786-d36b-5f796ba939a3.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=fb1a516bce854a12882e89173260eedc)
	- buffer内で字句検索(/でkeymapしてます)  
		[![telexcope-:-.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/8e160319-b2f8-37e8-85a4-8e8055cf4eaf.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F8e160319-b2f8-37e8-85a4-8e8055cf4eaf.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e232eedb2a9461a7d1bc2a7c2ad3784d)
	- workspace内で字句検索  
		[![telescope-fg.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/33e9dd77-cca6-b252-a8be-eb83b28adf78.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F33e9dd77-cca6-b252-a8be-eb83b28adf78.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=dbe7cdff40b5824178cc64571e739e1b)
	- colorscheme検索  
		[![telescope-fc.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/54d8de79-e2b0-7ec9-3454-09a9d94aafbc.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F54d8de79-e2b0-7ec9-3454-09a9d94aafbc.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=b1da4a3742df451948a3fb97f85ebb75)
	- `:Telescope planets` とコマンド実行すると天体観測できます（プラグイン名にちなんだ機能で粋ですね)  
		[![telescope-planet.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/048eabb4-2ecd-8444-3a5c-22021cf62321.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F048eabb4-2ecd-8444-3a5c-22021cf62321.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=d7373a78a44a702118e2cb49722fc16c)

## 8\. Git

- [lewis6991/gitsigns.nvim](https://github.com/lewis6991/gitsigns.nvim)  
	bufferの差分をハイライトしてくれたり、git blameを表示してくれたりとgit周りのインテリセンスとなるプラグインです。
	- ハイライトとgit blame  
		[![gitsigns.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/1f207a43-f480-bf70-d0c8-edba4654c102.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F1f207a43-f480-bf70-d0c8-edba4654c102.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=f309a2508c727b385b5e1dfe5cce2031)
	- 旧バージョンとの差分表示  
		[![gitsign2.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/5952bd3a-1642-5d0d-fd31-58bbd4919184.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F5952bd3a-1642-5d0d-fd31-58bbd4919184.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=7053a70255023f26e76b065de1018cfc)

## 9\. Util

- [FotiadisM/tabset.nvim](https://github.com/FotiadisM/tabset.nvim)  
	言語ごとにタブのインデント数を設定できます。
- [christoomey/vim-tmux-navigator](https://github.com/christoomey/vim-tmux-navigator)  
	windowの移動をtmux風にしてくれるプラグインです。
- [akinsho/toggleterm.nvim](https://github.com/akinsho/toggleterm.nvim)  
	terminalをtoggleしてくれるプラグインです。  
	また　terminalだけでなく、terminalで動作するTUIベースのアプリケーションもtoggleできます。  
	個人的に一番お世話になってるプラグインで全neovimユーザが使うべきだと思います。  
	[lazygit](https://github.com/jesseduffield/lazygit) や [lazydocker](https://github.com/jesseduffield/lazydocker) もtoggleすることが可能です。
	- terminalのtoggle  
		[![toggleterm.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/6ea9c6bd-5486-09af-c799-632a92c0bdaf.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F6ea9c6bd-5486-09af-c799-632a92c0bdaf.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=994b4ef8445b0fa2a668a26a47c855da)
	- lazygitのtoggle  
		[![togglelazygit.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/a489e991-a91d-0488-0f36-9c97e29f47fc.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2Fa489e991-a91d-0488-0f36-9c97e29f47fc.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=875d4381fb8863baa33cd2073f404ece)
	- lazydockerのtoggle  
		[![togglelazydocker.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/fc83c924-5c2f-25d1-0573-391cb8a61b22.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2Ffc83c924-5c2f-25d1-0573-391cb8a61b22.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=50f9e6027abd7612007891839e19fd7d)
- [kylechui/nvim-surround](https://github.com/kylechui/nvim-surround)  
	字句の囲い文字の追加・変更・削除を簡単にしてくれるプラグインです。  
	関数やタグでの囲いもできて便利です。  
	[![surround.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F3de90f86-0260-18f3-96c0-4ba826038598.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=475431e55379740b157ad1717a42ba63)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F3de90f86-0260-18f3-96c0-4ba826038598.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=475431e55379740b157ad1717a42ba63)
- [inkarkat/vim-ReplaceWithRegister](https://github.com/vim-scripts/ReplaceWithRegister)  
	選択した範囲をヤンクしたテキストで置換するためのプラグインです。  
	痒いところに手が届くプラグインです。  
	[![replaceregister.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2Fe863ef34-ca99-b747-d4ad-e9eced5e8d72.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=2b751c4dead1da685d8b1ef76ab57529)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2Fe863ef34-ca99-b747-d4ad-e9eced5e8d72.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=2b751c4dead1da685d8b1ef76ab57529)
- [numToStr/Comment.nvim](https://github.com/numToStr/Comment.nvim)  
	指定した範囲をコメントアウトしてくれるプラグインです。  
	[![comment.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F6c5b68d1-fc05-288f-9671-185e525e57ee.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=55ca4bab4a99836f72c288b63c01d96b)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F6c5b68d1-fc05-288f-9671-185e525e57ee.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=55ca4bab4a99836f72c288b63c01d96b)
- [windwp/nvim-autopairs](https://github.com/windwp/nvim-autopairs)  
	`()` や `{}` などの対になっている文字を補完してくれるプラグインです。
- [iamcco/markdown-preview.nvim](https://github.com/iamcco/markdown-preview.nvim)  
	markdownプレビュー用のプラグインです。  
	一時的にローカルにパーサ用のサーバが立つのでブラウザからプレビューを閲覧できます。
- [phaazon/hop.nvim](https://github.com/phaazon/hop.nvim)  
	neovimはf/Fでbuffer内の文字を検索してカーソルを移動できますが、その移動を楽にしてくれるプラグインです。  
	サンプルは、"foo"の箇所にカーソルをjumpさせたい時の動作です。  
	[![hop.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F2854e8f6-3d82-dc62-eb7f-7fb19eba3efd.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=37cabfc9aa8afe5a5c2c68ca227be61a)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F2854e8f6-3d82-dc62-eb7f-7fb19eba3efd.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=37cabfc9aa8afe5a5c2c68ca227be61a)
- [folke/zen-mode.nvim](https://github.com/folke/zen-mode.nvim)  
	みんな大好きzoenMode、neovimにもあります。  
	neovimは基本的にはUIシンプルになりがちですが、それでもすっきりしますね。  
	[![スクリーンショット 2023-12-31 18.02.12.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2947418/934e6b40-c792-12fd-4ddc-742e8c147304.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F934e6b40-c792-12fd-4ddc-742e8c147304.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=3d1fa0c09f6b0ce7ca35c7e6c0e32729)
- [Weissle/easy-action](https://github.com/Weissle/easy-action)  
	元々vimの方に [easy-motion](https://github.com/easymotion/vim-easymotion) という非常に有名で強力なプラグインがあったのですが、それのfolkです。  
	easy-actionはカーソルを動かさずに、操作したい場所と内容をコマンドで指定して遠隔でテキスト編集をするプラグインです。  
	サンプルでは、カーソルを動かさずに、"12345"の列をyank(してペースト)、"56789"の列をdd(してペースト)していいます。  
	[![ea.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2Fdf8e0981-72e8-f16b-f140-bc90e0e11c80.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=73c9b02a20f1789ab9e21ca7e6180045)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2Fdf8e0981-72e8-f16b-f140-bc90e0e11c80.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=73c9b02a20f1789ab9e21ca7e6180045)
- [magatti/auto-session](https://github.com/rmagatti/auto-session)  
	フォルダごとにsessionを記憶・復元するためのプラグインです。
- [jackMort/ChatGPT.nvim](https://github.com/jackMort/ChatGPT.nvim)  
	neovimからchatGPTを使うためのプラグインです。  
	サンプルはREADMEに豊富にあるので、そちらをご覧ください。めっちゃ便利です。
- [gcmt/wildfire.vim](https://github.com/gcmt/wildfire.vim)  
	テキスト選択を楽にしてくれるプラグインです。  
	`viw` とか打つ必要がなくなって結構便利です。  
	これも痒いところに手が届いて結構好きなプラグインの1つです。
- [gsuuon/note.nvim](https://github.com/gsuuon/note.nvim)  
	ちょっとしたメモを取る時に使ってます。  
	[![note.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F1eb98f2b-0cc0-5a3e-c008-f0939541b2ca.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=0349da48cf87de2478f8e8f4671b8228)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2947418%2F1eb98f2b-0cc0-5a3e-c008-f0939541b2ca.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=0349da48cf87de2478f8e8f4671b8228)

## 最後に

いかがでしたでしょうか。  
数えてみたら、全部で47個ありました。  
個人的お気に入りプラグインランキングは

- 1位： [akinsho/toggleterm.nvim](https://github.com/akinsho/toggleterm.nvim)
	- 多分一番使用頻度高いです
- 2位： [nvim-telescope/telescope.nvim](https://github.com/nvim-telescope/telescope.nvim)
	- 汎用性高すぎ
- 3位： [Weissle/easy-action](https://github.com/Weissle/easy-action)
	- なんかかっこいい

です！

何かのお役に立てれば幸いです！では！

[0](https://qiita.com/ysmb-wtsg/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fysmb-wtsg%2Fitems%2F79be0d97711eb49f5e82&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fysmb-wtsg%2Fitems%2F79be0d97711eb49f5e82&realm=qiita)

## Qiita Conference 2025 Autumn 11月5日(水)~7日(金)開催！

![](https://cdn.qiita.com/assets/public/official_campaigns/qiita_conference_2025_autumn/image-conference_2025_autumn_ogp-7cf3021de31b9ab76a7b3bbaf2909bb5.png)

Qiita Conferenceは、AI時代のエンジニアに贈るQiita最大規模のテックカンファレンスです！

基調講演ゲスト(敬称略)

piacere、牛尾 剛、Esteban Suarez、和田 卓人、seya、ミノ駆動、市谷 聡啓、からあげ、まつもとゆきひろ、みのるん

[イベント詳細を見る](https://qiita.com/official-campaigns/conference/2025-autumn)

[32](https://qiita.com/ysmb-wtsg/items/79be0d97711eb49f5e82/likers)

いいねしたユーザー一覧へ移動

22

[^1]: packerは2023年8月を以てリポジトリのメンテナンスが止まっています。代替として [lazy](https://github.com/folke/lazy.nvim) というパッケージマネージャが提示されています。