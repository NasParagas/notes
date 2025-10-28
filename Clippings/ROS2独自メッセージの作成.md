---
title: "ROS2独自メッセージの作成"
source: "https://qiita.com/NeK/items/26751b8ed12976ff977d"
author:
  - "[[NeK]]"
published: 2019-04-26
created: 2025-08-21
description: "【ROS2関係トップページへ】 【ROS2レクチャー:初級 -ROS1 style-】 【ROS2レクチャー:初級 -class style-】 【前:ROS2の最小構成publisher:初級 -ROS1 style-】 【前:ROS2の最小構成publisher:初級 ..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から1年以上が経過しています。

[@NeK (くら けん)](https://qiita.com/NeK)

最終更新日 投稿日 2019年04月26日

【 [ROS2関係トップページへ](https://qiita.com/NeK/items/7ac0f4ec10d51dbca084) 】  
【 [ROS2レクチャー:初級 -ROS1 style-](https://qiita.com/NeK/items/74e4bb9d1530e22a63fc) 】  
【 [ROS2レクチャー:初級 -class style-](https://qiita.com/NeK/items/14db31ff784a0bfafe3d) 】

【前:[ROS2の最小構成publisher:初級 -ROS1 style-](https://qiita.com/NeK/items/12faa483de71eb55162f) 】  
【前:[ROS2の最小構成publisher:初級 -class style-](https://qiita.com/NeK/items/3a123fadd8a574b14162) 】  
【次:[独自メッセージを用いたROS2 publisher/subscriber:初級 -ROS1 style-](https://qiita.com/NeK/items/f4f186af2401aa4dc13d) 】  
【次:[独自メッセージを用いたROS2 publisher/subscriber:初級 -class style-](https://qiita.com/NeK/items/cd00705f7e473f83ca44) 】

Eloquent ElusorまでのROS2独自メッセージの作成は [こちら](https://qiita.com/NeK/items/289415955bff745fb77c)

ROSには沢山の種類のメッセージが用意されている．しかし勿論自分独自のメッセージを作りたくなる．C言語でいうstructのように．  
ROS(ROS1)では実行ファイル=nodeでありnodeがメッセージを使用するため，nodeのパッケージの中に独自メッセージが付属的に作成された．  
ROS2ではメッセージも独立性を持つようで，それ自体がパッケージとして成り立ち，他のパッケージがメッセージに関するパッケージを利用する形となる．  
詳しくは当ページの最後の「参考」を参照のこと．

dashingからの変更?\[ROS2インストール・初期設定(for dashing用)\](https://qiita.com/NeK/items/d2bdd8798de7cdea1932)の「上記ガイド以外でインストールしたもの」に追加したが，lark-parserとnumpyをちゃんとインストールしないとエラーが出るようになった(2019/6)

## 作成物

データの基本型は [About ROS 2 Interfaces](https://index.ros.org/doc/ros2/Concepts/About-ROS-Interfaces/) に一覧がある．  
独自メッセージはtopic用とService用がある．ここでは  
両方つくる.

## topic用メッセージ

int64型のデータを二つ．

## service用メッセージ

service用メッセージは送信と受信がある．送信として二つのfloat64型のデータ，送信し処理した結果受信するデータとして二つのfloat64型のデータ．

## 準備

topic用のメッセージのためにmsgディレクトリをservice用のメッセージのためにsrvディレクトリを作成する．

terminal

```bash
$ cd ~/ros2_studies_ws/
$ ros2 pkg create my_messages --build-type ament_cmake
$ cd my_messages
$ mkdir msg
$ mkdir srv
```

## メッセージ作成

## topic用メッセージの作成

msgディレクトリ以下に以下のファイルを作成

- TwoInts.msg

msg/TwoInts.msg

```text
int64 a
int64 b
```

## service用メッセージの作成

srvディレクトリ以下に以下のファイルを作成

- CalcTwoFloats.srv

srv/CalcTwoFloats.srv

```text
float64 a
float64 b
---
float64 sum
float64 diff
```

## メッセージに関する注意点

メッセージを表すファイル名(\*\*\*.msgや\*\*\*.srv)にはいくつかの制限がある．

### メッセージファイル名に関する制限

- 最初の文字が大文字でなければならない
- \_(アンダースコア)を使ってはいけない
- 単語の区切り(意味の区切り)は大文字で行う

OKな例

- Test.msg
- TEST.msg
- TestMessage.msg

NGな例

- test.msg
- test\_message.msg

### 制限の理由とメッセージの利用方法

メッセージファイルはcolcon buildによって他のパッケージで利用できるようにビルドされる．この時，\*\*\*.msgはコンパイルされてクラスになる．この時，二個目の大文字の前に\_(アンダースコア)が挿入され全て小文字のヘッダファイルが作成されて，これをインクルードすることで利用できるようにしている．

例

- msgファイル
	- TestMessage.msg
- コンパイル後に作成されるメッセージ利用のためのクラスヘッダファイル
	- test\_message.hpp

この変換をスムーズに行うために上記のような制限が行われる．

このように他のパッケージで利用するためには，メッセージファイル名から上記のように変換したヘッダファイルを読み込むことを覚えておくこと．

## package.xmlとCMakeLists.txt

idl(interface description language)という名前を持つライブラリを使用している．interface，つまり他のnodeから使ってもらえるようにするためのもの．

### package.xml

package.xml

```xml
<package format="3">
  <build_depend>rosidl_default_generators</build_depend>
  <exec_depend>rosidl_default_runtime</exec_depend>
  <member_of_group>rosidl_interface_packages</member_of_group>
```

1行目:タグを利用するためには"3"にしなければならない．  
2～4行目:メッセージをビルド・実行するために必要．

注意点

- いつの間にか(Foxy Fitzroyから？)rodidl\_default\_generatorsがbuildtool\_dependではなくbuild\_dependになっている．
	- buildtool\_dependでも大丈夫
	- [公式の説明](http://wiki.ros.org/catkin/package.xml#Build.2C_Run.2C_and_Test_Dependencies) を見るとそもそもbuild\_dependが正しそう？

### CMakeLists.txt

CMakeLists.txt

```text
find_package(rosidl_default_generators REQUIRED)

set(msg_files
  "msg/TwoInts.msg"
)
set(srv_files
  "srv/CalcTwoFloats.srv"
)
rosidl_generate_interfaces(${PROJECT_NAME}
  ${msg_files}
  ${srv_files}
)
ament_export_dependencies(rosidl_default_runtime)
```

ament\_export\_dependencies(rosidl\_default\_runtime)は自動判別してくれるようになったのか書かなくても良くなったとは思う  
ただ，foxy, galacticのtutorialには書くようにとある．  
共有ライブラリとして使用するときに必要となるかな？([ROS2における共有ライブラリ](https://qiita.com/NeK/items/48c55bd473091d41cc75) 参考)

## ビルト・動作確認

terminal

```bash
$ cd ~/ros2_studies_ws/
$ colcon build --symlink-install --packages-up-to my_messages
$ . install/local_setup.bash
$ ros2 interface list # 全メッセージのリストが表示される．作成したメッセージが存在するか確認
$ ros2 interface show my_messages/msg/TwoInts # [パッケージ名]/[メッセージ]で中身が見えるので確認
$ ros2 interface show my_messages/srv/CalcTwoFloats # [パッケージ名]/[メッセージ]で中身が見えるので確認
```

## 既存メッセージの拡張

ある独自メッセージを作成する場合に，前に作成した独自メッセージをメンバとして用いることが可能．  
詳しくは [公式チュートリアルのExpanding on ROS2 interfaces](https://docs.ros.org/en/galactic/Tutorials/Single-Package-Define-And-Use-Interface.html) の5(Extra Use an existing interface definition参照．

## 同プロジェクトのメッセージを，あるメッセージの中で使用する場合

普通にメッセージの中に， `型 変数名` の形で書けばよい．  
以下は， `TwoInts.msg` を `DoubleTwoInts.msg` の中で使う場合．

msg/DoubleTwoInts.msg

```text
TwoInts a
TwoInts b
```

CMakeLists.txtの中の `msg_files` の中にもちゃんと追加．

CMakeLists.txt

```text
find_package(rosidl_default_generators REQUIRED)

set(msg_files
  "msg/TwoInts.msg"
  "msg/DoubleTwoInts.msg"
)
set(srv_files
  "srv/CalcTwoFloats.srv"
)
rosidl_generate_interfaces(${PROJECT_NAME}
  ${msg_files}
  ${srv_files}
)
ament_export_dependencies(rosidl_default_runtime)
```

## 外部のメッセージを，あるメッセージの中で使用する場合

同プロジェクトのメッセージを使用する場合と比べてCMakeLists.txtの書き方が変わる．  
以下は `Third.msg` の中で `std_msg/Header` を使用する場合の例．

CMakeLists.txtの中の

CMakeLists.txt

```text
find_package(std_msg REQUIRED)
find_package(rosidl_default_generators REQUIRED)

set(msg_files
  "msg/TwoInts.msg"
  "msg/Third.msg"
)
set(srv_files
  "srv/CalcTwoFloats.srv"
)
rosidl_generate_interfaces(${PROJECT_NAME}
  ${msg_files}
  ${srv_files}
  DEPENDENCIES std_msgs
)
ament_export_dependencies(rosidl_default_runtime)
```

find\_packageとrosidl\_generate\_interfacesの中にDEPENDENCIESが加わっていることがわかる．

## 参考

メッセージについて詳しくは以下のページに詳しい

- [Creating custom ROS 2 msg and srv files](https://index.ros.org/doc/ros2/Tutorials/Custom-ROS2-Interfaces/)
- [ROS2の紹介 by gbiggs](https://gbiggs.github.io/rosjp_ros2_intro/ros2_basics.html#%E3%83%A1%E3%83%83%E3%82%BB%E3%83%BC%E3%82%B8%E5%8F%97%E4%BF%A1%E3%83%8E%E3%83%BC%E3%83%89%E3%81%AE%E4%BD%9C%E6%88%90)

公式の使用例は以下のとおり．

- [ros2/tutorials](https://github.com/ros2/tutorials/tree/rosidl_tutorials/rosidl_tutorials)
	- [ROS互換のスタンダードな例?](https://github.com/ros2/tutorials/tree/rosidl_tutorials/rosidl_tutorials/rosidl_tutorials)．
	- [ROS独自の拡張含めたもの](https://github.com/ros2/tutorials/tree/rosidl_tutorials/rosidl_tutorials/rosidl_tutorials_msgs) ．こちら推奨．

【前:[ROS2の最小構成publisher:初級 -ROS1 style-](https://qiita.com/NeK/items/12faa483de71eb55162f) 】  
【前:[ROS2の最小構成publisher:初級 -class style-](https://qiita.com/NeK/items/3a123fadd8a574b14162) 】  
【次:[独自メッセージを用いたROS2 publisher/subscriber:初級 -ROS1 style-](https://qiita.com/NeK/items/f4f186af2401aa4dc13d) 】  
【次:[独自メッセージを用いたROS2 publisher/subscriber:初級 -class style-](https://qiita.com/NeK/items/cd00705f7e473f83ca44) 】

[0](https://qiita.com/NeK/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2FNeK%2Fitems%2F26751b8ed12976ff977d&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2FNeK%2Fitems%2F26751b8ed12976ff977d&realm=qiita)

[21](https://qiita.com/NeK/items/26751b8ed12976ff977d/likers)

いいねしたユーザー一覧へ移動

12