---
title: "ターミナル環境の構築: Terminatorのインストール"
source: "https://ryonakagami.github.io/2020/12/22/ubuntu-terminator/"
author:
  - "[[Ryo's Tech Blog]]"
published: 2020-12-22
created: 2025-08-21
description: "元経済学徒, 現在どっかでデータサイエンティストもどきやってる人間の勉強日記"
tags:
  - "clippings"
---
## Overview

**What I Want**

- `Terminator` を用いたターミナル環境の構築

Default TerminalだとTab分割は可能だが, Window分割ができません. Terminatorだと

- 軽量動作(Pythonベースだけど…)
- 画面分割
- レイアウトの設定

が簡単にできるので今回採用.

**What Did I Do?**

1. Terminatorのインストール
2. Terminatorのレイアウト設定

## What is Gnome-Terminator?

Ubuntu 20.04にはデフォルトでGnome Terminalがインストールされていますが, こちらはTabを増やすことはできますが以下のように画面分割で用いることができません. 一方, TerminatorというTerminal emulatorでは簡単なキーバインディングで分割が可能です.

![](https://github.com/ryonakimageserver/omorikaizuka/blob/master/linux/terminal/20201224_terminal_terminator.png?raw=true)

| Split terminal Vertically | `shift` + `ctrl` + `e` |
| --- | --- |
| Split terminal Horizontally | `shift` + `ctrl` + `o` |
| Focus to Next/Previous terminal | `shift` + `ctrl` + `n` / `p` |

ただし, 軽量といえどもメモリもDefaultのGnome Terminalよりも余分に必要とします. また, SSH接続を利用してリモートサーバーで作業する際に画面分割する場合はtmuxを利用することがどちらにしろ必要です.

### Features: Terminal出力結果に対して検索

出力画面のバッファーに対して, `Ctrl+Shift+F` でSearchが可能です. あくまでバッファー部分なので, `grep` を利用した上で最後に簡易に検索したい場合が多いと思いますが, `man` コマンドで出力したマニュアルにも検索がかけられるので, `grep` が利用しづらいときに重宝するかもしれません.

![](https://github.com/ryonakimageserver/omorikaizuka/blob/master/blog/Ubuntu/terminator-search.png?raw=true)

### Features: Setting Titles

マウス or ショートカットでWindowsやTabのtitle settingsが可能です.

| Edit | Mouse | Shortcut |
| --- | --- | --- |
| Window title | N/A | `ctrl` + `alt` + `w` |
| Tab title | double-click tab | `ctrl` + `alt` + `a` |
| Terminal title | double-click titlebar | `ctrl` + `alt` + `x` |

## Terminatorのインストール

公式ドキュメントに従いPPA for Ubuntu 20.04からrepositoryを追加してinstallを実施する

```
1
2
3
4
5
6
## you may not need to run this line
% sudo add-apt-repository ppa:mattrose/terminator

## install
% sudo apt update
% sudo apt install terminator
```

次にインストールされたバージョンを念の為確認する

```
1
2
3
4
5
6
7
8
9
% apt-cache policy terminator
terminator:
  Installed: 2.1.1-1
  Candidate: 2.1.1-1
  Version table:
 *** 2.1.1-1 500
        500 http://jp.archive.ubuntu.com/ubuntu jammy/universe amd64 Packages
        500 http://jp.archive.ubuntu.com/ubuntu jammy/universe i386 Packages
        100 /var/lib/dpkg/status
```

インストール後，デフォルトで起動するようになりますがもしならなかった場合は

```
1
% sudo update-alternatives --config x-terminal-emulator
```

を実行してください．

## Configuration

Terminatorの設定情報は `~/.config/terminator/config` に格納されています.

```
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
% cat ~/.config/terminator/config
[global_config]
  dbus = False
  title_transmit_fg_color = "#000000"
  title_transmit_bg_color = "#c0bfbc"
  title_inactive_bg_color = "#77767b"
[keybindings]
  reset_clear = ""
  broadcast_off = <Shift><Super>b
  broadcast_all = <Super>b
  insert_number = ""
  insert_padded = ""
  help = ""
[profiles]
  [[default]]
    background_color = "#300a24"
    background_darkness = 0.39
    cursor_color = "#aaaaaa"
    foreground_color = "#ffffff"
    palette = "#000000:#cc0000:#4e9a06:#c4a000:#3465a4:#75507b:#06989a:#d3d7cf:#555753:#ef2929:#8ae234:#fce94f:#729fcf:#ad7fa8:#34e2e2:#eeeeec"
[layouts]
  [[default]]
    [[[window0]]]
      type = Window
      parent = ""
    [[[child1]]]
      type = Terminal
      parent = window0
[plugins]
```

上記ではショートカットコマンドの一部Disable設定及び色の設定に留めています.

## Tips: Short cuts

`Preferences > Keybindings` からも確認できますが代表的なものを紹介します.

> 移動

| Action | Default Shortcut |
| --- | --- |
| 行頭への移動 | `Ctrl+A` |
| 行末に移動 | `Ctrl+E` |
| 行頭まで削除 | `Ctrl+U` |
| 行末まで削除 | `Ctrl+K` |
| ワード単位で行頭方向の文字列を削除 | `Ctrl+W` |

> Terminal split/move/close

| Action | Default Shortcut |
| --- | --- |
| Split terminal Horizontally | `Shift+Ctrl+E` |
| Split terminal Vertically | `Shift+Ctrl+O` |
| 分割されたterminalの移動 | `Ctrl+Tab` |
| 分割されたterminalの移動(前) | `Shift+Ctrl+Tab` |
| Close window | `Shift+Ctrl+Q` |
| Close terminal | `Shift+Ctrl+W` |
| Terminatorの起動 | `Ctrl+Alt+T` |

## References

- [Installing Terminator](https://github.com/gnome-terminator/terminator/blob/master/INSTALL.md)
- [Terminator GitHub Project](https://github.com/gnome-terminator/terminator)
- [Terminator Document](https://terminator-gtk3.readthedocs.io/en/latest/)

---

**Share Buttons  
**Share on:

---

**Feature Tags**  
**Leave a Comment  
(注意：GitHub Accountが必要となります）**

Content updated.