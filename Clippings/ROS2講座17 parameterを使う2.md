---
title: "ROS2講座17 parameterを使う2"
source: "https://qiita.com/srs/items/9ab7456e4a3a4c5cf645"
author:
  - "[[srs]]"
published: 2024-02-12
created: 2025-08-26
description: "環境 この記事は以下の環境で動いています。 項目 値 CPU Core i5-8250U Ubuntu 22.04 ROS2 Humble 概要 前回はROS2のパラメーターを操作するROSノードの記載について解説しました。今回はROS2の起..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から1年以上が経過しています。

## 環境

この記事は以下の環境で動いています。

| 項目 | 値 |
| --- | --- |
| CPU | Core i5-8250U |
| Ubuntu | 22.04 |
| ROS2 | Humble |

## 概要

前回はROS2のパラメーターを操作するROSノードの記載について解説しました。今回はROS2の起動時でのROSパラメーターの動きを解説します。

## runでのパラメーターの指定

'--ros-args -p (パラメーター名):=(パラメーター値)'を後ろにつけて実行します。

パラメーターを指定して実行

```shell
source ~/ros2_ws/install/setup.bash
ros2 run rclcpp_param_lecture param_server --ros-args -p int_param:=3
```

## ros\_launchでのパラメーターの指定

大きく分けてlaunchファイルで直接指定する方法とyamlファイルで指定する方法があります。指定するパラメーターの数が少ない時は前者、多い時は後者がおすすめです。なおこれらの記述を混ぜることも可能です。

## launchファイルで直接指定

- `Node` の引数の `parameter` のリストの中に辞書型で指定します。

## yamlファイルで指定

- `Node` の引数の `parameter` のリストの中で、yamlファイルのファイルパスを指定します。

rclcpp\_param\_lecture/config/param\_server.yaml

```yaml
/**:
  ros__parameters:
    int_param: 5
```

- ROS2では1プロセス中で複数のROSノードを実装することが可能です。この場合は(ノード名)/ros\_\_parameters以下がそのROSノード毎ののパラメーター指定となります。ノード名はnamespaceも含むことに注意です。
- `/**` はワイルドカードになります。ROSノードが1つの場合はこれで良いでしょう

## LaunchArgument経由で指定する

パラメーターとは別物ですが、ROS launchではlaunch argumentという引数を指定できます。この引数の値をパラメーターに渡すことで、ros launch時のコマンドでパラメーターを間接的に指定できます。

rclcpp\_param\_lecture/launch/param\_by\_arg.launch.py

```python
from launch import LaunchDescription
from launch.substitutions import LaunchConfiguration
from launch.actions import DeclareLaunchArgument
from launch_ros.actions import Node

def generate_launch_description():
    return LaunchDescription([
        DeclareLaunchArgument(name='param', default_value='0', description='int parameter'),
        Node(
            package='rclcpp_param_lecture',
            executable='param_server',
            parameters=[{'int_param': LaunchConfiguration('param')}],
            output='screen',
            emulate_tty=True,
        ),
    ])
```

ros launch実行時に `(arg名:=arg値)` という形で指定します。

実行

```shell
source ~/ros2_ws/install/setup.bash
ros2 launch rclcpp_param_lecture param_by_arg.launch.py param:=2
```

## 目次ページへのリンク

[ROS2講座の目次へのリンク](https://qiita.com/srs/items/5f0d765343ca78f524ed)

[0](https://qiita.com/srs/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fsrs%2Fitems%2F9ab7456e4a3a4c5cf645&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fsrs%2Fitems%2F9ab7456e4a3a4c5cf645&realm=qiita)

[1](https://qiita.com/srs/items/9ab7456e4a3a4c5cf645/likers)

いいねしたユーザー一覧へ移動

1