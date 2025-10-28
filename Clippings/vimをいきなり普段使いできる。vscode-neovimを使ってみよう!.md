---
title: "vimをいきなり普段使いできる。vscode-neovimを使ってみよう!"
source: "https://qiita.com/hwatahik/items/3e94ef647503ee0ed524"
author:
  - "[[hwatahik]]"
published: 2024-12-23
created: 2025-09-23
description: "本記事では、vscode-neovimをとりあえず使うための設定をします。 「明日からvimを使いたいが、vimをまだ使いこなすには時間がかかりそう...」という人におすすめです。 もしvimがなじまなくても拡張機能をoffにするだけで戻れます。 TL;DR keybin..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiitaにログインして、便利な機能を使ってみませんか？

あなたにマッチした記事をお届けします

便利な情報をあとから読み返せます

[ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fhwatahik%2Fitems%2F3e94ef647503ee0ed524&realm=qiita) [新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fhwatahik%2Fitems%2F3e94ef647503ee0ed524&realm=qiita)

本記事では、vscode-neovimをとりあえず使うための設定をします。  
「明日からvimを使いたいが、vimをまだ使いこなすには時間がかかりそう...」という人におすすめです。  
もしvimがなじまなくても拡張機能をoffにするだけで戻れます。

## TL;DR

`keybindings.json`, `settings.json`, `init.lua`  
をコピペして自環境に設定

- `space + e` でファイルツリーを開いたり閉じたりできる。
- `jk` でインサートモードから抜けられる  
	ことだけを知っていればとりあえず使えます。

## 経緯

エディタをいきなりvimにすることは難しいです。  
私は、プラグインの導入でバグを踏みまくり、vimに慣れないせいでかえって操作に時間がかかり、「コードを書く時間 < vimをいじる時間」になってしまいました。

そこで、 **いったんvimのキーバインドになれつつ、慣れたvscodeの機能を使えばよいのでは?** と思い、vscode-neovimを始めたところ、案外よかったので設定を共有します。

## vscode-neovimって?

vscodeのプラグインです。

vscode vimのようなvimエミュレータではなく、 **本物のneovimをvscodeの裏で動かしています** 。このおかげで爆速で動きます。neovimを土台にvscodeをフロント部分として使っているイメージです。

## vscode-neovimの導入

前提として

- vscodeがインストールされていること
- neovimがインストールされていること  
	が必要です。これらについては他のサイトに任せます。

### vscode-neovimの設定

設定項目は二つで

- `nvim` が起動するpath
- vscode-neovimで使用する `init.lua`  
	が必要です。
1. 拡張機能からvscode-neovimをダウンロードします  
	[![Pasted image 20241222202116.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2261812/ed7293f5-e7cb-f165-d0cb-c991addc895f.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2261812%2Fed7293f5-e7cb-f165-d0cb-c991addc895f.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=7395bea31eb51d5ecbd96167cfb80de9)
2. 上の歯車マークを押し、neovimの設定をします。
3. ここで2つの設定が必要です。  
	(1). 使用するneivimのパスを指定します  
	(2). vscode-neovimで使用するinit.luaを記述します。  
	[![Pasted image 20241222204156.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2261812/2e4d70b3-9164-620d-cbe2-58f019c6ad68.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F2261812%2F2e4d70b3-9164-620d-cbe2-58f019c6ad68.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=bac7f1cf21207aecd271db7ecdcde954)

余談ですが、私は普段wslで開発しています。  
wsl上でvscode-neovimを使用する場合は、windowsにインストールされたneovimをvscodeの裏で動かし、vscodeからwslにリモート接続して使っています。(ターミナル上で `code .`でvscodeが開きます)  
一方、wsl上で `nvim` コマンドからCLIで編集する場合はwsl(ubuntu)上のneovimを使用しています。そのため、実行パスはwindowsのものを書いています。

## 筆者の設定

ここからは、とりあえず使えるvscode-neovimの設定を記載します。  
プラグイン等ございませんので追加したい方はお好みでお願いします。

## 1\. init.lua の設定

| キーバインド (モード) | 操作内容 |
| --- | --- |
| `jk` (Insert モード) | ESC に切り替わる |
| `<S-h>` (Normal モード) | 行頭に移動（ `^` ） |
| `<S-l>` (Normal モード) | 行末に移動（ `$` ） |
| `<S-h>` (Visual モード) | 選択中の行頭に移動（ `^` ） |
| `<S-l>` (Visual モード) | 選択中の行末に移動（ `$` ） |
| `+` (Normal モード) | カーソル位置の数値をインクリメント（ `<C-a>` ） |
| `-` (Normal モード) | カーソル位置の数値をデクリメント（ `<C-x>` ） |

> **補足**  
> `if vim.g.vscode then ... end` の条件を使うと、VSCode-Neovim 上のみ・ネイティブの Neovim 上のみ、といったように設定を分けることができます。

### プラグインを入れたい場合

neovimのpluginがほとんどそのまま動作するようです。  
参考  
[https://zenn.dev/yutakatay/articles/vscode-neovim](https://zenn.dev/yutakatay/articles/vscode-neovim)

## 2\. keybindings.jsonの設定

1. `ctrl-shift-p` を押下して `keybindinfg.json` や `settings.json` を打つとコマンドや設定をファイルから登録できます。
2. vscodeでのwindow操作やターミナル操作はvimでなくvscode側を使うので、init.luaに書くのではなく、 `settings.json` にキーバインドを設定しています。

長ったらしく書いてありますが、 **`space + e` でファイルツリーが開いたり閉じたりする** ことだけ覚えれば何とかなります。

また、tmuxに慣れている方へ、この設定では `<C-f>` をprefixキーのようにして、tmuxライクにvscodeのターミナルを操作できるようにしています。 `<C-f>` 以外のprefixを使っている場合、別のキーに変更してください。

```json
[
    {
        "key": "ctrl+l",
        "command":"workbench.action.nextEditor",
        "when": "!terminalFocus && !sideBarFocus && neovim.mode != insert"
    },
    {
        "key": "ctrl+h",
        "command": "workbench.action.previousEditor",
        "when": "!terminalFocus && !sideBarFocus && neovim.mode !== insert '"
    },

    // LSP関連
    {
        "key": "g d",
        "command": "editor.action.revealDefinition",
        "when": "neovim.mode == normal && editorHasDefinitionProvider && editorTextFocus"
    },
    {
        "key": "g b",
        "command": "workbench.action.navigateBack",
        "when": "neovim.mode == normal && editorTextFocus",
    },
    {
        "key": "shift+k",
        "command": "editor.action.showHover",
        "when": "neovim.mode == normal && editorTextFocus",
    },

    // File Explorer の操作
    {
        "key": "space e",
        "command": "workbench.files.action.focusFilesExplorer",
        "when": "neovim.mode !== insert && !filesExplorerFocus && editorTextFocus"
    },
    {
        "key": "space e",
        "command": "workbench.action.closeSidebar",
        "when": "neovim.mode !== insert && filesExplorerFocus"
    },
    {
        "key": "Enter",
        "command": "list.toggleExpand",
        "when": "explorerViewletFocus && explorerViewletVisible && explorerResourceIsFolder && !inputFocus"
    },
    {
        "key": "shift+r",
        "command": "renameFile",
        "when": "explorerViewletVisible && filesExplorerFocus && !inputFocus"
    },
    {
        "key": "shift+c",
        "command": "filesExplorer.copy",
        "when": "explorerViewletVisible && filesExplorerFocus && !inputFocus"
    },
    {
        "key": "shift+p",
        "command": "filesExplorer.paste",
        "when": "explorerViewletVisible && filesExplorerFocus && !inputFocus"
    },
    {
        "key": "shift+n",
        "command": "explorer.newFile",
        "when": "explorerViewletVisible && filesExplorerFocus && !inputFocus"
    },
    {
        "key": "shift+d",
        "command": "deleteFile",
        "when": "explorerViewletVisible && filesExplorerFocus && !inputFocus"
    },
    {
        "key": "shift+k",
        "command": "explorer.newFolder",
        "when": "explorerViewletVisible && filesExplorerFocus && !inputFocus"
    },
    {
        "key": "shift+m",
        "command": "filesExplorer.cut",
        "when": "explorerViewletVisible && filesExplorerFocus && !inputFocus"
    },
    
    // ターミナル操作 (Ctrl+f を prefix のように)
    {
        "key": "ctrl+f space",
        "command": "workbench.action.togglePanel"
    },
    {
        "key": "ctrl+f t",
        "command": "workbench.action.terminal.focus"
    },
    {
        "key": "ctrl+f d",
        "command": "workbench.debug.action.focusRepl"
    },
    {
        "key": "ctrl+f o",
        "command": "workbench.panel.output.focus"
    },
    {
        "key": "ctrl+f p",
        "command": "workbench.panel.markers.view.focus"
    },
    {
        "key": "ctrl+f c",
        "command": "workbench.action.terminal.new"
    },
    {
        "key": "ctrl+f l",
        "command": "workbench.action.terminal.focusNext",
        "when": "terminalFocus"
    },
    {
        "key": "ctrl+f h",
        "command": "workbench.action.terminal.focusPrevious",
        "when": "terminalFocus"
    },
    {
        "key": "ctrl+f x",
        "command": "workbench.action.terminal.kill",
        "when": "terminalFocus"
    },
    {
        "key": "ctrl+f shift+\\",
        "command": "workbench.action.terminal.split",
        "when": "terminalFocus && terminalProcessSupported || terminalFocus && terminalWebExtensionContributedProfile"
    },

    // Neovim 側の Ctrl+f を呼ぶための設定
    {
        "key": "ctrl+f ctrl+f space",
        "command": "vscode-neovim.ctrl-f",
        "when": "editorTextFocus && neovim.ctrlKeysNormal.f && neovim.init && neovim.mode != 'insert' && editorLangId not in 'neovim.editorLangIdExclusions'"
    },
    
    // カーソル操作系 (VSCodeコマンドに紐づけ)
    {
        "key": "g g",
        "command": "cursorTop",
        "when": "editorTextFocus && neovim.mode != 'insert'"
    },
    {
        "key": "g k",
        "command": "cursorUp",
        "when": "editorTextFocus && neovim.mode != insert"
    },
    {
        "key": "g j",
        "command": "cursorDown",
        "when": "editorTextFocus && neovim.mode != insert"
    },
    {
        "key": "g 0",
        "command": "cursorHome",
        "when": "editorTextFocus && neovim.mode != insert"
    },
    {
        "key": "g shift+6",
        "command": "cursorHomeSelect",
        "when": "editorTextFocus && neovim.mode != insert"
    },
    {
        "key": "g shift+4",
        "command": "cursorEnd",
        "when": "editorTextFocus && neovim.mode != insert"
    },

    // suggest補完を Ctrl+n に割り当て
    {
        "key": "ctrl+n",
        "command": "editor.action.triggerSuggest",
        "when": "editorHasCompletionItemProvider && textInputFocus && !editorReadonly && !suggestWidgetVisible"
    }
]
```

### （1）エディタ切り替え・ウィンドウ操作など

| キーバインド | 操作内容 |
| --- | --- |
| `ctrl+l` (Normal) | 次のエディタへ移動 `workbench.action.nextEditor` |
| `ctrl+h` (Normal) | 前のエディタへ移動 `workbench.action.previousEditor` |

### （2）LSP 関連

| キーバインド | 操作内容 |
| --- | --- |
| `g d` (Normal) | 定義へ移動 `editor.action.revealDefinition` |
| `g b` (Normal) | 前の場所に戻る `workbench.action.navigateBack` |
| `shift+k` (Normal) | Hover 情報を表示 `editor.action.showHover` |

### （3）Explorer (ファイルツリー) の操作

| キーバインド | 操作内容 |
| --- | --- |
| `space e` | エクスプローラの表示/非表示をトグル (`focusFilesExplorer` or `closeSidebar`) |
| `Enter` (フォルダの場合) | フォルダの展開/折りたたみ `list.toggleExpand` |
| `shift+r` | ファイル名変更 `renameFile` |
| `shift+c` | ファイルコピー `filesExplorer.copy` |
| `shift+p` | ファイルペースト `filesExplorer.paste` |
| `shift+n` | 新規ファイル作成 `explorer.newFile` |
| `shift+d` | ファイル削除 `deleteFile` |
| `shift+k` | 新規フォルダ作成 `explorer.newFolder` |
| `shift+m` | ファイル/ディレクトリをカット `filesExplorer.cut` |

### （4）ターミナル操作（tmux ライクに Ctrl+f を prefix 化）

| キーバインド | 操作内容 |
| --- | --- |
| `ctrl+f space` | パネル(ターミナルなど) の表示/非表示をトグル `workbench.action.togglePanel` |
| `ctrl+f t` | ターミナルにフォーカス `workbench.action.terminal.focus` |
| `ctrl+f d` | デバッグコンソールにフォーカス `workbench.debug.action.focusRepl` |
| `ctrl+f o` | 出力パネルにフォーカス `workbench.panel.output.focus` |
| `ctrl+f p` | Problems パネルにフォーカス `workbench.panel.markers.view.focus` |
| `ctrl+f c` | ターミナルを新規作成 `workbench.action.terminal.new` |
| `ctrl+f l` (terminalFocus 時) | 次のターミナルへ移動 `workbench.action.terminal.focusNext` |
| `ctrl+f h` (terminalFocus 時) | 前のターミナルへ移動 `workbench.action.terminal.focusPrevious` |
| `ctrl+f x` (terminalFocus 時) | ターミナルを kill `workbench.action.terminal.kill` |
| `ctrl+f shift+\\` (terminalFocus) | ターミナル分割 `workbench.action.terminal.split` |
| `ctrl+f ctrl+f space` | Neovim 側の `Ctrl+f` を呼び出し `vscode-neovim.ctrl-f` |

### （5）カーソル操作（VSCode コマンドで移動）

| キーバインド | 操作内容 |
| --- | --- |
| `g g` | ファイル先頭へ移動 `cursorTop` |
| `g k` | 上へ移動 `cursorUp` |
| `g j` | 下へ移動 `cursorDown` |
| `g 0` | 行頭へ移動 `cursorHome` |
| `g shift+6` (`g ^` 相当) | 行頭へ移動＋選択 `cursorHomeSelect` |
| `g shift+4` (`g $` 相当) | 行末へ移動 `cursorEnd` |
| `ctrl+n` | サジェスト補完をトリガー `editor.action.triggerSuggest` |

## 3\. settings.jsonの設定

### 補足

### (1). ユーザ環境依存の設定

```json
"vscode-neovim.neovimExecutablePaths.win32": "<Your neovim executable path>",
    "vscode-neovim.neovimInitVimPaths.darwin": "<Your init.lua file path>",
```

に関しては、最初に設定したときに自動で `settings.json` に登録されます。

#### 重要: Escの代わりにjkを登録する

`ESC` の代わりに `jk` でノーマルモードに移行する設定は `init.lua` や `keybindings.json` に書くのではなく `settings.json` に記載します。(落とし穴)

```json
"vscode-neovim.compositeKeys": {
        "jk": {
          "command": "vscode-neovim.escape",
        }
    },
```

### (2). Workbench設定

ファイルを開いたときにpreviewモードを使わない設定にしています。

| 項目 | 説明 |
| --- | --- |
| `workbench.list.defaultFindMode` | リスト系UI(ファイルエクスプローラ/検索結果など)で文字入力した時の挙動を設定。   `filter` は入力内容で絞り込み。 |
| `workbench.list.typeNavigationMode` | リストのタイプナビゲーションモード。   `automatic` は入力内容に応じて自動的に移動/フィルタリングを行う |
| `workbench.editor.enablePreview` | `false` にすると、エディタでファイルを開くときにプレビューモードを使いません。 |
| `workbench.editor.enablePreviewFromQuickOpen` | `false` にすると、QuickOpen (⌘P/Ctrl+Pなど) でファイルを開いたときもプレビューモードを使いません。 |

---

### (3). Editor設定

ここはお好みです。エディタの折り返しや見た目の部分を設定しています。

| 項目 | 説明 |
| --- | --- |
| `editor.wordWrap` | `on` にすると、エディタの表示がウィンドウ幅に応じて折り返されます。 |
| `editor.wordWrapColumn` | 折り返しの基準カラムを指定しています。ここでは `100` 。 |
| `editor.renderWhitespace` | `all` にすると、すべての空白文字（スペースやタブ）を視覚的に表示します。 |
| `editor.renderControlCharacters` | `true` にすると、制御文字(改行文字や一部の非表示文字)を可視化します。 |
| `editor.quickSuggestions`   (`other`, `comments`, `strings`) | コード入力中にクイックサジェスト(インライン補完)を有効にします。   ここでは `on` にしており、他の要素・コメント・文字列内でも補完が働きます。 |
| `editor.acceptSuggestionOnCommitCharacter` | `false` にすると、カンマやスペースなどの「コミット文字」で補完が確定しなくなります。Enter などで確定。 |

## 終わりに

vscode-neovim最高!  
よきvimライフを!

[0](https://qiita.com/hwatahik/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fhwatahik%2Fitems%2F3e94ef647503ee0ed524&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fhwatahik%2Fitems%2F3e94ef647503ee0ed524&realm=qiita)

[29](https://qiita.com/hwatahik/items/3e94ef647503ee0ed524/likers)

いいねしたユーザー一覧へ移動

16