---
title: "ROS2 Jazzy 入門 (5) - Odometry｜npaka"
source: "https://note.com/npaka/n/n658ce56fa687"
author:
  - "[[npaka]]"
published: 2024-12-05
created: 2025-08-26
description: "「SDF」で作ったシンプルな差動二輪ロボットに「Odometry」を追加して「rviz2」で視覚化します。   ・Ubuntu 24.04 ・ROS2 Jazzy ・Gazebo Harmonic    前回  1. Odometry  「/odom トピック」は、ロボットの自己位置推定に関する位置・姿勢・速度に関する情報を提供します。これは、ロボットがどこにいるのか、どのように動いているのかを推定するために使用します。   【メモ】「/odom トピック」は、「/tfトピック」の「/odom フレーム」は関連はありますが別のデータになります。   2. Gazebo の起動  (1"
tags:
  - "clippings"
---
![見出し画像](https://assets.st-note.com/production/uploads/images/164487273/rectangle_large_type_2_369110a98e10b1f74a54e972b09a7d89.png?width=1200)

## ROS2 Jazzy 入門 (5) - Odometry

[npaka](https://note.com/npaka)

「SDF」で作ったシンプルな差動二輪ロボットに「Odometry」を追加して「rviz2」で視覚化します。

> **・Ubuntu 24.04  
> ・ROS2 Jazzy  
> ・Gazebo Harmonic**

## 前回

## 1\. Odometry

「 **/odom トピック** 」は、ロボットの自己位置推定に関する位置・姿勢・速度に関する情報を提供します。これは、ロボットがどこにいるのか、どのように動いているのかを推定するために使用します。

> **【メモ】** 「/odom トピック」は、「/tfトピック」の「/odom フレーム」は関連はありますが別のデータになります。

## 2\. Gazebo の起動

(1) ターミナルを開いて「 **Gazebo** 」を起動。  
「 [ROS2 Jazzy 入門 (2) - ロボット操作](https://note.com/npaka/n/n1c5820bfe0bf) 」の「 **SDF** 」を使います。

```ruby
$ gz sim simple_robot.sdf
```

(2) 別のターミナルを開いて「 **/cmd\_vel** 」と「 **/odom** 」をROS2にブリッジ。

(3) 別のターミナルを開いて「 **teleop\_twist\_keyboard** 」を起動。

```ruby
$ ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

(4) 「 **Gazebo** 」左下の▶をクリック。  
物理シミュレーションが開始します。

## 3\. Odometry の動作確認

(1) 「 **/cmd\_vel** 」と「 **/odom** 」がトピックに追加されていることを確認。

```php
ros2 topic list
```

```ruby
/cmd_vel
/odom
/parameter_events
/rosout
```

(2) 「 **/odom** 」のデータ型を確認。

```ruby
$ ros2 topic info /odom
```

(3)「 **/odom** 」のリアルタイムデータを確認。

```ruby
$ ros2 topic echo /odom
```

## 4\. rviz2 での視覚化

(1) rviz2 の起動。

```ruby
$ rviz2
```

(2) 左側の設定項目を以下のように設定。

> **・Fixed Frame**: simple\_robot/odom  
> 「ros2 topic echo /odom」の「frame\_id」を指定します。

![画像](https://assets.st-note.com/img/1733530901-EF9ibfgzuyYMeXsOS57mkV2l.png?width=1200)

(2) 左下の「Add」をクリックし、「By topic → /odom → Odometry」を選択し、「OK」をクリック。

![画像](https://assets.st-note.com/img/1733356000-jQTYJzqbNgAm6De31psUaSc8.png?width=1200)

(3) 矢印が表示されるのを確認。

![画像](https://assets.st-note.com/img/1733530916-su6pH54TbVI83Gwa1ldjf9ch.png?width=1200)

(4) 「 **teleop\_twist\_keyboard** 」でロボットを移動。  
矢印の位置が変わることを確認します。  
「Keep」(デフォルト100)の数だけ矢印履歴が表示されます。

![画像](https://assets.st-note.com/img/1733530926-0aRwvAK4GPbVtoUD5xYqTh9O.png?width=1200)

## 5\. launchファイル

(1) launchファイルの作成。

**・run\_simple\_robot\_odom.py**

(2) launchファイルの実行。

```ruby
ros2 launch ~/run_simple_robot_odom.py
```

## 【おまけ】 /tf と /odom の比較

> **・/tf  
> ****・データ型**: tf2\_msgs/TFMessage  
> **・目的**: フレーム間の位置・姿勢 (Transform) の配列を提供  
> **・主な用途**: 座標変換、ロボットのフレームツリーの構築  
>   
> **・/odom  
> ****・データ型**: nav\_msgs/Odometry  
> **・目的**: ロボットの位置 (Pose) と速度 (Twist) を提供  
> **・主な用途**: ロボットの移動の追跡、SLAMやナビゲーションでの利用

## 次回

  

[**ROS2とPythonで作って学ぶAIロボット入門 改訂第2版 (KS理工学専門書)** *www.amazon.co.jp*](https://www.amazon.co.jp/dp/4065386160?tag=npaka-22&linkCode=ogi&th=1&psc=1)

[*3,850 円* (2025年03月18日 07:40時点 詳しくはこちら)](https://www.amazon.co.jp/dp/4065386160?tag=npaka-22&linkCode=ogi&th=1&psc=1)

[

Amazon.co.jpで購入する

](https://www.amazon.co.jp/dp/4065386160?tag=npaka-22&linkCode=ogi&th=1&psc=1)

  

## いいなと思ったら応援しよう！

ROS2 Jazzy 入門 (5) - Odometry｜npaka