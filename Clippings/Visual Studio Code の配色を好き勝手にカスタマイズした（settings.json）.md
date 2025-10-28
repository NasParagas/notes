---
title: "Visual Studio Code の配色を好き勝手にカスタマイズした（settings.json）"
source: "https://qiita.com/deren2525/items/6bc099ae8c05e3076055"
author:
  - "[[deren2525]]"
published: 2018-10-24
created: 2025-09-09
description: "私得です。忘備録的な VSCodeの背景や細かいパーツの色、自分好みに変えたいなんて思ったことないでしょうか 世の中にはたくさんの配色テーマが落ちているのできっと自分好みの配色に出会えます。 とはいえ、長く使っていると、このVSCodeを自分だけの特別な配色にカスタマイズし..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiitaにログインして、便利な機能を使ってみませんか？

あなたにマッチした記事をお届けします

便利な情報をあとから読み返せます

[ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fderen2525%2Fitems%2F6bc099ae8c05e3076055&realm=qiita) [新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fderen2525%2Fitems%2F6bc099ae8c05e3076055&realm=qiita)

この記事は最終更新日から5年以上が経過しています。

私得です。忘備録的な

VSCodeの背景や細かいパーツの色、自分好みに変えたいなんて思ったことないでしょうか  
世の中にはたくさんの配色テーマが落ちているのできっと自分好みの配色に出会えます。  
とはいえ、長く使っていると、このVSCodeを自分だけの特別な配色にカスタマイズしてみたいとか思ったり思わなかったり....と、私は思ってしまったのでただひたすらに配色をカスタマイズしました。  
VSCode細かい配色設定はワークスペース設定の settings.json でカスタマイズすることができます。

## settings.json の場所

Windowsの場合  
ファイル ＞ メニュー ＞ 基本設定 ＞ 設定  
macOSの場合  
Code ＞ メニュー ＞ 基本設定 ＞ 設定

そこまで進むと settings.json を開くことができ json を直接編集することができます。（画像の右側）  
当方、Macです。もしかしたらWindowsと見た目が少しだけ違うかもしれないです。でも、ほぼ同じなはず  
[![スクリーンショット 2018-10-24 0.05.10.png](https://qiita-image-store.s3.amazonaws.com/0/302304/3c8ed585-200f-6ddf-4abb-b82fc1d01683.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F302304%2F3c8ed585-200f-6ddf-4abb-b82fc1d01683.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=c11e99d861562d4a631d2187951f26e2)

## カラー設定

settings.json を直接編集できる場所があるので以下のものを追加します。  
この中にどんどん書き込んでいきます。

settings.json

```json
{
    "workbench.colorCustomizations": {
        "_comment": "この中にどんどん書く"
    }
}
```

例えば、下のようなかんじ。下に書いてあるのはエディタの背景色を設定しています。  
元々使っている配色テーマから一部パーツの色を変えるのも、ここに書いていけば可能です。

settings.json

```json
{
    "workbench.colorCustomizations": {
        "editor.background": "#f4f4f5"
    }
}
```

詳しいことは下のリンク先に書いてあります  
[https://code.visualstudio.com/docs/getstarted/theme-color-reference](https://code.visualstudio.com/docs/getstarted/theme-color-reference)

↑読んだけど、どれがどこのパーツのこといってるのかわからんわ！という人用に  
全部ではないですが、下につらつらメモしました。参考にしてください。

settings.json

## ついでに

私の今のエディタの配色です。なんだか全体的に白いですが気に入っています。  
（配色テーマでこんなやつ普通にありそうとかいうのは禁句で( ；∀；)）  
[![スクリーンショット 2018-10-24 1.00.04.png](https://qiita-image-store.s3.amazonaws.com/0/302304/0e764a77-d400-14a6-259b-40890228c715.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F302304%2F0e764a77-d400-14a6-259b-40890228c715.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=fa557e54b4c80afd0b1e8fc2d7aa1f8b)

ついでに晒します。

### 追記（2019/04/05）

今気づいたのですが、私の場合、  
配色テーマを「Qiuet Light」にした上で自分のカスタマイズを設定していたみたいです。  
自分でカスタマイズもしていますが一部パーツは配色テーマの力で色が付いています。  
配色テーマデフォルト（「Dark」）のまま上のカスタマイズを取り込むと一部配色がカオスになる可能性ありますね

## さいごに

今回は配色カスタマイズのみのご紹介でしたが、 settings.json では配色以外にも様々な設定を書くことができます。配色のカスタマイズでも感じたことなのですが自由度が高いのでまじでキリがないです。  
自分好みの配色テーマが落ちてたらそれをもらった方が断然早いので細々と設定を書くよりもそちらがおすすめです（おい）  
それでも私は謎の達成感を味わうのが好きなタイプなので、こうしてちまちまjsonに書いていたのでした（完）

[0](https://qiita.com/deren2525/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fderen2525%2Fitems%2F6bc099ae8c05e3076055&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fderen2525%2Fitems%2F6bc099ae8c05e3076055&realm=qiita)

[163](https://qiita.com/deren2525/items/6bc099ae8c05e3076055/likers)

いいねしたユーザー一覧へ移動

140