---
title: "モテるターミナルにカスタマイズしよう（WezTerm）"
source: "https://zenn.dev/mozumasu/articles/mozumasu-wezterm-customization"
author:
  - "[[Zenn]]"
published: 2024-09-23
created: 2025-11-23
description:
tags:
  - "clippings"
---
293

33[tech](https://zenn.dev/tech-or-idea)

## はじめに完成系を晒す

ターミナルがかっこいいとモテるらしいというのをどこかの記事で読んだので、かっこよくターミナルをカスタマイズしてみました。  
意外と簡単にできるので身構えずにコーヒーでも飲みながらやってみてください。☕️

完成系はこんな感じです。スケスケ&ぼかしが入っていていい感じですね！

![alt WezTerm after setting](https://res.cloudinary.com/zenn/image/fetch/s--yKHLYOYL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/78039bb7d2e857e7a0b4e84d.png%3Fsha%3D638c3175b9f2a176f538e2e0e1012a35e4f21ded)  
現在有効になっているタブが黄色くなります。

![alt WezTerm after setting](https://storage.googleapis.com/zenn-user-upload/deployed-images/08a43a1e073bbbdd72d4a4d3.gif?sha=5754a4180c7b26d61c0699e7df1e903ca46a63c8)

ついでにNeovimの画面も晒しておきます。  
![alt WezTerm after setting](https://res.cloudinary.com/zenn/image/fetch/s--gdTvEYqy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/d1954b2b78c01ad63e2b83b7.png%3Fsha%3D56fc201c5804bb39a91bddf0fdbb9327c865549d)

使用しているターミナルは [WezTerm](https://wezfurlong.org/wezterm/) です。  
WezTermの特徴は以下の通りです。

- Rust製でとにかく早い
- 軽量
- カスタマイズ性高め
- 設定ファイルはNeovimと同じLua言語
- tmuxのような画面分割ができる
- 豊富なドキュメントがある
- **コピーモードが使いやすい！！！**

`Leader` キー(自分はControl+Q) + `[`でコピーモードに入り、Vimのキーバインドで操作してコピーすることができます。  
![alt WezTerm after setting](https://storage.googleapis.com/zenn-user-upload/deployed-images/f96e76c5ff783844cbd0d3aa.gif?sha=4d6a8372525d51ae14cb0703d5f09ce9dc97a61b)

コピーモードのキーバインドはデフォルトでは `Control+Shift+X` に割り当てられています。

他にも色々良さがあるので、詳しくは公式ドキュメントを参照していただければと思います。  
カスタマイズをたくさんしたい人にはオススメのターミナルです。

### 忙しい人向け：完成系の設定ファイル

読むのが面倒な人向けに完成系の設定ファイルを晒しておきます。

WezTerm設定ファイル

~/.config/wezterm/wezterm.lua

```lua
local wezterm = require("wezterm")
local config = wezterm.config_builder()

config.automatically_reload_config = true
config.font_size = 12.0
config.use_ime = true
config.window_background_opacity = 0.85
config.macos_window_background_blur = 20

----------------------------------------------------
-- Tab
----------------------------------------------------
-- タイトルバーを非表示
config.window_decorations = "RESIZE"
-- タブバーの表示
config.show_tabs_in_tab_bar = true
-- タブが一つの時は非表示
config.hide_tab_bar_if_only_one_tab = true
-- falseにするとタブバーの透過が効かなくなる
-- config.use_fancy_tab_bar = false

-- タブバーの透過
config.window_frame = {
  inactive_titlebar_bg = "none",
  active_titlebar_bg = "none",
}

-- タブバーを背景色に合わせる
config.window_background_gradient = {
  colors = { "#000000" },
}

-- タブの追加ボタンを非表示
config.show_new_tab_button_in_tab_bar = false
-- nightlyのみ使用可能
-- タブの閉じるボタンを非表示
config.show_close_tab_button_in_tabs = false

-- タブ同士の境界線を非表示
config.colors = {
  tab_bar = {
    inactive_tab_edge = "none",
  },
}

-- タブの形をカスタマイズ
-- タブの左側の装飾
local SOLID_LEFT_ARROW = wezterm.nerdfonts.ple_lower_right_triangle
-- タブの右側の装飾
local SOLID_RIGHT_ARROW = wezterm.nerdfonts.ple_upper_left_triangle

wezterm.on("format-tab-title", function(tab, tabs, panes, config, hover, max_width)
  local background = "#5c6d74"
  local foreground = "#FFFFFF"
  local edge_background = "none"
  if tab.is_active then
    background = "#ae8b2d"
    foreground = "#FFFFFF"
  end
  local edge_foreground = background
  local title = "   " .. wezterm.truncate_right(tab.active_pane.title, max_width - 1) .. "   "
  return {
    { Background = { Color = edge_background } },
    { Foreground = { Color = edge_foreground } },
    { Text = SOLID_LEFT_ARROW },
    { Background = { Color = background } },
    { Foreground = { Color = foreground } },
    { Text = title },
    { Background = { Color = edge_background } },
    { Foreground = { Color = edge_foreground } },
    { Text = SOLID_RIGHT_ARROW },
  }
end)

----------------------------------------------------
-- keybinds
----------------------------------------------------
config.disable_default_key_bindings = true
config.keys = require("keybinds").keys
config.key_tables = require("keybinds").key_tables
config.leader = { key = "q", mods = "CTRL", timeout_milliseconds = 2000 }

return config
```

~/.config/wezterm/keybinds.lua

```lua
local wezterm = require("wezterm")
local act = wezterm.action

-- Show which key table is active in the status area
wezterm.on("update-right-status", function(window, pane)
  local name = window:active_key_table()
  if name then
    name = "TABLE: " .. name
  end
  window:set_right_status(name or "")
end)

return {
  keys = {
    {
      -- workspaceの切り替え
      key = "w",
      mods = "LEADER",
      action = act.ShowLauncherArgs({ flags = "WORKSPACES", title = "Select workspace" }),
    },
    {
      --workspaceの名前変更
      key = "$",
      mods = "LEADER",
      action = act.PromptInputLine({
        description = "(wezterm) Set workspace title:",
        action = wezterm.action_callback(function(win, pane, line)
          if line then
            wezterm.mux.rename_workspace(wezterm.mux.get_active_workspace(), line)
          end
        end),
      }),
    },
    {
      key = "W",
      mods = "LEADER|SHIFT",
      action = act.PromptInputLine({
        description = "(wezterm) Create new workspace:",
        action = wezterm.action_callback(function(window, pane, line)
          if line then
            window:perform_action(
              act.SwitchToWorkspace({
                name = line,
              }),
              pane
            )
          end
        end),
      }),
    },
    -- コマンドパレット表示
    { key = "p", mods = "SUPER", action = act.ActivateCommandPalette },
    -- Tab移動
    { key = "Tab", mods = "CTRL", action = act.ActivateTabRelative(1) },
    { key = "Tab", mods = "SHIFT|CTRL", action = act.ActivateTabRelative(-1) },
    -- Tab入れ替え
    { key = "{", mods = "LEADER", action = act({ MoveTabRelative = -1 }) },
    -- Tab新規作成
    { key = "t", mods = "SUPER", action = act({ SpawnTab = "CurrentPaneDomain" }) },
    -- Tabを閉じる
    { key = "w", mods = "SUPER", action = act({ CloseCurrentTab = { confirm = true } }) },
    { key = "}", mods = "LEADER", action = act({ MoveTabRelative = 1 }) },

    -- 画面フルスクリーン切り替え
    { key = "Enter", mods = "ALT", action = act.ToggleFullScreen },

    -- コピーモード
    -- { key = 'X', mods = 'LEADER', action = act.ActivateKeyTable{ name = 'copy_mode', one_shot =false }, },
    { key = "[", mods = "LEADER", action = act.ActivateCopyMode },
    -- コピー
    { key = "c", mods = "SUPER", action = act.CopyTo("Clipboard") },
    -- 貼り付け
    { key = "v", mods = "SUPER", action = act.PasteFrom("Clipboard") },

    -- Pane作成 leader + r or d
    { key = "d", mods = "LEADER", action = act.SplitVertical({ domain = "CurrentPaneDomain" }) },
    { key = "r", mods = "LEADER", action = act.SplitHorizontal({ domain = "CurrentPaneDomain" }) },
    -- Paneを閉じる leader + x
    { key = "x", mods = "LEADER", action = act({ CloseCurrentPane = { confirm = true } }) },
    -- Pane移動 leader + hlkj
    { key = "h", mods = "LEADER", action = act.ActivatePaneDirection("Left") },
    { key = "l", mods = "LEADER", action = act.ActivatePaneDirection("Right") },
    { key = "k", mods = "LEADER", action = act.ActivatePaneDirection("Up") },
    { key = "j", mods = "LEADER", action = act.ActivatePaneDirection("Down") },
    -- Pane選択
    { key = "[", mods = "CTRL|SHIFT", action = act.PaneSelect },
    -- 選択中のPaneのみ表示
    { key = "z", mods = "LEADER", action = act.TogglePaneZoomState },

    -- フォントサイズ切替
    { key = "+", mods = "CTRL", action = act.IncreaseFontSize },
    { key = "-", mods = "CTRL", action = act.DecreaseFontSize },
    -- フォントサイズのリセット
    { key = "0", mods = "CTRL", action = act.ResetFontSize },

    -- タブ切替 Cmd + 数字
    { key = "1", mods = "SUPER", action = act.ActivateTab(0) },
    { key = "2", mods = "SUPER", action = act.ActivateTab(1) },
    { key = "3", mods = "SUPER", action = act.ActivateTab(2) },
    { key = "4", mods = "SUPER", action = act.ActivateTab(3) },
    { key = "5", mods = "SUPER", action = act.ActivateTab(4) },
    { key = "6", mods = "SUPER", action = act.ActivateTab(5) },
    { key = "7", mods = "SUPER", action = act.ActivateTab(6) },
    { key = "8", mods = "SUPER", action = act.ActivateTab(7) },
    { key = "9", mods = "SUPER", action = act.ActivateTab(-1) },

    -- コマンドパレット
    { key = "p", mods = "SHIFT|CTRL", action = act.ActivateCommandPalette },
    -- 設定再読み込み
    { key = "r", mods = "SHIFT|CTRL", action = act.ReloadConfiguration },
    -- キーテーブル用
    { key = "s", mods = "LEADER", action = act.ActivateKeyTable({ name = "resize_pane", one_shot = false }) },
    {
      key = "a",
      mods = "LEADER",
      action = act.ActivateKeyTable({ name = "activate_pane", timeout_milliseconds = 1000 }),
    },
  },
  -- キーテーブル
  -- https://wezfurlong.org/wezterm/config/key-tables.html
  key_tables = {
    -- Paneサイズ調整 leader + s
    resize_pane = {
      { key = "h", action = act.AdjustPaneSize({ "Left", 1 }) },
      { key = "l", action = act.AdjustPaneSize({ "Right", 1 }) },
      { key = "k", action = act.AdjustPaneSize({ "Up", 1 }) },
      { key = "j", action = act.AdjustPaneSize({ "Down", 1 }) },

      -- Cancel the mode by pressing escape
      { key = "Enter", action = "PopKeyTable" },
    },
    activate_pane = {
      { key = "h", action = act.ActivatePaneDirection("Left") },
      { key = "l", action = act.ActivatePaneDirection("Right") },
      { key = "k", action = act.ActivatePaneDirection("Up") },
      { key = "j", action = act.ActivatePaneDirection("Down") },
    },
    -- copyモード leader + [
    copy_mode = {
      -- 移動
      { key = "h", mods = "NONE", action = act.CopyMode("MoveLeft") },
      { key = "j", mods = "NONE", action = act.CopyMode("MoveDown") },
      { key = "k", mods = "NONE", action = act.CopyMode("MoveUp") },
      { key = "l", mods = "NONE", action = act.CopyMode("MoveRight") },
      -- 最初と最後に移動
      { key = "^", mods = "NONE", action = act.CopyMode("MoveToStartOfLineContent") },
      { key = "$", mods = "NONE", action = act.CopyMode("MoveToEndOfLineContent") },
      -- 左端に移動
      { key = "0", mods = "NONE", action = act.CopyMode("MoveToStartOfLine") },
      { key = "o", mods = "NONE", action = act.CopyMode("MoveToSelectionOtherEnd") },
      { key = "O", mods = "NONE", action = act.CopyMode("MoveToSelectionOtherEndHoriz") },
      --
      { key = ";", mods = "NONE", action = act.CopyMode("JumpAgain") },
      -- 単語ごと移動
      { key = "w", mods = "NONE", action = act.CopyMode("MoveForwardWord") },
      { key = "b", mods = "NONE", action = act.CopyMode("MoveBackwardWord") },
      { key = "e", mods = "NONE", action = act.CopyMode("MoveForwardWordEnd") },
      -- ジャンプ機能 t f
      { key = "t", mods = "NONE", action = act.CopyMode({ JumpForward = { prev_char = true } }) },
      { key = "f", mods = "NONE", action = act.CopyMode({ JumpForward = { prev_char = false } }) },
      { key = "T", mods = "NONE", action = act.CopyMode({ JumpBackward = { prev_char = true } }) },
      { key = "F", mods = "NONE", action = act.CopyMode({ JumpBackward = { prev_char = false } }) },
      -- 一番下へ
      { key = "G", mods = "NONE", action = act.CopyMode("MoveToScrollbackBottom") },
      -- 一番上へ
      { key = "g", mods = "NONE", action = act.CopyMode("MoveToScrollbackTop") },
      -- viweport
      { key = "H", mods = "NONE", action = act.CopyMode("MoveToViewportTop") },
      { key = "L", mods = "NONE", action = act.CopyMode("MoveToViewportBottom") },
      { key = "M", mods = "NONE", action = act.CopyMode("MoveToViewportMiddle") },
      -- スクロール
      { key = "b", mods = "CTRL", action = act.CopyMode("PageUp") },
      { key = "f", mods = "CTRL", action = act.CopyMode("PageDown") },
      { key = "d", mods = "CTRL", action = act.CopyMode({ MoveByPage = 0.5 }) },
      { key = "u", mods = "CTRL", action = act.CopyMode({ MoveByPage = -0.5 }) },
      -- 範囲選択モード
      { key = "v", mods = "NONE", action = act.CopyMode({ SetSelectionMode = "Cell" }) },
      { key = "v", mods = "CTRL", action = act.CopyMode({ SetSelectionMode = "Block" }) },
      { key = "V", mods = "NONE", action = act.CopyMode({ SetSelectionMode = "Line" }) },
      -- コピー
      { key = "y", mods = "NONE", action = act.CopyTo("Clipboard") },

      -- コピーモードを終了
      {
        key = "Enter",
        mods = "NONE",
        action = act.Multiple({ { CopyTo = "ClipboardAndPrimarySelection" }, { CopyMode = "Close" } }),
      },
      { key = "Escape", mods = "NONE", action = act.CopyMode("Close") },
      { key = "c", mods = "CTRL", action = act.CopyMode("Close") },
      { key = "q", mods = "NONE", action = act.CopyMode("Close") },
    },
  },
}
```

## WezTermのインストール

Homebrewでインストールする場合は下記のコマンドを実行します。  
※一部nightly限定の設定があるため、この記事ではnightly版を使用しています。

```bash
# 通常版
brew install --cask wezterm

# nightly版
brew install --cask wezterm@nightly
```

> Homebrew: [https://formulae.brew.sh/cask/wezterm](https://formulae.brew.sh/cask/wezterm)  
> WezTerm公式: [https://wezfurlong.org/wezterm/installation](https://wezfurlong.org/wezterm/installation)

WezTermを立ち上げると下記のような画面が表示されます。  
![alt WezTerm before setting](https://res.cloudinary.com/zenn/image/fetch/s--rK38J7c2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/a558c3d84d75f4d0de4afe4b.png%3Fsha%3D58d2ae85ea61d23f82bf6c63c52dd4c905417180)  
※画像のターミナルは [starship](https://starship.rs/) を導入しているため、プロンプトの部分はすでにカスタマイズされています。  
starshipのカスタマイズ方法は記憶の彼方にあるのでとりあえずコードだけ晒しておきます。

starshipの設定

~/.config/starship.toml

```toml
format = """
$directory\
[ ](fg:#88C0D0 bg:#1d2230)\
$git_branch\
$git_status\
[ ](fg:#1d2230)\
\n$character
"""

right_format = """
$cmd_duration\
$username\
✨
$time
"""

# コマンドラインに1行分のスペースを入れる
add_newline = true

[username]
style_user = "white bold"
style_root = "black bold"
format = "user: [$user]($style) "
disabled = false

# left_promptとright_promptの間を何で埋めるか設定
[fill]
symbol = ' '

[directory]
style = "fg:#2E3440 bg:#88C0D0 bold"
# format = "[ $path ]($style)"
truncation_length = 10
truncate_to_repo = false
truncation_symbol = "…/"
# truncation_symbol = ' ' # nf-fa-folder_open
# truncate_to_repo = false
# style = 'fg:#7aa2f7 bg:#1a1b26'
read_only = ' 󰌾 ' # nf-md-lock
read_only_style = 'fg:#f7768e bg:#1a1b26'
format = '[ $path ]($style)[$read_only]($read_only_style)'

[directory.substitutions]
"Documents" = "󰈙 "
"Downloads" = " "
"Music" = " "
"Pictures" = " "

[aws]
disabled = true
[gcloud]
disabled = true

[git_branch]
symbol = ""
style = "bg:#1d2230"
format = '[[ $symbol $branch ](fg:#769ff0 bg:#1d2230)]($style)'

[git_status]
style = "bg:#1d2230"
format = '[[($all_status$ahead_behind )](fg:#769ff0 bg:#1d2230)]($style)'

[cmd_duration]
min_time = 1
style = 'fg:#e0af68'
format = "[$duration]($style)" # nf-pl-right_soft_divider, nf-mdi-clock

[time]
disabled = false
time_format = "%R" # Hour:Minute Format
# style = "bg:#1d2230"
format = '[[   $time ](fg:#a0a9cb)]($style)'

[character]
vimcmd_symbol = '[V](bold green) '
```

## 設定ファイルの用意

設定ファイルは `~/.wezterm.lua` に配置します。  
uiの設定とキーバインドを別のファイルに分けて管理する派の方は `~/.config/wezterm/wezterm.lua` と `~/.config/wezterm/keybind.lua` で分けるのもありです。  
この記事では後者で設定していきます。

このカスタマイズを機に設定ファイルをGitHubで管理したくなった場合は下記の記事が参考にしてください。

まずは、 `~/.config/wezterm` ディレクトリを作成し、その中に `wezterm.lua` を作成します。

```bash
mkdir -p ~/.config/wezterm
touch ~/.config/wezterm/wezterm.lua
```

続いて、設定ファイルに下記のような雛形を記述します。

~/.config/wezterm/wezterm.lua

```lua
-- Pull in the wezterm API
local wezterm = require 'wezterm'

-- This will hold the configuration.
local config = wezterm.config_builder()
config.automatically_reload_config = true

-- This is where you actually apply your config choices

-- For example, changing the color scheme:
config.color_scheme = 'AdventureTime'

-- and finally, return the configuration to wezterm
return config
```

> [Configuration - Wez's Terminal Emulator](https://arc.net/l/quote/upsihqso)

## window全体の設定

### fontサイズの変更

まずは作業がしやすいようにフォントサイズを変更しましょう。  
また、カラースキームは設定しないため削除します。  
(Luaのコメントアウトがdiffで-表示されてしまうためコメントアウトも削除しています。)

~/.config/wezterm/wezterm.lua

```
- -- Pull in the wezterm API
local wezterm = require 'wezterm'
-
- -- This will hold the configuration.
local config = wezterm.config_builder()

config.automatically_reload_config = true
+ config.font_size = 12.0

- -- This is where you actually apply your config choices
-
- -- For example, changing the color scheme:
- config.color_scheme = 'AdventureTime'
-
- -- and finally, return the configuration to wezterm
return config
```

> [font\_size - Wez's Terminal Emulator](https://wezfurlong.org/wezterm/config/lua/config/font_size.html?h=font_size)

### IMEで日本語を入力をできるようにする

IMEで日本語を入力するには下記の設定が必要なので追加します。

~/.config/wezterm/wezterm.lua

```
local wezterm = require 'wezterm'
local config = wezterm.config_builder()

config.automatically_reload_config = true
config.font_size = 12.0
+ config.use_ime = true

return config
```

> [use\_ime - Wez's Terminal Emulator](https://wezfurlong.org/wezterm/config/lua/config/use_ime.html?h=use_ime)

### 背景の透過

続いては己の気分をブチ上げるための設定をします。  
そう、背景の透過です。  
人類皆一度はスケスケのターミナルに憧れを抱くものです。

~/.config/wezterm/wezterm.lua

```
local wezterm = require 'wezterm'
local config = wezterm.config_builder()

config.automatically_reload_config = true
config.font_size = 12.0
config.use_ime = true
+ config.window_background_opacity = 0.85

return config
```

> [Colors & Appearance - Wez's Terminal Emulator](https://wezfurlong.org/wezterm/config/appearance.html?h=window_background_opacity#window-background-opacity)

値は0~1の間で設定します。0に近いほどスケスケになります。  
0.85だと画像のような透け感になります。

![alt opacity 0.85](https://res.cloudinary.com/zenn/image/fetch/s--qaW0c9kh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/d05b4d6b32487d4cb77e1039.png%3Fsha%3Dedb52c569fdb75c1a2f2e32f5dd3728e46fb252d)

なんだかちょっとできる人の雰囲気がでてきましたね。

### ぼかしを追加

とはいえ、スケスケすぎると文字が見づらいというもの。  
ぼかしを入れてスケスケと視認性を両立し、「こいつ、、、デキる！」感を演出していきましょう。

~/.config/wezterm/wezterm.lua

```
local wezterm = require 'wezterm'
local config = wezterm.config_builder()

config.automatically_reload_config = true
config.font_size = 12.0
config.use_ime = true
config.window_background_opacity = 0.85
+ config.macos_window_background_blur = 20

return config
```

> mac: [macos\_window\_background\_blur - Wez's Terminal Emulator](https://wezfurlong.org/wezterm/config/lua/config/macos_window_background_blur.html?h=macos_window_background_blur)  
> windows: [win32\_system\_backdrop - Wez's Terminal Emulator](https://wezfurlong.org/wezterm/config/lua/config/win32_system_backdrop.html)

![alt blur 20](https://res.cloudinary.com/zenn/image/fetch/s--TAnNIsRp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/66dd41f59e8dbee0d2984c08.png%3Fsha%3Df3f78e57c87098d03b7b41bd6bb61462a34a5445)

＼( 'ω')／ウオオオオオアアアーーーーッ！！！良いｯ！！！  
ここまでくるともう、あなたはデキる人です。  
私が保証しましょう。

## タブの設定

先のターミナルを見てあなたはきっとこう思ったはずです。

「なんかタブがしっくりこないなぁ、、、」

そんなあなたに朗報です。  
何とこのタブ、カスタマイズできます！！！！そう！WezTermならね！！！

### タイトルバーの削除

まずはタブバーの上部のタイトルバーを消しましょう。

~/.config/wezterm/wezterm.lua

```
local wezterm = require 'wezterm'
local config = wezterm.config_builder()

config.automatically_reload_config = true
config.font_size = 12.0
config.use_ime = true
config.window_background_opacity = 0.85
config.macos_window_background_blur = 20
+ config.window_decorations = "RESIZE"

return config
```

> [window\_decorations - Wez's Terminal Emulator](https://wezfurlong.org/wezterm/config/lua/config/window_decorations.html)

![alt window_decorations](https://res.cloudinary.com/zenn/image/fetch/s--PbUTlKoO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/64ff3ef9fa5c58c130d07e0e.png%3Fsha%3Db95b5f8ddadce772be37e718f319bdbbbce6eafe)

タイトルバーが消えてちょっとすっきりしました。

### タブバーを非表示

タブを使用しない場合は下記の設定で非表示にすることができます。  
私はタブのヘビーユーザーなのでこちらは設定していません。

~/.config/wezterm/wezterm.lua

```lua
config.show_tabs_in_tab_bar = false
```

### タブが一つしかない時に非表示

タブが一つしかない時はタブバー要らんな、、、という場合は下記の設定で非表示にすることができます。

~/.config/wezterm/wezterm.lua

```
local wezterm = require 'wezterm'
local config = wezterm.config_builder()

config.automatically_reload_config = true
config.font_size = 12.0
config.use_ime = true
config.window_background_opacity = 0.85
config.macos_window_background_blur = 20
config.window_decorations = "RESIZE"
+ config.hide_tab_bar_if_only_one_tab = true

return config
```

### タブバーを透明にする

タイトルバーが消え去り、残すところはタブバーのみです。  
こいつの色も透明にしてやりましょう。

~/.config/wezterm/wezterm.lua

```
local wezterm = require 'wezterm'
local config = wezterm.config_builder()

config.automatically_reload_config = true
config.font_size = 12.0
config.use_ime = true
config.window_background_opacity = 0.85
config.macos_window_background_blur = 20
config.window_decorations = "RESIZE"
config.hide_tab_bar_if_only_one_tab = true

+ config.window_frame = {
+   inactive_titlebar_bg = "none",
+   active_titlebar_bg = "none",
+ }

return config
```

> [window\_frame - Wez's Terminal Emulator](https://wezfurlong.org/wezterm/config/lua/config/window_frame.html?h=window_frame)

![alt window_frame](https://res.cloudinary.com/zenn/image/fetch/s--txpTOGx3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/18b425386c305848ecd41c09.png%3Fsha%3D670d9b81094f3e7b650b06c7cdc8763a5768efcb)  
透明になりましたね！！

~~`config.use_fancy_tab_bar = false` を設定すると下記の画像のようになり、タブバーは透過ができなくなるため注意してください。~~  
~~カクカクしたタブをとるか、透過したタブをとるかはあなた次第です。~~

![alt use_fancy_tab_bar](https://res.cloudinary.com/zenn/image/fetch/s--HNQjM4fo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/7a1a2804fb9d9fb32377e168.png%3Fsha%3Dc4fb35992e77946f803bf638fce0e7e679cfc546)

> [use\_fancy\_tab\_bar - Wez's Terminal Emulator](https://wezfurlong.org/wezterm/config/lua/config/use_fancy_tab_bar.html)

### タブバーを背景と同じ色にする

透明にしたもののこれはこれで何だが違和感があります。  
この違和感を消すべく、タブバーを背景と同じ色にしましょう。  
デフォルトの背景が黒色なので、colorsには `#000000` を指定します。

~/.config/wezterm/wezterm.lua

```
local wezterm = require 'wezterm'
local config = wezterm.config_builder()

config.automatically_reload_config = true
config.font_size = 12.0
config.use_ime = true
config.window_background_opacity = 0.85
config.macos_window_background_blur = 20
config.window_decorations = "RESIZE"
config.hide_tab_bar_if_only_one_tab = true

 config.window_frame = {
   inactive_titlebar_bg = "none",
   active_titlebar_bg = "none",
 }
+ config.window_background_gradient = {
+   colors = { "#000000" },
+ }

return config
```

> [window\_background\_gradient - Wez's Terminal Emulator](https://wezfurlong.org/wezterm/config/lua/config/window_background_gradient.html?h=window_background_gradient)

![alt window_background_gradient](https://res.cloudinary.com/zenn/image/fetch/s--b61vA6K5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/2512482439b0a5c82564a628.png%3Fsha%3D6c9a590be6213c617c6cbeeed7eb520315b17225)  
タブバーが背景と同じ色になりました！！見栄えがいいね！！

`window_background_gradient` は背景にグラデーションをつけることもできます。  
下記は公式ドキュメントの例です。  
![alt radial-gradient](https://res.cloudinary.com/zenn/image/fetch/s--nI4qbHxJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://wezfurlong.org/wezterm/screenshots/linear-gradient.png)  
*参照: [window\_background\_gradient - Wez's Terminal Emulator](https://wezfurlong.org/wezterm/config/lua/config/window_background_gradient.html?h=window_background_gradient)*

### タブバーの+を消す

タブを追加する時は `Cmd+T` を使うので、タブバーの+はなくても困ることがありません。  
非表示にてスッキリしましょう。視界から入る情報は必要なもののみにして集中力をマシマシにしましょう。

~/.config/wezterm/wezterm.lua

```
local wezterm = require 'wezterm'
local config = wezterm.config_builder()

config.automatically_reload_config = true
config.font_size = 12.0
config.use_ime = true
config.window_background_opacity = 0.85
config.macos_window_background_blur = 20
config.window_decorations = "RESIZE"
config.hide_tab_bar_if_only_one_tab = true

 config.window_frame = {
   inactive_titlebar_bg = "none",
   active_titlebar_bg = "none",
 }
 config.window_background_gradient = {
   colors = { "#000000" },
 }
+ config.show_new_tab_button_in_tab_bar = false

return config
```

> [show\_new\_tab\_button\_in\_tab\_bar - Wez's Terminal Emulator](https://wezfurlong.org/wezterm/config/lua/config/show_new_tab_button_in_tab_bar.html?h=show_new_tab_button_in_tab_bar)

設定前  
![alt close button available](https://res.cloudinary.com/zenn/image/fetch/s---cqe1bKq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/9d5fb868daaf1a3f324c98ba.png%3Fsha%3D5d3cf1c09a445bd386ff6e2d2be0e5d1e715d4cf)  
設定後  
![alt close tab button unavailable](https://res.cloudinary.com/zenn/image/fetch/s--I-40awpt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/f98de83667415519b00e22c8.png%3Fsha%3Dca32efff9537e49ab2808284055697f0d141fc69)

### タブの閉じるボタンを非表示

タブを削除する時は `Cmd+W` を使うので、タブを閉じるボタンも非表示にしちゃいましょう。  
こちらの設定はnightlyでのみ使用可能です。(2024/9/13現在)

~/.config/wezterm/wezterm.lua

```
local wezterm = require 'wezterm'
local config = wezterm.config_builder()

config.automatically_reload_config = true
config.font_size = 12.0
config.use_ime = true
config.window_background_opacity = 0.85
config.macos_window_background_blur = 20
config.window_decorations = "RESIZE"
config.hide_tab_bar_if_only_one_tab = true

 config.window_frame = {
   inactive_titlebar_bg = "none",
   active_titlebar_bg = "none",
 }
 config.window_background_gradient = {
   colors = { "#000000" },
 }
config.show_new_tab_button_in_tab_bar = false
+ config.show_close_tab_button_in_tabs = false

return config
```

> [show\_close\_tab\_button\_in\_tabs - Wez's Terminal Emulator](https://wezfurlong.org/wezterm/config/lua/config/show_close_tab_button_in_tabs.html)

設定前  
![alt close button available](https://res.cloudinary.com/zenn/image/fetch/s--Lm7Q4ReP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/5a55e6a2b335c00dc70a31c4.png%3Fsha%3Db1228d5f04d4fdf41385c557596b49fc0ce31164)  
設定後  
![alt close tab button unavailable](https://res.cloudinary.com/zenn/image/fetch/s--yHk-qAVQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/70b830e5d13d0fa418a55ff1.png%3Fsha%3D4939fc82a6fa846b38c5d3debb57034b57a21ed7)

### タブ同士の境界線を非表示

タブの形を変更したいため、タブ同士の不要な境界線も非表示にしていきます。

~/.config/wezterm/wezterm.lua

```
local wezterm = require 'wezterm'
local config = wezterm.config_builder()

config.automatically_reload_config = true
config.font_size = 12.0
config.use_ime = true
config.window_background_opacity = 0.85
config.macos_window_background_blur = 20
config.window_decorations = "RESIZE"
config.hide_tab_bar_if_only_one_tab = true

 config.window_frame = {
   inactive_titlebar_bg = "none",
   active_titlebar_bg = "none",
 }
 config.window_background_gradient = {
   colors = { "#000000" },
 }
config.show_new_tab_button_in_tab_bar = false
config.show_close_tab_button_in_tabs = false
+ config.colors = {
+   tab_bar = {
+     inactive_tab_edge = "none",
+   },
+ }

return config
```

> [Colors & Appearance - Wez's Terminal Emulator](https://wezfurlong.org/wezterm/config/appearance.html?h=inactive_tab_edge#dynamic-color-escape-sequences)

設定前  
![alt before inactive_tab_edge](https://res.cloudinary.com/zenn/image/fetch/s--FWybYAKg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/646d8e76b27a193b6f00b3ba.png%3Fsha%3D96ebe2ab05138cca8884c7b296852345f091e730)

設定後  
![alt after inactive_tab_edge](https://res.cloudinary.com/zenn/image/fetch/s--KhlhkPAR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/2e81d339d8dfd98e6cac1394.png%3Fsha%3D9df4a947d1db926e5c6ce0030dc73ec6c4d53bce)

ちょっとした間違い探しみたいですね。

### アクティブタブに色をつける

アクティブの状態になっているタブの色も設定できます。  
通常時のタブの色と、アクティブ時のタブの色を定義しています。

~/.config/wezterm/wezterm.lua

```
local wezterm = require 'wezterm'
local config = wezterm.config_builder()

config.automatically_reload_config = true
config.font_size = 12.0
config.use_ime = true
config.window_background_opacity = 0.85
config.macos_window_background_blur = 20
config.window_decorations = "RESIZE"
config.hide_tab_bar_if_only_one_tab = true

 config.window_frame = {
   inactive_titlebar_bg = "none",
   active_titlebar_bg = "none",
 }
 config.window_background_gradient = {
   colors = { "#000000" },
 }
config.show_new_tab_button_in_tab_bar = false
config.show_close_tab_button_in_tabs = false
config.colors = {
  tab_bar = {
    inactive_tab_edge = "none",
  },
}
+ wezterm.on("format-tab-title", function(tab, tabs, panes, config, hover, max_width)
+   local background = "#5c6d74"
+   local foreground = "#FFFFFF"
+
+   if tab.is_active then
+     background = "#ae8b2d"
+     foreground = "#FFFFFF"
+   end
+
+   local title = "   " .. wezterm.truncate_right(tab.active_pane.title, max_width - 1) .. "   "
+
+   return {
+     { Background = { Color = background } },
+     { Foreground = { Color = foreground } },
+     { Text = title },
+   }
+ end)

return config
```

> [format-tab-title - Wez's Terminal Emulator](https://wezfurlong.org/wezterm/config/lua/window-events/format-tab-title.html?h=title)

無事に色がつきましたね！  
![alt format-tab-title](https://res.cloudinary.com/zenn/image/fetch/s--XMTBIAMs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/5b2c4b03575c8dbe9c19e7b8.png%3Fsha%3De7c9ea4b8d02f1d4ca72e086048dfccc616b32ab)

### タブの形を変更

タブの形も変更しちゃいましょう。

~/.config/wezterm/wezterm.lua

```
local wezterm = require 'wezterm'
local config = wezterm.config_builder()

config.automatically_reload_config = true
config.font_size = 12.0
config.use_ime = true
config.window_background_opacity = 0.85
config.macos_window_background_blur = 20
config.window_decorations = "RESIZE"
config.hide_tab_bar_if_only_one_tab = true

 config.window_frame = {
   inactive_titlebar_bg = "none",
   active_titlebar_bg = "none",
 }
 config.window_background_gradient = {
   colors = { "#000000" },
 }
config.show_new_tab_button_in_tab_bar = false
config.show_close_tab_button_in_tabs = false
config.colors = {
  tab_bar = {
    inactive_tab_edge = "none",
  },
}
+ local SOLID_LEFT_ARROW = wezterm.nerdfonts.ple_lower_right_triangle
+ local SOLID_RIGHT_ARROW = wezterm.nerdfonts.ple_upper_left_triangle

wezterm.on("format-tab-title", function(tab, tabs, panes, config, hover, max_width)
  local background = "#5c6d74"
  local foreground = "#FFFFFF"
+   local edge_background = "none"
  if tab.is_active then
    background = "#ae8b2d"
    foreground = "#FFFFFF"
  end
+   local edge_foreground = background
  local title = "   " .. wezterm.truncate_right(tab.active_pane.title, max_width - 1) .. "   "
  return {
+     { Background = { Color = edge_background } },
+     { Foreground = { Color = edge_foreground } },
+     { Text = SOLID_LEFT_ARROW },
     { Background = { Color = background } },
     { Foreground = { Color = foreground } },
    { Text = title },
+     { Background = { Color = edge_background } },
+     { Foreground = { Color = edge_foreground } },
+     { Text = SOLID_RIGHT_ARROW },
  }
end)

return config
```

> [wezterm.nerdfonts - Wez's Terminal Emulator](https://wezfurlong.org/wezterm/config/lua/wezterm/nerdfonts.html?h=nerd)

\\\\⭐️⭐️完成⭐️⭐️//

![alt WezTerm after setting](https://res.cloudinary.com/zenn/image/fetch/s--yKHLYOYL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/78039bb7d2e857e7a0b4e84d.png%3Fsha%3D638c3175b9f2a176f538e2e0e1012a35e4f21ded)

公式ドキュメントには他にもたくさんの設定があります。  
ぜひ自分好みにカスタマイズしてみてください。

## キーバインドの設定

見た目が整ったところで、操作性も整えていきましょう。

### キーバインド設定ファイルを作成

WezTermのキーバインドはたくさんあります。  
下記のコマンドを入力すると、現在の設定が表示されます。

```shell
wezterm show-keys
```

...結構ありますね。この量を全て書いていくのは大変なので現在の設定をファイルに流し込んでからカスタマイズしていきましょう。

```shell
cd ~/.config/wezterm
wezterm show-keys --lua > keybinds.lua
```

keybinds.luaを読み込めるようにwezterm.luaに下記を追加しましょう。

~/.config/wezterm/wezterm.lua

```lua
config.keys = require("keybinds").keys
config.key_tables = require("keybinds").key_tables
```

デフォルトのキーバインドを無効にする場合は下記の設定を追加します。

~/.config/wezterm/wezterm.lua

```lua
config.disable_default_key_bindings = true
```

### leaderキーの設定

`leader` キーとはprefixキーのことで、Vimでいうところの `<Leader>` にあたります。  
つまり、「これからショートカットキーを打つモードに入るぜ！」という合図をするのようなものです。  
デフォルトでは `Control` + `a` に設定されていますが、このキーバインドはEmacsの行頭へ移動するキーバインドと被るため私は `Control` + `q` に変更しています。

~/.config/wezterm/wezterm.lua

```lua
config.leader = { key = "q", mods = "CTRL", timeout_milliseconds = 2000 }
```

> [Key Binding - Wez's Terminal Emulator](https://wezfurlong.org/wezterm/config/keys.html#voidsymbol)

自分が設定しているキーバインドも置いておきます。

keybinds.lua

~/.config/wezterm/keybinds.lua

```lua
local wezterm = require("wezterm")
local act = wezterm.action

-- Show which key table is active in the status area
wezterm.on("update-right-status", function(window, pane)
  local name = window:active_key_table()
  if name then
    name = "TABLE: " .. name
  end
  window:set_right_status(name or "")
end)

return {
  keys = {
    {
      -- workspaceの切り替え
      key = "w",
      mods = "LEADER",
      action = act.ShowLauncherArgs({ flags = "WORKSPACES", title = "Select workspace" }),
    },
    {
      --workspaceの名前変更
      key = "$",
      mods = "LEADER",
      action = act.PromptInputLine({
        description = "(wezterm) Set workspace title:",
        action = wezterm.action_callback(function(win, pane, line)
          if line then
            wezterm.mux.rename_workspace(wezterm.mux.get_active_workspace(), line)
          end
        end),
      }),
    },
    {
      key = "W",
      mods = "LEADER|SHIFT",
      action = act.PromptInputLine({
        description = "(wezterm) Create new workspace:",
        action = wezterm.action_callback(function(window, pane, line)
          if line then
            window:perform_action(
              act.SwitchToWorkspace({
                name = line,
              }),
              pane
            )
          end
        end),
      }),
    },
    -- コマンドパレット表示
    { key = "p", mods = "SUPER", action = act.ActivateCommandPalette },
    -- Tab移動
    { key = "Tab", mods = "CTRL", action = act.ActivateTabRelative(1) },
    { key = "Tab", mods = "SHIFT|CTRL", action = act.ActivateTabRelative(-1) },
    -- Tab入れ替え
    { key = "{", mods = "LEADER", action = act({ MoveTabRelative = -1 }) },
    -- Tab新規作成
    { key = "t", mods = "SUPER", action = act({ SpawnTab = "CurrentPaneDomain" }) },
    -- Tabを閉じる
    { key = "w", mods = "SUPER", action = act({ CloseCurrentTab = { confirm = true } }) },
    { key = "}", mods = "LEADER", action = act({ MoveTabRelative = 1 }) },

    -- 画面フルスクリーン切り替え
    { key = "Enter", mods = "ALT", action = act.ToggleFullScreen },

    -- コピーモード
    -- { key = 'X', mods = 'LEADER', action = act.ActivateKeyTable{ name = 'copy_mode', one_shot =false }, },
    { key = "[", mods = "LEADER", action = act.ActivateCopyMode },
    -- コピー
    { key = "c", mods = "SUPER", action = act.CopyTo("Clipboard") },
    -- 貼り付け
    { key = "v", mods = "SUPER", action = act.PasteFrom("Clipboard") },

    -- Pane作成 leader + r or d
    { key = "d", mods = "LEADER", action = act.SplitVertical({ domain = "CurrentPaneDomain" }) },
    { key = "r", mods = "LEADER", action = act.SplitHorizontal({ domain = "CurrentPaneDomain" }) },
    -- Paneを閉じる leader + x
    { key = "x", mods = "LEADER", action = act({ CloseCurrentPane = { confirm = true } }) },
    -- Pane移動 leader + hlkj
    { key = "h", mods = "LEADER", action = act.ActivatePaneDirection("Left") },
    { key = "l", mods = "LEADER", action = act.ActivatePaneDirection("Right") },
    { key = "k", mods = "LEADER", action = act.ActivatePaneDirection("Up") },
    { key = "j", mods = "LEADER", action = act.ActivatePaneDirection("Down") },
    -- Pane選択
    { key = "[", mods = "CTRL|SHIFT", action = act.PaneSelect },
    -- 選択中のPaneのみ表示
    { key = "z", mods = "LEADER", action = act.TogglePaneZoomState },

    -- フォントサイズ切替
    { key = "+", mods = "CTRL", action = act.IncreaseFontSize },
    { key = "-", mods = "CTRL", action = act.DecreaseFontSize },
    -- フォントサイズのリセット
    { key = "0", mods = "CTRL", action = act.ResetFontSize },

    -- タブ切替 Cmd + 数字
    { key = "1", mods = "SUPER", action = act.ActivateTab(0) },
    { key = "2", mods = "SUPER", action = act.ActivateTab(1) },
    { key = "3", mods = "SUPER", action = act.ActivateTab(2) },
    { key = "4", mods = "SUPER", action = act.ActivateTab(3) },
    { key = "5", mods = "SUPER", action = act.ActivateTab(4) },
    { key = "6", mods = "SUPER", action = act.ActivateTab(5) },
    { key = "7", mods = "SUPER", action = act.ActivateTab(6) },
    { key = "8", mods = "SUPER", action = act.ActivateTab(7) },
    { key = "9", mods = "SUPER", action = act.ActivateTab(-1) },

    -- コマンドパレット
    { key = "p", mods = "SHIFT|CTRL", action = act.ActivateCommandPalette },
    -- 設定再読み込み
    { key = "r", mods = "SHIFT|CTRL", action = act.ReloadConfiguration },
    -- キーテーブル用
    { key = "s", mods = "LEADER", action = act.ActivateKeyTable({ name = "resize_pane", one_shot = false }) },
    {
      key = "a",
      mods = "LEADER",
      action = act.ActivateKeyTable({ name = "activate_pane", timeout_milliseconds = 1000 }),
    },
  },
  -- キーテーブル
  -- https://wezfurlong.org/wezterm/config/key-tables.html
  key_tables = {
    -- Paneサイズ調整 leader + s
    resize_pane = {
      { key = "h", action = act.AdjustPaneSize({ "Left", 1 }) },
      { key = "l", action = act.AdjustPaneSize({ "Right", 1 }) },
      { key = "k", action = act.AdjustPaneSize({ "Up", 1 }) },
      { key = "j", action = act.AdjustPaneSize({ "Down", 1 }) },

      -- Cancel the mode by pressing escape
      { key = "Enter", action = "PopKeyTable" },
    },
    activate_pane = {
      { key = "h", action = act.ActivatePaneDirection("Left") },
      { key = "l", action = act.ActivatePaneDirection("Right") },
      { key = "k", action = act.ActivatePaneDirection("Up") },
      { key = "j", action = act.ActivatePaneDirection("Down") },
    },
    -- copyモード leader + [
    copy_mode = {
      -- 移動
      { key = "h", mods = "NONE", action = act.CopyMode("MoveLeft") },
      { key = "j", mods = "NONE", action = act.CopyMode("MoveDown") },
      { key = "k", mods = "NONE", action = act.CopyMode("MoveUp") },
      { key = "l", mods = "NONE", action = act.CopyMode("MoveRight") },
      -- 最初と最後に移動
      { key = "^", mods = "NONE", action = act.CopyMode("MoveToStartOfLineContent") },
      { key = "$", mods = "NONE", action = act.CopyMode("MoveToEndOfLineContent") },
      -- 左端に移動
      { key = "0", mods = "NONE", action = act.CopyMode("MoveToStartOfLine") },
      { key = "o", mods = "NONE", action = act.CopyMode("MoveToSelectionOtherEnd") },
      { key = "O", mods = "NONE", action = act.CopyMode("MoveToSelectionOtherEndHoriz") },
      --
      { key = ";", mods = "NONE", action = act.CopyMode("JumpAgain") },
      -- 単語ごと移動
      { key = "w", mods = "NONE", action = act.CopyMode("MoveForwardWord") },
      { key = "b", mods = "NONE", action = act.CopyMode("MoveBackwardWord") },
      { key = "e", mods = "NONE", action = act.CopyMode("MoveForwardWordEnd") },
      -- ジャンプ機能 t f
      { key = "t", mods = "NONE", action = act.CopyMode({ JumpForward = { prev_char = true } }) },
      { key = "f", mods = "NONE", action = act.CopyMode({ JumpForward = { prev_char = false } }) },
      { key = "T", mods = "NONE", action = act.CopyMode({ JumpBackward = { prev_char = true } }) },
      { key = "F", mods = "NONE", action = act.CopyMode({ JumpBackward = { prev_char = false } }) },
      -- 一番下へ
      { key = "G", mods = "NONE", action = act.CopyMode("MoveToScrollbackBottom") },
      -- 一番上へ
      { key = "g", mods = "NONE", action = act.CopyMode("MoveToScrollbackTop") },
      -- viweport
      { key = "H", mods = "NONE", action = act.CopyMode("MoveToViewportTop") },
      { key = "L", mods = "NONE", action = act.CopyMode("MoveToViewportBottom") },
      { key = "M", mods = "NONE", action = act.CopyMode("MoveToViewportMiddle") },
      -- スクロール
      { key = "b", mods = "CTRL", action = act.CopyMode("PageUp") },
      { key = "f", mods = "CTRL", action = act.CopyMode("PageDown") },
      { key = "d", mods = "CTRL", action = act.CopyMode({ MoveByPage = 0.5 }) },
      { key = "u", mods = "CTRL", action = act.CopyMode({ MoveByPage = -0.5 }) },
      -- 範囲選択モード
      { key = "v", mods = "NONE", action = act.CopyMode({ SetSelectionMode = "Cell" }) },
      { key = "v", mods = "CTRL", action = act.CopyMode({ SetSelectionMode = "Block" }) },
      { key = "V", mods = "NONE", action = act.CopyMode({ SetSelectionMode = "Line" }) },
      -- コピー
      { key = "y", mods = "NONE", action = act.CopyTo("Clipboard") },

      -- コピーモードを終了
      {
        key = "Enter",
        mods = "NONE",
        action = act.Multiple({ { CopyTo = "ClipboardAndPrimarySelection" }, { CopyMode = "Close" } }),
      },
      { key = "Escape", mods = "NONE", action = act.CopyMode("Close") },
      { key = "c", mods = "CTRL", action = act.CopyMode("Close") },
      { key = "q", mods = "NONE", action = act.CopyMode("Close") },
    },
  },
}
```

## 最後に

WezTermの記事を書いた人はコメント欄でぜひ宣伝してください！  
ターミナル&dotfiles自慢もお待ちしています！  
いろんな人のカスタマイズを参考にしたい場合はWezTermのGitHub Discussionsを見てみるのもいいかもしれません。  
[Show your wezterms · wez/wezterm · Discussion #628](https://github.com/wez/wezterm/discussions/628)

**WezTermはいいぞ**

[GitHubで編集を提案](https://github.com/mozumasu/zenn/blob/main/articles/mozumasu-wezterm-customization.md)

293

33

293

33