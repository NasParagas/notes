---
title: "Ubuntu 22.04 をインストールしたら設定すること 10 ヶ条"
source: "https://zenn.dev/sprout2000/articles/8ea4a77d81583a"
author:
  - "[[Zenn]]"
published: 2022-04-23
created: 2025-08-28
description:
tags:
  - "clippings"
---
189

8[tech](https://zenn.dev/tech-or-idea)

## 1\. ソフトウェアのダウンロード元を変更してシステムをアップデートする (\*)

Super キー(= Windows キー)を押して、 `software` と入力し、「ソフトウェアとアップデート」を選択します。

![](https://storage.googleapis.com/zenn-user-upload/48ee08fef43b-20220423.png)

「設定...」ボタンを押して、「Ubuntu のソフトウェア」タブの「ダウンロード元:」から `ftp.jaist.ac.jp` などの国内ミラーサーバーを選択します。

![](https://storage.googleapis.com/zenn-user-upload/35d16e7f1a0e7a8b32308112.png)

アップデートが存在する場合は、表示されるウィンドウの指示にしたがってパッケージを更新し、Ubuntu を再起動します。

## 2\. キーボードの CapsLock キーを Control へ置き換える

`/etc/default/keyboard` を編集します。

bash

```shell
sudo vi /etc/default/keyboard
```

/etc/default/keyboard

```
- XKBOPTIONS=""
+ XKBOPTIONS="ctrl:nocaps"
```

デーモンを再起動してログアウト・ログインすれば設定が反映されます。

```shell
sudo systemctl restart console-setup
```

## 3\. 時計合わせのサーバを NICT へ変更する (\*)

bash

```shell
sudo sed -i 's/#NTP=/NTP=ntp.nict.jp/g' /etc/systemd/timesyncd.conf
```

Ubuntu を再起動すると設定が反映されます。

## 4\. ウィンドウスイッチャーが上下に伸び縮みするのを直す (\*\*)

UI に使われるフォントを変更します。

bash

```shell
gsettings set org.gnome.desktop.interface font-name 'Noto Sans CJK JP 11'
```

![](https://storage.googleapis.com/zenn-user-upload/1f7e5dddf0d8-20220423.png)  
*Alt + Tab でタスクをスイッチしているとき*

## 5\. 開かれたウィンドウが自動的に最大化されないようにする

bash

```shell
gsettings set org.gnome.mutter auto-maximize false
```

## 6\. 詳細設定アプリをインストールする

標準の Settings (設定) アプリでは変更できない設定を変更できるようにするアプリをインストールします。

bash

```shell
sudo apt install gnome-tweaks
```

![](https://storage.googleapis.com/zenn-user-upload/0ac32d00a71d4430eb82acfc.png)

## 7\. 日本語のフォルダ名を英語にする (\*\*)

コマンドラインからのフォルダ/ファイル操作を楽にするため、英語表記へ切り替えます。

bash

```shell
env LANGUAGE=C LC_MESSAGES=C xdg-user-dirs-gtk-update
```

![](https://storage.googleapis.com/zenn-user-upload/79140d3cab1f4643b620538b.png)  
*"Don't ask me this again" にチェックを入れて "Update Names" をクリック*

## 8\. 変換・無変換で IME のオンオフを切り替える (\*\*)

macOS 風にスペースバー左右のキーで IME を切り替えられるようにします。

![](https://storage.googleapis.com/zenn-user-upload/d22f3f7bd0088f8d053919e6.png)  
*Super キー + "mozc" と入力*

Mozc のプロパティ画面から以下のキーバインドを割り当てましょう。

| モード | 入力キー | コマンド |
| --- | --- | --- |
| 変換中 | Muhenkan | IME を無効化 |
| 入力文字なし | Muhenkan | IME を無効化 |
| 直接入力 | Muhenkan | IME を無効化 |
| 変換前入力中 | Muhenkan | IME を無効化 |
| 入力文字なし | Henkan | ひらがなに入力切り替え |
| 直接入力 | Henkan | ひらがなに入力切り替え |
| 変換前入力中 | Henkan | ひらがなに入力切り替え |

![](https://storage.googleapis.com/zenn-user-upload/6b3b02cd0ddf393fb9049df2.png)

## 9\. Dock のアプリ表示ボタンを一番上（または左）へ移動する

bash

```shell
gsettings set org.gnome.shell.extensions.dash-to-dock show-apps-at-top true
```

Dock を画面下部に水平に配置している場合は一番左へと移動されます。  
![](https://storage.googleapis.com/zenn-user-upload/2b45357d862c-20220423.png)

## 10\. 動画再生用コーデックをインストールする (\*)

mp4 などの動画ファイルを再生できるようにするパッケージを追加します。

bash

```shell
sudo apt install ubuntu-restricted-extras
```

インストールが完了したら Ubuntu を再起動します。

## (オプション) 動画再生アプリをインストールする

Gnome 標準の Videos (totem) をインストールします。

bash

```shell
sudo apt install totem
```

![](https://storage.googleapis.com/zenn-user-upload/37a7f7289fd97ea6018813b4.png)

## 参考にした記事（感謝）

[GitHubで編集を提案](https://github.com/sprout2000/zenn/blob/main/articles/8ea4a77d81583a.md)

189

8

189

8