---
title: "Gitにおけるブランチ戦略について調べてみた"
source: "https://qiita.com/trsn_si/items/cfecbf7dff20c64628ea"
author:
  - "[[trsn_si]]"
published: 2020-01-26
created: 2025-07-07
description: "なんとなく使っているGitブランチ、みんなどうやって管理しているのだろう？ その謎を解明すべく、我々はジャングルの奥地へと向かった。 1.ブランチとは Git上で別々の作業を並行して行うための仕組みをブランチという。 # testing-branchという名前のブランチを..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

[@trsn\_si](https://qiita.com/trsn_si) in [株式会社インテック](https://qiita.com/organizations/intec)

投稿日

なんとなく使っているGitブランチ、みんなどうやって管理しているのだろう？  
その謎を解明すべく、我々はジャングルの奥地へと向かった。

## 1.ブランチとは

Git上で別々の作業を並行して行うための仕組みをブランチという。

```text
# testing-branchという名前のブランチを作る場合
$ git branch testing-branch

# testing-branchブランチで作業する場合
$ git checkout testing-branch
```

そもそも、ブランチっていつ使うのよ？SVNでいーじゃん！  
などと思っていた時代がわたしにもありました。

SVNなどの中央集権リポジトリを利用していると、自分のコミットがどうやっても他人に影響を及ぼしてしまいます。  
（コミットしたら、Aさんが修正したソースが壊れましたとかね）

自分のソースをマージして挙動確認したい！でも他の人には迷惑かけたくない！  
そんな時にとても便利なのがブランチ。

本番環境で動くソースリポジトリから自分の開発用ブランチを作って、自分が修正した分だけをテストしてマージ、なんてことが簡単にできます。

## 2.ブランチ戦略とは

ブランチは誰でもいくつでも作ることができる。  
ブランチのブランチ、とかもできちゃう。  
無法地帯の予感・・・！！！

というわけで、無法地帯にならないように計画的にブランチを作って運用していくために編み出されたもの、それがブランチ戦略。  
Gitを使っているチームの数だけブランチ戦略がある。たぶん。

## 3.代表的なブランチ戦略

代表的なブランチ戦略について調べてみる。

1. git-flow
2. GitHub Flow
3. gitworkflows
4. 安定trunkパターン
5. メインラインモデル
6. GitLab Flow

#### 1\. git-flow

下記のブランチを主軸に運用する方法。

- master
- hotfix
- release branches
- develop
- feature branches

#### 開発の流れ

[![git-flow.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/542703/2d2102cc-0f0e-922a-1e08-f0694413f060.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F542703%2F2d2102cc-0f0e-922a-1e08-f0694413f060.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=5d612b09e8e6f47bc814cf46ac792acb)

下記ルールに従い運用します。

- developはmasterの最新から作成
- featureはdevelopの最新から作成
- releaseはリリース対象featureマージ済developの最新から作成
- 各featureのマージ先はdevelop
- releaseはリリース時のバージョン調整などに使用
- リリースモジュールはmasterから作成
- リリースバージョンが上がるタイミングでreleaseからmasterにマージ
- マージ済featureは削除
- hotfixは致命的バグが発生した場合に使用

#### 2\. GitHub Flow

下記のブランチを主軸に運用する方法。

- master
- work branches
- (integration branches)

#### 開発の流れ

[![GitHub-flow.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/542703/f7229497-30d0-80a2-9693-78e11a77dab8.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F542703%2Ff7229497-30d0-80a2-9693-78e11a77dab8.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=2d1764fcf162e8a430453c1b40babe64)

下記ルールに従い運用します。

- masterは常にデプロイできる状態とする
- 作業ブランチはmasterから作業内容がわかるような名前のブランチを作成する
- 複数の作業ブランチを統合するための統合ブランチを作成しても良い
- 開発者は作成した各ブランチにコミットする
- 定期的にpushする
- masterへのマージ前に、PullRequestを作成し他の開発者からレビューを受ける
- masterへマージ後、直ちにデプロイするためにデプロイ作業を完全自動化する
- テストを重要視する

#### 3\. gitworkflows

下記のブランチを主軸に運用する方法。

- maint
- master
- next
- pu(proposed updates, 提案中の変更)

#### 開発の流れ

[![gitworkflow.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/542703/c021cc0d-606b-6628-dd49-2bfce35ea814.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F542703%2Fc021cc0d-606b-6628-dd49-2bfce35ea814.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=3ccd8dedc38596cf1dd2d7eaed10bfc1)

下記ルールに従い運用します。

- maintでは前回リリースされた安定バージョンのアップデートを管理
- masterでは次回のリリースに入るべきコミットを管理
- nextは、masterトピックの安定化のための テスト用ブランチとして使用。masterリリース後に `git reset --hard master` する。
- puは、含めるにはまだ時期尚早なコミット用の統合ブランチとして使用。masterリリース後に `git reset --hard master` する。
- マージ作業が多い
- プルリクエストを利用しない（メーリングリストへメールすることで変更を知らせる）

#### 4\. 安定trunkパターン

下記のブランチを主軸に運用する方法。

- stable
- Milestone1
- Milestone2
- (以下略)

#### 開発の流れ

[![stable-trunk.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/542703/c374f455-28b1-77c8-1474-717f2eda41c5.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F542703%2Fc374f455-28b1-77c8-1474-717f2eda41c5.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=31023413eb7b1b14ac0736201cf6f963)

下記ルールに従い運用します。

- Milestoneは必ずstableブランチから作成
- stable = trunk = リリースされているもの / いつでもリリース可能なもの
- 複数のリリース向けの開発、結合が可能
- マイルストーンを新設するたびに継続的インテグレーションの設定を行う必要あり
- 複数のバージョンのメンテナンスが出来ない

#### 5\. メインラインモデル

下記のブランチを主軸に運用する方法。

- develop
- version 1.x
- version 2.x
- (以下略)

#### 開発の流れ

[![mainline.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/542703/be177ea7-41d5-ec45-fd08-37e6d0eaae6e.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F542703%2Fbe177ea7-41d5-ec45-fd08-37e6d0eaae6e.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=0d032ef823677eab24332580a839acb7)

下記ルールに従い運用します。

- developブランチで作業
- 複数リリースバージョンを管理できる
- 複数バージョンを管理しない場合は、いわゆるdevelop & stableモデルと同じ

#### 6\. Git Lab Flow

下記のブランチを主軸に運用する方法。

- master
- pre-production/staging
- production
- (feature/hotfix)

#### 開発の流れ

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/542703/fd6545ea-7826-0cb7-d88e-f95a3e643250.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F542703%2Ffd6545ea-7826-0cb7-d88e-f95a3e643250.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a3c42c67a85394fcea43695f78993ea0)

下記ルールに従い運用します。

- pre-production = GitHub Flowの統合ブランチ = git-flowのrelease
- 作業が終わったらfeatureブランチにコミット
- CIでのテストをパスしたらpre-productionに自動デプロイ
- pre-productionでのテストをパスしたらproductionに自動デプロイ

## 4.所感

- GitHub flowとGitLab-flowの使い分けがまだよくわからない。
- CI環境が整っているならマージが多く発生するフローでも問題なさそう
- 小さいチームやプロジェクトならmasterとdevelopのみのGitHub flowが使いやすそう
- チームの規模、リリース頻度、チームのスキルレベル、運用難易度などの観点で再度整理したい

## 参考

[GitHub実践入門　Pul Requestによる開発の変革](https://www.amazon.co.jp/GitHub%E5%AE%9F%E8%B7%B5%E5%85%A5%E9%96%80-Pull-Request%E3%81%AB%E3%82%88%E3%82%8B%E9%96%8B%E7%99%BA%E3%81%AE%E5%A4%89%E9%9D%A9-PRESS-plus/dp/477416366X)  
大塚弘記 著/技術評論社/2014.04.25初版/

\[A successful Git branching model翻訳\]  
([http://keijinsonyaban.blogspot.com/2010/10/a-successful-git-branching-model.html](http://keijinsonyaban.blogspot.com/2010/10/a-successful-git-branching-model.html))

\[書籍「Pro Git」のコンテンツ\]  
([https://github.com/progit/progit/blob/master/ja/03-git-branching/01-chapter3.markdown](https://github.com/progit/progit/blob/master/ja/03-git-branching/01-chapter3.markdown))

\[git運用フローの選び方\]  
([https://qiita.com/arai-ta/items/7350d1053d14078a2a97](https://qiita.com/arai-ta/items/7350d1053d14078a2a97))

\[Gitのブランチモデルについて\]  
([https://qiita.com/okuderap/items/0b57830d2f56d1d51692](https://qiita.com/okuderap/items/0b57830d2f56d1d51692))

\[Using git-flow to automate your git branching workflow\]  
([https://jeffkreeftmeijer.com/git-flow/](https://jeffkreeftmeijer.com/git-flow/))

\[(分散)バージョン管理システムの組織化\]  
([http://troter.jp/pyconjp-2012-dvcs/#slide1](http://troter.jp/pyconjp-2012-dvcs/#slide1))

\[Gitlab-flowの説明\]  
([https://qiita.com/tlta-bkhn/items/f2950aaf00bfb6a8c30d](https://qiita.com/tlta-bkhn/items/f2950aaf00bfb6a8c30d))

\[Introduction to GitLab Flow\]  
()

[0](https://qiita.com/trsn_si/items/#comments)

コメント一覧へ移動

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Ftrsn_si%2Fitems%2Fcfecbf7dff20c64628ea&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Ftrsn_si%2Fitems%2Fcfecbf7dff20c64628ea&realm=qiita)

[510](https://qiita.com/trsn_si/items/cfecbf7dff20c64628ea/likers)

383