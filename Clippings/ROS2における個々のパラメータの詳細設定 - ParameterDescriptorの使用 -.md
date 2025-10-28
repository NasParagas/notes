---
title: "ROS2における個々のパラメータの詳細設定 - ParameterDescriptorの使用 -"
source: "https://qiita.com/NeK/items/74f5d45b01a277f40d1c"
author:
  - "[[NeK]]"
published: 2021-08-03
created: 2025-08-26
description: "【ROS2導入&レクチャー】 概要 個々のパラメータをdeclare_parameterで宣言する時にそのパラメータの詳細を設定できる．それにより，例えばread only(変更不可)にしたりできる． パラメータタイプの動的変更について パラメータのタイプ(int型とか..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から3年以上が経過しています。

[@NeK (くら けん)](https://qiita.com/NeK)

最終更新日 投稿日 2021年08月03日

【 [ROS2導入&レクチャー](https://qiita.com/NeK/items/7ac0f4ec10d51dbca084) 】

## 概要

個々のパラメータをdeclare\_parameterで宣言する時にそのパラメータの詳細を設定できる．それにより，例えばread only(変更不可)にしたりできる．

## パラメータタイプの動的変更について

パラメータのタイプ(int型とか)は，foxy以前のデフォルトが「変更可」であった．これがgalactic以降のデフォルトは「変更不可」となった．

- [foxy以前のParameterDescriptor.msg](https://github.com/ros2/rcl_interfaces/blob/master/rcl_interfaces/msg/ParameterDescriptor.msg)
- [galacticで言及されているParameterDescriptor.msg](https://github.com/ros2/rcl_interfaces/blob/09b5ed93a733adb9deddc47f9a4a8c6e9f584667/rcl_interfaces/msg/ParameterDescriptor.msg)

25行目を見比べると， `bool dynamic_typing false` が追加されていることがわかる．

## ParameterDescriptorによるパラメータの詳細設定

設定できる項目については [ParameterDescriptor.msg](https://github.com/ros2/rcl_interfaces/blob/master/rcl_interfaces/msg/ParameterDescriptor.msg) を参照のこと．

## 設定手順例

例として， [ParameterDescriptor.msg](https://github.com/ros2/rcl_interfaces/blob/master/rcl_interfaces/msg/ParameterDescriptor.msg) にある項目 `read_only` を試す．デフォルトでは `false` になっており，変更可になっている．これを変えて変更不可として宣言する方法を述べる．  
なお参考元は [ros2 githubのここ](https://github.com/ros2/rclcpp/blob/master/rclcpp/doc/notes_on_statically_typed_parameters.md) ．

cpp

```c++
...(コンストラクタの中とかのパラメータの宣言部分)
  rcl_interfaces::msg::ParameterDescriptor descriptor;
  descriptor.read_only = true;

  this->declare_parameter("test_param", 0, descriptor);

...
```

## 項目の説明

[ParameterDescriptor.msg](https://github.com/ros2/rcl_interfaces/blob/master/rcl_interfaces/msg/ParameterDescriptor.msg) の各項目についての説明．

### string name

パラメータの名前．現在のところ何に使うのか不明．単なる識別するための文字？

### uint8 type

typeの指定．詳しくは [rcl\_interfaces::msg::ParameterType.msg](https://github.com/ros2/rcl_interfaces/blob/master/rcl_interfaces/msg/ParameterType.msg) を見ろとのこと．

- uint8 PARAMETER\_NOT\_SET=0
- uint8 PARAMETER\_BOOL=1
- uint8 PARAMETER\_INTEGER=2
- uint8 PARAMETER\_DOUBLE=3
- uint8 PARAMETER\_STRING=4
- uint8 PARAMETER\_BYTE\_ARRAY=5
- uint8 PARAMETER\_BOOL\_ARRAY=6
- uint8 PARAMETER\_INTEGER\_ARRAY=7
- uint8 PARAMETER\_DOUBLE\_ARRAY=8
- uint8 PARAMETER\_STRING\_ARRAY=9

### string description

覚書かな？たんなるメモ？

### string additional\_constraints

ParameterDesctiptorで設定できない制約条件を書いておくこと．例えば"only prime numbers"(素数のみ！)とか．

### bool read\_only

デフォルト値は `false` ．

- read\_only=true
	- 書き込み不可
	- undeclare\_parameterもエラーになる
- read\_only=false
	- 書き込みOK

### bool dynamic\_typing

galacticより追加された項目．  
galactic以降でのデフォルト値は `false` ．  
foxy以前でのデフォルト値は `true` ．

- dynamic\_typing=true
	- 実行途中でパラメータの型を変更可
- dynamic\_typing=false
	- 実行途中でパラメータの型を変更不可

### FloatingPointRange\[<=1\] floating\_point\_range

もし `uint8 type` で `uint8 PARAMETER_DOUBLE=3` または `uint8 PARAMETER_DOUBLE_ARRAY=8` を選んだ場合，使用する変数の範囲について制限を加えられる．  
デフォルト値は `空` ． `空` の時，特に制限は加えられず，何か値を設定したときにこの制限は有効になる．  
また\[IntegerRange\[<=1\] integer\_range\](#IntegerRange\[<=1\] integer\_range)と排他的(どっちかしか使えない)．  
詳しくは [rcl\_interfaces::msg::FloatingPointRange.msg](https://github.com/ros2/rcl_interfaces/blob/master/rcl_interfaces/msg/FloatingPointRange.msg) を参照のこと．

基本的に設定できる内容は以下の三つ．

- from\_value
- to\_value
- step
	- step=0.0の時，from\_valueからto\_valueの間の連続値が有効な値となる．
	- 逆に言うと，stepが0.0以外の値であれば飛び飛びの値になる．
	- 正負は区別されず絶対値が使用されると考えればよい．

例

- from\_value=1.0, to\_value=2.0, step=0.5
	- 有効な値: {1.0, 1.5, 2.0}
- from\_value=1.0, to\_value=2.0, step=0.8
	- 有効な値: {1.0, 1.8, 2.0}
- from\_value=1.0, to\_value=2.0, step=10.0
	- 有効な値: {1.0, 2.0}

1番目の例を設定する場合([参考](https://github.com/ros2/rclcpp/blob/master/rclcpp/test/rclcpp/test_node.cpp#L1078))．

```c++
...
  rcl_interfaces::msg::ParameterDescriptor d;
  d.type=3; // PARAMETER_DOUBLE=3
  d.floating_point_range.resize(1);
  auto& range = d.floating_point_range.at(0);
  range.from_value=1.0;
  range.to_value=2.0;
  range.step=0.5;
...
```

ちなみにrcl\_interfaces::msg::ParameterDescriptorに関する#includeはいらない(rclcpp/rclcpp.hppに入っている？)  
また判定のタイミングはadd\_on\_set\_parameters\_callback関数で指定されたパラメータ変更用のコールバック関数の「処理後」となる．考え方としては，

1. パラメータ変更の指示が届く(例えば `ros2 param set <package名> <パラメータ名> <値>`)
2. 値に従って前処理がおこなわれる．
3. 前処理が大丈夫なら，いよいよパラメータ変更．この時にfrom\_value, to\_value, stepで判定が行われる．

### IntegerRange\[<=1\] integer\_range

\[FloatingPointRange\[<=1\] floating\_point\_range\](#FloatingPointRange\[<=1\] floating\_point\_range)と全く同じ．対象としているのが実数値か整数値かの違い．stepに関しても同じ．  
詳しくは [rcl\_interfaces::msg::IntegerRange.msg](https://github.com/ros2/rcl_interfaces/blob/master/rcl_interfaces/msg/IntegerRange.msg) を参照のこと．

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

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2FNeK%2Fitems%2F74f5d45b01a277f40d1c&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2FNeK%2Fitems%2F74f5d45b01a277f40d1c&realm=qiita)

[1](https://qiita.com/NeK/items/74f5d45b01a277f40d1c/likers)

いいねしたユーザー一覧へ移動

0