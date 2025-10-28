---
title: "Neovim で VS Code みたいにコーディングする"
source: "https://zenn.dev/forcia_tech/articles/202411_deguchi_neovim"
author:
  - "[[Zenn]]"
published: 2024-11-22
created: 2025-09-26
description:
tags:
  - "clippings"
---
114

9[tech](https://zenn.dev/tech-or-idea)

はじめまして、新卒1年目エンジニアの出口です。

私は以前 [Visual Studio Code](https://code.visualstudio.com/) (VS Code) を使ってプログラムを書いていました。  
VS Code はインストールしたらすぐに様々な言語でコーディングを始めることができ、便利です。  
ただ、VS Code の統合ターミナル上のシェルと、VS Code のキーボードショートカットが干渉してしまうことが多い点では不便だったため、 [Neovim](https://neovim.io/) に移行しました。

移行してみてしばらく経ち、さほど不満は出てこなかったので、Neovim で開発することで感じたメリットと、VS Code から体験を落とさずに Neovim で開発するために使っているプラグインを記したいと思います。

## Neovim とは

> hyperextensible Vim-based text editor

([https://neovim.io/](https://neovim.io/) より)

ターミナル上で動作する (TUI の)、Vim から派生して作られたテキストエディタです。

Vim との最も大きな違いは、Vim script に加えて Lua プラグインをサポートしていることです。  
Neovim では旧来からの Vim プラグインも使えますし、Neovim 専用の Lua プラグインも使えます。

## Neovim (Vim) を使うメリット

VS Code みたいに開発がしたいだけであれば VS Code を使えばよいというのはもっともですが、Vim ならではの多くのメリットもあると感じています。

私が使っているのは Neovim ですが、多くの点は Vim にも共通しています。

### キーボードによる操作体系

すべての操作がキーボードでできます。  
手の移動量が少なくなるという点も素敵ですが、マウス不要なことがラップトップと相性が良く嬉しいです。

また、カーソルキーを連打しなくても一気に移動できますし、count やマクロによって繰り返しの操作が直感的にできるようになっています。  
他にも、カッコやクォート内の文字列のコピーや変更が素早くできるのも嬉しいポイントです。

### 設定をスクリプトで記述できる

Vim は昔からスクリプトによって設定可能なエディタでした [^1] [^2] 。

設定をコード (JSON など) として記述するエディタは多数ありますが、スクリプトが記述できることにより、複雑な操作をキーマップして呼び出したり、Vim の用意した API にアクセスして見た目を変えたりできます。

有用な設定を作って公開してきた先人がたくさんいるため、Vim ではこれをプラグインとして [^3] 管理できるようになっています。  
プラグインは多数ありますが、後ほどいくつか紹介します。

### シェルと一緒に使いやすい

ターミナル上で動くので当たり前なのですが、tmux のようなターミナルマルチプレクサと一緒に動かせば、ファイルの編集をしている横でシェルを使うことができます。  
この使い方だと、シェルはターミナルエミュレータと tmux の上に乗っているだけであり、Vim の外にいるので、Vim 側のキーバインドと被っても困りません。

シェル (外部コマンド) との協調について言えば、コマンドの出力を編集中のファイル上に流し込んだり、選択範囲をコマンドに通して変換したり [^4] でき、この点も結構便利です。

### 起動が早い

めっちゃ早いです。自分の設定した Neovim は起動して 50ms 前後でファイルが表示されます [^5] 。  
特にマルチリポジトリなプロジェクトで開発しているとディレクトリを切り替える事が多く、高速で起動する恩恵は大きそうです。

### タスクバー/Dock からアイコンが1つ減る

エディタとターミナルエミュレータが両方あったのが、ターミナルエミュレータだけになります。  
タスクバーに登録したアプリケーションは Windows+1 のようなショートカットで起動できますが、6個目あたりから左手だけでは打ちづらくなってきます。そのため、少ないほうがありがたいです。

![タスクバーにアイコンが3つしか並んでいない様子(左から Google Chrome、Slack、Windows ターミナル)](https://res.cloudinary.com/zenn/image/fetch/s--DRtt-XRM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/85e0d4999f81e468dc4f00d2.png%3Fsha%3D9739ee9c7d8bdc6802c75543985b55b1d8d3fa6c)

## VS Code から Neovim に移行してできなくなったこと

### HTML 等のドキュメントの表示

Markdown から HTML を作ったり、TeX で PDF を作ったりすることがあると思いますが、ターミナルと web ブラウザの統合はあまり進んでいないので、別途ビューアを開く必要があります。  
ビューアを別ウィンドウで開けばいいので、そこまで困っているわけではありません。

### VS Code 専用拡張機能が使えなくなった

フォルシア社内でも VS Code 拡張機能を開発している方がいますが、恩恵にあずかれません。

とても勝手ですが、可能であれば Language Server として実装して欲しさがあります。

## Neovim でコーディングするための準備

Neovim は拡張性の高いエディタですが、初期設定ではただテキストをエディットできるだけで、コードをエディットするための設定はされていません。  
画面の左にファイラは出てきてくれませんし、コンパイルエラーを赤線で出してはくれませんし、変数名の補完すら型やスコープを考慮してくれません [^6] 。

幸いにも、これらはユーザ・コミュニティが開発しているプラグインによって実現できるため、設定例を紹介します。

### Neovim を入れる

[公式のインストール手順はここ](https://github.com/neovim/neovim/blob/master/INSTALL.md) にあります。

2024年11月13日現在、Neovim の最新安定版は であり、機能が次々に追加されています。  
最新安定版を要求するプラグインも多いため、最新安定版を入れることをおすすめします。  
特に、Ubuntu の apt など一部のパッケージマネージャで導入するとアップデートが取り込まれない場合があるため、tarball や AppImage を落とすか、ソースからビルドすると良いと思います。

なお、起動するコマンドは `neovim` ではなく、通常 `nvim` です。

### Vim に慣れる

まずは `:Tutor` コマンドを入力して、チュートリアルをやりましょう。  
最初の段階ではプラグインを何も入れなくていいので、メモ専用に使うとよいでしょう。

あるいは、VS Code 用の [VSCodeVim](https://marketplace.visualstudio.com/items?itemName=vscodevim.vim) や [VSCode Neovim](https://marketplace.visualstudio.com/items?itemName=asvetliakov.vscode-neovim) という拡張機能を使ってみて、もし不便さを感じたら本物の Vim に移行するのでも良いと思います。

### クリップボード連携

まず、clipboard provider を何かしら入れる必要があります (xsel や xclip といったクリップボードを操作できるコマンドにパスが通っていればよいです)。

Vim は、 `y{motion}` や `{Visual}y` でヤンク (コピーのこと)、 `p` で貼り付けといった操作ができます。ただしこれは Vim 内の `""` レジスタと結びついており、クリップボードと連携されてはいません。  
クリップボードと連携されているのは `"+` レジスタなので、 `{Visual}"+y` や `"+p` のように打鍵すればクリップボードにコピーしたりクリップボードから貼り付けたりはできます。ただ、わざわざ指定しないといけません [^7] 。  
クリップボードと連携されていたほうが便利だと思う場合は、設定しておきます [^8] 。

~/.config/nvim/init.lua

```lua
vim.opt.clipboard = "unnamedplus"
```

### <Esc> へのキーマップ

全く必須ではないのですが、自分は手に馴染んでやめられなくなってしまったので書きます。

Vim ではモード切替を頻繁に行いますが、insert から normal への切り替えは `<Esc>` (または `<C-[>`) です。  
ホームポジションからかなり遠く、頻繁に打鍵するには向いていないキーだと思います。  
そこで、 `jj` や `jk` といった、この順に打鍵することが少なくホームポジションで打ちやすいキーシーケンスを割り当てておきます [^9] 。

~/.config/nvim/init.lua

```lua
vim.keymap.set("i", "jk", "<Esc>")
```

### プラグインマネージャ: lazy.nvim

プラグインの管理には、プラグインマネージャがあると便利です。  
あくまで便利になるだけであって必須ではないのですが、数十個のプラグインを利用するようになることを見越し、使うものとして話を進めます。

プラグインマネージャは色んな種類がありますが、ここでは [lazy.nvim](https://github.com/folke/lazy.nvim) を使います。  
設定が簡単で、メンテナンスが続いており、多少チューニングできるからです。

lazy.nvim を入れてしまえば、数行書けば他のプラグインを入れられるようになります。

[https://lazy.folke.io/installation](https://lazy.folke.io/installation) に記載の設定を適切なファイルにコピーすれば、次回の Neovim 起動時に lazy.nvim がインストールされます。

プラグインの入れ方ですが、 [structured setup](https://lazy.folke.io/installation) の手順に従ってセットアップした場合は、 `~/.config/nvim/lua/plugins/` 内に適当な.lua ファイルを作り、GitHub のリポジトリ名を Lua の table で返すだけです (最も簡単な場合)。  
依存関係やその他の設定がある場合も色々書けます [(詳細)](https://lazy.folke.io/spec) 。

追加した状態で Neovim を起動すると、自動的にインストールされます。

---

以下はプラグイン紹介ですが、VS Code で誰もが使っているであろう機能の再現に使えるプラグインに絞って紹介します。そのため、他の記事のほうが充実していると思います。

### ファイラ: nvim-tree

これまた必須ではないのですが、なんだかんだソースを編集するときは周辺のディレクトリにあるファイルを一緒に編集することが多いです。なのでファイラも入れます。

ファイラも色々ありますが、筆者が実際に使っている [nvim-tree](https://github.com/nvim-tree/nvim-tree.lua) を紹介します。

`:NvimTreeToggle` コマンドで画面左にエクスプローラを出したり消したりできます (わざわざ打っていられないので筆者は `<Leader>ee` にマップしています)。

![nvim-treeでエクスプローラが出ている様子](https://res.cloudinary.com/zenn/image/fetch/s--K08CxGbs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/5908089f2e43dbd8945cedb2.png%3Fsha%3D052b00bfb64ffa530dd6dc96c6d7bdc8263c3aa6)

~/.config/nvim/lua/plugins/filer.lua

```lua
return {
  {
    "nvim-tree/nvim-tree.lua",
    opts = {},
  },
}
```

### ファインダ: telescope.nvim

ファイラに近い使い方をするものですが、ファインダとして [telescope.nvim](https://github.com/nvim-telescope/telescope.nvim) も紹介しておきます。

こちらは、ファイルを検索して Neovim で開くことができます。ファイル名だけでなく、grep してマッチしたファイルを開いたり、現在開いているバッファ (≒ファイル) を検索したり、LS (後述) から受け取ったエラー箇所にジャンプできたりします。  
ファイル以外にも色々検索できるので、詳細な使い方は README を確認してください。

![telescope.nvimでファイルを検索している様子](https://res.cloudinary.com/zenn/image/fetch/s--3f2KoZFZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/0bc155b6ac26253377811a04.png%3Fsha%3D56c8512008ba7b544a1c2d529a4cd5d3e275e5c3)

~/.config/nvim/lua/plugins/finder.lua

```lua
return {
  {
    "nvim-telescope/telescope.nvim",
    dependencies = {
      "nvim-lua/plenary.nvim",
    },
  },
}
```

### Language Server: nvim-lspconfig, mason.nvim

[Language Server Protocol (LSP)](https://microsoft.github.io/language-server-protocol/) という、エディタを問わず、言語を問わず使われる共通機能 (例えば、補完、定義ジャンプ、ドキュメントのホバー表示など) を RPC [(→ Wikipedia)](https://ja.wikipedia.org/wiki/%E9%81%A0%E9%9A%94%E6%89%8B%E7%B6%9A%E3%81%8D%E5%91%BC%E5%87%BA%E3%81%97) として定義したプロトコルがあります。その言語ごとのサーバ実装が Language Server (LS) です。

LS に対する LSP Client ですが、Neovim にはビルトインされているのでそれを使うことにします。

さて、LS は単なる RPC サーバなので、Python スクリプトを開いても勝手に Python の LS が起動するわけではなく、LS を起動してエディタから接続するよう設定する必要があります。  
サーバの起動設定を細かく書くのは手間なので、 [nvim-lspconfig](https://github.com/neovim/nvim-lspconfig) を入れて起動オプションなどを任せることにします。

加えて、LS のインストール管理を楽するために [mason.nvim](https://github.com/williamboman/mason.nvim) と [mason-lspconfig.nvim](https://github.com/williamboman/mason-lspconfig.nvim) を入れます。

mason.nvim を使えば、こんな具合で LS のインストールが TUI で簡単にできます。

![mason.nvimを使っている様子](https://res.cloudinary.com/zenn/image/fetch/s--0EYLiTI---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/a5eb614dd87ec31e0d05f98e.png%3Fsha%3D2ec4868edec0aff99ef95cfc6b14a9cb9e7b055c)

lua-language-server を入れて、試しに閉じ括弧を消してみると、きちんと文法エラーが指摘されますね。

![Luaの文法エラーがlua-language-serverから指摘されている様子](https://res.cloudinary.com/zenn/image/fetch/s--wX98GTui--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/0dcf41d381eb451730b802e4.png%3Fsha%3De9d144167fb864c9ecf3f80c64b60b0afeb77ce2)

~/.config/nvim/lua/plugins/language-server.lua

```lua
return {
  "neovim/nvim-lspconfig",
  "williamboman/mason.nvim",
  "williamboman/mason-lspconfig.nvim",
}
```

~/.config/nvim/init.lua

```lua
require("mason").setup()
require("mason-lspconfig").setup()

-- 以下は mason でインストールした各 LS の setup を自動で呼び出すためのもの
-- これを書いていない場合、自分で \`require("lspconfig").サーバ名.setup({})\` をそれぞれ書く必要がある
require("mason-lspconfig").setup_handlers({
  function (server_name)
    require("lspconfig")[server_name].setup({})
  end,
})

-- 以下は LS から受け取ったエラーなどの診断情報を表示するのに必要
vim.diagnostic.config()
```

### 自動補完エンジン: nvim-cmp

一応、omni completion という機能によって `<C-x><C-o>` を入力すれば補完はできます。  
でも、補完候補の表示ぐらい何も入力しなくても出てきてほしいので、 **自動** 補完 [^10] エンジンを入れます。

自動補完エンジンには [nvim-cmp](https://github.com/hrsh7th/nvim-cmp/) を選びました。GitHub の star が多く、様々なものを補完できるからです。

さて、nvim-cmp は単体で入れても (恐らく) 何も補完してくれません。補完ソースを別のプラグインとして導入し、何を補完対象にするかをカスタマイズできます。  
下記の設定では [buffer 内の文字列](https://github.com/hrsh7th/cmp-buffer) 、 [ファイルパス](https://github.com/hrsh7th/cmp-path) 、 [コマンド](https://github.com/hrsh7th/cmp-cmdline) 、そして [Neovim ビルトインの LSP クライアント](https://github.com/hrsh7th/cmp-nvim-lsp) の4つを入れることにします。ちょっと変わったものだと、 [電卓](https://github.com/hrsh7th/cmp-calc) とか [絵文字](https://github.com/hrsh7th/cmp-emoji) とかの補完ソースなどもあります👍

![nvim-cmpで補完している様子](https://res.cloudinary.com/zenn/image/fetch/s--iy8riSlc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/b5daaa054ea6cf1608d879c4.png%3Fsha%3D05389f3edc1f73901c8f786084292298eab362cd)

~/.config/nvim/lua/plugins/completion.lua

```lua
return {
  {
    "hrsh7th/nvim-cmp",
    event = { "InsertEnter", "CmdlineEnter" },
    config = function ()
      local cmp = require("cmp")
      cmp.setup({
        mapping = cmp.mapping.preset.insert({
          ["<C-Space>"] = cmp.mapping.complete(),
          ["<CR>"] = cmp.mapping.confirm({ select = true }),
        }),
        sources = cmp.config.sources({
          { name = "nvim_lsp" },
        }, {
          { name = "buffer" },
          { name = "path" },
        }),
      })

      cmp.setup.cmdline({ "/", "?" }, {
        mapping = cmp.mapping.preset.cmdline(),
        sources = {
          { name = "buffer" },
        },
      })

      cmp.setup.cmdline(":", {
        mapping = cmp.mapping.preset.cmdline(),
        sources = {
          { name = "cmdline" },
        },
      })
    end,
  },
  "hrsh7th/cmp-nvim-lsp",
  "hrsh7th/cmp-buffer",
  "hrsh7th/cmp-path",
  "hrsh7th/cmp-cmdline",
}
```

### Git 統合

筆者は素直に (？) git コマンドを叩いているので、名前の紹介のみとします。

- [lazygit.nvim](https://github.com/kdheepak/lazygit.nvim)
- [neogit](https://github.com/NeogitOrg/neogit)
- [vim-fugitive](https://github.com/tpope/vim-fugitive)

### GitHub Copilot: copilot.lua, copilot-cmp

最後に、 [GitHub Copilot](https://github.com/features/copilot) との連携の紹介です。

Vim/Neovim 用の Copilot プラグイン [copilot.vim](https://github.com/github/copilot.vim) を GitHub が作っているため、これを導入することで Neovim でも Copilot を利用できます。

また、Lua で書き直してパフォーマンスを改善した [copilot.lua](https://github.com/zbirenbaum/copilot.lua) と、Copilot を nvim-cmp ソースとして利用するための [copilot-cmp](https://github.com/zbirenbaum/copilot-cmp) もあります。この記事で nvim-cmp を紹介したため、これらを導入します。

下に記す設定を書くほか、最初に使う前に `:Copilot auth` コマンドで GitHub アカウント認証することを忘れないようにお気をつけください 🫡

![copilot.lua と copilot-cmp を使ってコメントをいい感じに AI 補完している様子](https://res.cloudinary.com/zenn/image/fetch/s--FrMXHiL5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/b887d3ae4f21a0c537bea626.png%3Fsha%3D5affbf156084dc50db377f34f9e0a49fb5739f0d)

~/.config/nvim/lua/plugins/completion.lua

```lua
return {
  {
    "hrsh7th/nvim-cmp",
    event = { "InsertEnter", "CmdlineEnter" },
    config = function ()
      local cmp = require("cmp")
      cmp.setup({
        -- (省略)
        sources = cmp.config.sources({
          -- LSP の補完が邪魔して Copilot が出ないのも、
          -- Copilot の補完が邪魔して buffer の補完が出ないのも困るので group をやめている
          { name = "copilot" },
          { name = "nvim_lsp" },
          { name = "buffer" },
          { name = "path" },
        }),
      })
      -- (省略)
    end,
  },
  -- (省略)
  {
    "zbirenbaum/copilot-cmp",
    opts={},
  },
}
```

~/.config/nvim/lua/plugins/copilot.lua

```lua
return {
  {
    "zbirenbaum/copilot.lua",
    cmd = { "Copilot" },
    event = { "InsertEnter" },
    opts = {
      -- copilot-cmp と一緒に使う場合は copilot.lua の UI を無効にする
      suggestion = {
        enabled = false,
      },
      panel = {
        enabled = false,
      },
    },
  },
}
```

---

これで、VS Code でやっていたことの大抵は Neovim でもできるようになったのではないでしょうか？

## おわりに

この記事では Neovim で VS Code からコーディングのレベルを落とさないための最低限の設定を説明してきました。  
ここまでに紹介したプラグインはほんの僅かなので、自分にあったプラグインを少しずつ足していって、時にはプラグインを自作して、あなたのための最強のエディタを構築しましょう！

## この記事を書いた人

> 出口　智己  
> 2024年新卒入社  
> SSH 先や root に昇格して使う Vim はカスタムされていないので、正直不便です。

脚注

114

9

114

9

[^1]: Vim script で if や while のようなフロー制御ができるようになったのが Vim 5.0、ユーザ定義関数/コマンドが Vim 5.2 (いずれも1998年) のようです。  
[https://vimdoc.sourceforge.net/htmldoc/version5.html](https://vimdoc.sourceforge.net/htmldoc/version5.html)  
.vimrc はもっと前からあります。

[^2]: Emacs のほうが先に Lisp で本格的な設定ファイルが書けたと言われると困ってしまいます。

[^3]: 設定ファイルすべてをプラグインと呼ばれると違和感がある方もいるかもしれませんが、Vim においてプラグインと設定ファイルは同列のものです (一旦 Neovim の remote-plugin のことは忘れてください)。  
[Vim User Manual にもこのように](https://neovim.io/doc/user/usr_05.html#plugin) 書いてあります。

A plugin is nothing more than a Vim script file that is loaded automatically when Vim starts.

[^4]: filter といいます。sed や tr による置換はもちろん、CSV のヘッダ行より下をソートしたり、Markdown 内コードブロックを formatter で整形したりできます。

[^5]: 正確には、lazy.nvim の Profile タブの UIEnter に表示されている値が 50ms です。

[^6]: 補完に関して言えば omni completion というやつは一応いるのですが、わざわざキーを打たないといけないのと、Rust も TypeScript も Python も第一級のサポートがあるわけではないので、LSP client を使うのが無難だと思います。

[^7]: ターミナルエミュレータ次第ですが、insert モードで Ctrl+Shift+V や Shift+Insert を入力すれば貼り付けられるかもしれません。  
この操作で満足していれば、不要でしょうね。

[^8]: この設定を貼り付ける際も、 `p` ではなく `"+p` ということですね。以降は `p` で良くなります。

[^9]: この記事に `jk` と書くとき、 `<C-v>jk` と打つ必要が生まれてしまいました 😅

[^10]: 自動補完という単語は [https://github.com/neovim/nvim-lspconfig/wiki/Autocompletion](https://github.com/neovim/nvim-lspconfig/wiki/Autocompletion) を参考に、この記事では、「入力途中にユーザが指示しなくても候補を提示するもの」として使っています。