---
title: "Rviz2の使用方法｜RaspberryPi OSではじめるROS2"
source: "https://zenn.dev/array/books/5efdb438cf8be3/viewer/bbf361"
author:
  - "[[Zenn]]"
published:
created: 2025-08-21
description:
tags:
  - "clippings"
---
このチャプターの目次

1. [起動方法](https://zenn.dev/array/books/5efdb438cf8be3/viewer/#%E8%B5%B7%E5%8B%95%E6%96%B9%E6%B3%95)
	1. [既存の設定の読み込み](https://zenn.dev/array/books/5efdb438cf8be3/viewer/#%E6%97%A2%E5%AD%98%E3%81%AE%E8%A8%AD%E5%AE%9A%E3%81%AE%E8%AA%AD%E3%81%BF%E8%BE%BC%E3%81%BF)
	2. [launch.pyファイルを用いた起動](https://zenn.dev/array/books/5efdb438cf8be3/viewer/#launch.py%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E3%82%92%E7%94%A8%E3%81%84%E3%81%9F%E8%B5%B7%E5%8B%95)
2. [画面の説明](https://zenn.dev/array/books/5efdb438cf8be3/viewer/#%E7%94%BB%E9%9D%A2%E3%81%AE%E8%AA%AC%E6%98%8E)
	1. [設定の出力・読み込み](https://zenn.dev/array/books/5efdb438cf8be3/viewer/#%E8%A8%AD%E5%AE%9A%E3%81%AE%E5%87%BA%E5%8A%9B%E3%83%BB%E8%AA%AD%E3%81%BF%E8%BE%BC%E3%81%BF)
3. [Rviz2ウィンドウの操作](https://zenn.dev/array/books/5efdb438cf8be3/viewer/#rviz2%E3%82%A6%E3%82%A3%E3%83%B3%E3%83%89%E3%82%A6%E3%81%AE%E6%93%8D%E4%BD%9C)
4. [終了する](https://zenn.dev/array/books/5efdb438cf8be3/viewer/#%E7%B5%82%E4%BA%86%E3%81%99%E3%82%8B)

Rvizとは、ROS（Robot Operating System）に付属する3Dビジュアライザーです。  
Rvizを使うことで、ロボットやセンサーなどのデータを3D空間上で表示することができます。

ロボットのシミュレーションや、実際にロボットを動かした際のデータを可視化することができるので、ロボット開発に役立ちます。

![](https://storage.googleapis.com/zenn-user-upload/a48d248119da-20221210.png)

## 起動方法

```bash
source /opt/ros/humble/setup.bash
rviz2
```

ros2 run コマンドでも起動可能です。

```bash
source /opt/ros/humble/setup.bash
ros2 run rviz2 rviz2
```

### 既存の設定の読み込み

既存の `.rviz` ファイルを使用する場合は `-d <.rvizのパス>` の引数を与えます。

```bash
rviz2 -d <.rvizのパス>
# rviz2 -d ./abc.rviz
```

### launch.pyファイルを用いた起動

他のノードと一緒に立ち上げることもできます。

```python
from launch import LaunchDescription
from launch_ros.actions import Node

def generate_launch_description():
    node = Node(
        package='rviz2',
        executable='rviz2',
        name='rviz2',
        arguments=[
            '-d', './abc.rviz'])

    return LaunchDescription([node])
```

## 画面の説明

Rviz2の画面でよく使うパーツを説明します。

![](https://storage.googleapis.com/zenn-user-upload/78e4be1dd21f-20221210.png)

1. Fixed Frame: Rviz2の画面（黒い画面）の基準フレームを指定します。設定とデータのframe\_idが一致しない場合、描画されません。
2. Background Color: 背景の色を変更します。デフォルトは（48:48:48）です。
3. Frame Rate: フレームレートの上限です。デフォルトは30です。
4. チェックボックス: チェックボックスがついている場合のみ描画します。
5. Addボタン: 新しいビジュアライズツールを追加します。
6. Removeボタン: 既存のビジュアライズツールを削除します。
7. 2D Pose Estimate: 現在位置をパブリッシュします。
8. 2D Goal Pose: 目標位置(Goal)をパブリッシュします。
9. Viewsフレーム: 現在のRviz2の画面の状態を表示します。こちらを編集しても視点を変更します。
10. フレームレート: 現在のフレームレートを示します。③よりも下回っている場合、コンピュータのリソースが不足している可能性が高いです。

### 設定の出力・読み込み

![](https://storage.googleapis.com/zenn-user-upload/68c613a556e1-20221210.png)

- Open Config: 対象の.rviz2ファイル（xmlファイル）を開きます。
- Save Config: 現在のRviz2情報を開いているファイルに対して保存します。
- Save Config As: 現在のRviz2情報を別名で保存します。
- Recent Config: 過去に開いた.rvizファイルを開きます。
- Save Image: Rviz2の画面を保存します。スクリーンショットに近いです。

## Rviz2ウィンドウの操作

左ドラッグ: ウィンドウの角度を調整できます。

![](https://storage.googleapis.com/zenn-user-upload/005bfad7cf5f-20221210.gif)

マウスホイールのドラッグ: ウィンドウの平行移動を行います。

![](https://storage.googleapis.com/zenn-user-upload/8187a8b83fd7-20221210.gif)

右ドラッグ (マウスホイールの回転): ウィンドウの拡大・縮小を行います。

![](https://storage.googleapis.com/zenn-user-upload/69e9bfb0a639-20221210.gif)

Odomフレームの追加: T265を使用した場合です。

1. Addボタンを押して、 `By topic` から対象のトピックを選びます。 `/camera/pose/sample/Odometry` を選択します。
2. `Fixed Frame` を選択して適切なフレームを選択します。ここでは、 `odom_frame` を選択します。
3. 適切なトピックの通信方法を選択します。 `Odometry` の `Topic/Reliability Policy` を `Reliable` から `Best Effort` に変更しました。

![](https://storage.googleapis.com/zenn-user-upload/8b3c5851047a-20221210.gif)

トピックの除去: 対象のトピックを選択してRemoveボタンを押します。

![](https://storage.googleapis.com/zenn-user-upload/a308a3b3f18c-20221210.gif)

画像の追加: Addボタンを押して、 `By topic` から対象のトピックを選びます。 `/camera/color/image_rect_raw/Image` を選択します。  
複数のフレームを追加することもできます。また、ドラッグすることも可能です。

![](https://storage.googleapis.com/zenn-user-upload/437a5b191b6e-20221210.gif)

## 終了する

Rviz2ウィンドウを終了したい場合は、Rvizウィンドウの右上のバツボタンを押すか、Ctrl+Cを起動したターミナル上で押します。

launchで起動した場合は、launchが終了した時点で終了します。