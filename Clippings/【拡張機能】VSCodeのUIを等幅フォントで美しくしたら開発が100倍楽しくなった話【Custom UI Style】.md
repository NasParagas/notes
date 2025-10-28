---
title: "【拡張機能】VSCodeのUIを等幅フォントで美しくしたら開発が100倍楽しくなった話【Custom UI Style】"
source: "https://qiita.com/FlatBone/items/8a919f9003fc9d96dfc1"
author:
  - "[[FlatBone]]"
published: 2025-03-06
created: 2025-10-12
description: "はじめに 「VSCodeのサイドバーとかメニューとか、デフォルトのフォントが等幅じゃないの、気持ち悪くない？」 って思うことありませんか？ 私はあります！！ ファイル名が微妙にズレてたり、エクスプローラーの見た目が揃ってなかったりすると、なんか集中できないんですよね。あと..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiitaにログインして、便利な機能を使ってみませんか？

あなたにマッチした記事をお届けします

便利な情報をあとから読み返せます

[ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2FFlatBone%2Fitems%2F8a919f9003fc9d96dfc1&realm=qiita) [新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2FFlatBone%2Fitems%2F8a919f9003fc9d96dfc1&realm=qiita)

### はじめに

**「VSCodeのサイドバーとかメニューとか、デフォルトのフォントが等幅じゃないの、気持ち悪くない？」** って思うことありませんか？

**私はあります！！**  
ファイル名が微妙にズレてたり、エクスプローラーの見た目が揃ってなかったりすると、なんか集中できないんですよね。あとはUIの見た目好みじゃないなーとｋ

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3755504/267823e2-3a59-4d19-a29e-c0bb19ceb55a.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3755504%2F267823e2-3a59-4d19-a29e-c0bb19ceb55a.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=4c80406f8e1c7fe58fa37285d032e33f)  
README.ja.mdとREADME.ko.mdがちょっとずれるのが嫌。  
ログで連番ファイルとかだともっと嫌になりやすい。

そんな小さなストレスを解消すべく、VSCodeのエディタ以外の部分（UI全体）のフォントを、自分好みの等幅で美しいフォントに変更する挑戦をしてみました。

この記事を読めば、あなたも自分の好きなフォント・CSSでVSCodeをドレスアップできるはず！

#### この記事でできる・わかること

- VSCodeのUIフォントを好きなフォントに変更する方法
- 拡張機能 **「Custom UI Style」** の使い方と設定手順
- 開発者ツールを使ったカスタムCSSの書き方
- ハマりがちなポイントとその解決策

#### 記事の対象者

- VSCodeを使っていて見た目をカスタマイズしたい人
- CSSや設定ファイルいじりに抵抗がない人
- 「デフォルトの見た目に飽きた！」って感じてる人

#### 動作環境

- **VSCode**: バージョン1.87
- **拡張機能**: Custom UI Style v0.5.4
- **設定ファイル**: `settings.json`
- **スキル**: 基本的なCSSの理解（セレクタとか `font-size` くらい分かればOK）

---

### 先に結論

これが  
[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3755504/267823e2-3a59-4d19-a29e-c0bb19ceb55a.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3755504%2F267823e2-3a59-4d19-a29e-c0bb19ceb55a.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=4c80406f8e1c7fe58fa37285d032e33f)

**こうなる**  
[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3755504/5aa232dd-1b78-4e33-a67b-822086196042.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3755504%2F5aa232dd-1b78-4e33-a67b-822086196042.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=bb247c6ded15bf40fa7a93240a8c066c)

### 等幅じゃないフォントにモヤモヤ

サイドバーでファイル名が並ぶと、微妙にズレてたり、  
「エクスプローラー」ってタイトルの見た目がなんかチグハグだったり。

コードを書くエディタ部分は等幅でバッチリなのに、UI部分がそうじゃないって、デザインの統一感が崩れてる気がして我慢ならなかったんです。

最初は「設定で変えられるでしょ？」って軽い気持ちでVSCodeの `settings.json` を開いてみました。

エディタ部分のフォントは簡単に変えられますが、サイドバーとかメニューとかのUI部分はまるで手つかず。  
標準設定じゃ無理っぽいので、拡張機能を探します。

**好きなフォント**:UDEV Gothic JPDOC ←全部これにしたい。おすすめです。  
Hackgenも良いですよね

### 拡張機能探しの旅と「Custom UI Style」への着陸

VSCodeのマーケットプレイスで「UI font」とか「customize UI」で検索しまくって、最初に見つけたのが「Customize UI」って拡張機能。

でも、調べてみると、近年のVSCodeのアップデートで破壊的変更が入って使えなくなってたみたい。がっかりしたけど、ここで諦めるわけにはいかない。

そこで、同じ悩みを持つ人の記事を発見したので参考に。

最終的に「Custom UI Style」に落ち着いたってことなので早速VSCodeの拡張機能タブから検索してインストール。※設定変更時は管理者権限でVSCodeを開く必要があるかも。  
[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3755504/f9a4318d-6e2f-45a4-a72b-00c4a68dbb82.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3755504%2Ff9a4318d-6e2f-45a4-a72b-00c4a68dbb82.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=9eda4648cb83ebe6c30b4434f12aeab0)

#### 最初の設定: 大好きなフォントをぶち込む

**「Custom UI Style」** の設定は簡単で、拡張機能設定からできます。今回はUI全体のフォントを変えたかったので、

**「Font: Sans Serif (すべてのプロファイルに適用)」** って項目に、私の大好きな等幅フォント「UDEV Gothic JPDOC」を入力してみた。設定画面はこんな感じ。

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3755504/439bb63e-0a96-4805-9bf7-0e0392e60d33.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3755504%2F439bb63e-0a96-4805-9bf7-0e0392e60d33.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=77efabb056ffd36e2669ed970757c971)

んで、VSCodeを再起動すると…  
[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3755504/cf1a052e-631a-4882-9956-8f155ccdc761.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3755504%2Fcf1a052e-631a-4882-9956-8f155ccdc761.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=64d3f7f295c5e6e7983263a8d769c53d)  
[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3755504/521ed7cb-2bc6-40d8-8bb8-e5624f0e7418.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3755504%2F521ed7cb-2bc6-40d8-8bb8-e5624f0e7418.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=c6d989971e6678bc4f9fef4eebc41b9c)

サイドバーもメニューも「UDEV Gothic JPDOC」に変りました！  
[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3755504/8c1d1479-4896-4817-b7f1-40e2c50ff086.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3755504%2F8c1d1479-4896-4817-b7f1-40e2c50ff086.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=3cbe5bc2c40997b5140d4b9d85d184ff)

等幅で美しい見た目でテンション上がったけど、ちょっと待てよ。  
サイドバーの文字サイズが小さすぎて見づらい…。  
[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3755504/348ab198-afdf-46aa-b83f-6de1bee9b3e4.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3755504%2F348ab198-afdf-46aa-b83f-6de1bee9b3e4.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=30c6fc5a53c7f8f8b3ba1eff04596bd1)

### 文字サイズ問題に立ち向かう

我慢しようかとも思いましたが、やっぱり小さい文字は目に悪いし、気持ち悪いままなのは嫌です。

そこで「Custom UI Style」の公式リポジトリ（ [GitHub](https://github.com/subframe7536/vscode-custom-ui-style) ）のREADMEをチェック。どうやら `settings.json` にカスタムCSSを書けば色々いじれるらしい。  
[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3755504/c6831e4f-ef6d-4213-b540-b26db1496ad3.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3755504%2Fc6831e4f-ef6d-4213-b540-b26db1496ad3.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=7423a8ade262d69c0f4163e3b0984d34)

でも、フォントサイズを直接変える設定は見当たらない。issueを漁ることに。

「size」で検索したら、 [Issue #33](https://github.com/subframe7536/vscode-custom-ui-style/issues/33) でフォントサイズをいじってる話題を発見！サイドバーじゃない部分の話だったけど、「CSSでなんとかなるならいけるんじゃね？」って希望が見えてきました。

### 開発者ツールで要素を特定してCSSを書きまくる

VSCodeの 「ヘルプ → 開発者ツールを切り替え」で **デベロッパーツールを起動** 。  
サイドバーを右クリックして要素を調べてみたら、`.monaco-workbench` ってクラスが全体を包んでるっぽいことが分かりました。

まずはサイドバーのファイル名とかフォルダ名を大きくしようと、CSSを `settings.json` に追加。

再起動したら…成功！ファイル名がいい感じに大きくなりました！  
でも、「エクスプローラー」とかフォルダのタイトル部分はまだ小さいまま。

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3755504/2f839339-f8c8-4456-915d-6ec61e82e285.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3755504%2F2f839339-f8c8-4456-915d-6ec61e82e285.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=2a50d04274304f07e0d19ed84858519f)

### ワイルドカードでゴリ押し解決

タイトル部分のクラスを特定しようとしたけど、`.monaco-workbench` から `title` までの階層が深すぎて挫折しかけました。  
そこでissue #33をもう一度見直したら、 `*` （ワイルドカード）を使ってる例があったのを思い出しました。「これ使えるんじゃね？」って試しにこんな感じで。

再起動したら…キタ！サイドバー全体がちょうどいいサイズ感に！  
「UDEV Gothic JPDOC」の等幅の美しさがフルに活きて、見た目が完璧に整った瞬間でした。ファイル名は等幅がやっぱり好き。タイポも見つけやすい気がする。

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3755504/464d319f-929f-4068-bbe0-5f4b98af1cb6.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3755504%2F464d319f-929f-4068-bbe0-5f4b98af1cb6.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=419d17e908e920f883f28d1c8da2b284)

### (おまけ)ハマった罠とその脱出法

試行錯誤してるときに1つ罠にハマりました。 `settings.json` を頻繁に編集して再起動してたら、なぜか再起動ダイアログが出なくなって、反映されない状態に。焦りましたけど、Ctrl+Shift+P→Custom UI Style reloadで反映・復活します。  
[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3755504/e36f262c-05f2-4b27-adcd-80e2d4d2a757.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3755504%2Fe36f262c-05f2-4b27-adcd-80e2d4d2a757.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=9ffde001f4b10b583de911fdde0765ca)

## 結論

VSCodeのUIフォントを「UDEV Gothic JPDOC」に変えて、サイドバーの文字サイズも自分好みに調整できました。

最初は「等幅じゃないのが気持ち悪い」ってモヤモヤから始まりましたが、拡張機能で見た目も気分もスッキリする開発環境を手に入れられたので満足しています。

あなたも好きなフォントでVSCodeをカスタマイズして、毎日使うツールをちょっと楽しくしてみませんか？

他の機能として背景に画像を置いたりできるみたいです。CSSをオーバーライドするのでなんでもできるともいえる。

## 参考資料

[0](https://qiita.com/FlatBone/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2FFlatBone%2Fitems%2F8a919f9003fc9d96dfc1&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2FFlatBone%2Fitems%2F8a919f9003fc9d96dfc1&realm=qiita)

## Qiita Conference 2025 Autumn 11月5日(水)~7日(金)開催！

![](https://cdn.qiita.com/assets/public/official_campaigns/qiita_conference_2025_autumn/image-conference_2025_autumn_ogp-7cf3021de31b9ab76a7b3bbaf2909bb5.png)

Qiita Conferenceは、AI時代のエンジニアに贈るQiita最大規模のテックカンファレンスです！

基調講演ゲスト(敬称略)

piacere、牛尾 剛、Esteban Suarez、和田 卓人、seya、ミノ駆動、市谷 聡啓、からあげ、岩瀬 義昌、まつもとゆきひろ、みのるん and more…

[イベント詳細を見る](https://qiita.com/official-campaigns/conference/2025-autumn)

[6](https://qiita.com/FlatBone/items/8a919f9003fc9d96dfc1/likers)

いいねしたユーザー一覧へ移動

10