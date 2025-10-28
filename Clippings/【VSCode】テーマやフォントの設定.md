---
title: "【VSCode】テーマやフォントの設定"
source: "https://windows.joho.info/vscode/vscode-setting-theme-font/"
author:
  - "[[西住技研]]"
published: 2025-06-24
created: 2025-10-19
description: "VSCodeのテーマやフォントの設定方法を解説します。"
tags:
  - "clippings"
---
VSCodeのテーマやフォントの設定方法を解説します。

## テーマの設定方法

① 上部メニューから「ファイル」→「ユーザー設定」→「テーマ」→「配色テーマ」を選択します。

② 一覧から好きなテーマをクリックすると、即座に反映されます。

【コマンドパレットを使う場合】  
ショートカット\`Ctrl + Shift + P\`でコマンドパレットを開き、「Color Theme」と入力し、「 **Preferences: Color Theme** 」を選択することでも同様にテーマの選択を行うことができます。  
※Macは「\`Cmd + Shift + P\`」

【一覧にないテーマを追加したい場合】  
拡張機能（Extensions）から「theme」で検索し、インストールして追加することもできます。ただし、インストール前には「 **マルウェアが混入していないか** 」など、評判を見て慎重に判断することをおすすめします。  
例：One Dark Pro、Dracula Official、Tokyo Nightなど。

### 解説動画

![](https://www.youtube.com/watch?v=gJTOpYEecrA)

## フォントの設定方法（種類とサイズ）

① 上部メニューから「ファイル」→「ユーザー設定」→「設定」を選択し、設定画面を開きます。  
※ショートカット `Ctrl +` （Macは「 `Cmd +` 」）でも開くことができます・

② 検索バーに「font」と入力し、以下の項目を設定します。

| 項目 | 概要 |
| --- | --- |
| `Editor: Font Size` | フォントサイズ（例：14） |
| `Editor: Font Family` | 使用するフォント名（例： `'Fira Code', 'ＭＳ 明朝', monospace` ）   デフォルトで設定されているフォントの種類は「Consolas, ‘Courier New’, monospace」になっています。複数のフォントを指定する場合はカンマ(,)で区切って入力します。（前に指定したフォントが優先されます）。フォント名に空白が含まれる場合はシングルクオーテーション（’）で囲って記述します。 |
| `Editor: Font Ligatures` | 合字（ligature、リガチャ）を使うかどうか（例： `true` ）。合字とは、2文字以上の文字を、1文字として表記する文字のことです。 |

【補足】  
「settings.json」は、エディタの動作やデザインを細かくカスタマイズできる設定ファイルです。このファイルを弄ってフォントを設定することもできます。設定画面右上の「{}」 アイコンをクリックして開くことができます。また、コマンドパレット（Ctrl+Shift+P）を開いて「Preferences: Open Settings (JSON)」と入力して選択することでも開くことができます。プロジェクトごとに上書きしたい設定があれば、「s.vscode/settings.json」に記述します。

【記述例】settings.json

```json
"editor.fontFamily": "'Fira Code', 'Source Han Code JP', monospace",
   "editor.fontSize": 14,
   "editor.fontLigatures": true
```

## おすすめ日本語対応フォント

| フォント名 | 特徴 |
| --- | --- |
| Source Han Code JP | Adobe製。日本語と英語のバランスが良い |
| PlemolJP | IBM Plexベース。視認性が高く等幅 |
| HackGen | Hack + 源ノ角ゴシック。開発向けに最適 |
| Moralerspace | Monaspace + 日本語フォントの合成版 |

## 関連ページ

[

![](https://algorithm.joho.info/wp-content/uploads/2023/08/404-160x90.jpg)

【VSCode超入門】基本的な使い方から応用例まで解説

VSCode（Visual Studio Code）の基本的な使い方から応用例まで入門者向けに解説します。

windows.joho.info

2025.06.23

](https://windows.joho.info/vscode-tutorial/ "【VSCode超入門】基本的な使い方から応用例まで解説")[

![](https://algorithm.joho.info/wp-content/uploads/2023/08/404.jpg)

Python超入門速報

python.joho.info

](https://python.joho.info/ "Python超入門速報")[

![](https://algorithm.joho.info/wp-content/uploads/2023/08/404-160x90.jpg)

【Windows超入門】初心者から上級者までテクニックを解説

Windowsの基本操作と設定基本操作Windowsの基本操作便利なショートカット大全集画面のカスタマイズカスタマイズWindows 11の右クリックメニューを昔のスタイル（Windows 10）に戻す方法Windowsを高速化・軽量化する...

windows.joho.info

2024.06.29

](https://windows.joho.info/ "【Windows超入門】初心者から上級者までテクニックを解説")![](https://algorithm.joho.info/wp-content/uploads/2023/08/404-160x90.jpg)

[

【VSCode】拡張機能「Rainbow CSV」の使い方

](https://windows.joho.info/vscode/plugin-rainbow-csv/ "【VSCode】拡張機能「Rainbow CSV」の使い方")[![](https://windows.joho.info/wp-content/uploads/2025/08/maxresdefault-3-120x68.jpg)

【VSCode】便利なショートカット集

](https://windows.joho.info/vscode/vscode-main-shortcut/ "【VSCode】便利なショートカット集")

[ホーム](https://windows.joho.info/)

- [お問合せ](https://joho.info/contact/)
- [プライバシーポリシー](https://joho.info/privacy-policy/)
- [運営者情報（プロフィール）](https://joho.info/profile/)