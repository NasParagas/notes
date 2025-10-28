---
title: "【Git】Visual Studio Code で競合を解決しよう (3 way merge editor)"
source: "https://futureys.tokyo/lets-resolve-conflict-on-git-by-vs-code-3-way-merge-editor/"
author:
  - "[[篠田 将彦]]"
published: 2023-01-02
created: 2025-07-04
description: "Git でブランチをマージしたとき、競合が発生することがあります。この記事では競合をどのように解決するかを順を"
tags:
  - "clippings"
---
Git でブランチをマージしたとき、競合が発生することがあります。  
この記事では競合をどのように解決するかを順を追って解説します。  
  
この記事では Visual Studio Code を使います。  
TortoiseGit を使って競合の解決を行いたい場合は、  
次の記事を参照してください:  
[【Git】競合を解決しよう](https://futureys.tokyo/lets-resolve-conflict-on-git/)  
  
ここでは、Visual Studio Code の [3 way merge editor](https://code.visualstudio.com/updates/v1_69#_3-way-merge-editor "3 way merge editor") という機能を使って競合の解決を行います。  
現段階 (March 2023 (version 1.77)) では、機能のリリース当初にあった不具合は改善され、  
筆者は問題なく常用しています。  
万一、問題があった場合は、次の従来の方法で競合の解決を行うことができます。  
[【Git】Visual Studio Code で競合を解決しよう](https://futureys.tokyo/lets-resolve-conflict-on-git-by-vs-code/ "【Git】競合を解決しよう")  
  
[Linux 知識の要らない Git 講座 目次にもどる](https://futureys.tokyo/table-of-contents-git-lesson-without-linux-knowledge/)

## 3 way merge editor とは

3 way merge editor は次の 3 つの画面を使って従来の方法よりも効率的に競合の解決を行うエディターです:

- 現在のブランチの更新内容と競合部分が表示される画面
- 現在のブランチにマージするブランチの更新内容と競合部分が表示される画面
- 競合の解決を行う画面
![Visual Studio Code 競合の解決 (3 way merge editor)](https://futureys.tokyo/app/uploads/2023/01/img-vscode-git-3-way-merge-conflict-window-expression-3-way-1024x555.png)

Visual Studio Code 競合の解決 (3 way merge editor)

## 3 way merge editor と inline editor の比較

従来の競合の解決方法で使っているエディターは inline editor と呼ばれています。

### 3 way merge editor

- 競合の解決を支援するための機能が充実しているので、慣れれば効率的に競合の解決を行うことができます。
	- 互いに同じ行を更新した場合でも、  
		変更が単語レベルで重なっていない限り、自動的に両方の更新を統合して解決できます。
- エディター自体の機能を把握する必要があります。
	- この記事を執筆時点 (November 2022 (version 1.74)) では、  
		Visual Studio Code 公式の解説はありません。
	- inline editor よりも新しく導入された機能なので、不具合が発生する可能性が inline editor よりも高く、  
		競合の解決が続けられない場合は inline editor に切り替えて競合を解決する必要があります。

### inline editor

- エディター自体の機能がシンプルで憶えるのが楽です。
- 競合の解決のための機能が充実していないので、競合を解決するのが面倒です。
	- 1 文字でも互いに同じ行を更新していると、自力で両方の更新を統合して解決する必要があります。  
		(差分を一望する別タブを開くことはできます)

## 競合の解決のための画面を開こう

ここからは、実際に 3 way merge editor を使って競合の解決を行っていきます。

手順

1.  
Git のマージを行い、競合が発生している状態で、Visual Studio Code を開きます。  
  
2.  
画面左 \[Activity Bar\] の \[Source Control\] のアイコンをクリックすると、  
\[Side Bar\] の \[MERGE CHANGES\] という欄に競合があるファイルが表示されるので、  
クリックします。  
すると、inline editor が表示されるので、  
下部に表示される \[Resolve in Merge Editor\] ボタンをクリックすると、3 way merge editor が表示されます。

![Visutal Studio Code マージで競合が発生しているときの画面](https://futureys.tokyo/app/uploads/2023/01/img-vscode-git-3way-merge-conflict-open-3way-merge-editor-1024x555.png)

Visutal Studio Code マージで競合が発生しているときの画面

## 競合の解決作業のすすめかた

改めて、画面の役割を説明します。  
まず、画面の上半分 (Incoming と Current) は確認用の表示で、編集できません。  
そして、画面の下半分 (Result) は編集用の画面になっています。

![Visual Studio Code 競合の解決 3 may merge editor 画面の役割](https://futureys.tokyo/app/uploads/2023/01/img-vscode-git-merge-conflict-window-3-way-merge-editor-expression-abstract-1024x555.png)

Visual Studio Code 競合の解決 3 may merge editor 画面の役割

画面上半分にそれぞれのブランチの編集内容が表示されるので、  
画面下半分の編集用画面を、「競合を解決した状態」に編集していきます。  
画面下半分は、ほぼ通常時と同様に操作でき、  
すべての言語機能 (診断、ブレークポイント、テストを含む) を使用できます。  
  
編集が競合している行は、画面の上半分では文字列がハイライトして表示され、  
その中でもう一方と異なる部分は特に明るく表示されています。  
マージする上で参考になるかもしれません。

![編集が競合している行](https://futureys.tokyo/app/uploads/2023/01/img-vscode-git-merge-conflict-3-way-merge-editor.png)

編集が競合している行

各画面右端のスクロールバーに  
ファイル全体の中の競合が発生している箇所が表示されます。

![各画面右端のスクロールバー](https://futureys.tokyo/app/uploads/2023/01/img-vscode-git-conflict-scroll-bar-3-way-merge-editor-1024x555.png)

各画面右端のスクロールバー

この表示は、後述する、競合を解決するための操作をしていくと消えていきます。  
すべての競合発生箇所を示す表示をなくすことを目標に、競合を解決していきます。

すべての競合の解決が終わったら、画面下部 \[Complete Merge\] ボタンをクリックすると  
行った編集をファイルに保存して、Git 上で更新内容をステージします。  
すべての競合が解決されていない状態で \[Complete Merge\] ボタンをクリックすると  
警告ダイアログが表示されます。

![未解決の競合が残っていることを示す確認ダイアログ](https://futureys.tokyo/app/uploads/2023/01/img-vscode-git-merge-conflict-3-way-merge-editor-conirmation-dialog-1024x555.png)

未解決の競合が残っていることを示す確認ダイアログ

## 競合を解決してみよう

競合を解決する上で最も基本的な考え方は、  
「どちらか一方が正しいわけではない」  
ということです。  
  
双方にそれぞれの編集意図があるので、  
双方の編集意図を汲んだマージを行う必要があります。  
相手の編集意図がコミットメッセージや編集内容から読み取れなければ  
相手に連絡をとって編集意図を確認してマージしましょう。  
  
この例では、競合した行が更新された編集履歴である  
「営業時間の変更」と「キャッチコピーの追加」の  
両方の意図を汲んだマージを行う必要があります。

![競合した行が更新された編集履歴](https://futureys.tokyo/app/uploads/2020/07/img-vscode-git-merge-conflict-revision-including-conflict-row.png)

競合した行が更新された編集履歴

#### 手順

##### 1\. 競合部分を解決します

画面上部の競合部分で、Incoming Current どちら側でもよいので、\[Accept Combination\] をクリックします。

![[Accept Combination] をクリック](https://futureys.tokyo/app/uploads/2023/01/img-vscode-git-merge-conflict-click-accept-combination-1024x555.png)

\[Accept Combination\] をクリック

すると、画面下部の競合部分が変更され、競合発生箇所を示す表示が灰色に変化します。

![両方の編集内容を反映したことを示す「Incoming + Current」の表示](https://futureys.tokyo/app/uploads/2023/01/img-vscode-git-merge-conflict-accepted-combination.png-1024x555.png)

両方の編集内容を反映したことを示す「Incoming + Current」の表示

両方の編集内容が反映されていることを確認します:

```markup
未知との出会いを応援する 勉強会カフェ 「First Knowledge」 オープン！ 営業時間：平日 07:00〜13:00, 18:00〜23:00 / 土日祝 07:30〜21:00
```

##### 2\. ファイルを保存してステージします

画面下部 \[Complete Merge\] ボタンをクリックします。

![[Complete Merge] ボタンをクリック](https://futureys.tokyo/app/uploads/2023/01/img-vscode-git-merge-conflict-click-complete-merge-1024x555.png)

\[Complete Merge\] ボタンをクリック

すると、行った編集がファイルに保存され、  
画面左 \[Activity Bar\] の \[Source Control\] を確認すると更新内容がステージされています。

![[Complete Merge] ボタンで自動的にステージされた更新内容](https://futureys.tokyo/app/uploads/2023/01/img-vscode-git-merge-conflict-completed-merge.png)

\[Complete Merge\] ボタンで自動的にステージされた更新内容

##### 3\. マージをコミットします

画面右側 \[Side Bar\] (表示内容が \[Source Control\] の状態) で \[✔Commit\] ボタンをクリックしてコミットします。

![競合を解決したマージをコミット](https://futureys.tokyo/app/uploads/2023/01/img-vscode-git-merge-conflict-3-way-merge-editor-commit.png)

競合を解決したマージをコミット

これでマージが完了しました。

## 結果を確認してみよう

HTML ファイルをブラウザで表示すると次のようになっています。

![ブラウザでの表示 競合の解決後](https://futureys.tokyo/app/uploads/2019/09/img-git-lesson-preview-resolve-conflict-1024x555.jpg)

ブラウザでの表示 競合の解決後

ログは次のようになっています:

![競合の解決後のログ](https://futureys.tokyo/app/uploads/2020/07/img-vscode-git-merge-resolve-conflict-result.png)

競合の解決後のログ

これでプッシュできるようになりました。  
成功するはずです。

![競合の解決後にプッシュした結果](https://futureys.tokyo/app/uploads/2020/07/img-vscode-git-push-resolved-conflict-result.png)

競合の解決後にプッシュした結果

以上でプロジェクトの編集履歴の競合の解消が完了しました。

## 競合を解決するときの選択肢

この記事を執筆している 2023-01-02 時点では、  
3 way merge editor の競合部分の機能を使って次のことができます:

| 競合部分への対応の選択肢 | 操作方法 |
| --- | --- |
| Incoming と Current 両方の更新内容を自動的にマージして適用する | 画面上部 \[Incoming\] または \[Current\] どちらか好きな方の \[Accept Combination\] をクリック |
| Incoming 側の更新内容だけを採用する | 画面上部 \[Incoming\] の \[Accept Incoming\] をクリック |
| Current 側の更新内容だけを採用する | 画面上部 \[Current\] の \[Accept Current\] をクリック |
| 自分で更新内容を記述する | 画面下部、競合の発生箇所の文字列をクリックし、直接編集 |
| どちら側の更新内容も採用せず、スクロールバー上から対象の競合の発生箇所を示す表示を消す | 画面下部 \[No Changes Accepted\] をクリック |
| Incoming のスクロールバー上からのみ対象の競合の発生箇所を示す表示を消す | 画面上部 \[Incoming\] の \[Ignore\] をクリック |
| Current のスクロールバー上からのみ対象の競合の発生箇所を示す表示を消す | 画面上部 \[Current\] の \[Ignore\] をクリック |

3 way merge editor を使って競合を解決するときは、  
できる限りエディターの機能を使って解決を行うようにすると楽です。

## 最初から 3 way merge editor を開きたいとき

競合の解決のために 3 way merge editor を開くとき、  
競合しているファイルをクリックして inline editor を開いてから、3 way merge editor を開きました。  
  
これを、最初から 3 way merge editor を開くようにするには、Visual Studio Code の設定を変更します。  
  
Visual Studio Code のメニューバーで  
\[File (ファイル)\] → \[Preference (ユーザー設定)\] →\[settings (設定)\] をクリック。  
(ショートカット: \[Ctrl +,\])  
  
すると設定画面が開きます。  
  
設定画面上部の \[Serch Settings (設定の検索)\] に “git.mergeEditor” と入力し、  
表示されたチェックボックス: \[Git: MergeEditor\] にチェックを入れます。

すると、次回から画面左 \[Activity Bar\] の \[Source Control\] で競合しているファイルをクリックすると、  
最初から 3 way merge editor が開きます。

### この設定のまま inline editor を開きたいとき

競合しているファイルを右クリック → \[Open File (ファイルを開く)\] で開けます。

### 3 way merge editor が初期状態で有効でない経緯

3 way merge editor は July 2022 (version 1.70) のときに初期状態で有効になりましたが、  
ユーザーからのフィードバックを確認したところ従来の方法の方が好評だったため、  
September 2022 (version 1.72) の段階で再度無効となりました。

参考:  
[Change default git conflict experience to be the inline editor · Issue #160806 · microsoft/vscode](https://github.com/microsoft/vscode/issues/160806)

ただし、当時は 3 way merge editor の UI が今ほど洗練されておらず、不具合も多い状態でした。

タイトルとURLをコピーしました