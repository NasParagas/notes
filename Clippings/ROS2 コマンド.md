---
title: "ROS2 コマンド"
source: "https://qiita.com/Dorebom/items/9e906035092f5e343f76"
author:
  - "[[Dorebom]]"
published: 2020-04-30
created: 2025-11-07
description: "ROS2を勉強する中で知ったコマンドのメモです． action ROSグラフのすべてのactionを表示 ros2 action list -tオプションは，actionのタイプも表示する． actionのclient/server情報を表示 ros2 ac..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

ROS2を勉強する中で知ったコマンドのメモです．

## action

#### ROSグラフのすべてのactionを表示

```text
ros2 action list
```

- `-t` オプションは，actionのタイプも表示する．

#### actionのclient/server情報を表示

```text
ros2 action info <action_name>
```

#### actionの実行

```text
ros2 action send_goal <action_name> <action_type> <values>
```

- `<values>` はYAML形式．
- 例) `ros2 action send_goal /turtle1/rotate_absolute turtlesim/action/RotateAbsolute {'theta: 1.57'}`
- `--feedback` オプションをコマンドの最後に追加すると，目標完了まで現在の状態をフィードバックとして受け取り続ける．

## interface

#### topicのインターフェイスの表示

```text
ros2 interface show <topic type>.msg
```

`topic type` は， `ros2 topic list -t` で分かる．

#### serviceのインターフェイスの表示

```text
ros2 interface show <type_name>.srv
```

`service name` および，それらの `type_name` は， `ros2 service list -t` で分かる．

#### actionのインターフェイスの表示

```text
ros2 interface show <action_type>.action
```

`action_name` および，それら `action_type` は， `ros action list -t` で分かる．

## launch

#### launchの方法

```text
ros2 launch <launchファイル>
```

ここで，launchファイルはpython形式で書かれた `〜.launch.py` ファイル．

## node

#### node名の検索

```text
ros2 node list
```

そのノードと対話するsubscriber，publisher，service、およびaction（ROS graph接続）のリストを返します．

#### nodeの詳細情報の検索

```text
ros2 node info <node_name>
```

## pkg

#### packageの作成

```text
ros2 pkg create
```

## param

#### nodeに属するparameterを表示

```text
ros2 param list
```

#### parameterの現在値を取得

```text
ros2 param get <node_name> <parameter_name>
```

#### 実行時にparameterの値を変更

```text
ros2 param set <node_name> <parameter_name> <value>
```

#### nodeの現在のパラメータ値をダンプする

```text
ros2 param dump <node_name>
```

nodeの現在のparameter値をすべてファイルに「ダンプ」して後で保存する．

## rqt

#### ROS2 graphの表示

```text
rqt_graph
```

rqtを開き，\[ プラグイン\] > \[ イントロスペクション\] > \[ ノードグラフ\]を選択して，rqt\_graphを開くこともできます．

## run

#### packageから実行可能ファイルの実行

```text
ros2 run <package_name> <executable_name>
```

#### parameterをファイルからロードする．

```text
ros2 run <package_name> <executable_name> --ros-args --params-file <file_name>
```

フラグ `--ros-args` が追加され， `--params-file` オプションの後にロードするファイルが続く．

## service

#### システムで現在アクティブなすべてのserviceのリストを表示

```text
ros2 service list
```

- service名がリスト表示される．
- `-t` オプションは，すべてのアクティブなサービスのタイプを確認する．

#### serviceのタイプを表示

```text
ros2 service type <service_name>
```

- サービスの要求と応答の2つのデータがどのように構造化されているかを記述するタイプがある．
- `Empty` タイプは，サービスコールが要求を行う際にデータを送信していないし，応答を受信した場合にはデータを受信しないことを意味する．

#### 特定のタイプのすべてのserviceを検索

```text
ros2 service find <type_name>
```

- 例) `ros2 service find std_srvs/srv/Empty`
	- → `/clear, /reset`

#### serviceの呼び出し

```text
ros2 service call <service_name> <service_type> <arguments>
```

- 例1) `ros2 service call /clear std_srvs/srv/Empty`
- 例2) `ros2 service call /spawn turtlesim/srv/Spawn "{x: 2, y: 2, theta: 0.2, name: ''}"`

## source

#### ROS2環境の調達

```text
source /opt/ros/<distro>/setup.bash
```

ROS2コマンドにアクセスするには，新しいシェルを開くたびにこのコマンドを実行する必要がある．

## topic

#### システムで現在アクティブなすべてのtopicのリストを表示

```text
ros2 topic list
```

`ros2 topic list -t` は同じくtopicのリストが返されますが，今回はtopicタイプが括弧の後に追加される．

#### topicが公開しているデータを表示

```text
ros2 topic echo <topic_name>
```

#### topicが送受信されている数を表示

```text
ros2 topic info <topic_name>
```

#### コマンドラインから直接topicにデータをpubする．

```text
ros2 topic pub <topic_name> <msg_type> '<args>'
```

- この引数 `args` は，YAML構文で入力する必要があることに注意してください．
- `--rate 1` オプションは，コマンドを1 Hzの安定したストリームで発行するように指示します．
- `--once` オプションは，「1つのメッセージをパブリッシュしてから終了する」ことを意味する．

#### topicがpublishされる周期を表示

```text
ros2 topic hz <topic name>
```

## 参考サイト

## 更新履歴

2020.4.30 Init.

[0](https://qiita.com/Dorebom/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2FDorebom%2Fitems%2F9e906035092f5e343f76&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2FDorebom%2Fitems%2F9e906035092f5e343f76&realm=qiita)

[

## @Dorebom(dorebom b)

](https://qiita.com/Dorebom)

趣味で自律移動ロボット作ってる．数学好き．

[RSS](https://qiita.com/Dorebom/feed)

この記事は以下の記事からリンクされています

- [勉強メモ](https://qiita.com/Dorebom/items/7a8cd663040d880e745a)

## Qiita Conference 2025 Autumn 11月5日(水)~7日(金)開催！

![](https://cdn.qiita.com/assets/public/official_campaigns/qiita_conference_2025_autumn/image-conference_2025_autumn_ogp-d35d2500cd0420d93f2ed69a8d162d74.png)

Qiita Conferenceは、AI時代のエンジニアに贈るQiita最大規模のテックカンファレンスです！

基調講演ゲスト(敬称略)

piacere、牛尾 剛、Esteban Suarez、和田 卓人、seya、ミノ駆動、市谷 聡啓、からあげ、岩瀬 義昌、まつもとゆきひろ、みのるん、 Null-Sensei

[イベント詳細を見る](https://qiita.com/official-campaigns/conference/2025-autumn)

## Qiita Advent Calendar 開催！

[7](https://qiita.com/Dorebom/items/9e906035092f5e343f76/likers)

いいねしたユーザー一覧へ移動

8