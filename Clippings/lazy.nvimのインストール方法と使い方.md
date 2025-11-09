---
title: "lazy.nvimのインストール方法と使い方"
source: "https://zenn.dev/siteyo/articles/980b6205e93914"
author:
  - "[[Zenn]]"
published: 2024-05-13
created: 2025-11-10
description:
tags:
  - "clippings"
---
59

9[tech](https://zenn.dev/tech-or-idea)

## はじめに

ありがたいことに、最近になって身の回りでVim/Neovimに興味を持つ人がポツポツと現れてきました。いままでエンジニアとして働いてきたなかで、興味を持ってくれる人や使っている人に出会ったことがなかったためとても嬉しい話です。  
さらにプラグインにも興味持ってもらえればな〜と思い、プラグインマネージャーのlazy.nvimの使い方を紹介していこうと思います。

lazy.nvimはNeovimで使用可能なプラグインマネージャーで、とても人気のあるプラグインです。筆者もこれを愛用しています。  
闇の力に手を染めるほどカスタムしたい訳ではないので、自分にはこのぐらいがいいのかなーと思っています。(Vimを使っていたときは [vim-plug](https://github.com/junegunn/vim-plug) を利用していました。)

他のプラグインマネージャーについては、以下の記事がとても参考になるので興味があれば一読をオススメします。

## lazy.nvimを使うために必要なもの

2024年5月現在、lazy.nvimを使うために必要なのは以下になります。

- Neovim >= 0.8.0
- Git >= 2.19.0
- [Nerd Font](https://www.nerdfonts.com/) (optional)

Nerd Fontについては必須ではないですが、導入すると見た目がかっこよくなるのでできれば対応しているフォントを導入してみましょう。

上記がインストールされていない場合は適宜インストールもしくはアップデートしてください。

## init.lua

次に `init.lua` にlazy.nvimのセットアップスクリプトを書いていきます。と、その前に。  
どのディレクトリに `init.lua` を置けばよいのか、どこの `init.lua` が読みこまれるのかわからない場合は以下のコマンドを実行してみてください。

```shell
nvim -V1 -es -c "echo stdpath('config') . '/init.vim'" -cq
```

表示されたパスの `init.lua` が読み込まれるので、そこにスクリプトを書いていきましょう。  
ファイルが存在しなければ、 `touch` コマンドなどを使ってファイルを作成します。

ちなみに、上記のコマンドについて解説すると、

- `-V1`: ログ出力レベルを設定します。1〜16まで設定でき、それぞれのレベルに応じた出力をします。([参照](https://neovim.io/doc/user/options.html#'verbose'))
- `-es`: スクリプトモード、サイレントモード、バッチモードなどと呼ばれているモードです。呼び名が多いですね。ExモードでNeovimを起動します。(筆者もあまり詳しくありませんが、UIなしでExコマンドを実行するモードと思っています。)
- `-c`: コマンドラインから直接Exコマンドを指定して実行します。こちらは `-es` とは異なりUIが起動します。
- `-cq`: `-c "q"` と等価です。

(上記の説明に誤りがあればコメントください。)

各オプションについて詳しくは以下を参照してください。

## lazy.nvimをインストールするスクリプトをinit.luaに書く

[README.md](https://github.com/folke/lazy.nvim/blob/main/README.md) に記載されている以下のスクリプトを `init.lua` に追加しましょう。

init.lua

```lua
local lazypath = vim.fn.stdpath("data") .. "/lazy/lazy.nvim"
if not (vim.uv or vim.loop).fs_stat(lazypath) then
  vim.fn.system({
    "git",
    "clone",
    "--filter=blob:none",
    "https://github.com/folke/lazy.nvim.git",
    "--branch=stable", -- latest stable release
    lazypath,
  })
end
vim.opt.rtp:prepend(lazypath)
```

このスクリプトにより、Neovim起動時に `lazypath` へlazy.nvimがインストールされているか確認するようになります。  
インストールされていなければ `git clone` を使って `lazypath` へlazy.nvimがインストールされます。

## プラグインのインストール

インストールしたいプラグインをスクリプトに追加していきます。  
例えば、 [folke/noice.nvim](https://github.com/folke/noice.nvim) というプラグインをインストールしたければ `init.lua` に以下のようにスクリプトを追加します。

init.lua

```lua
require("lazy").setup({
  -- ここに追加したいプラグインを書く。
  -- GitHubに公開されているプラグインなら、"ユーザー名/リポジトリ名"のフォーマットで記載する。
  "folke/noice.nvim",
})
```

追加したらNeovimを実行してみてください。上記の設定をNeovim上で実施していたなら、一度終了して再度実行してみてください。なにやらかっこいいUIが立ち上がって、プラグインがインストールされるはずです。  
他にも追加したいプラグインがあれば、上記のコードと同様に記載していけば起動のタイミングで自動インストールされます。どんどん追加していきましょう。

## プラグインごとの設定方法

ここまででlazy.nvimを使ったプラグインのインストール方法がなんとなくわかったと思います。  
さらにプラグインごとの設定を追加してみましょう。  
lazy.nvimでは [こちら](https://github.com/folke/lazy.nvim/blob/main/README.md#-plugin-spec) に記載されているPlugin Specにならって記載することでいろいろな設定ができるようになっています。  
どんな設定ができるのか筆者がよく使うものを紹介します。

## dependencies

先ほど設定したnoice.nvimですが、実はこれを動かすためには他にプラグインが必要なため単体では動作しません。 [nui.nvim](https://github.com/MunifTanjim/nui.nvim) を追加する必要があります。このように、プラグインを動作させるための前提になるプラグインがある場合には `dependencies` を使いましょう。

noice.nvimのREADMEではInstallationに以下のコードが記載されています。

```lua
-- lazy.nvim
{
  "folke/noice.nvim",
  event = "VeryLazy",
  opts = {
    -- add any options here
  },
  dependencies = {
    -- if you lazy-load any plugin below, make sure to add proper \`module="..."\` entries
    "MunifTanjim/nui.nvim",
    -- OPTIONAL:
    --   \`nvim-notify\` is only needed, if you want to use the notification view.
    --   If not available, we use \`mini\` as the fallback
    "rcarriga/nvim-notify",
    }
}
```

`dependencies` には、 `MunifTanjim/nui.nvim` と `rcarriga/nvim-notify` の2つのプラグインが記載されています。このように設定することで、nui.nvimとnvim-notifyはNeovim起動時にインストールされ、noice.nvimが読み込まれるタイミングでこの2つのプラグインがロードされるようになります。

これを先ほど設定したスクリプトに組み込むと、 `init.lua` は以下のようになります。

init.lua

```
require("lazy").setup({
- "folke/noice.nvim",
+ {
+   "folke/noice.nvim",
+   event = "VeryLazy",
+   opts = {
+     -- add any options here
+   },
+   dependencies = {
+     -- if you lazy-load any plugin below, make sure to add proper \`module="..."\` entries
+     "MunifTanjim/nui.nvim",
+     -- OPTIONAL:
+     --   \`nvim-notify\` is only needed, if you want to use the notification view.
+     --   If not available, we use \`mini\` as the fallback
+     "rcarriga/nvim-notify",
+     }
+ }
})
```

このようにプラグインを使うために他にロードが必要なプラグインがある場合は `dependencies` に設定しましょう。  
`dependencies` の使い方については以下の記事も参考にしてください。

## opts / config

プラグインが用意している設定を行いたいときは、 `opts` もしくは `config` を使います。

ほとんどのプラグインは `setup` という関数を用意していて、これを呼び出すことでプラグインが使用可能な状態になります。あわせて `setup` の引数にテーブルで設定項目を渡し、ユーザの好みに合わせてカスタマイズできるようになっているつくりもよく見る形式です。

`opts` に対してプラグインが用意しているオプションを設定すると、lazy.nvimの内部的に `setup(opts)` が呼び出されてオプションの内容がそのプラグインに反映されます。  
`setup` を呼び出すだけでなく、もっと複雑な設定を行いたいときには `config` を使用しましょう。 `config` には関数を設定でき、その関数の内部にセットアップで必要な処理を記載し、プラグインがロードされるタイミングで設定した関数が呼び出されます。  
また、プラグインに対して設定する項目がない(デフォルトの設定を使用したいだけの場合)は、 `config = true` とすることで内部的に `setp({})` が呼び出されてプラグインが設定されます。

`opts` と `config` は設定したい内容によって使い分けてみてください。

## event

Neovim起動時にすべてのプラグインを読み込むようにしていると、起動のたびに時間がかかってしまいストレスです。そのため最近のプラグインマネージャーでは遅延ロードが基本的には搭載されています。  
lazy.nvimのデフォルトの設定では遅延ロードがオフになっており、特になにも設定していなければ起動時に読み込みを行います。  
特定のタイミングで読み込むようにしたい場合は `event` を使います。  
例えばバッファを新規作成したタイミングでプラグインをロードしたいのであれば、 `event = "BufNewFile` と設定する必要があります。インサートモードになったときにプラグインを読み込むのであれば、 `event = "InsertEnter"` です。  
複数の `event` を設定することももちろん可能で、その場合は `event = { "BufNewFile", "InsertEnter" }` のように設定します。  
これを設定することで、そのイベントが実行されるまでプラグインはロードされません。どのようなイベントがあるのかについては以下を参照してください。

また、lazy.nvimはいくつか独自のイベントを追加しています。こちらも見ておくとよいです。なかでもよく使うのは `VeryLazy` (VimEnter相当？)です。

## ft / cmd / keys

他にも特定のファイルタイプのファイルを開いたとき、特定のコマンドを実行したとき、特定のキー入力があったときをトリガーにプラグインを遅延ロードすることもできます。  
それぞれ `ft` 、 `cmd` 、 `keys` を使って設定でさます。

この中で特に `keys` は便利で、こちらに設定をすることでマッピングと遅延ロードの設定が1つの設定で可能になります。  
どういうことかというと、lazy.nvimでは例として以下のコードがREADMEに掲載されています。

```lua
-- Example for neo-tree.nvim
{
  "nvim-neo-tree/neo-tree.nvim",
    keys = {
      { "<leader>ft", "<cmd>Neotree toggle<cr>", desc = "NeoTree" },
    },
    config = function()
      require("neo-tree").setup()
    end,
}
```

この場合、 `<Leader>ft` が入力されたタイミングでneo-tree.nvimが読み込まれるようになります。  
かつ、 `<Leader>ft` に `<cmd>neotree toggle<cr>` というマッピングを設定しています。

`event` とあわせて使って、必要なタイミングで必要なプラグインを読み込むようにすることで、時々で不要なプラグインを読み込まずにスピーディーにNeovimを起動できるようになります。

## 設定ファイルの分割

これまでは `init.lua` にすべての設定を記載してきましたが、使用するプラグインの数が100や200となっていくとファイルが非常に大きく見通しが悪いものになってしまいます。  
気にならない方はそれでも大丈夫ですが、分割したいという人も一定数いると思います。

lazy.nvimはデフォルトで `~/.config/nvim/lua/plugins/*.lua` に格納されているLuaファイルは自動で読み込むようになっています。  
そのため、こちらに分割したい単位でファイルを作成し、Plugin Specの形式でテーブルを `return` するように記載することで分割することができます。

また、特定のディレクトリ配下のLuaファイルを読み込むようにしたい場合は、 `require("lazy").setup({ spec = {}})` を使いましょう。  
例えば、筆者は `spec = { { import = "config.plugins" } }` と設定しています。これににより `~/.config/nvim/lua/config/plugins/*.lua` が読み込まれるようになります。

詳細は以下のセクションを確認してください。

## 使い方

ここではlazy.nvimのコマンドについて説明します。

## :Lazy /:Lazy home

`:Lazy` もしくは `Lazy home` を実行すると、lazy.nvimのUIが起ち上がります。  
この画面では、どのプラグインがロードされているのか、ロードされていないのか、いつロードされる設定になっているのかなどの情報を確認できます。

また、上部に `Install (I)` の括弧内に記載されている文字を入力すると、その画面に遷移して各コマンドが実行されます。

## :Lazy install

`:Lazy install` を実行すると、インストールされていないプラグインをインストールします。  
他の画面からこちらに遷移してきた場合も同様にインストールを試みます。

また、プラグインをインストールすると同時に `lazy-lock.json` というファイルが生成されます。  
こちらには、インストールしているプラグインのコミットハッシュが記録されています。  
`:Lazy restore` コマンドを実行した際に、このファイルを参照して現在インストールされているバージョンと `lazy-lock.json` のバージョンが異なる場合は、 `lazy-lock.json` に記載されているバージョンに戻す動作をします。

## :Lazy update /:Lazy check

`:Lazy update` を実行すると、プラグインのアップデートを行います。  
`:Lazy check` ではプラグインの更新情報だけを取得し、アップデートは行いません。

## :Lazy clean

`:Lazy clean` を実行すると、使用していないプラグインを削除します。  
使用していないプラグインになにがあるのかは、`:Lazy home` のClean欄で確認できます。

## まとめ

lazy.nvimの使い方について説明しました。  
かなりのところを端折ってしまっているので、もっと使い方を知りたい場合はリポジトリのREADMEを読んでみてください。

59

9

59

9