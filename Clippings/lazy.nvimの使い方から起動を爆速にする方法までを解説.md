---
title: "lazy.nvimの使い方から起動を爆速にする方法までを解説"
source: "https://eiji.page/blog/neovim-lazy-nvim-intro/"
author:
  - "[[eiji.page]]"
published: 2024-12-11
created: 2025-11-08
description: "ソフトウェアに関する備忘録を書く個人ブログです！"
tags:
  - "clippings"
---
Neovimのプラグインマネージャー [lazy.nvim](https://github.com/folke/lazy.nvim) について解説します。

基本的な使い方はもちろん、おすすめの **ディレクトリ構成の例** や遅延読み込みによる **起動速度の高速化** についても紹介します。

## lazy.nvimの導入

最終的に目指すのは次のようなディレクトリ構成です。

```txt
.config/nvim/
├── init.lua // おなじみの
├── lazy-lock.json // 自動で生成される
├── lua
│   ├── config
│   │   ├── ...
│   │   └── lazy.lua // lazy.nvimの設定
│   └── plugins // 各プラグインの設定
│       ├── bufferline.lua
│       ├── dial.lua
│       ├── ...
│       └── winresizer.lua
```

全部の設定を1つのファイルに書きたい人は [公式ドキュメント](https://lazy.folke.io/installation) を読みましょう。

### init.luaに書く内容

まず、 `init.lua` に次のように書きます。

```lua
require("config.lazy")
```

これは「`./config/nvim/lua/config/lazy.lua` を読み込んで！」という処理です。

ファイル名はあくまでも例です。`./config/nvim/lua/fooooooo/barrrrr.lua` のように書きたいなら `require("fooooooo.barrrrr")` と書きましょう。

長いのでこの記事では `lazy.lua` と呼びます。

### lazy.luaに書く内容

`lazy.lua` には次のように書きます。

```lua
local lazypath = vim.fn.stdpath("data") .. "/lazy/lazy.nvim"
if not (vim.uv or vim.loop).fs_stat(lazypath) then
  local lazyrepo = "https://github.com/folke/lazy.nvim.git"
  local out = vim.fn.system({ "git", "clone", "--filter=blob:none", "--branch=stable", lazyrepo, lazypath })
  if vim.v.shell_error ~= 0 then
    vim.api.nvim_echo({
      { "Failed to clone lazy.nvim:\n", "ErrorMsg" },
      { out, "WarningMsg" },
      { "\nPress any key to exit..." },
    }, true, {})
    vim.fn.getchar()
    os.exit(1)
  end
end
vim.opt.rtp:prepend(lazypath)

require("lazy").setup({
  spec = {
    -- ここを次の項目で変更します
  },
})
```

いきなり行数が増えましたが、やっていることは単純で「lazy.nvimのダウンロード」と「プラグインの読み込み」です。  
このスクリプトを書くことで、Neovim起動時にlazy.nvimがダウンロードされていなければ **自動でlazy.nvimをダウンロード** してくれます。

## プラグインを読み込む方法

プラグインを読み込む方法はいくつかあります。

たとえば次のように書くと、 [https://github.com/machakann/vim-highlightedyank](https://github.com/machakann/vim-highlightedyank) をプラグインとして管理できます。

```lua
require("lazy").setup({
  spec = {
    { "machakann/vim-highlightedyank" },
    { "foo/bar" },
    { "piyo/buz" },
    -- ...
  },
})
```

`{ "ユーザー名/リポジトリ名" }` を書いていくだけで、Neovim起動時に **lazy.nvimが自動でプラグインをインストール** してくれます。

プラグインが少ない場合はこの `spec` の中にプラグイン名を羅列すればよいですが、プラグイン数が多くなると `lazy.lua` が長くなってしまいます。そこで、 **プラグイン毎に設定ファイルを分ける方法** を解説します。

### ディレクトリをまとめて読み込む

`spec` を次のように書くと、 `~/.config/nvim/lua/plugins` ディレクトリにあるファイルをまとめて読み込んでくれます。

```lua
require("lazy").setup({
  spec = {
    { import = "plugins" },
  },
})
```

つまり、次のような `bufferline.lua` 、 `dial.lua` 、 `winresizer.lua` が全部読み込まれるということです。

```txt
.config/nvim/
├── lua
│   ├── ...
│   └── plugins
│       ├── bufferline.lua
│       ├── dial.lua
│       ├── ...
│       └── winresizer.lua
├── ...
```

#### 各プラグインのファイルの書き方

`~/.config/nvim/lua/plugins/` にあるファイルは、次のように `return` の中に書きます。

```lua
return {
  "ユーザー名/リポジトリ名",
  -- ここに設定を書いていく。後で解説！
}
```

実際に筆者が使っている [nightfox.nvim](https://github.com/EdenEast/nightfox.nvim) の例を見てみましょう。

```lua
return {
  "EdenEast/nightfox.nvim",
  config = function()
    vim.cmd("colorscheme terafox")
    vim.cmd("highlight! link WinSeparator GlyphPalette2")
    vim.cmd("highlight! Visual guibg=#4a3332")
  end,
}
```

おやおや、 `config` なるものが出てきましたね。他に `build` や `event` などいくつかプロパティがありますが、これらについては **後で解説** します。

### トラブル対応しやすいように読み込む

前述のようにディレクトリをまとめて読み込む方法は書くのが楽です。ただ、 **何か問題が起きた時にどのプラグインが原因なのか特定しにくい** です。

そこで、 **トラブルが発生しても特定しやすく** かつ **柔軟に設定を分割できる方法** を紹介します。

lazy.luaにて、次のように `import` でひとつずつファイルを指定します。

```lua
require("lazy").setup({
  spec = {
    { import = "plugins.nightfox" },
    { import = "plugins.bufferline" },
    { import = "plugins.dial" },
    { import = "plugins.lsp.index" },
    -- ...
    { "vim-jp/vimdoc-ja" },
  },
})
```

たとえば、 `plugins.nightfox` は `~/.config/nvim/lua/plugins/nightfox.lua` を指します。  
ファイル名は自分が分かればプラグイン名と一致しなくても問題ありません。

```txt
.config/nvim/
├── lua
│   ├── ...
│   └── plugins
│       ├── bufferline.lua
│       ├── dial.lua
│       ├── lsp
│       │   ├── ...
│       │   └── index.lua
│       ├── ...
│       └── winresizer.lua
├── ...
```

設定が必要なかったり行数が少ない場合は、ファイルを分けないほうが楽です。例にある `vim-jp/vimdoc-ja` のように `spec` の中に書いちゃいましょう。

```lua
require("lazy").setup({
  spec = {
    { import = "plugins.nightfox" },
    -- ...
    { "vim-jp/vimdoc-ja" },
  },
})
```

#### トラブルシューティングの方法

問題が発生してどのプラグインなのか特定したいときに **二分探索できます** 。半分ずつコメントアウトして原因を突き止めるのです。

コメントアウトしたらトラブルが解消された場合、その中に問題のあるプラグインが存在していることになります。

```lua
require("lazy").setup({
  spec = {
    { import = "plugins.nightfox" },
    { import = "plugins.bufferline" },
    { import = "plugins.dial" },
    -- 半分ずつコメントアウト
    -- { import = "plugins.foo" }, -- こいつがトラブルメーカー
    -- { import = "plugins.lsp.index" },
    -- { "vim-jp/vimdoc-ja" },
  },
})
```

#### 1つファイルに複数のプラグインをまとめて書く

再び `plugins` ディレクトリに書くプラグインファイルについての解説に戻ります。

1つのファイルに複数のプラグインをまとめて書けます。

```lua
return {
  { "foo/bar" },
  { "piyo/buz" },
}
```

次の例では、treesitter関係の2つのプラグインをまとめて書いています。

```lua
return {
  {
    "nvim-treesitter/nvim-treesitter",
    build = ":TSUpdate",
    config = function()
      -- ...
    end,
  },
  {
    "nvim-treesitter/nvim-treesitter-context",
    dependencies = { "nvim-treesitter/nvim-treesitter" },
    event = { "BufNewFile", "BufRead" },
    opts = {
      -- ...
    },
  },
}
```

のちほど解説しますが、筆者はLSPや自動補完に関するプラグインの読み込みをそれぞれ1つのファイルにまとめています。

## 依存関係の指定

「プラグイン **A** の中では別のプラグイン **B** が使われている。AはBに依存しているんだ！」  
そんなケースでは、 `dependencies` にそのプラグイン名を書きます。

```lua
return {
  "foo/plugin-a",
  dependencies = { "piyo/plugin-b" },
  -- ...
}
```

プラグイン **B** はプラグイン **A** が読み込まれる前に読み込まれます。

このプラグイン **B** が他の場所で使われていないのであれば、 `dependencies` に書くだけで問題ありません。

```lua
return {
  {
    "foo/plugin-a",
    dependencies = { "piyo/plugin-b" },
  }
  { "piyo/plugin-b" }, -- この記述は不要
}
```

次の例では [noice.nvim](https://github.com/folke/noice.nvim) について書いています。

```lua
return {
  "folke/noice.nvim",
  dependencies = {
    "MunifTanjim/nui.nvim",
    "rcarriga/nvim-notify"
  },
}
```

## プラグインの設定方法

いよいよプラグインの設定の書き方です。

### Luaのプラグインの場合

Luaで書かれたプラグインのREADMEにはだいたい「 `require("foo").setup({})` を呼び出してね」と書いてありますが……。

**lazy.nvimの `opts` を使えば、 `require` の記述は不要** です。 `setup` に渡す引数を `opts` に書けば設定できます。

```lua
return {
  "petertriho/nvim-scrollbar",
  event = { "VeryLazy" },
  opts = {
    excluded_filetypes = {
      "prompt",
      "TelescopePrompt",
      "noice",
      "LspsagaHover",
    },
  },
}
```

lazy.nvimが自動で `require("foo").setup` の引数として渡し、実行してくれます。

```lua
require("scrollbar").setup({
  excluded_filetypes = {
    "prompt",
    "TelescopePrompt",
    "noice",
    "LspsagaHover",
  },
})
```

#### 複雑な設定を書きたい場合

`setup` の呼び出し以外にも設定が必要な場合は `config` に書きましょう。

```lua
return {
  "windwp/nvim-autopairs",
  event = "InsertEnter",
  config = function()
    local npairs = require("nvim-autopairs")
    local Rule = require("nvim-autopairs.rule")
    npairs.setup({
      -- カーソルの後ろに何があってもautopairsを有効にする
      ignored_next_char = "",
    })
    npairs.add_rules({
      Rule("\`\`\`", "\`\`\`", { "mdx" }),
      -- デフォルトだと "not_add_quote_inside_quote" により
      -- クオートの中でautopairできないので自前で定義にする
      Rule('"', '"'),
      Rule("'", "'"),
    })
  end,
}
```

`config` を使う場合は **自分で `require("foo").setup` を呼び出す必要があります** 。

### Vimプラグインの場合

Vimプラグインの場合、設定は `config` か `init` に書きます。

たいていのVimプラグインは **`init` で設定したほうがいい** でしょう。そうしないと設定したグローバル変数などをプラグインで読み込めないケースがあります。

```lua
return {
  "simeji/winresizer",
  keys = { "<C-e>", mode = { "n" } },
  init = function()
    vim.g.winrisizer_vert_resize = 1
    vim.g.winresizer_horiz_resize = 1
  end,
}
```

### ビルドが必要なプラグイン

ビルドが必要な場合、 `build` に指定できます。そういうプラグインはだいたいREADMEに書いてあるので、 `build` のことは頭の片隅にどうぞ。

```lua
return {
  "iamcco/markdown-preview.nvim",
  build = function()
    vim.fn["mkdp#util#install"]()
  end,
  -- ...
}
```

## プラグインのアップデート方法

`:Lazy` で立ち上がる画面にて `U` を入力するか、`:Lazy update` でプラグインをアップデートできます。

## プラグインのアンインストール方法

プラグインをアンインストールしたい場合は、「設定を書いた `{}` 」や「ファイルと `importの部分` 」を削除します。

```lua
return {
  "ユーザー名/リポジトリ名",
  -- 設定
}
```

```lua
require("lazy").setup({
  spec = {
    { import = "plugins.ファイル名" }, -- 別のファイルに切り出していた場合
    { "ユーザー名/リポジトリ名" }, -- 直接lazy.nvimに書いていた場合
    { "foo/bar" },
    { "vim-jp/vimdoc-ja" },
  },
})
```

最後に、`:Lazy` で立ち上がる画面にて `X` を入力します。または`:Lazy clean` を実行します。

## 遅延読み込みで爆速起動

プラグインを遅延読み込みさせることで、Neovimの起動がめっちゃ速くなります。  
筆者のNeovimは改善前が0.452秒、 **改善後が0.044秒** でした。やってみると体感でも速さを感じられます。 **せっかくなら速いほうがいい** ですよね。

### 起動にかかった時間を調べよう

実際に起動速度を測ってみましょう。

次のコマンドで起動の処理時間などが `start.log` に記録されます。

```sh
nvim --startuptime start.log
```

最後の行の1列目が起動にかかった時間です。例では `054.547` ミリ秒です。

```txt
--- Startup times for process: Primary/TUI ---

times in msec
clock   self+sourced   self:  sourced script
clock   elapsed:              other lines

000.001  000.001: --- NVIM STARTING ---
000.095  000.094: event init
// ...
054.384  000.113: opening buffers
054.450  000.065: BufEnter autocommands
054.453  000.004: editing files in windows
054.547  000.093: --- NVIM STARTED ---
```

参考： [Vimのstartuptime出力結果の読み方 - rbtnn雑記](https://rbtnn.hateblo.jp/entry/2022/01/29/005732)

### 遅延読み込みの方法

プラグインがどのタイミングで必要になるのかを記載することで、遅延読み込みできます。

#### ft

次のように `ft` にファイルタイプを指定すると、そのファイルタイプが開かれるときにプラグインが読み込まれます。

```lua
return {
  "MeanderingProgrammer/render-markdown.nvim",
  dependencies = { "nvim-treesitter/nvim-treesitter", "nvim-tree/nvim-web-devicons" },
  ft = { "markdown", "mdx" },
  opts = {
    render_modes = true,
    heading = {
      width = "block",
      left_pad = 0,
      right_pad = 4,
      icons = {},
    },
    code = {
      width = "block",
    },
  },
}
```

`ft = "markdown"` のようにリストではなく文字列でもかまいません。

#### cmd

次のように `cmd` にコマンドを指定すると、そのコマンドが実行されるときにプラグインが読み込まれます。

```lua
return {
  "folke/trouble.nvim",
  cmd = "Trouble",
  opts = {},
}
```

`cmd = {"Foo", "Bar"}` のようにリストでも指定できます。

`cmd` に書いたコマンドは、プラグイン読み込み前でも補完に表示されます。

![cmdに書いたコマンドの例](https://storage.eiji.page/neovim-lazy-nvim-intro/lazy-cmd.avif)

画像の例では、プラグイン [trouble.nvim](https://github.com/folke/trouble.nvim) 読み込み前でも`:Trouble` が補完候補に表示されている様子です。

#### keys

`keys` では **キーマップと遅延読み込みが同時に設定** できます。

次の例は、 `<leader>w` のキーマップを設定しています。また、 `<leader>w` が呼び出されたらプラグインを読み込むように指定しています。

```lua
return {
  "bkad/CamelCaseMotion",
  keys = {
    { "<leader>w", "<Plug>CamelCaseMotion_w", mode = { "n", "x" }, silent = true },
    { "<leader>b", "<Plug>CamelCaseMotion_b", mode = { "n", "x" }, silent = true },
    { "<leader>e", "<Plug>CamelCaseMotion_e", mode = { "n", "x" }, silent = true },
    { "<leader>ge", "<Plug>CamelCaseMotion_ge", mode = { "n", "x" }, silent = true },
  },
}
```

##### keysの書き方

keysの中身は次のように書きます。

```lua
{ "key", "実行されるコマンド", オプション…… }
```

オプション `mode` を書かない場合はノーマルモードのみにマップされます。具体的な文字列の指定方法は[:help map-table](https://neovim.io/doc/user/map.html#map-table) を読みましょう。

オプション `ft` でfiletypeごとのキーマップを設定できます。

その他のオプションは `vim.keymap.set` と同じです。

##### いろいろなkeyの書き方

プラグイン側でキーマップを設定してくる場合はコマンドなしで書きます。

```lua
{ "key", オプション…… }
```

プラグイン [simeji/winresizer](https://github.com/simeji/winresizer) を例にあげます。このプラグインは `<C-e>` を設定してきます。プラグインの読み込みを `<C-e>` 入力時に設定するには次のように書きます。

```lua
return {
  "simeji/winresizer",
  keys = { "<C-e>" },
  init = function()
    vim.g.winrisizer_vert_resize = 1
    vim.g.winresizer_horiz_resize = 1
  end,
}
```

次のように、各keyの2番目にはコマンドではなく **luaの関数** も書けます。

```lua
return {
  "monaqa/dial.nvim",
  keys = {
    {
      "<C-a>",
      function()
        require("dial.map").manipulate("increment", "normal")
      end,
      mode = "n",
    },
    -- ...
  },
  -- ...
}
```

#### event

次のように `event` を指定すると、そのイベント発火時にプラグインが読み込まれます。

```lua
return {
  "windwp/nvim-autopairs",
  event = "InsertEnter",
  config = function()
    local npairs = require("nvim-autopairs")
    local Rule = require("nvim-autopairs.rule")
    npairs.setup({
      -- カーソルの後ろに何があってもautopairsを有効にする
      ignored_next_char = "",
    })
    npairs.add_rules({
      Rule("\`\`\`", "\`\`\`", { "mdx" }),
      -- デフォルトだと "not_add_quote_inside_quote" により
      -- クオートの中でautopairできないので自前で定義にする
      Rule('"', '"'),
      Rule("'", "'"),
    })
  end,
}
```

Neovimで定義されているイベントの一覧は[:help events](https://neovim.io/doc/user/autocmd.html#events) から探せます。

##### event VeryLazy

「起動してすぐ必要ではないけど、起動してちょっとしたら読み込んでほしいなぁ」という願いを叶えるのが `VeryLazy` eventです。

```lua
return {
  "delphinus/auto-cursorline.nvim",
  event =  "VeryLazy" ,
  config = function()
    require("auto-cursorline").setup()
    vim.api.nvim_create_autocmd("FileType", {
      group = "my_nvim_rc",
      pattern = { "TelescopePrompt", "TelescopeResults", "gitblame", "css" },
      callback = function()
        vim.b.auto_cursorline_disabled = true
      end,
    })
  end,
}
```

`VeryLazy` といっても **感覚としてはすぐ読み込まれます** 。 `VeryLazy` はlazy.nvimが定義してくれているイベントです。

その他のlazy.nvimのイベントは`:help lazy.nvim-🚀-usage-📆-user-events` を参照してください（絵文字つきなのすごい）。

#### lazy=trueって何？

`lazy = true` を指定すると、そのプラグインが呼び出されるまでは読み込まれません。

```lua
-- ...
require("lazy").setup({
  spec = {
    { "nvim-lua/plenary.nvim", lazy = true },
    -- ...
  },
})
```

いろんなプラグインで使われているライブラリのような位置付けの **Luaプラグイン** であれば、毎回 `dependencies` に書かずとも `lazy = true` で1行の記述だけですみます。 [plenary.nvim](https://github.com/nvim-lua/plenary.nvim) や [nvim-web-devicons](https://github.com/nvim-tree/nvim-web-devicons) などです。

### 実際に遅延読み込みされているか確認する方法

遅延読み込みが想定どおりに動いたのかどうかは`:Lazy` コマンドで確認できます。

`Loaded` のプラグイン名の右を見ればどのタイミングで読み込まれたのか分かります。

![すでに読み込まれたプラグインの一覧](https://storage.eiji.page/neovim-lazy-nvim-intro/lazy-loaded.avif)

画像の例では `auto-cursorline.nvim` が `VeryLazy` eventのタイミングで読み込まれたのが分かります。  
`cellwidths.nvim` の `start` は「遅延読み込みせずNeovim起動時に読み込まれた」ということです。

一方、読み込まれていないプラグインは `Not Loaded` に表示されます。

![まだ読み込まれていないプラグインの一覧](https://storage.eiji.page/neovim-lazy-nvim-intro/lazy-not-loaded.avif)

画像では `actions-preview.nvim` を `lazy=true` にしており、プラグイン名の右には何も表示されていません。

## 設定が大きくなったら分割してみる

LSPや自動補完、ステータスラインなどのプラグインは行数が多くなりがちです。  
ここでは **LSPの設定をそこそこ分割** している筆者の構成を紹介します。

筆者は `plugins` の中に `lsp` というディレクトリを作り、そこにLSP関連の設定をまとめています。

```txt
.config/nvim/
├── lua
│   └── plugins
│       ├── lsp
│       │   ├── ...
│       │   └── lspsaga.lua
│       │   └── format.lua
│       │   └── index.lua
│       ├── ...
├── ...
```

`lazy.lua` では `plugins.lsp.index` をimportに指定します。

```lua
require("lazy").setup({
  spec = {
    { import = "plugins.lsp.index" },
    -- ...
    { "vim-jp/vimdoc-ja" },
  },
})
```

`index.lua` にLSP関係のプラグインの読み込みをまとめています。各プラグインの設定はほとんど別のファイルに書き、 `require` や `import` しています。

```lua
return {
  {
    "neovim/nvim-lspconfig",
    event = { "BufReadPost", "BufNewFile" },
    cmd = { "LspInfo", "LspInstall", "LspUninstall" },
    dependencies = {
      { "williamboman/mason.nvim" },
      { "williamboman/mason-lspconfig.nvim" },
      { "b0o/schemastore.nvim" },
      { "hrsh7th/cmp-nvim-lsp" },
    },
    config = function()
      require("plugins.lsp.diagnostic")
      require("plugins.lsp.format")
      require("plugins.lsp.attach")
      require("plugins.lsp.code-actions")
      require("plugins.lsp.server-register")
    end,
  },
  { import = "plugins.lsp.lspsaga" },
  { import = "plugins.lsp.conform" },
  { import = "plugins.lsp.none-ls" },
  { import = "plugins.lsp.actions-preview" },
  {
    -- Neovimの設定をluaを書きやすくなる
    "folke/lazydev.nvim",
    ft = "lua",
    opts = {
      library = {
        { path = "${3rd}/luv/library", words = { "vim%.uv" } },
      },
    },
  },
}
```

`import` ではプラグインの設定を `return` していましたね。 `require` では単純に処理を読み込むだけです。

```lua
vim.api.nvim_create_autocmd("LspAttach", {
  group = "my_nvim_rc",
  callback = function(ev)
    local bufopts = { noremap = true, silent = true, buffer = ev.buf }
    vim.keymap.set("n", "gr", "<cmd>Telescope lsp_references<CR>", bufopts)
    vim.keymap.set("n", "[d", "<cmd>Lspsaga diagnostic_jump_prev<CR>", bufopts)
    -- ...
  end,
})
```

気になる人は [記事執筆時のdotfiles](https://github.com/eetann/dotfiles/tree/063c2e400f545b5ac31569cb91f58aa9a023ccf5/.config/nvim/lua/plugins/lsp) をどうぞ。

## Specっていうんだよ

今まで `return { "ユーザー名/リポジトリ名" }` のように書いていた `{}` のまとまりはSpecという名前があるようです。実は `dependencies` も「SpecまたはSpecのリスト」として書けるため、次のように `dependencies` 内で設定可能です。

```lua
return {
  "folke/noice.nvim",
  event = "VeryLazy",
  dependencies = {
    "MunifTanjim/nui.nvim",
    { "rcarriga/nvim-notify", opts = { top_down = false, stages = "static" } },
  },
  config = function()
    -- ...
  end,
}
```

## 公式リソースと参考リンク

最後に一次情報をまとめておきます。

- `:help lazy.nvim.txt`
- GitHub： [folke/lazy.nvim](https://github.com/folke/lazy.nvim)
- 公式ドキュメント： [🚀 Getting Started | lazy.nvim](https://lazy.folke.io/)

`dependencies` や `lazy = true` について深堀りすると読む気が無くなってしまうと考えて解説を短くしたため、気になる方は調べてみてください。

ちなみに名前が超絶似ている [LazyVim](https://www.lazyvim.org/) はlazy.nvimの作者folke氏が作ったNeovimのフレームワークです。

---

今まではREADMEにある設定をコピペしてちょろっと変えたり、オレオレファイル分割でした。

今回の作業で速度やディレクトリ構成を改善できてうれしいです。突き詰めればもっと速くできそうです。