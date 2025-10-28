---
title: "ViにおけるESCの代替キーマップは何が良いのか?"
source: "https://qiita.com/RepublicOfKorokke/items/0a267b53156cd8a64a59"
author:
  - "[[RepublicOfKorokke]]"
published: 2023-06-26
created: 2025-09-23
description: "前書き 全てのIDEを捨てNeoVimに乗り換えた人です。 ふと最近、Vimにおける要石「ESC」が押しにくいなと思い代替案を探していました。 調べると同じような人は多くいて、 参考になるような設定がいくつか見つかりましたので紹介しようと思います。 注意 「設定は全て個..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiitaにログインして、便利な機能を使ってみませんか？

あなたにマッチした記事をお届けします

便利な情報をあとから読み返せます

[ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2FRepublicOfKorokke%2Fitems%2F0a267b53156cd8a64a59&realm=qiita) [新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2FRepublicOfKorokke%2Fitems%2F0a267b53156cd8a64a59&realm=qiita)

この記事は最終更新日から1年以上が経過しています。

[@RepublicOfKorokke](https://qiita.com/RepublicOfKorokke)

## ViにおけるESCの代替キーマップは何が良いのか?

投稿日

## 前書き

**全てのIDEを捨てNeoVimに乗り換えた人です。**

ふと最近、Vimにおける要石「 **ESC** 」が押しにくいなと思い代替案を探していました。  
調べると同じような人は多くいて、  
参考になるような設定がいくつか見つかりましたので紹介しようと思います。

### 注意

「設定は全て個人の好みである」という考えが前提です。  
優劣を決めようという意図はありませんので予めご了承ください。

## 候補一覧

## ESC

本記事の主題に沿わないが、一応紹介

とあるRedditの投稿によると、圧倒的に使用者が多いようだ  
結局バニラが一番ということだろうか?

## CapsLock

### ESCに疲れた多くのVimmerが辿り着くであろう境地

キーボードの一等地を占有する癖に使用頻度はワースト10に入るであろう **CapsLock**  
ここにESCがあるだけでUXがとてつもなく改善される  
先ほどのRedditでもCapsLock派を支持する声はとても多かった。

個人的にはとても好きな設定だが、残念ながらここはCtrlキーとして設定したいため却下。  
Viしか使わないならESCでもいいが、それ以外のアプリで左手小指にESCがあっても嬉しくないのだ。

## Ctrl-\[

### Vim標準の隠れESC

知っている人も多いと思うが、実はこれでもESCと **全く同じ** 挙動になる。  
なぜそうなるのかは以下の回答が参考になった。

> In a terminal Ctrl-A is ASCII 0x01, Ctrl-B is ASCII 0x02 and so on. Ctrl-Z is 0x1A and Ctrl-\[ is 0x1B.  
> And 0x1B is also the ASCII code for ESC.  
> Vim can't differentiate between ESC and Ctrl-\[, because it gets the same key code when either is pressed.

`ESC` と `Ctrl-[`のキーコードが同じであるため、Vi側で区別がつかなくなり挙動が一致するらしい  
そういうこともあるんだなと思いちょっと面白かった

個人的にはあまり押しやすくない。

## Ctrl-C

### 間違えてVi開いた人を陥れる罠

Vi慣れしていない者が `visudo` や `git config --edit` などでうっかり入るとこれで詰む  
「コマンドなんてCtrl-Cで止めれるだろ！」という甘い考えを捨てることになる要因

NeoVimにおける `Ctrl-C` の定義は、

help Ctrl-c

```
CTRL-C                  Interrupt current (search) command.
                        In Normal mode, any pending command is aborted.
```

とのことで、 **ESCと同義ではない** ことに注意。

打ちなれているキーだということでしばらくこれを使っていたのだが、以下のような問題があった。

- VisualModeから `Shift-i` で入れる複数行同時入力ができない
	- ESCで閉じないとキャンセル扱いになり消える
- Pluginによっては `ESC` 時に実行されるものがあり、 `Ctrl-C` では実行されない
- そもそも `ESC` と機能的に異なるので気持ち悪さがある

人によっては問題に感じないかもしれないが、いつか困ることになる可能性があるので注意。

## jj / jk / kj

### 最初に思いついた人天才だと思うキーマップ

絶妙に押さないコンビネーション `jj` を `ESC` にする。  
ホームポジションを崩さないで押せる上に、すぐにカーソル移動できる状態を保証できる。

ただし、 `j` ないし `k` 単体を入力したい際に、 `timeoutlen` 秒の待ちが発生するため注意が必要。

個人的には打ち間違いしにくい & Normal Modeで打っても元の位置に戻る `jk` が好きです。

## ii

### 一癖あるが合理的なアイデア

`i` でInsertになるなら `i` でNormalになるべきだと言わんばかりの設定。  
2回押しが必要なため予期せぬ `ESC` は起きにくいだろうが、3回押すと再度Inserになってしまう。  
(`i` 連打でループできる)

`jk` と比べるとホームポジションを外れるため少し押しにくい気もするが、 `ESC` に比べたら遥かに良い。  
こちらも `i` 単体を入力したい際に、 `timeoutlen` 秒の待ちが発生するため注意が必要。

## Tab

### インデントに困らないなら非常に優秀

インデントSpace派なら結構良いと思う。  
Tabキーの使用頻度が大幅に落ちるため、そこを `ESC` とする。

ふとTab使いたい時に困りそうだが、ポジションは最高。

## Enter

### 鬼才

Enterを上書く。

改行したい時は `Shift-Enter` や `Ctrl-Enter` で良いらしい。  
押しやすいけどもUX悪そう。

発想は面白いけど絶対使わない。

## Ctrl + xxx系

### 可能性は無限大

デフォルトでInsert時のCtrl + xxxってそこまで多くないので、空いているキーを自由に変える。  
よく見かけたのが `Ctrl-L` や `Ctrl-Space`

`Ctrl-Space` は結構好きなバインドで、左手で完結できるあたりがとても良い。

## 個人的結論

色々調べて試し、今現在落ち着いたのは `Ctrl-g` でした。理由はいくつかあります

- 元々 `ESC` は左手で押すので、左手で完結するキーが良い
- なるべくホームポジションから押しやすい場所がいい
- Normal Mode時によく使うキーバインドと同じものは避ける
	- 誤操作防止
- `Ctrl-Space` はTmuxで使っているためVimでは使えない
- Karabiner-Elementsでも同様の設定が簡単にできる

`jk` とこれで悩みましたが、単体入力時の待ちが少し気になったためやめました。  
とはいえ今でも癖で `jk` してしまう時があるため、とても良い設定だったんだなと思います。

`ESC` 代わりのキーをどこに置くかだけでも操作性は大きく変わるので、  
みなさんも自分に合った設定を探してみてはいかがでしょうか。

[2](https://qiita.com/RepublicOfKorokke/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2FRepublicOfKorokke%2Fitems%2F0a267b53156cd8a64a59&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2FRepublicOfKorokke%2Fitems%2F0a267b53156cd8a64a59&realm=qiita)

[![RepublicOfKorokke](https://qiita-user-profile-images.imgix.net/https%3A%2F%2Favatars.githubusercontent.com%2Fu%2F64845541%3Fv%3D4?ixlib=rb-4.0.0&auto=compress%2Cformat&lossless=0&w=128&s=433a14f0ec37271a1e8b1a23922f8a44)](https://qiita.com/RepublicOfKorokke)

[

## @RepublicOfKorokke

](https://qiita.com/RepublicOfKorokke)

環境構築は好きだけど、構築して満足しがち 趣味は散歩

[Web](https://www.instagram.com/republic_of_korokke/) [RSS](https://qiita.com/RepublicOfKorokke/feed)

[15](https://qiita.com/RepublicOfKorokke/items/0a267b53156cd8a64a59/likers)

いいねしたユーザー一覧へ移動

2