---
title: "Neovim のプラグインマネージャー lazy.nvim をインストールしてプラグインを入れる"
source: "https://www.sambaiz.net/article/500/"
author:
  - "[[sambaiz]]"
published: 2024-10-20T21:11:00JST
created: 2025-11-10
description: "lazy.nvim は 2022年の11月にリリースされた nvim のプラグインマネージャー。高速に起動しプラグインは nvim 起動時に自動でインストールされる"
tags:
  - "clippings"
---
[lazy.nvim](https://github.com/folke/lazy.nvim) は 2022年の11月に nvim のプラグインマネージャー。高速に nvim が起動しプラグインは自動でインストールされる。

次のスクリプトで lazy.nvim を [インストール](https://lazy.folke.io/installation) する。 Lua のパッケージマネージャー [luarocks](https://luarocks.org/) が必要。

```lua
$ brew install luarocks
$ cat ~/.config/nvim/init.lua
-- Bootstrap lazy.nvim
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

-- Make sure to setup \`mapleader\` and \`maplocalleader\` before
-- loading lazy.nvim so that mappings are correct.
-- This is also a good place to setup other settings (vim.opt)
vim.g.mapleader = " "
vim.g.maplocalleader = "\\"

-- Setup lazy.nvim
require("lazy").setup({
  spec = {
    -- add your plugins here
  },
  -- Configure any other settings here. See the documentation for more details.
  -- colorscheme that will be used when installing plugins.
  install = { colorscheme = { "habamax" } },
  -- automatically check for plugin updates
  checker = { enabled = true },
})
```

nvim を起動するとスクリプトが実行されてリポジトリが clone される。

問題なく動いているか確認する。

:Lazy で UI が開く。

[![UI](https://www.sambaiz.net/article/500/images/ui_hu_6ecb26c39705b4d6.png)](https://www.sambaiz.net/article/500/images/ui.png)

fuzzy finder である [telescope.nvim](https://github.com/nvim-telescope/telescope.nvim) を入れる。 直接書くか ~/.config/nvim/lua/plugins ディレクトリの [lua ファイル](https://github.com/LazyVim/LazyVim/blob/main/lua/lazyvim/plugins/treesitter.lua) に書いて import する。 [live grep のため](https://github.com/nvim-telescope/telescope.nvim?tab=readme-ov-file#suggested-dependencies) に ripgrep もインストールする。

```lua
spec = {
  {
    'nvim-telescope/telescope.nvim', tag = '0.1.8',
    dependencies = { 'nvim-lua/plenary.nvim' } -- & brew install ripgrep for live grep
  }
  -- { import = "plugins" },
},
```

:Telescope find\_files でプレビューと共にファイルを検索して開ける。C-t で新しいタブで開く。

[![Telescope](https://www.sambaiz.net/article/500/images/telescope_hu_f375fe6aa00a4fc.png)](https://www.sambaiz.net/article/500/images/telescope.png)

キーマップを設定する。

```lua
vim.g.mapleader = " "

local builtin = require('telescope.builtin')
vim.keymap.set('n', '<leader>ff', builtin.find_files, { desc = 'Telescope find files' })
vim.keymap.set('n', '<leader>fg', builtin.live_grep, { desc = 'Telescope live grep' })
```

colorscheme を設定したり [lualine.nvim](https://github.com/nvim-lualine/lualine.nvim) を入れてモードなどが下に表示されるようにしたり、 [neo-tree.nvim](https://github.com/nvim-neo-tree/neo-tree.nvim) で File explorer を表示したり、Copilot がはたらくようにする。

[![lualine.nvim と neo-tree.nvim](https://www.sambaiz.net/article/500/images/lualine_hu_db5b85558fe15e5e.png)](https://www.sambaiz.net/article/500/images/lualine.png)

## 参考

[プラグインマネージャーの歴史と新世代のプラグインマネージャー dpp.vim](https://zenn.dev/shougo/articles/dpp-vim-beta)

[Neovim環境構築とおすすめプラグイン - SunLTechLogs](https://techblog.sunl.jp/neovim-setup/)