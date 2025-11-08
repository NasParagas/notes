---
title: "📘あまり紹介されていないけど かけがえのないNeovimプラグインたち"
source: "https://minerva.mamansoft.net/2024-03-27-underrated-essential-neovim-plugins"
author:
  - "[[Minerva]]"
published:
created: 2025-09-26
description: "📘あまり紹介されていないけど かけがえのないNeovimプラグインたち - Minerva"
tags:
  - "clippings"
---
[📒Articles](https://minerva.mamansoft.net/%F0%9F%93%98Articles/%F0%9F%93%92Articles) > [📒2024 Articles](https://minerva.mamansoft.net/%F0%9F%93%98Articles/%F0%9F%93%922024+Articles)

![cover-picture](https://publish-01.obsidian.md/access/35d05cd1bf5cc500e11cc8ba57daaf88/%F0%9F%93%98Articles/attachments/2024-03-27.webp)

cover-picture

[Neovimプラグイン](https://minerva.mamansoft.net/Notes/Neovim%E3%83%97%E3%83%A9%E3%82%B0%E3%82%A4%E3%83%B3) の中で、日本語サイトでほぼ見かけないものに限定してお気に入りをいくつか紹介してきます。

Info

この記事は [Vim駅伝](https://minerva.mamansoft.net/Notes/Vim%E9%A7%85%E4%BC%9D) [#2024/03/27](https://minerva.mamansoft.net/#2024/03/27) の 参加記事です。

## メインエディタとして使うための生命線

[Neovim](https://minerva.mamansoft.net/Notes/Neovim) をメインエディタ([IDE](https://minerva.mamansoft.net/Notes/IDE))として利用するなら、 [Neovimプラグイン](https://minerva.mamansoft.net/Notes/Neovim%E3%83%97%E3%83%A9%E3%82%B0%E3%82%A4%E3%83%B3) の選出と利用は生命線だと感じています。いくら [Vim](https://minerva.mamansoft.net/Notes/Vim) や [Neovim](https://minerva.mamansoft.net/Notes/Neovim) のモードや操作が優れているとしても、素の状態で [VSCode](https://minerva.mamansoft.net/Notes/VSCode) や [IntelliJ IDEA](https://minerva.mamansoft.net/Notes/IntelliJ+IDEA) などと張り合うのは厳しいでしょう。少なくとも私はそう感じます。

[IntelliJ IDEA](https://minerva.mamansoft.net/Notes/IntelliJ+IDEA) をメインで使っていた頃の私は、 [Vim](https://minerva.mamansoft.net/Notes/Vim) のプラグインは極力入れない派でした。 [📚実践Vim](https://minerva.mamansoft.net/Notes/%F0%9F%93%9A%E5%AE%9F%E8%B7%B5Vim) にも書かれていましたが、素の状態で [Vim](https://minerva.mamansoft.net/Notes/Vim) を使うことが美しく、それこそが [Vimmer](https://minerva.mamansoft.net/Notes/Vimmer) であろうと思っていました。

ですが、 [Neovim](https://minerva.mamansoft.net/Notes/Neovim) をメインで使うようになってからは、多くの [Neovimプラグイン](https://minerva.mamansoft.net/Notes/Neovim%E3%83%97%E3%83%A9%E3%82%B0%E3%82%A4%E3%83%B3) をインストールしています。時期はちょうど2023年から2024年にかけての頃、具体的には以下の [📒Articles](https://minerva.mamansoft.net/%F0%9F%93%98Articles/%F0%9F%93%92Articles) で [Vim駅伝](https://minerva.mamansoft.net/Notes/Vim%E9%A7%85%E4%BC%9D) に参加した頃です。

📘Neovimを使ったことがなかったころの君へ

2023年のおわり、1年のふりかえり記事を書こうとネタを考えながら帰路につき、いつもは見ないポストを開けるとそこには見慣れない1通の手紙が...。

![2023-12-29.jpg](https://publish-01.obsidian.md/access/35d05cd1bf5cc500e11cc8ba57daaf88/%F0%9F%93%98Articles/attachments/2023-12-29.jpg)

あれから3ヶ月以上が経ち、私のプラグイン事情も安定してきたので、このタイミングで一度有益なプラグインの共有ができればと思い筆をとりました。

## 紹介するプラグインの選定条件

数えてみたところ、今使っているプラグインの数は60でした。これらをすべて紹介してしまうと、名前だけを紹介する超浅い記事になってしまうことでしょう。その中には、他ページでもっと詳しく有益な紹介をされているものもあると思います。

なので、この記事では **日本語の [Neovimプラグイン](https://minerva.mamansoft.net/Notes/Neovim%E3%83%97%E3%83%A9%E3%82%B0%E3%82%A4%E3%83%B3) 紹介サイトで紹介されていなかったもの** の中から **私がオススメするもの5つ** をピックアップして紹介することにしました。

事前に調べたサイトは、Google検索で『Neovimプラグイン』のような検索ワードでヒットした、複数の [Neovimプラグイン](https://minerva.mamansoft.net/Notes/Neovim%E3%83%97%E3%83%A9%E3%82%B0%E3%82%A4%E3%83%B3) を紹介している日本語サイトです。対象サイトは20弱で、そこに一度も登場しなかったものが候補となります。

## 除外するプラグイン

私が使っているプラグインのうち、事前調査に引っかかり除外したものを一覧で紹介します。これらのプラグインは本記事での紹介対象外となります。

| 紹介数 | プラグイン                                                                                                |
| --- | ---------------------------------------------------------------------------------------------------- |
| 12  | [telescope.nvim](https://minerva.mamansoft.net/Notes/telescope.nvim)                                 |
| 9   | [nvim-treesitter](https://minerva.mamansoft.net/Notes/nvim-treesitter)                               |
| 7   | [gitsigns.nvim](https://minerva.mamansoft.net/Notes/gitsigns.nvim)                                   |
| 7   | [lualine.nvim](https://minerva.mamansoft.net/Notes/lualine.nvim)                                     |
| 6   | [nvim-cmp](https://minerva.mamansoft.net/Notes/nvim-cmp)                                             |
| 4   | [Tokyo Night](https://minerva.mamansoft.net/Notes/Tokyo+Night+\(Neovim\))                            |
| 4   | [nvim-autopairs](https://minerva.mamansoft.net/Notes/nvim-autopairs)                                 |
| 4   | [nvim-lspconfig](https://minerva.mamansoft.net/Notes/nvim-lspconfig)                                 |
| 4   | [toggleterm.nvim](https://minerva.mamansoft.net/Notes/toggleterm.nvim)                               |
| 3   | [LuaSnip](https://minerva.mamansoft.net/Notes/LuaSnip)                                               |
| 3   | [Noice](https://minerva.mamansoft.net/Notes/Noice)                                                   |
| 3   | [aerial.nvim](https://minerva.mamansoft.net/Notes/aerial.nvim)                                       |
| 3   | [barbar.nvim](https://minerva.mamansoft.net/Notes/barbar.nvim)                                       |
| 3   | [colorizer.lua](https://minerva.mamansoft.net/Notes/colorizer.lua)                                   |
| 3   | [lazy.nvim](https://minerva.mamansoft.net/Notes/lazy.nvim)                                           |
| 3   | [nvim-scrollbar](https://minerva.mamansoft.net/Notes/nvim-scrollbar)                                 |
| 3   | [nvim-surround](https://minerva.mamansoft.net/Notes/nvim-surround)                                   |
| 3   | [trouble.nvim](https://minerva.mamansoft.net/Notes/trouble.nvim)                                     |
| 2   | [Lspsaga](https://minerva.mamansoft.net/Notes/Lspsaga)                                               |
| 2   | [Markdown Preview for (Neo)vim](https://minerva.mamansoft.net/Notes/Markdown+Preview+for+\(Neo\)vim) |
| 2   | [cmp-buffer](https://minerva.mamansoft.net/Notes/cmp-buffer)                                         |
| 2   | [cmp-nvim-lsp](https://minerva.mamansoft.net/Notes/cmp-nvim-lsp)                                     |
| 2   | [cmp-path](https://minerva.mamansoft.net/Notes/cmp-path)                                             |
| 2   | [cmp\_luasnip](https://minerva.mamansoft.net/Notes/cmp_luasnip)                                      |
| 2   | [illuminate.vim](https://minerva.mamansoft.net/Notes/illuminate.vim)                                 |
| 2   | [lspkind-nvim](https://minerva.mamansoft.net/Notes/lspkind-nvim)                                     |
| 1   | [CamelCaseMotion](https://minerva.mamansoft.net/Notes/CamelCaseMotion+\(Vim\))                       |
| 1   | [Fidget](https://minerva.mamansoft.net/Notes/Fidget)                                                 |
| 1   | [ReplaceWithRegister](https://minerva.mamansoft.net/Notes/ReplaceWithRegister)                       |
| 1   | [Todo Comments](https://minerva.mamansoft.net/Notes/Todo+Comments)                                   |
| 1   | [VIM Table Mode](https://minerva.mamansoft.net/Notes/VIM+Table+Mode)                                 |
| 1   | [Which Key](https://minerva.mamansoft.net/Notes/Which+Key)                                           |
| 1   | [bufdelete.nvim](https://minerva.mamansoft.net/Notes/bufdelete.nvim)                                 |
| 1   | [cmp-cmdline](https://minerva.mamansoft.net/Notes/cmp-cmdline)                                       |
| 1   | [none-ls.nvim](https://minerva.mamansoft.net/Notes/none-ls.nvim)                                     |
| 1   | [nvim-bqf](https://minerva.mamansoft.net/Notes/nvim-bqf)                                             |
| 1   | [nvim-hlslens](https://minerva.mamansoft.net/Notes/nvim-hlslens)                                     |
| 1   | [nvim-treesitter-context](https://minerva.mamansoft.net/Notes/nvim-treesitter-context)               |
| 1   | [nvim-ts-context-commentstring](https://minerva.mamansoft.net/Notes/nvim-ts-context-commentstring)   |
| 1   | [nvim-ufo](https://minerva.mamansoft.net/Notes/nvim-ufo)                                             |
| 1   | [repeat.vim](https://minerva.mamansoft.net/Notes/repeat.vim)                                         |
| 1   | [replacer.nvim](https://minerva.mamansoft.net/Notes/replacer.nvim)                                   |
| 1   | [telescope-frecency.nvim](https://minerva.mamansoft.net/Notes/telescope-frecency.nvim)               |

また、集計に利用したサイトは以下です。

それでは1つずつ紹介していきます。

## flash.nvim

効率的な移動や [モーション](https://minerva.mamansoft.net/Notes/%E3%83%A2%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3) を提供する [Neovimプラグイン](https://minerva.mamansoft.net/Notes/Neovim%E3%83%97%E3%83%A9%E3%82%B0%E3%82%A4%E3%83%B3) です。作者は [lazy.nvim](https://minerva.mamansoft.net/Notes/lazy.nvim) や [LazyVim](https://minerva.mamansoft.net/Notes/LazyVim) の作者として有名な [👤Folke Lematire](https://minerva.mamansoft.net/Notes/%F0%9F%91%A4Folke+Lematire) 氏。

![frame](https://publish-01.obsidian.md/access/35d05cd1bf5cc500e11cc8ba57daaf88/%F0%9F%93%98Articles/attachments/2024-03-24-19-20-27.webp)  
*任意の文字数入力後にラベル文字かEnterで移動する*

その中でも私が [flash.nvim](https://minerva.mamansoft.net/Notes/flash.nvim) を使っている理由は以下3点です。

- [lazy.nvim](https://minerva.mamansoft.net/Notes/lazy.nvim) をはじめとして [👤Folke Lematire](https://minerva.mamansoft.net/Notes/%F0%9F%91%A4Folke+Lematire) 氏のファンであり信頼している
- 他プラグインと比較して操作が直感的であり、パフォーマンス面も含めて安定している
- 使いたい機能がすべて含まれている

ここでは [flash.nvim](https://minerva.mamansoft.net/Notes/flash.nvim) の中でもお気に入りの機能を2つ紹介します。

### Remote Actions

現在のカーソルから離れた場所でアクション([オペレータ](https://minerva.mamansoft.net/Notes/%E3%82%AA%E3%83%9A%E3%83%AC%E3%83%BC%E3%82%BF) & [モーション](https://minerva.mamansoft.net/Notes/%E3%83%A2%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3))を発動し、元のカーソルから操作を再開する機能です。言葉で言われてもピンとこないと思いますので具体例を挙げてみます。

![frame](https://publish-01.obsidian.md/access/35d05cd1bf5cc500e11cc8ba57daaf88/%F0%9F%93%98Articles/attachments/Pasted%20image%2020240324193541.png)  
*やりたいこと*

普通にやると以下のような操作、移動して [ヤンク](https://minerva.mamansoft.net/Notes/%E3%83%A4%E3%83%B3%E3%82%AF) して戻ってきてputの流れになります。

![frame](https://publish-01.obsidian.md/access/35d05cd1bf5cc500e11cc8ba57daaf88/%F0%9F%93%98Articles/attachments/2024-03-24-20-24-49.webp)  
*`sano<CR>yi"<C-o>_i"` 13 keys*

Remote Actionsを使うと、 [オペレータ](https://minerva.mamansoft.net/Notes/%E3%82%AA%E3%83%9A%E3%83%AC%E3%83%BC%E3%82%BF) を先に指定することで元の位置に戻る `<C-o>` を省略できます。

![frame](https://publish-01.obsidian.md/access/35d05cd1bf5cc500e11cc8ba57daaf88/%F0%9F%93%98Articles/attachments/2024-03-24-20-31-51.webp)  
*`yrano<CR>i"_i"` 11 keys*

少しだけキーを節約でき、見ている人に何が起こったのかわからん感を与えることはできます。ただ、時間としてはほとんど変わらないので、頑張ってまで使うべき機能かと言われると... 個人的には... といった回答になります。

### Treesitter Integration

[nvim-treesitter](https://minerva.mamansoft.net/Notes/nvim-treesitter) で解析したノードを範囲として利用できる機能です。たとえば以下のコードがあり、カーソル位置が41行目にあったとします。

![Pasted image 20240324204338.png](https://publish-01.obsidian.md/access/35d05cd1bf5cc500e11cc8ba57daaf88/%F0%9F%93%98Articles/attachments/Pasted%20image%2020240324204338.png)

Pasted image 20240324204338.png

ここでキーを入力すると、 [nvim-treesitter](https://minerva.mamansoft.net/Notes/nvim-treesitter) のパースした単位の範囲がラベルとして表示されます。

![Pasted image 20240324204415.png](https://publish-01.obsidian.md/access/35d05cd1bf5cc500e11cc8ba57daaf88/%F0%9F%93%98Articles/attachments/Pasted%20image%2020240324204415.png)

Pasted image 20240324204415.png

初見ではごちゃごちゃして分かりにくいですが、以下のルールに従って解読します。

- 範囲の開始より1つ前と、終了より1つ後に同じラベルが登場する
	- たまに表示されないラベルがある
- 上記2つのラベル(ペアのラベル)に挟まれた領域が対象範囲である

たとえば、 `f` を押すと、 `f` で挟まれた以下の部分が対象となります。

![Pasted image 20240324205059.png](https://publish-01.obsidian.md/access/35d05cd1bf5cc500e11cc8ba57daaf88/%F0%9F%93%98Articles/attachments/Pasted%20image%2020240324205059.png)

Pasted image 20240324205059.png

これを先ほどのRemote Actionsと組み合わせると、関数をコピーしてきてその場に貼り付け...みたいなこともできます。

![2024-03-24-20-57-05.webp](https://publish-01.obsidian.md/access/35d05cd1bf5cc500e11cc8ba57daaf88/%F0%9F%93%98Articles/attachments/2024-03-24-20-57-05.webp)

[flash.nvim](https://minerva.mamansoft.net/Notes/flash.nvim) には他にも多くの機能があります。

## lsp\_signature.nvim

タイピング中に関数のシグニチャを表示する [Neovimプラグイン](https://minerva.mamansoft.net/Notes/Neovim%E3%83%97%E3%83%A9%E3%82%B0%E3%82%A4%E3%83%B3) です。個人的には必須レベルですが、意外と紹介されていませんでした。

GitHub - ray-x/lsp\_signature.nvim: LSP signature hint as you type

LSP signature hint as you type. Contribute to ray-x/lsp\_signature.nvim development by creating an account on Git...

![lsp_signature.nvim](https://opengraph.githubassets.com/91aebc4fc52511bbba4697f5e98b6abca01cce92bc9dcbc7d1d9a05f52e34f03/ray-x/lsp_signature.nvim)

例として、 [TypeScript](https://minerva.mamansoft.net/Notes/TypeScript) のコード `lib.ts` に `sum` という関数が3つのシグニチャを [オーバーロード](https://minerva.mamansoft.net/Notes/%E3%82%AA%E3%83%BC%E3%83%90%E3%83%BC%E3%83%AD%E3%83%BC%E3%83%89+\(TypeScript\)) していた場合を紹介します。

```ts
export function sum(a: number, b: number): number;
export function sum(a: number, b: number, c: number): number;
export function sum(a: number, b: number, c: number, d: number): number;
export function sum(a: number, b: number, c?: number, d?: number): number {
  return a + b + (c ?? 0) + (d ?? 0);
}
```

これを呼び出すとき、以下のようにシグニチャが表示されます。

![2024-03-24-21-10-28.webp](https://publish-01.obsidian.md/access/35d05cd1bf5cc500e11cc8ba57daaf88/%F0%9F%93%98Articles/attachments/2024-03-24-21-10-28.webp)

また、既に記述済みである関数の引数にカーソルをあわせて [挿入モード](https://minerva.mamansoft.net/Notes/%E6%8C%BF%E5%85%A5%E3%83%A2%E3%83%BC%E3%83%89) に移行すると、自動でシグニチャと現在の引数がハイライトされるのでとても便利です。

## no-neck-pain.nvim

スクリーンの中央に現在の [ウィンドウ](https://minerva.mamansoft.net/Notes/%E3%82%A6%E3%82%A3%E3%83%B3%E3%83%89%E3%82%A6+\(Vim\)) を寄せる [Neovimプラグイン](https://minerva.mamansoft.net/Notes/Neovim%E3%83%97%E3%83%A9%E3%82%B0%E3%82%A4%E3%83%B3) です。

GitHub - shortcuts/no-neck-pain.nvim: ☕ Dead simple yet super extensible zen mode plugin to protect your neck.

☕ Dead simple yet super extensible zen mode plugin to protect your neck. - shortcuts/no-neck-pain.nvim

![no-neck-pain.nvim](https://opengraph.githubassets.com/47c690f3218557548d745a2914d1febf011fafb82a2366b809b169f61ec1ef4d/shortcuts/no-neck-pain.nvim)

ウルトラワイドモニタのような横に長いスクリーンを想像してみてください。 [Neovim](https://minerva.mamansoft.net/Notes/Neovim) でファイルを開くとこんな感じになると思います。

![Pasted image 20240324211600.png](https://publish-01.obsidian.md/access/35d05cd1bf5cc500e11cc8ba57daaf88/%F0%9F%93%98Articles/attachments/Pasted%20image%2020240324211600.png)

Pasted image 20240324211600.png

見事なほど左に寄ってますね。 [Neovim](https://minerva.mamansoft.net/Notes/Neovim) でコーディングするとき、貴方は常に左を向いていることになります。そんな日々が続けばneckもpainしてしまうでしょう。正面を向いていないと集中力も落ちると思います。

そんな悩みを [no-neck-pain.nvim](https://minerva.mamansoft.net/Notes/no-neck-pain.nvim) が解決してくれます。 [no-neck-pain.nvim](https://minerva.mamansoft.net/Notes/no-neck-pain.nvim) を使って同じくファイルを開いてみましょう。

![Pasted image 20240324211800.png](https://publish-01.obsidian.md/access/35d05cd1bf5cc500e11cc8ba57daaf88/%F0%9F%93%98Articles/attachments/Pasted%20image%2020240324211800.png)

Pasted image 20240324211800.png

左右に [スクラッチウィンドウ](https://minerva.mamansoft.net/Notes/%E3%82%B9%E3%82%AF%E3%83%A9%E3%83%83%E3%83%81%E3%82%A6%E3%82%A3%E3%83%B3%E3%83%89%E3%82%A6+\(Vim\)) が展開され、対象ファイルが中央に表示されました。新しく [ウィンドウ](https://minerva.mamansoft.net/Notes/%E3%82%A6%E3%82%A3%E3%83%B3%E3%83%89%E3%82%A6+\(Vim\)) を分割してみます。

![Pasted image 20240324212000.png](https://publish-01.obsidian.md/access/35d05cd1bf5cc500e11cc8ba57daaf88/%F0%9F%93%98Articles/attachments/Pasted%20image%2020240324212000.png)

Pasted image 20240324212000.png

まだ少し余裕があるので左右に若干の余白ができます。ファイルの最大幅はオプションで設定できますので、自分にあったサイズを指定して首に優しい超集中環境をつくっていきましょう！ (今回の例では100を設定しています)

## oil.nvim

[バッファ](https://minerva.mamansoft.net/Notes/%E3%83%90%E3%83%83%E3%83%95%E3%82%A1+\(Vim\)) システムを最大限に利用したファイルエクスプローラーを提供する [Neovimプラグイン](https://minerva.mamansoft.net/Notes/Neovim%E3%83%97%E3%83%A9%E3%82%B0%E3%82%A4%E3%83%B3) 。

GitHub - stevearc/oil.nvim: Neovim file explorer: edit your filesystem like a buffer

Neovim file explorer: edit your filesystem like a buffer - stevearc/oil.nvim

![oil.nvim](https://opengraph.githubassets.com/685d8b011cf2180a82bb668dea531ce5c3425f21f2dde10186ea39ab0f72a110/stevearc/oil.nvim)

つい最近まで、ファイルエクスプローラーには [nvim-tree.lua](https://minerva.mamansoft.net/Notes/nvim-tree.lua) を使っていました。同様のツリー型エクスプローラーで [Neo-tree.nvim](https://minerva.mamansoft.net/Notes/Neo-tree.nvim) や [fern.vim](https://minerva.mamansoft.net/Notes/fern.vim) を使っている方も多いと思います。ですが、最近になって [oil.nvim](https://minerva.mamansoft.net/Notes/oil.nvim) に乗り換えました。

その用途が目的から外れたら、意外と [nvim-tree.lua](https://minerva.mamansoft.net/Notes/nvim-tree.lua) を使うシーンは少ないのではということに気づきました。加えて、 [no-neck-pain.nvim](https://minerva.mamansoft.net/Notes/no-neck-pain.nvim) と [nvim-tree.lua](https://minerva.mamansoft.net/Notes/nvim-tree.lua) を同時に使うと、しばしば [ウィンドウ](https://minerva.mamansoft.net/Notes/%E3%82%A6%E3%82%A3%E3%83%B3%E3%83%89%E3%82%A6+\(Vim\)) のレイアウトが崩れることがありました。それなら、 [nvim-tree.lua](https://minerva.mamansoft.net/Notes/nvim-tree.lua) ではなく別のもっとエモいプラグインを使いたいな... と思い、 [oil.nvim](https://minerva.mamansoft.net/Notes/oil.nvim) にたどり着きました。

そんな [oil.nvim](https://minerva.mamansoft.net/Notes/oil.nvim) の魅力を2点ほど紹介します。

### Vimをリスペクトした操作感

Attention

デモ動画として見やすいので、 `<C-p>` でプレビューをONにするときがあります。こうすることで、フォーカスされたファイルのプレビューが別 [ウィンドウ](https://minerva.mamansoft.net/Notes/%E3%82%A6%E3%82%A3%E3%83%B3%E3%83%89%E3%82%A6+\(Vim\)) に表示されます。

![frame](https://publish-01.obsidian.md/access/35d05cd1bf5cc500e11cc8ba57daaf88/%F0%9F%93%98Articles/attachments/2024-03-24-23-06-30.webp)  
*`Ctrl+P` でプレビューを有効にした状態*

#### ファイルの作成

当たり前のようにリストに追加編集するだけです。 [バッファ](https://minerva.mamansoft.net/Notes/%E3%83%90%E3%83%83%E3%83%95%E3%82%A1+\(Vim\)) を保存すると反映されます。

![2024-03-24-23-17-50.webp](https://publish-01.obsidian.md/access/35d05cd1bf5cc500e11cc8ba57daaf88/%F0%9F%93%98Articles/attachments/2024-03-24-23-17-50.webp)

上記は `hoge.md` と `parent/child.md` を新規作成しています。

#### 名前変更・コピー・削除

これも普通に編集するだけ。コピーは [ヤンク](https://minerva.mamansoft.net/Notes/%E3%83%A4%E3%83%B3%E3%82%AF) したファイルの実体までキチンとコピーされます。

![2024-03-24-23-26-29.webp](https://publish-01.obsidian.md/access/35d05cd1bf5cc500e11cc8ba57daaf88/%F0%9F%93%98Articles/attachments/2024-03-24-23-26-29.webp)

上記は以下の操作を行っています。

- `index.ts` を `main.ts` と名称変更
- `main.ts` を `main2.ts` と `main3.ts` に複製
- `main.ts` を削除

コピーや削除の時は確認の [フローティングウィンドウ](https://minerva.mamansoft.net/Notes/%E3%83%95%E3%83%AD%E3%83%BC%E3%83%86%E3%82%A3%E3%83%B3%E3%82%B0%E3%82%A6%E3%82%A3%E3%83%B3%E3%83%89%E3%82%A6+\(Neovim\)) が表示されますので、意図通りの操作が行われるかをしっかり確認できます。

Hint

途中の操作で先ほど紹介した [flash.nvim](https://minerva.mamansoft.net/Notes/flash.nvim) を使っています。

### カレントバッファの同階層が表示される

もう1点は小さな魅力です... が私にとっては非常に大事です。 [oil.nvim](https://minerva.mamansoft.net/Notes/oil.nvim) はカレント [バッファ](https://minerva.mamansoft.net/Notes/%E3%83%90%E3%83%83%E3%83%95%E3%82%A1+\(Vim\)) のパスに対してエクスプローラーを開きます。

たとえば、 `node_modules/@types/bun/package.json` を開いた状態を考えます。

![Pasted image 20240324233141.png](https://publish-01.obsidian.md/access/35d05cd1bf5cc500e11cc8ba57daaf88/%F0%9F%93%98Articles/attachments/Pasted%20image%2020240324233141.png)

Pasted image 20240324233141.png

ここで `Oil` コマンドを実行すると、以下のようになります。

![Pasted image 20240324233247.png](https://publish-01.obsidian.md/access/35d05cd1bf5cc500e11cc8ba57daaf88/%F0%9F%93%98Articles/attachments/Pasted%20image%2020240324233247.png)

Pasted image 20240324233247.png

私がファイル操作をしたいときは、現在編集中のファイルから相対的に対象を探して操作したいことが多いのでこの仕様は非常に助かります。大抵のプラグインはカレントディレクトリ ([Neovim](https://minerva.mamansoft.net/Notes/Neovim) を開いたパス) が表示されてしまうので一手間ありました。

なお、 `_` キーを押せば1発でカレントディレクトリに移動できますので、そちらが望ましい場合も問題ありません。また、分割して複数 [ウィンドウ](https://minerva.mamansoft.net/Notes/%E3%82%A6%E3%82%A3%E3%83%B3%E3%83%89%E3%82%A6+\(Vim\)) で別々の操作もできますので、 [nvim-tree.lua](https://minerva.mamansoft.net/Notes/nvim-tree.lua) のように特別な [バッファ](https://minerva.mamansoft.net/Notes/%E3%83%90%E3%83%83%E3%83%95%E3%82%A1+\(Vim\)) を意識しすぎる必要もありません。Freedomです！

![Pasted image 20240324233525.png](https://publish-01.obsidian.md/access/35d05cd1bf5cc500e11cc8ba57daaf88/%F0%9F%93%98Articles/attachments/Pasted%20image%2020240324233525.png)

Pasted image 20240324233525.png

## smart-open.nvim

本記事で紹介する最後のプラグインは、 [telescope.nvim](https://minerva.mamansoft.net/Notes/telescope.nvim) の結果をスマートに表示するものです。

GitHub - danielfalk/smart-open.nvim: Neovim plugin for fast file-finding

Neovim plugin for fast file-finding. Contribute to danielfalk/smart-open.nvim development by creating an account...

![smart-open.nvim](https://opengraph.githubassets.com/7c9633d43bfb8bec5ee494bb75e4b5728762d0d0c306282fba56b09514a72bd5/danielfalk/smart-open.nvim)

### telescope.nvimには検索コマンドの決定版がない

皆さんもご存じのとおり、 [telescope.nvim](https://minerva.mamansoft.net/Notes/telescope.nvim) は素晴らしいプラグインです。今回、 [除外するプラグイン](https://minerva.mamansoft.net/2024-03-27-underrated-essential-neovim-plugins#%E9%99%A4%E5%A4%96%E3%81%99%E3%82%8B%E3%83%97%E3%83%A9%E3%82%B0%E3%82%A4%E3%83%B3) で紹介した紹介数でもダントツ1位の12サイトでした。しかし、個人的には [telescope.nvim](https://minerva.mamansoft.net/Notes/telescope.nvim) が表示する結果には少しモヤモヤしていました。

私の [telescope.nvim](https://minerva.mamansoft.net/Notes/telescope.nvim) のキーバインド設定です。

```lua
keys = {
    { "<C-j>f", ":Telescope find_files find_command=rg,--files,--hidden,--glob,!*.git <CR>", silent = true },
    { "<C-j>z", ":Telescope frecency<CR>", silent = true },
    { "<C-j>e", ":Telescope smart_open<CR>", silent = true },
    { "<C-j>g", ":Telescope live_grep<CR>", silent = true },
    { "<C-j>l", ":Telescope current_buffer_fuzzy_find<CR>", silent = true },
    { "<C-j>p", ":Telescope commands<CR>", silent = true },
    { "<C-j>:", ":Telescope command_history<CR>", silent = true },
    { "<C-j>m", ":Telescope vim_bookmarks all<CR>", silent = true },
    { "<C-j>s", ":Telescope lsp_dynamic_workspace_symbols<CR>", silent = true },
    { "<C-j>c", ":lua require'telescope.builtin'.git_status{}<CR>", silent = true },
  },
```

`:Telescope find_files` はカレントディレクトリ配下をファイル名で検索する機能ですが、現在開いているファイルの場所や最後に開いた/編集した時間などが考慮されないと思います。ファイル名で検索したいときは便利ですが、『最近開いたアレ』とか『このファイルと同階層のアレ』みたいなときは少しもどかしいときがあります。

次に `:Telescope oldfiles` を試してみました。これで開いたファイル順に表示されるかと思いきや、微妙に違う順番で表示されてしまったり、フィルタリングの結果はマッチ率が優先して考慮されたりと、かえって迷いが生じました。

その次は [telescope-frecency.nvim](https://minerva.mamansoft.net/Notes/telescope-frecency.nvim) を試してみました。

> A telescope.nvim extension that offers intelligent prioritization when selecting files from your editing history.

`intelligent prioritization`... といえば、私が開発している [Obsidianプラグイン](https://minerva.mamansoft.net/Notes/Obsidian%E3%83%97%E3%83%A9%E3%82%B0%E3%82%A4%E3%83%B3) の [🦉Various Complements](https://minerva.mamansoft.net/%F0%9F%A6%89My+Products/%F0%9F%A6%89Various+Complements) に実装した機能 [Intelligent suggestion prioritization](https://minerva.mamansoft.net/Notes/Intelligent+suggestion+prioritization) に似た名前です。READMEを読んでも同じような思想が垣間見えましたので期待して使い始めました。

カレントディレクトリという枷を外して、最近目にしたものを絞り込みつつ検索できるという点で、このプラグインは非常に便利でした。ですが、 [Neovim](https://minerva.mamansoft.net/Notes/Neovim) で開発をしているときは、やはりカレントプロジェクトのファイルを開きたくなることが多いです。そんなときに、 [telescope-frecency.nvim](https://minerva.mamansoft.net/Notes/telescope-frecency.nvim) や先の2機能では要求を満たせないのです。

- [telescope-frecency.nvim](https://minerva.mamansoft.net/Notes/telescope-frecency.nvim) はよく開く別プロジェクトのファイルを優先してしまいます
- `oldfiles` は厳密に開いた順でファイルを表示してくれません
- `find_files` は純粋にファイルのマッチ率で表示します

強いて言うなら、プロジェクトで開発するときは `find_files` を多用していました。別プロジェクトのファイルが表示されるよりは、マッチ率に頼ってカレントプロジェクトのファイル一覧を表示した方がいいからです。

### smart-open.nvimは本当にスマートだった

そんな迷える貴方に... [smart-open.nvim](https://minerva.mamansoft.net/Notes/smart-open.nvim) ！ このプラグインは2つの意味で非常にスマートです。

#### 並び順がスマート

最も大切なポイントです。並び順が実にスマートです。READMEによると以下の要素を考慮しているようです。

- ファイルパスが検索文字列にマッチするか
- ファイル **名** が検索文字列にマッチするか
- 最後にファイルを開いたのはいつか
- 最後に編集されたファイルかどうか
- ファイルをいずれかの [ウィンドウ](https://minerva.mamansoft.net/Notes/%E3%82%A6%E3%82%A3%E3%83%B3%E3%83%89%E3%82%A6+\(Vim\)) で開いているか
- ファイルの親ディレクトリパスが、現在開いているファイルの親ディレクトリパスにどれだけ近いか
- ファイルがどれくらい頻繁に開かれているか
- ファイルがカレントディレクトリ内のどこにあるか

おおよそ人間が考えることに近いのではないのでしょうか。さらに、これらの重みづけはユーザー([Neovim](https://minerva.mamansoft.net/Notes/Neovim) 利用者)の行動によっても変化するようなことが書かれています。

> This ranking algorithm is self-tuning. Over time, the weights of the factors above will be adjusted based upon your interaction with it. The tuning process is especially sensitive to selecting a suggestion that is not at the top. Weights will be adjusted relative to the higher-ranked suggestions that were not selected.

少なくとも私の場合、 [smart-open.nvim](https://minerva.mamansoft.net/Notes/smart-open.nvim) を使い続けてから10日間で、 **[smart-open.nvim](https://minerva.mamansoft.net/Notes/smart-open.nvim) の候補に欲しい結果が表示されなかったことはほぼありません** でした。

#### 表示がスマート

[telescope.nvim](https://minerva.mamansoft.net/Notes/telescope.nvim) の通常の結果表示を見てみましょう。

![Pasted image 20240326215114.png](https://publish-01.obsidian.md/access/35d05cd1bf5cc500e11cc8ba57daaf88/%F0%9F%93%98Articles/attachments/Pasted%20image%2020240326215114.png)

Pasted image 20240326215114.png

ハイライトもされていて見やすいんじゃない? と思うかもしれません。それでは [smart-open.nvim](https://minerva.mamansoft.net/Notes/smart-open.nvim) の結果表示も並べてみます。

![Pasted image 20240326215226.png](https://publish-01.obsidian.md/access/35d05cd1bf5cc500e11cc8ba57daaf88/%F0%9F%93%98Articles/attachments/Pasted%20image%2020240326215226.png)

Pasted image 20240326215226.png

[smart-open.nvim](https://minerva.mamansoft.net/Notes/smart-open.nvim) はファイル名が先頭に表示され、その後にパスが表示されます。一部例外はありますが、フリーワードで絞る場合、一番ターゲットになるのはファイル名だと思います。なので、私は非常に見やすいと思いました。

なお、左側の●と〇はそれぞれ、『開いているファイルのうち最も直前に開いたもの』と『現在開いているファイル(1つ以上)』です。

## まとめ

私が利用している [Neovimプラグイン](https://minerva.mamansoft.net/Notes/Neovim%E3%83%97%E3%83%A9%E3%82%B0%E3%82%A4%E3%83%B3) の中で、日本語サイトで見かけなかったものの中から、特にオススメの5つを紹介しました。

もし利用されたことのない方がいらっしゃいましたら、是非一度試してみてください。これらのプラグインには他にも、本記事内では紹介しきれなかった便利な機能が沢山あります。

また、普段あまりお目にかかれないけど便利なプラグインとしては、以下もオススメです。

[Neovim](https://minerva.mamansoft.net/Notes/Neovim) は沼です。永遠に自分の理想環境にはたどり着けない気がします。ですが、今回紹介したような素晴らしくもなかなか出会えない [Neovimプラグイン](https://minerva.mamansoft.net/Notes/Neovim%E3%83%97%E3%83%A9%E3%82%B0%E3%82%A4%E3%83%B3) という出会いつつ、不思議なダンジョンのように終わらぬ旅を続けるのも、また一興ではないかと私は思っています。

---

※ 本ページのイラストはAI(DALL·E)により生成されたものです