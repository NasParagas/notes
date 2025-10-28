---
title: "Neovimでヤンクした内容をmacOSのクリップボードに共有する方法"
source: "https://zenn.dev/dume5579/articles/3da1cbfa9617dd"
author:
  - "[[Zenn]]"
published: 2025-03-08
created: 2025-09-29
description:
tags:
  - "clippings"
---
1[tech](https://zenn.dev/tech-or-idea)

## 課題

Neovimでコードや文章を「ヤンク」（コピー）すると、通常はNeovim内部のレジスタにだけ保存され、macOSのシステムクリップボードには共有されません。そのため、以下のような不便があります：

- Neovimでヤンクした内容を別アプリケーションで貼り付けできない
- 別アプリでコピーした内容をNeovimで貼り付けるには特別なコマンドが必要

これではNeovimと他のアプリケーションを行き来する作業が非効率になってしまいます。

## 解決方法

Neovimのクリップボード設定を変更することで、ヤンクした内容を自動的にmacOSのシステムクリップボードと共有できるようになります。

### 手順1: 設定ファイルの場所を確認

macOSでNeovimの設定ファイルは以下の場所にあります：

```
~/.config/nvim/init.vim
```

もしくはLuaを使用している場合：

```
~/.config/nvim/init.lua
```

### 手順2: 設定ファイルがない場合は作成する

設定ファイルがまだ存在しない場合は、以下のコマンドで作成します：

```bash
# 設定ディレクトリの作成（存在しない場合）
mkdir -p ~/.config/nvim

# Vimスクリプト形式の設定ファイルを作成
touch ~/.config/nvim/init.vim

# もしくはLua形式の設定ファイルを作成
# touch ~/.config/nvim/init.lua
```

### 手順3: クリップボード共有の設定を追加

エディタ（Neovim自体でも可）で設定ファイルを開き：

```bash
nvim ~/.config/nvim/init.vim
```

以下の設定を追加します：

```
" Neovimでクリップボードとヤンクを共有
set clipboard+=unnamedplus
```

Luaを使用している場合は、 `init.lua` に以下を追加します：

```lua
-- Neovimでクリップボードとヤンクを共有
vim.opt.clipboard = "unnamedplus"
```

### 手順4: Neovimを再起動

設定を反映させるため、Neovimを再起動します。

## 動作確認

設定が正しく適用されていれば：

1. Neovimで文字列を選択して `y` でヤンクする
2. 別のアプリケーション（Browserなど）で `Cmd+V` を押す
3. ヤンクした内容が貼り付けられる

逆に：

1. 別アプリケーションで文字列をコピー（ `Cmd+C` ）
2. Neovimで `p` を押す
3. コピーした内容が貼り付けられる

## トラブルシューティング

もし設定後も機能しない場合は、以下を確認してください：

1. Neovimのクリップボード機能が有効か確認：
	```
	:checkhealth clipboard
	```
2. macOSでは通常クリップボードプロバイダが標準で動作しますが、問題がある場合は `pbcopy` と `pbpaste` コマンドが使える状態か確認してください。

## まとめ

この設定により、Neovimとmacのシステムクリップボードが統合され、テキスト操作がより効率的になります。Vimのヤンクコマンド（ `y` ）を使うだけで、その内容が自動的にmacOSのクリップボードにも保存されるため、異なるアプリケーション間でのテキストのコピー＆ペーストがスムーズになります。

1

### Discussion

![](https://static.zenn.studio/images/drawing/discussion.png)

ログインするとコメントできます

1