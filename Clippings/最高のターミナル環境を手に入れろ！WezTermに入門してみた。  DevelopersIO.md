---
title: "最高のターミナル環境を手に入れろ！WezTermに入門してみた。 | DevelopersIO"
source: "https://dev.classmethod.jp/articles/wezterm-get-started/"
author:
  - "[[平野重利]]"
published: 2024-05-26
created: 2025-11-23
description:
tags:
  - "clippings"
---
Rust製のターミナルアプリ WezTermをご存知ですか？

もしご存知なければ、最高の相棒を紹介させてください！！

[WezTerm - Wez's Terminal Emulator](https://wezfurlong.org/wezterm/index.html)

## 基本的な性能

ターミナルアプリに求められる要素はとてもシンプルです。

**1に安定！**

**2に軽量！**

3,4がなくて

**5に機能**

です。 安定と軽量の2つが満足でないとターミナルアプリは 比較という俎上に上がること自体が難しいです。

さて、WezTermはというと、 あくまでも私の実感レベルですが、どちらも全く問題ないです。 ターミナルアプリでは日本語の入力系などで難ありなものも結構あったりしますが 今の所その辺でも特に問題を感じたことはないです。

非常に主観的な記述だけで申し訳ないですが、 この辺は色々書いたところで使ってみないことにはわからないと思いますので、 まずは使ってみてください。

また私はMacで利用していますが、 WindowsやLinuxでの動作もサポートされているようなので、 OSがロックインされてしまう心配が少なそうなのも嬉しいですね。

## 機能面もいいぞ

軽量で安定していますが、それだけじゃないです！

### 設定は全てLuaで書く

WezTermの設定は全てLua言語で書けます。 Luaは軽量なプログラミング言語で、 Neovimで使われているので触ったことがある方もいるかもしれません。

Lua自体がどうという話ではなく、 設定をプログラムでかけるということは条件分岐や繰り返しなども表現できるため、 設定ファイルでありながら、簡単なプラグインのように機能を追加することもできます。 言葉だけだとあまりイメージが湧かないかもしれませんので 後ほど画面の透過率をホットキーで変更する例をお見せしたいと思います。

またGUIの設定画面はありません。 このターミナルをわざわざ使う人にとっては、 GUIでの設定画面というもの自体が 「GUIで設定した設定がどうCUIに反映されるのかな？ いつの間にか設定ファイル書き換わっちゃうのやだな」 という悩みの種でしかないと思いますので、GUI設定画面がないことは非常に価値があります。

という冗談はさておき（なお本人は一切冗談と思っていない模様）、 全てテキストファイルで設定できるので、全ての設定をGitで管理できます。 はい、めちゃめちゃだいじですね。

### コマンドラインからの操作

WezTermはAPIを利用して操作が可能です。 例えば新しいタブやペインを開くという操作をホットキーやメニュー画面から行う以外に、 シェルスクリプトなどからもそれらが実行できます。

APIを使って任意の入力シーケンスを特定のペインに送ることも可能です。 例えば `Ctrl+c` で処理を止めるなども別のプログラムから操作ができますし、 ほかにも、あるペインで実行したコマンドでの出力結果を、 別のペインのバッファ（Enterを押したらコマンドが実行される入力フィールド）に流し込む、なんてことも可能です。

### 画面分割が柔軟

tmuxと同じようなペイン分割が可能です。 具体的には、横や縦にペインを分割して、その大きさを調整もできます。

これは多くのターミナルアプリでも可能かもしれませんが、 tmuxから難なく乗り換えられるという点で、個人的には重要なポイントでした。

## インストール

すごい所がわかったところで、インストールをしましょう。

とはいえインストール手順は特に難しくないので特に書くことはありません。

私は [Mac版](https://wezfurlong.org/wezterm/install/macos.html) のzipをダウンロードし、 解凍した中身を/Applicationsに入れて使っています。 brewでのインストールも可能です。

## 設定例

インストールしたら早速いくつかの設定をしていきましょう。 設定ファイルはデフォルトでは `~/.config/wezterm/wezterm.lua` に配置します。

下記は私の環境の代表的なものを取り出してきたものです。 設定ファイルを編集して保存すると、 WezTermを再起動することなく設定が読み込まれます。 （設定にエラーがあるとエラー情報が表示されて、直近の状態が維持されるようです） これめちゃくちゃ大事です。

```
-- Pull in the wezterm API
local wezterm = require 'wezterm'

-- This table will hold the configuration.
local config = {}

-- In newer versions of wezterm, use the config_builder which will
-- help provide clearer error messages
if wezterm.config_builder then
    config = wezterm.config_builder()
end

-- ここまでは定型文
-- この先でconfigに各種設定を書いていく

-- フォントサイズ
config.font_size = 13.0
-- 背景の非透過率（1なら完全に透過させない）
config.window_background_opacity = 0.80

-- キーバインド
config.keys = {
    -- ¥ではなく、バックスラッシュを入力する。おそらくMac固有
    {
        key = "¥",
        action = wezterm.action.SendKey { key = '\\' }
    },
    -- Altを押した場合はバックスラッシュではなく¥を入力する。おそらくMac固有
    {
        key = "¥",
        mods = "ALT",
        action = wezterm.action.SendKey { key = '¥' }
    },
    -- ⌘ + でフォントサイズを大きくする
    {
        key = "+",
        mods = "CMD|SHIFT",
        action = wezterm.action.IncreaseFontSize,
    },
    -- ⌘ w でペインを閉じる（デフォルトではタブが閉じる）
    {
        key = "w",
        mods = "CMD",
        action = wezterm.action.CloseCurrentPane { confirm = true },
    },
    -- ⌘ Ctrl ,で下方向にペイン分割
    {
    {
        key = ",",
        mods = "CMD|CTRL",
        action = wezterm.action { SplitVertical = { domain = "CurrentPaneDomain" } },
    },
    -- ⌘ Ctrl .で右方向にペイン分割
    {
        key = ".",
        mods = "CMD|CTRL",
        action = wezterm.action { SplitHorizontal = { domain = "CurrentPaneDomain" } },
    },
    -- ⌘ Ctrl oでペインの中身を入れ替える
    {
        key = "o",
        mods = "CMD|CTRL",
        action = wezterm.action.RotatePanes 'Clockwise'
    },
    -- ⌘ Ctrl hjklでペインの移動
    {
        key = 'h',
        mods = 'CMD|CTRL',
        action = wezterm.action.ActivatePaneDirection 'Left',
    },
    {
        key = 'j',
        mods = 'CMD|CTRL',
        action = wezterm.action.ActivatePaneDirection 'Down',
    },
    {
        key = 'k',
        mods = 'CMD|CTRL',
        action = wezterm.action.ActivatePaneDirection 'Up',
    },
    {
        key = 'l',
        mods = 'CMD|CTRL',
        action = wezterm.action.ActivatePaneDirection 'Right',
    },
    -- ⌘ Ctrl Shift hjklでペイン境界の調整
    {
        key = 'h',
        mods = 'CMD|CTRL|SHIFT',
        action = wezterm.action.AdjustPaneSize { 'Left', 2 },
    },
    {
        key = 'j',
        mods = 'CMD|CTRL|SHIFT',
        action = wezterm.action.AdjustPaneSize { 'Down', 2 },
    },
    {
        key = 'k',
        mods = 'CMD|CTRL|SHIFT',
        action = wezterm.action.AdjustPaneSize { 'Up', 2 },
    },
    {
        key = 'l',
        mods = 'CMD|CTRL|SHIFT',
        action = wezterm.action.AdjustPaneSize { 'Right', 2 },
    },

}

-- マウス操作の挙動設定
config.mouse_bindings = {
    -- 右クリックでクリップボードから貼り付け
    {
        event = { Down = { streak = 1, button = 'Right' } },
        mods = 'NONE',
        action = wezterm.action.PasteFrom 'Clipboard',
    },
}

-- タブを下に表示（デフォルトでは上にある）
config.tab_bar_at_bottom = true

return config
```

最も重要なことはキーボードだけで日常的な操作を全て完結させることですので、 ペイン分割やペイン境界の調整などのキーバインド設定は必須となります。 現状ではマウスの無効化をしていないので、マウスクリックした箇所のペインがアクティブになるのですが、 この機能は邪魔な時も多いのでマウスは無効化をしても良さそうです。

WezTermのデフォルトキーバインドは私の感覚と遠くなかったので、あまり色々な設定をせずに済んだのも嬉しいです。 特にデフォルトでマウス選択した箇所が自動的にクリップボードに送られるのは嬉しいですね。

Luaを使って設定をしていますが、 先頭の部分を定型文だと思えば、一般的な設定とあまり変わりなく使うことができます。

## プログラムで設定を書く

Luaを使うことで、プログラム的に動く設定を書くことができます。 ここでは例として、ターミナル背景の透過率を変更するホットキーを作ってみます。

```
-- 共通部分省略

wezterm.on("decrease-opacity", function(window)
    local overrides = window:get_config_overrides() or {}
    if not overrides.window_background_opacity then
        overrides.window_background_opacity = 1.0
    end
    overrides.window_background_opacity = overrides.window_background_opacity - 0.1
    if overrides.window_background_opacity < 0.1 then
        overrides.window_background_opacity = 0.1
    end
    window:set_config_overrides(overrides)
end)

wezterm.on("increase-opacity", function(window)
    local overrides = window:get_config_overrides() or {}
    if not overrides.window_background_opacity then
        overrides.window_background_opacity = 1.0
    end
    overrides.window_background_opacity = overrides.window_background_opacity + 0.1
    if overrides.window_background_opacity > 1.0 then
        overrides.window_background_opacity = 1.0
    end
    window:set_config_overrides(overrides)
end)

config.keys = {
    -- 中略
    {
        key = "n",
        mods = "CMD|CTRL|SHIFT",
        action = wezterm.action { EmitEvent = "decrease-opacity" },
    },
    {
        key = "m",
        mods = "CMD|CTRL|SHIFT",
        action = wezterm.action { EmitEvent = "increase-opacity" },
    },
}
```

`decrease-opacity` と `increase-opacity` という関数を定義しホットキーに割り当てています。 これらの関数は、中で `overrides.window_background_opacity` という値を相対的に変更して実現しています。

普通の設定ファイルであればここには定数を入れることしかできないので、 ホットキー操作による相対的な変化を実現することはできません。 しかしWezTermはLuaによるプログラミングで設定をするので、 関数化した処理をホットキーに割り当てることが可能です。

なお、私はLuaは書き慣れていませんが、 ChatGPTを使えば上記くらいの設定はすぐにできてしまいました。 Lua以外でもプログラミング言語をある程度知っている人にとっては色々な設定ができるので、 Lua（に限らずプログラミング言語）を使って設定を書くというのはいいなと思っています。

## wezterm cliを使ってみる

最後に少しAPIを利用してみます。

`wezterm` というコマンドが `/Applications/WezTerm.app/Contents/MacOS/wezterm` に配置されています。 下記例ではフルパスで指定していますがもちろんPATHを通しても良いです。

`wezterm` コマンドを使って新しいペインを生成し、 さらにそのペインでVimを起動してみます。

```
# 別のウィンドウ（ターミナルアプリ）で
# 今のペインに対してペイン分割を指示
$ /Applications/WezTerm.app/Contents/MacOS/wezterm cli split-pane
43
# 生成されたペインのidが出力される

# 新しく生成されたペインに、Vimを実行するコマンドラインを流し込む
# --pane-id で対象のペインidを指定
# send-textで対照ペインに入力シーケンスを送る
# 明示的に '\n' をつけた文字列を送り、--no-pasteオプションをつけると実行が可能
$ echo 'vim sample.lua\n' | /Applications/WezTerm.app/Contents/MacOS/wezterm cli send-text --pane-id 43 --no-paste
```

これで新しいペインを開いて、そこでVimが実行できます！ 入力シーケンスを送り、その文字列をコマンドとして実行することも可能なのです。 控えめに言って、何でもできます。これは夢が広がりますね。

ちなみに、上記内容を素直にやるなら下記のようにもできます。

```
# 別のウィンドウ（ターミナルアプリ）で
# ペイン分割をしてそのペインでVimを実行
$ /Applications/WezTerm.app/Contents/MacOS/wezterm cli split-pane -- vim sample.lua
```

`--` の後にコマンドを続けることで、そのコマンドを起動できます。 （正確には、 `--` はその後をオプションとして解釈しないという意味です）

## まとめ

WezTermの魅力と、設定ファイル例、 またAPIを使ってAPIでの操作の例をご紹介しました。

個人的にはAPIで操作できる機能が特に大きいです。 いくつかの自作プラグインはtmuxの同様の機能を使うことで実現していたのですが、 それを移植することができ、ついにtmuxを卒業することができました （tmux自体は大好きです）。

tmuxやZshをカスタマイズすることで ターミナルアプリケーションより上のレイヤーは諸々カスタマイズができましたが、 WezTermを使うことでターミナル自体もさまざまなカスタマイズが可能になりそうです！ これはカスタマイズが捗りますね！

もしこの記事を見て興味を持った方は 一度インストールしてみてください！

この記事をシェアする