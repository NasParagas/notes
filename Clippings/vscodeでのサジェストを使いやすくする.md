---
title: "vscodeでのサジェストを使いやすくする"
source: "https://zenn.dev/hulk510/articles/vscode-suggest-more-useful"
author:
  - "[[Zenn]]"
published: 2020-12-21
created: 2025-10-28
description:
tags:
  - "clippings"
---
76

2[tech](https://zenn.dev/tech-or-idea)

## vscodeのサジェスト

普段テキストエディタにvscodeを使っていて、文字入力の際にクソ長い変数名を途中まで書いたら補完してくれたり、スニペットを使ったりするときに補完してくれたりと、自分は結構サジェスト機能を利用していて重宝してるのですが、  
自分の環境で最初にサジェストとして選択された状態になって表示されるのがだいたいkeywords [^1] が最初に表示されていました。

![const keyword](https://storage.googleapis.com/zenn-user-upload/mtkb4r6d736owrcp82vf7w798ofa)

ここではjavascriptで使う `const` を入力しています。  
この場合は別にconstと打ちたいので別に出てきてもらっても大丈夫なのですが、  
たまに違うkeywordsが表示される時 [^2] があり、  
またEnterキーでサジェストの決定にしていたので、改行したいだけなのにみたいな時でも決定され入力してしまい  
一々クソ長い、自分の意図とは違う名前を決定してしまってイライラしていました ~~🖕~~

いい方法ないかなーって調べてもだいたいサジェスト自体を消すみたい記事が多くて自分的にはサジェストは残しつつこのkeywordsだけ消したい😭って感じだったので思っているのと違っていました。

いろいろ調べた結果結局は本家のサイトに載ってたのですが、  
一応記事として残しておこうと思います。

## サジェストの設定変えていく

とりあえずvscodeの設定画面を開いときます。

だいたいは最初からvscodeでサジェスト機能はオンになっていると思います。  
デフォルトの設定だとここに記載されている値が設定されています。  
リンクいくのめんどくさいと思うので一応書いときます

default.json

```json
{
    // Controls if quick suggestions should show up while typing
    "editor.quickSuggestions": {
        "other": true,
        "comments": false,
        "strings": false
    },

     // Controls whether suggestions should be accepted on commit characters. For example, in JavaScript, the semi-colon (\`;\`) can be a commit character that accepts a suggestion and types that character.
    "editor.acceptSuggestionOnCommitCharacter": true,

    // Controls if suggestions should be accepted on 'Enter' - in addition to 'Tab'. Helps to avoid ambiguity between inserting new lines or accepting suggestions. The value 'smart' means only accept a suggestion with Enter when it makes a textual change
    "editor.acceptSuggestionOnEnter": "on",

    // Controls the delay in ms after which quick suggestions will show up.
    "editor.quickSuggestionsDelay": 10,

    // Controls if suggestions should automatically show up when typing trigger characters
    "editor.suggestOnTriggerCharacters": true,

    // Controls if pressing tab inserts the best suggestion and if tab cycles through other suggestions
    "editor.tabCompletion": "off",

    // Controls whether sorting favours words that appear close to the cursor
    "editor.suggest.localityBonus": true,

    // Controls how suggestions are pre-selected when showing the suggest list
    "editor.suggestSelection": "recentlyUsed",

    // Enable word based suggestions
    "editor.wordBasedSuggestions": true,

    // Enable parameter hints
    "editor.parameterHints.enabled": true,
}
```

パラメーター多すぎるので、とりあえず自分のやりたかったこととそれに必要なパラメーターだけ書いていきます。（英語読めないからよくわからないよ⭐️）

今回自分が設定したかったものとしては

- Enterでサジェストを決定しない
- サジェストの時いらない項目はもう表示しない(keywordsとかwordsとか [^3])
- 最近イキってスニペットを使い出したのでsnippetをサジェストの最初に出すようにする  
	って感じで、最終的にこんな感じで設定しました。

setting.json

```json
// enterでサジェストを決定しない
  "editor.acceptSuggestionOnEnter": "off",
  // これをfalseにするとkeywordだけ消してくれる
  "editor.suggest.showKeywords": false,
  // textもいらないので消す
  "editor.suggest.showWords": false,
  // snippetのサジェストを最初に出す。
  "editor.snippetSuggestions": "top",
  // 入力した時にどの項目にカーソルを合わせた状態にするかみたいなやつrecentlyUsedByPrefixにした
  "editor.suggestSelection": "recentlyUsedByPrefix",
```

だいたいどの項目も特に説明はいらないかなと思うのですが、他にも設定項目あるので最後にリンク先書いたのでみてくれると助かり魔す。

ただ自分的にrecentlyUsedとrecentlyUsedByPrefixの違いがあんまりわかりづらかったのですが、  
要はどこまで文字を入力して、その時にどの項目をサジェストとして選択した状態にするかの設定項目になるみたいです。

例えば拡張機能のgoのスニペットにfmainがあります。  
これはこんな感じにfunc mainを書いてくれます。  
![](https://storage.googleapis.com/zenn-user-upload/7wo515fthzwz8ifazy3j9ru7zssy)

```
func main() {

}
```

この時両者の違いは、fmaまで入力してfmainを一度サジェストを決定して、  
もう一度入力した際に、どの段階でfmainをサジェストで選択された状態にするかが違います。

- recentlyUsedの場合  
	もう一度入力する際、fだけ入力するとfmainをサジェストで選択された状態で表示してくる
- recentlyUsedByPrefixの場合  
	もう一度入力する際、fmaまで入れないと出してこない。  
	つまり前決定した時に入れていた文字（fma）と同じ文字を入れないとサジェストで選択された状態で表示されない

gifで出したいですがちょっとめんどくさいのでわかりにくかったらすみません。🙇♂️  
長い文字のサジェストだとrecentlyUsedがいいかもしれませんが、自分の場合それすると長いやつがずっと出てきてうざいのでrecentlyUsedByPrefixにしています。

やはり一番自分が嬉しかったのがこれ！（やっぱり用意してくれてるんですねありがとうございます）  
`"editor.suggest.showKeywords": false,`  
これでkeywordのやつが表示されなくなります。  
あんまり文章打たないので知らんけど使う機会ないし消したった。

## 終わりに

ここに書いたことは全部本家のサイトに載ってます。まずちゃんとドキュメントみてから話せよって思いますがめんどくさいよね！！☺️サジェストのアイコンとかこれを期に初めて知れたのでそれはよかった気がする。

~~ただ未だにできてないことがあって、~~  
~~**スニペット使ってる時にサジェスト使えない!!!**~~  
~~スニペット使うとTabキーで設定変数っていうのかな？を素早く移動できるけど、移動した中で、またサジェスト効かせて新しいスニペットを実行するみたいな方法を知ってる方がいたら教えていただきたいです。~~  
~~説明しづらいけど、ずっと選択された状態というかタブで移動できる状態になってる時にスニペットの項目を選択した状態になっててサジェスト使えない。~~  
~~まぁ画面クリックしたりしたら使えるけどできればキーボードで完結したい。なんとかならんものか....~~

~~調べたくないので誰か教えてください。😨~~

### できる方法あった

```json
"editor.suggest.snippetsPreventQuickSuggestions": false
```

の設定でいけるみたいですね。  
いらないスニペットを無効にもできるみたいなので今度やってみようっと。☺️  
ありがたし。

おわり。

脚注

76

2

[^1]: vscodeでサジェストの時に左に出てくるアイコンには名前があって以下リンクで確認できます。 ![設定できる項目](https://storage.googleapis.com/zenn-user-upload/hivxxw7vudck474w5f977i8rb091)

[^2]: 例えば、constantHogeHoge みたいな感じ

[^3]: 脚注 1 にある項目が設定できると思います。例えば Folders をサジェスト候補から消すなら  
`editor.suggest.showFolders` てのがおそらくあるはず。なかったらごめん