---
title: "”Kubernetes” の基本を徹底解説"
source: "https://qiita.com/tadashiro_ninomiya/items/6e6fea807b2a16732b5b"
author:
  - "[[tadashiro_ninomiya]]"
published: 2023-10-29
created: 2025-08-28
description: "本記事では、Kubernetes（K８s）の基本を徹底解説します！ 「Kubernetesという言葉をチラッと聞いたことがあるけど、よく分からん」 「何となく使ってはいるけど、何してんのかよく分からん」 「Dockerは使ってことあるけど、Kubernetesって何？」 ..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiitaにログインして、便利な機能を使ってみませんか？

[ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Ftadashiro_ninomiya%2Fitems%2F6e6fea807b2a16732b5b&realm=qiita) [新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Ftadashiro_ninomiya%2Fitems%2F6e6fea807b2a16732b5b&realm=qiita)

この記事は最終更新日から1年以上が経過しています。

本記事では、Kubernetes（K８s）の基本を徹底解説します！

- 「Kubernetesという言葉をチラッと聞いたことがあるけど、よく分からん」
- 「何となく使ってはいるけど、何してんのかよく分からん」
- 「Dockerは使ってことあるけど、Kubernetesって何？」

そんな方は、ぜひ読み進めてください！

今後システム開発やPMを行うエンジニアとして活動する中で、大規模なサービスを担当することになればなるほどKubernetesに触れる機会は必ず増えます。上昇志向のあるエンジニアであれば、知っておいて損はありません！

また最後により深くKubernetesについて理解するための学習方法を紹介しているため、「Kubernetes詳しいぜ！」という方もぜひ一読していただければと思います。

弊社Nucoでは、他にも様々なお役立ち記事を公開しています。よかったら、Organizationのページも覗いてみてください。  
また、Nucoでは一緒に働く仲間も募集しています！興味をお持ちいただける方は、 [こちら](https://www.recruit.nuco.co.jp/?qiita_item_id=6e6fea807b2a16732b5b) まで。

## Kubernetesって何が出来るの？

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3486778/fd5604f1-b5f9-29be-3dea-fe07768bdb86.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3486778%2Ffd5604f1-b5f9-29be-3dea-fe07768bdb86.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=7ed7d7c3e8480b6df08bb656bfa49842)

分かりやすく言うと、Kubernetesはコンテナを操る物です。例えば、以下のようなことをしてくれます。

- コンテナの起動管理
- コンテナの起動ノードの自動選定
- コンテナとKubernetesクラスタ外部との接続や負荷分散
- コンテナとボリュームの連携

コンテナ自体を作成する（コンテナ化）はすることが出来ないので、最も人気が高く、広く使用されているコンテナ化ツールであるDockerと併用することが一般的です！

クラウド上でサービスをデプロイする場合はコンテナ化する場合が多いと思います。大規模なサービスであれば、複数のコンテナを扱う必要があります。そういった場合にKubernetesを用いてその連携を行います。この世の著名サービスの多くがKubernetesを使用していることでしょう。

Kubernetesについて詳しい解説を行う場合は、コンテナやDockerについて多少の解説を行う必要があります。以下の章で解説します！

## Kubernetesの解説前に理解するべきこと

この章ではKubernetesの解説前に理解するべきことを解説します。Kubernetesはコンテナ、Dockerだけだと実装が面倒な部分を請け負う存在です。そのため、Kubernetesを理解するためにはコンテナ、Docker、活用における課題などを理解する必要があります。

- コンテナとは？
- Dockerとは？
- コンテナとDockerが解決してきたこと
- コンテナとDocker活用における課題

コンテナやDockerに対して十分な理解がある場合は、ほとんど読まなくても大丈夫です。「コンテナとDocker活用における課題」だけ目を通しておいてください！

## コンテナとは？

コンテナとは仮想化技術の１つです。Linuxカーネルの機能を利用して、すべてのプロセスからリソース割り当てや名前空間を分離します。その他の仮想化技術として仮想マシンが挙げられますが、それと比較してリソースの消費や目的外の処理（オーバーヘッド）が小さくて済むという特徴があります。

コンテナは次の2つに分類可能です。

- システムコンテナ
- アプリケーションコンテナ

システムコンテナは1つのコンテナに対して複数のアプリケーションの立ち上げが可能ですが、アプリケーションコンテナは1つのコンテナに対して1つのアプリケーションのみ立ち上げ可能です。

またDockerが構築するコンテナはアプリケーションコンテナであるため、本記事で頻出する”コンテナ”は基本的にアプリケーションコンテナを指しています。

## Dockerとは？

Dockerとはコンテナ化ツールの1つです。Dockerはアプリケーションコンテナを構築し、アプリケーションの開発・配布・実行を行います。Dockerは公開依頼、システム開発者の間で急速に普及しました。その理由は以下です。

- 仮想マシンよりも軽量で高速
- 開発者のマシンリソースが節約可能
- 環境構築が簡単

DockerはDockerfileと呼ばれるテキストファイルの内容を基にDockerイメージ（Dockerコンテナの基）を作成します。

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3486778/48c03365-5bcb-f9d5-ed5c-368f0c91f2b9.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3486778%2F48c03365-5bcb-f9d5-ed5c-368f0c91f2b9.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e3d429a849bf298637341727d7a3d913)

例えば以下はDockerfileの簡単な例です。このファイルを基にしてDockerコンテナを作成可能だと考えると簡単に思えますよね？それ故コンテナの構成が更新された場合も、Dockerfileを基準として変更点を比較することも容易です。

```text
# syntax=docker/dockerfile:1
FROM ubuntu:22.04
COPY . /app
RUN make /app
CMD python /app/app.py
```

Kubernetesを理解するにはDockerの理解は必要不可欠です。Docker初心者の方は以下の参考記事を通してDockerの理解を深めましょう。以下のDocker記事を読んでからKubernetesについて学んでもいいですし、本記事でKubernetesについて理解を進めてから以下のDocker記事を読んでも大丈夫です。

参考記事： [Docker初学者がやるべきこと3選](https://qiita.com/nuco_fn/items/22cf85a2646d96361d0b)

## コンテナとDockerが解決してきたこと

コンテナやDockerがリリースされるまでのシステム開発では、事前に用意された開発サーバ上で開発を行うか、仮想マシン環境上で開発に取り組んでいました。

本パートではコンテナとDocker（Dockerコンテナ）が解決してきたことを合計7つ解説します。後にDockerコンテナでは解決出来ないことについて解説します。この内容は前提となるため把握しておきましょう。

### 環境維持

コンテナはコンテナ内で環境が完結している特徴があります。そのため、Dockerコンテナを利用する場合はパッチ（最新版との相違点）の適応やOSアップデートへの対応はDockerfileを修正し、入れ替えるだけで済みます。  

### 環境依存

アプリケーションを実行するにはソースコード、ランタイム、ライブラリが必要不可欠です。アプリケーションの実行環境が変化すると、ランタイムやライブラリのバージョンが変化する場合があり、システムは正常に動作しません。しかし、Dockerコンテナによって作成されるイメージは環境から切り離されています。正常動作することが確認出来た時点で、実行環境を変更しても挙動は変化しません。  

### セキュリティの維持

コンテナを利用していない場合にセキュリティの脆弱性が発見されると、複数の環境で脆弱性に対して対応を進める必要があります。しかしコンテナを利用している場合はコンテナを新しいものに変更するだけで対応可能です。なぜならコンテナイメージは環境に依存していないためです。

またDockerコンテナを立ち上げるために必要なDocker Desktopと呼ばれるサービスでは、脆弱性を自動で発見してくれる脆弱性スキャンと言われる機能もあります。このような機能によってもコンテナを利用したセキュリティ維持の難易度は大幅に下がりました。  

### リソース消費量の低下

コンテナはその他の仮想化技術に比べてリソース消費量が少ないと言われています。コンテナはおよそアプリケーションが必要とするリソースのみを要求するためコンテナ立ち上げにおける実行時間が短くて済みます。  

### アプリケーション構成の変化による開発の高速化

コンテナ利用が普及したことにより、アプリケーション構成が「モノリシック」から「マイクロサービス」に変化しました。

- モノリシック  
	複数の機能を備えた1つの巨大で複雑なアプリケーション
- マイクロサービス  
	それぞれの機能ごとに実装されたアプリケーションを連携する

モノリシックアーキテクチャを採用している場合は、1つの機能を修正するために全体のリビルドとテストを実行する必要がありました。しかし1つのアプリケーションしか立ち上げられないコンテナが普及したことで、機能ごとに実装されたアプリケーションを連携するマイクロサービスアーキテクチャの普及が進みました。

その結果、修正コストやデプロイの負担の減少に繋がりシステム開発の効率が格段に上がりました。

## コンテナとDocker活用における課題

次にコンテナとDocker（Dockerコンテナ）を活用するうえで生じた課題について解説します。1台のサーバでいくつかのコンテナを立ち上げるだけであれば何も問題は生じなさそうですよね。

しかし、より多くのコンテナを商用環境で動かすことを考えたらどうでしょうか？結構大変そうじゃないですか？ここらへんの内容を深掘って、コンテナとDockerだけを活用する場合の課題を解説します。

Kubernetesはこれらの課題を解決するサービスとしての一面があるので、しっかりと理解しましょう。

### デプロイ

複数のコンテナを管理しながらデプロイを行うのは非常に手間のかかる作業です。例えば商用環境におけるデプロイではダウンタイムが発生しないデプロイの仕組みを考える必要があります。ダウンタイムが発生しないためのデプロイの仕組みは複数考案されていますが、どれも手作業で行うには手間がかかります。それに加えて複雑な作業を絶対にミス出来ない緊張感も伴います。  

### 複数コンテナの管理、連携、運用

高可用性（システムを常に使える状態にする）の観点からコンテナは複数のサーバー上で管理されます。そのため複数コンテナを管理する運用者はどのサーバー上でコンテナが起動しているかを把握する必要があります。加えてサービスの継続における優先順位の高いコンテナを優先的に起動させるために優先順位の低いバッチ処理のコンテナなどは停止しておくべきです。

このように起動するコンテナの数や場所を把握することでシステムの高可用性を担保する必要があります。しかしこれらを完璧に人間が管理するのは不可能です。  

### コンテナイメージの運用

コンテナイメージを利用する場合は、簡易的な修正であっても毎回リビルドを行う必要があります。コンテナは軽量ではありますが、コンテナイメージをリビルドする度に修正箇所に該当する部分が追加作成されストレージを圧迫します。そのためコンテナイメージは定期的に削除するなど整理を行わなければなりません。管理するべきコンテナが複数あってもなくても、その作業は非常に面倒ですね。  

### コンテナ障害発生時の対応

アプリケーションの運用において予期せぬ障害にぶつかることは稀ではありません。単一のコンテナを利用しているのであれば、そのコンテナをロールバック（処理前の状態に戻す）すれば良いだけなのでそこまで手間では無いかもしれません。しかし複数のコンテナを利用している場合に、全てのコンテナ同士の手違いなくロールバックを行うことは現実的ではありません。

## Kubernetesとは？

コンテナとDocker活用における課題を理解したところでようやくKubernetesの解説に入ります。

Kubernetesはコンテナオーケストレーションツールの１つです。コンテナオーケストレーションツールは、コンテナの運用管理と自動化を実現しコンテナの管理を容易にします。Kubernetesに提供される機能によって複数のコンテナやDockerを活用する上での課題の多くを解決することが可能になります。

Kubernetesは複数のマシン（＝ノード）にわたってコンテナ化されたアプリケーションの実行管理を行います。１つ、もしくは複数のノードによってクラスタを構築し、そのクラスタ内でコンテナの立ち上げなどを行います。最大５,０００ノードからなるクラスタで最大300,000個のコンテナを実行可能です。

余談ですが、KubernetesはK8sと省略されることがあります。「K」と「s」の間が８文字であることに由来しています。

## Kubernetesを利用するメリットと必要性

繰り返しになりますが、Kubernetesを利用することのメリットは複数コンテナの管理が容易になることです。仮に商用環境でコンテナを管理する場合の課題を考えてみましょう。以下の項目を全て決定し、実行しなければなりません！

- どのコンテナをどのサーバを用いて実行するのか
- 起動したコンテナに外部からリクエストを行うための設定は？
- ロードバランシングはどのように行うのか
- コンテナ間の通信可否をコントロールするには？
- ストレージを払い出す際に、コンテナにマウント/アンマウントする方法は？

正直、かなり面倒ですよね。十数個のコンテナで構成されるマイクロサービスであればまだ「面倒だなぁ」くらいの温度感で済むかもしれませんが、桁が１つ増えれば発狂ものです。システムの保守運用コストを下げるためにもKubernetesは必要になります。

## Kubernetesが不必要な場合やデメリット

逆にKubernetesの不必要性やデメリットについても言及しておきます。当然、全てのサービスにおいてKubernetesを利用する必要はありません。この記事を読んでくれているQiita民の皆さんの中にもKubernetesを触ったことがない人が多いと思います。

以下に３つのデメリットを紹介します。

### 1\. とにかく学習コストが高い

Kubernetesはやれることが多い便利なサービスであるため、学習することが本当に沢山あります。 Kubernetesを触ったことがあるエンジニアが周りにいない場合もありうるので、自分で学ぶ覚悟が必要です。

### 2\. Kubernetesのアップデートが頻繁

Kubernetesは3ヶ月に１度のアップデートがあります。そのため少し前にアップロードされたKubernetesの参考コードを使ってみても動かない、みたいな状況は頻繁に起こりえます。ただせさえ学習コストが高いKubernetesですが、常に知識をアップデートしておく必要があります。

### 3\. 拡張性が高すぎて逆に困る

Kubernetesは拡張性が高いことで知られています。仕様上、複数サーバーの利用や外部のファイルシステムとの連携などを行う必要がありますがその際にどのサービスを使えば良いか迷ってしまうこともあります。Azure、AWS、GCPなどを用いていれば簡単にプラグインを決定出来そうな一方で、用いていない場合は複数のサービスを比較検討する必要があります。

  

これらがKubernetesを用いる上で考えられるデメリットです。いくつか書きましたが、やはり結局は「学習コストが高い」という点に集約されますね。

**「大規模なサービスで継続的な開発が実施されている」** 場合は Kubernetesを使うことをお勧めしますが、そうでない場合は無駄に学習コストが必要になるので導入の必要は無いでしょう。

## KubernetesとDockerの動作イメージを比較してみる

本章ではKubernetesとDockerの動作イメージを比較することでKubernetesについての理解を深めましょう。まずはKubernetes周りの用語や構成要素（コンポーネント）、構成を解説します。

### Kubernetesの用語、構成要素、構成の解説

#### ・Kubernetesの用語

まずは基本的な用語を解説します。以下は、基本的な用語を１つにまとめた図です。用語と合わせて解説します。

[![スクリーンショット 2023-11-20 15.13.27.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3486778/4b400543-13db-bb19-9c34-15b1fe7c29bc.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3486778%2F4b400543-13db-bb19-9c34-15b1fe7c29bc.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=5a0ade95d17d019b6713250111d94f1e)

- **クラスター**  
	Kubernetesのリソースを管理する集合体を **クラスター** と呼びます。Kubernetesを活用するマイクロサービスでは、複数のクラスターによって構成されています。
- **ノード**  
	クラスターは、複数の **ノード** から構成されています。ノードはマスターノードと通常のノードに分類することが可能です。マスターノードはKubernetesを管理するために利用され、通常のノードはアプリケーションなどを実際に実行する役割を持ちます。
- **ポッド**  
	**ポッド** はコンテナの集合体です。ポッドは通常のノードの中に存在し、最低１つのコンテナを有しています。Kubernetesにデプロイする場合は、ポッド単位で行われます。
- **コンテナ**  
	この **コンテナ** はDockerコンテナのことです。１つのアプリケーションの立ち上げが可能です。

これらの用語は前提をして押さえておいてください！次は、本格的にKubernetesの構成要素について紹介します。

#### ・Kubernetesの構成要素

以下はKubernetesの構成を表す図です。特にノードとマスターノードの中身について詳細な説明を行います。

[![スクリーンショット 2023-11-20 16.13.42.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3486778/d735108b-ba25-ca88-8ca3-1d9c27ad57f4.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3486778%2Fd735108b-ba25-ca88-8ca3-1d9c27ad57f4.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=539c0ef7fa24452a01d73e4d8e0476c6)

先ほど説明した図の中に新しい要素を加えました。これらはKubernetesの中でもかなり重要な構成要素です。まず最初に **マスターノードの中身** を解説します！以下はマスターノードの中身をまとめた表です。

マスターノードの中身は以下のようになっています。これらは、Kubernetesを管理する役割を果たします。またマスターノードはControl Planeと呼ばれています。

| マスターノードのコンポーネント | 内容 |
| --- | --- |
| etcd | データストアとしての役割を果たす |
| kube-api-server | control planeノードを動かすためのAPIサーバー |
| kube-scheduler | ポッドを実行するノード(workerノード)を選定する |
| kube-controller-manager | APIサーバーによる宣言による実行を管理する |
| cloud-controller-manager | Kubernetesと任意のクラウド環境との連携を行う |

次に **通常のノード** の解説を行います。一般的に通常のノードは **workerノード** と呼ばれています。このノードはユーザーが動かしたい各種コンテナを実行するためのノードです。

| workerノードのコンポーネント | 内容 |
| --- | --- |
| kubelet | 各workerノードで実行するコンテナ、ボリューム、ポッドネットワークなどの設定を管理 |
| kube-proxy | クラスター内の各workerノードで動作しているネットワークプロキシ（他workerノードとの連携） |

では、これらがどのように構成されているのかを以下のパートで解説します！

#### ・Kubernetesの構成と動作イメージ

上記にてKubernetesの構成要素について解説しました。以下にそれぞれの構成要素がどのように繋がっているのかを図で示します。

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3486778/9ddef5ac-183c-824e-2f39-eb9c3444cd9c.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3486778%2F9ddef5ac-183c-824e-2f39-eb9c3444cd9c.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=710aba9d7f6e01e24a2d288a9cddfe43)

Kubernetesをデプロイすると上図のようなクラスターが展開されます。作業者はkubectlと呼ばれるコマンドラインツールを通してKubernetesをコントロールします。

KubernetesではYAML形式で記述された **「マニフェスト」** と言われるDockerで言うところのDockerfileのようなものが存在します。マニフェストにKubernetes内で実現したいことを記述することで指定のコンテナを設定したり、起動したり出来ます。

マニフェストがインストールされKubernetesがデプロイされると、マスターノードのkube-controller-managerやkube-schedulerがkube-api-serverからの命令を受け取り「指定のコンテナをどのノードで実行するのか？」を決定します。

その情報を受け取ったkube-api-serverがノード（workerノード）のkuberletやkube-proxyに司令を渡しポッドの中に存在する実際のコンテナを起動させます。

以上がKubernetesの動作イメージです。

## Kubernetesが提供する基本機能のまとめ

これまでの内容を踏まえたうえで、Kubernetesの基本機能を解説します！全ての機能を列挙するのは不可能なので、以下の６つに絞って解説します！

- サービス検出と負荷調整
- ストレージの自動化
- 自動化されたデプロイ管理
- 自己回復
- 自動化されたスケジューリング機能
- 秘密情報と設定の管理

それぞれが基本的な機能であるため、必ず把握しておきましょう！

### サービス検出と負荷調整

Kubernetesはポッドという単位でコンテナのグループを実行管理しています。ポッドには「ラベル」が定義されており、Kubernetesの「サービス」と呼ばれる設定したラベルにマッチするポッドを抽出して負荷分散を行う機能があります。このサービス機能を用いることで、Kubernetesクラスタの内外からリクエストを受け付け対象となるポッドに接続します。

例えば以下の図のように、アプリケーションコンテナが減少した場合もサービス機能を用いることで増加したコンテナにリクエストを流し負荷分散を行うことができます。

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3486778/f5f35ca2-e39a-13f9-162c-cbec2a1306e6.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3486778%2Ff5f35ca2-e39a-13f9-162c-cbec2a1306e6.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=03c19a83ed5d754bcb7208f305ec1bc6)

### ストレージの自動化

Kubernetesにはポッド内のコンテナで様々なストレージを利用するためのボリューム（データの永続化）プラグインが組み込まれています。設定ファイルとして記述されるマニフェストファイルを記述するだけでボリュームの作成、ボリューム利用の準備などが自動で行われます。これらの機能をストレージオーケストレーションと呼んでいます。これらの機能によって以下のことが可能になります。

- 空の共有ボリュームを払い出して、同一ポッド内のコンテナでデータを共有する
- Kubernetesに設定した秘密情報や設定情報をボリュームとしてマウントすることで利用する
- Kubernetesクラスタ外部のクラウドストレージなどを利用する

また近年ではCIS（Container Storage Interface）と呼ばれる自由にストレージプラグイン作成し使用することのできる機能が提供されました。より柔軟なストレージやボリュームの活用が可能です。

### 自動化されたデプロイ管理

Kubernetesは宣言的APIと呼ばれるアーキテクチャが採用されています。宣言的APIとは、マニフェスト（設定）ファイルで「あるべき状態」を定義し、Kubernetesが自動的に実現・維持を行うことを意味します。このアーキテクチャを採用することでKubernetesは様々な機能を実現しています。

```text
apiVersion: app/v1
kind: Deployment
metadata:
    name: nginx
    labels:
        app: nginx
spec:
    replicas: 3
    selector:
        matchLabels:
            app: nginx
    template:
        metadata:
            labels:
                app: nginx
        spec:
            containers:
            - name: nginx
              image: nginx:1.14.2
              ports:
              - containerPort: 80
```

例えば上記のマニフェストにおいては、下図のようにDeploymentというリソースを用いることでアプリケーションのデプロイ（ロールアウト）やそのリセット（ロールバック）などを自動で行うことが可能です。

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3486778/19837fb2-5e2c-e3b0-db9c-37a86186cc19.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3486778%2F19837fb2-5e2c-e3b0-db9c-37a86186cc19.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=ee960d8d33e6c361ba216d4e546e1ac0)

### 自己回復

Kubernetesの「あるべき状態」を目指す宣言的APIアーキテクチャは、自己回復（Self-healing）機能も備えています。何かしらの理由でポッドがクラッシュしてしまった場合、宣言的APIに従ってKubernetesは自動で再起動を行います。これはポッドに限った話ではなく、Kubernetesの関与するコンテナやネットワークにおいても同様です。

例えば以下のような自己回復機能の例が挙げられます。

- 起動しておくポッド数の維持
- Kubernetesと連携する外部負荷分散システムの再作成や設定値の復元
- ネットワーク上の位置を特定可能なサービスディスカバリ機能を提供するiptablesや　ipvsなどの設定値の復元

これらの自己回復機能によりKubernetesはユーザーの「こんな状態を保ってほしい」という要望に常に自動で応え続けることが可能です。

### 自動化されたスケジューリング機能

Kubernetesは以下のような情報を基にしてどのworkerノードがポッドを実行させるかを自動で決定します。

- ポッド内のコンテナ群が必要とするリソース情報
- 各ノードのCPU
- ノードに設定されたラベルの情報

ポッドを実行させるにあたって最適なノードを自動で選択することで以下のような要件を実現可能です。

- ノードの空きリソースが限られている場合はポッドAよりもポッドBを優先的に実行したい
- 異なるworkerノードにポッドを分散配置することで可用性を高めたい

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3486778/2815dd06-fa0a-b701-b810-8091c0017d09.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3486778%2F2815dd06-fa0a-b701-b810-8091c0017d09.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=f98dad402a494c8fa4655dc79e33e332)

ちなみに、これらの自動スケジューリング機能を「自動ビンパッキング機能」といいます。

### 秘密情報と設定の管理

これはKubernetes特有の機能というよりも、Kubernetesを用いる場合の秘密情報と設定の管理ツールが存在しているという紹介になります。

コンテナアプリケーションでは環境変数や秘密情報をコンテナ内に持ち込まずに、コンテナ外から設定値を渡す構成を構築します。この機能をKubernetesで提供するのは「Secret」と「ConfigMap」です。

Secretは主に秘密情報を扱い、ConfigMapはそれ以外の設定値を扱います。Secretのデータはストレージ上ではなくメモリ上に保存され、ConfigMapはBase64と呼ばれる方式でエンコードされることで安全性が担保されます。

また実際にポッドからそれらの情報を利用する際には、以下の方法を取ることができます。

- ボリュームマウント
- 環境変数
- マニフェスト上で展開される変数

## Kubernetesとクラウド・プロバイダー間の互換性

Kubernetesは様々なクラウド、プロバイダーとの互換性があります。多くのサービスが対応している中でもKubernetesマネージドサービスは特に利用しやすいと言われています。

Kubernetesマネージドサービスとは、Kubernetesのデプロイ、管理、運用をクラウドプロバイダーが行うサービスのことです。このようなサービスを用いることでKubernetes導入時のクラスタ設定、メンテナンスの手間を省くことが可能です。手間を省くことでより多くの時間を要件定義や開発に割くことが出来ます。

具体的には以下のようなKubernetesマネージドサービスが各社から提供されています。

- GKE: Google Kubernetes Engine (Google)
- AKS: Azure Kubernetes Service (Microsoft)
- EKS: Amazon Elastic Kubernetes Servic (Amazon)
- Red Hat OpenShift (Red Hat)
- IDCFクラウド コンテナ (IDCフロンティア)
- OKE: Oracle Cloud Infrastructure Container Engine for Kubernetes (Oracle)

余程の理由がない限りKubernetes導入予定のプロジェクトで利用されているクラスドサービスに紐づくKubernetesマネージドサービスを利用しましょう。

## Kubernetesについてもっと勉強したい方は…

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3486778/6244ecef-90f6-6859-5a41-f1e8ef5c1e20.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3486778%2F6244ecef-90f6-6859-5a41-f1e8ef5c1e20.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=504dcfad3e9e8bd82f954f3b6d7d12f9)

本記事ではKubernetesの構成や可能とする事ついて徹底的に解説をしました。一方で、「実際にどうやって使うの？」という疑問が残ると思います。確かに技術は使ってなんぼです。

本パートではKubernetesを実際に利用したい方やもっと勉強したい方に向けた内容を提供します。実際に使いこなせるようになるまでには、次の2つの勉強方法があります。

- チュートリアルや技術書を読む（初学者向け）
- 最新の情報収集を行う（上級者向け）

これらの内容を紹介します！

### チュートリアル、技術書、資格試験を活用する（初学者向け）

本記事でKubernetesについて初めて聞いた方や、聞いたことはあったけど触ったことは無い方に向けて、本パートでは初学者向けの学習方法を紹介します。

#### チュートリアルを活用する場合

- [Kubernetes公式のチュートリアル](https://kubernetes.io/ja/docs/tutorials/hello-minikube/)
- GCPを利用している場合： [Google Cloud Skills Boost](https://www.cloudskillsboost.google/course_templates/744)
- AWSを利用している場合： [Introduction to Amazon EKS](https://catalog.us-east-1.prod.workshops.aws/workshops/f5abb693-2d87-43b5-a439-77454f28e2e7/ja-JP)
- Azureを利用している場合： [Azure Kubernetes Service (AKS) - チュートリアル](https://learn.microsoft.com/ja-jp/azure/aks/tutorial-kubernetes-prepare-app)

まずは公式のチュートリアルに取り組むことをおすすめしています。公式のチュートリアルを経験することで実際に利用するうえでの最低限の知識を確保できます。もし仮に、クラウド上でKubernetesを活用している案件などに入る予定があればその都度上記で紹介したクラウドごとのチュートリアルを活用しましょう。

チュートリアルのないクラウドサービス上でKubernetesを動かす必要がある場合は、ひとまずGCP、AWS、Azureの中で触ったことのあるサービスから選択してKubernetesを体験すると良いでしょう。

#### 技術書を活用する場合

- [Kubernetes実践入門](https://www.kinokuniya.co.jp/f/dsg-01-9784297104382)  
	Kubernetesの基礎についてじっくり学びたい方向け
- [Kubernetes完全ガイド第2版](https://www.kinokuniya.co.jp/f/dsg-01-9784295009795)  
	細かな機能までしっかりと把握しておきたい方向け
- [Docker/Kubernetes開発・運用のためのセキュリティ実践ガイド](https://www.kinokuniya.co.jp/f/dsg-01-9784839970505)  
	セキュリティ周りについて深く理解したい方向け

上記はおすすめの技術書です。上から順番に難易度は上がっていきます。本当のKubernetes初心者の方であれば「Kunbernetes実践入門」を読破しましょう。その後は、実際に自ら実装を行う必要が出てきた際に「Kubernetes完全ガイド第2版」を読みましょう。そして、セキュリティなどシステム全体の設計などを考えなければならない立場に至った場合に「Docker/Kubernetes開発・運用のためのセキュリティ実践ガイド」を読破すると良いと思います。

業務で必要になるにつれて読み進めれば良いと思います。暇な場合は全体像を掴みやすいと思うので全部一気に読んでみても良いですね。

#### 資格試験を活用する場合

- [Linux Foundationが提供している資格試験](https://training.linuxfoundation.org/certification/certified-kubernetes-administrator-cka/)

自分が本当にKubernetesについて詳しくなったかどうかを知っておきたい方に向けて、上記の資格試験をおすすめしています。Linux Foundationが提供しているKubernetesを活用できることを証明する資格試験（Certified Kubernetes Administrator - CKA）です。

$395（約6万円）の試験料を支払う必要があるため、正直そんなお金があれば欲しいガジェットを買いたいですよね。モチベーションを上昇させたい場合や、会社が資格取得費用を負担してくれる場合は活用しても良いかもしれませんね。

### 情報収集を行う（上級者向け）

ある程度Kubernetesを自由に扱えるようになった方には、最新のアップデート情報をキャッチアップしたり、初学者向けの本からは得られない情報を収集しましょう。以下のような方法で情報収集を行うことが可能です。

- [Kubernetesの公式ドキュメント](https://kubernetes.io/ja/)  
	一番最初に紹介するのは、Kubernetesの公式ドキュメントです。ブロク記事も執筆しているので更新された場合に一読しておくと良いでしょう。Kubernetesの各種機能の利用方法だけでなく、アーキテクチャについても詳しく説明されているのでおすすめできます。
- [Kubernetes Slack](https://kubernetes.slack.com/messages/CNXNB0ZTN)  
	次に紹介するのは、Kubernetes公式が提供しているSlackの無料コミュニティです。英語話者向けのチャンネルもあれば、日本語向けのチャンネルもあるので疑問点を解消させるために質問をしてみましょう。
- [Kubernetes Enhancement Proposals](https://www.kubernetes.dev/resources/keps/)  
	Kubernetesの新機能追加や変更について詳しく知りたい方はKubernetes Enhancement Proposalsを参考にしましょう。機能追加、変更の背景が記述されているので、興味のある機能に該当するページを探して読んでみるといいですね。Kubernetesの機能についての理解を深めることが出来ますよ。
- [Kubernetes Meetup Tokyo](https://k8sjp.connpass.com/)  
	サイトというよりもコミュニティを求めている方にはKubernetes Meetup Tokyoをおすすめします。日本最大のKubnernetesコミュニティとして知られています。Kubernetesに関する最新の情報を日本語で学習する勉強会セッションが開かれているので、コミュニティを通して学習を深めたい場合はおすすめします！

## まとめ

いかがだったでしょうか？本記事ではKubernetesの基本を徹底解説しました。具体的な使い方というよりも、構成や学習の進め方などをメインに理解して頂ければ幸いです。

将来的に大規模なサービスの開発に携わってみたい方は多いと思います。そのためにもKubernetesの学習をさらに進めて頂ければと思います。

弊社Nucoでは、他にも様々なお役立ち記事を公開しています。よかったら、Organizationのページも覗いてみてください。  
また、Nucoでは一緒に働く仲間も募集しています！興味をお持ちいただける方は、 [こちら](https://www.recruit.nuco.co.jp/?qiita_item_id=6e6fea807b2a16732b5b) まで。

## 参考資料

- [Kubernetes公式ドキュメント](https://kubernetes.io/)
- [Docker初学者がやるべきこと3選](https://qiita.com/nuco_fn/items/22cf85a2646d96361d0b)
- [数時間で完全理解！わりとゴツいKubernetesハンズオン](https://qiita.com/Kta-M/items/ce475c0063d3d3f36d5d)
- [Kubernetesの基本用語](https://qiita.com/ryu-yama/items/2e23342fd0f37e43a925)

[0](https://qiita.com/tadashiro_ninomiya/items/#comments)

コメント一覧へ移動

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Ftadashiro_ninomiya%2Fitems%2F6e6fea807b2a16732b5b&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Ftadashiro_ninomiya%2Fitems%2F6e6fea807b2a16732b5b&realm=qiita)

[436](https://qiita.com/tadashiro_ninomiya/items/6e6fea807b2a16732b5b/likers)

いいねしたユーザー一覧へ移動

528