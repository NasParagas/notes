---
title: "Nav2 講座 4: 自分で作成したマップを使って TurtleBot シミュレーションをする #ROS 2 #nav2"
source: "https://www.remma.net/?p=7492"
author:
  - "[[remma_tech_12]]"
published: 2025-03-02
created: 2025-11-07
description: "ROS 2 に関して筆者は以下の本で学びました。ROS1 との比較を含めかなりわかりやすく ROS 2 について書かれており、おすすめの一冊です。前回の記事で、nav2_bringup を模倣した custom_nav2_bringup と"
tags:
  - "clippings"
---
ROS 2 に関して筆者は以下の本で学びました。ROS1 との比較を含めかなりわかりやすく ROS 2 について書かれており、おすすめの一冊です。

[

![](https://s0.wordpress.com/mshots/v1/https%3A%2F%2Famzn.to%2F4luacyG?w=160&h=90)

](https://amzn.to/4luacyG "Amazon.co.jp")

[前回の記事](https://www.remma.net/?p=7458) で、 `nav2_bringup` を模倣した `custom_nav2_bringup` という ROS 2 のパッケージを作成し、nav2 の設定をカスタマイズする準備を整えました。今回はマップファイルを自ら作成し、そのマップ上で TurtleBot シミュレーションを動かしてみます。

## 過去記事

- [Nav2 講座 1: docker 上に ROS 2 環境を構築し TurtleBot simulation を動かす](https://www.remma.net/?p=7216)
- [Nav2 講座 2: Rust で ROS 2 ノードを作る: footprint の動的変更](https://www.remma.net/?p=7263)
- [Nav2 講座 3: nav2\_bringup の設定をカスタマイズする準備](https://www.remma.net/?p=7458)

## PC 環境

- Thinkpad X1 Intel Core i7
- Ubuntu 24.04

## 事前準備

以下のコマンドを事前に実行しておいてください。「」節と「」節を飛ばして「」節から実施したい場合は、article\_7566\_prep ブランチの代わりに article\_7566\_ready ブランチに checkout してください。

```
$ git clone git@github.com:remmaTech12/ros2_nav2.git
$ cd ros2_nav2
$ git checkout article_7566_prep
$ docker compose up -d --build
$ xhost +
$ docker exec -it ros2_nav2 bash
/workspace$ ros2 launch turtlebot3_gazebo turtlebot3_world.launch.py  ## Takes about 5 min.
```

最後のコマンドの実行には 5 分程度かかります。最後のコマンドの実行により、以下が表示されたら準備完了です。

![](https://www.remma.net/wp-content/uploads/2025/03/Screenshot-from-2025-03-02-11-05-06-1024x645.png)

## 地図作成

### .pgm

以下の地図 maze.png をダウンロードして custom\_nav2\_bringup/maps ディレクトリに保存してください。GIMP 等でてきとうに.pgm ファイルを作成してもいいです。以下の画像のサイズは 150 x 150 px です。

[Download link](https://drive.google.com/file/d/1zn9VnoUrOVe-uk7THjm75Stda-5himoV/view?usp=drive_link)

![](https://www.remma.net/wp-content/uploads/2025/03/maze.png)

maze.png

### .yaml

さらに、同ディレクトリに maze.yaml ファイルを作成し、以下を記載して保存します。

```yaml
image: maze.pgm
resolution: 0.050000
origin: [-3.75, -3.75, 0.0]
negate: 0
occupied_thresh: 0.65
free_thresh: 0.196
```

pgm ファイルが 150 x 150 px で解像度が 0.05 \[m/px\] なので、150 x 0.05 = 3.75 \[m\] だけマップ画像を x, y 方向ともにずらせばマップの原点を RViz の座標系の原点と一致させることができます。

### .world

以下の “.pgm ファイルを.world ファイルに変換するスクリプト” convert\_pgm\_to\_world.py を custom\_nav2\_bringup/maps ディレクトリに配置し、実行します。

```python
import numpy as np
from PIL import Image

input_file  = "maze.pgm"
output_file = "maze.world"
image = Image.open(input_file).convert("L")
image = np.array(image, dtype=np.uint8)

resolution = 0.05  # [m/px]: x [m/px] means x meter for 1 px.
width, height = image.shape

# Header part of .world file
world_template_list = ["""<?xml version="1.0" ?>
<sdf version="1.6">
  <world name="maze">
    <include>
      <uri>model://ground_plane</uri>
    </include>
    <include>
      <uri>model://sun</uri>
    </include>

    <gui fullscreen='0'>
      <camera name='user_camera'>
        <pose frame=''>0 0 10 0 1.570796 0</pose>
        <view_controller>orbit</view_controller>
        <projection_type>perspective</projection_type>
      </camera>
    </gui>
"""]

wall_height = 1.0

# Convert a consecutive lateral line to a wall
for x in range(width):
    y = 0
    while y < height:
        black_threshold = 128
        if image[x, y] < black_threshold:  # Judge black parts
            start_y = y
            # Search for consecutive black parts
            while y < height and image[x, y] < black_threshold:
                y += 1
            end_y = y

            wall_x = ((start_y + end_y - 1) / 2 - height / 2) * resolution
            wall_y = (width / 2 - x) * resolution
            wall_length = (end_y - start_y) * resolution
            world_template_list.append(f"""
    <model name="wall_{start_y}_{x}">
      <static>true</static>
      <pose>{wall_x} {wall_y} {wall_height/2} 0 0 0</pose>
      <link name="link">
        <collision name="collision">
          <geometry>
            <box>
              <size>{wall_length} {resolution} {wall_height}</size>
            </box>
          </geometry>
        </collision>
        <visual name="visual">
          <geometry>
            <box>
              <size>{wall_length} {resolution} {wall_height}</size>
            </box>
          </geometry>
        </visual>
      </link>
    </model>
""")
        y += 1

# Footer part of .world file
world_template_list.append("""
  </world>
</sdf>
""")

# Save as .world file
with open("../worlds/" + output_file, "w") as f:
    f.write("".join(world_template_list))

print(output_file + " has been created!!")
```

上記スクリプトの処理の大まかな流れは以下のようになっています。

- custom\_nav2\_bringup/maps/maze.pgm ファイル読み込む
- ground\_plane, sun, カメラの位置姿勢の基本的な設定を Header 部分に書き込む
- 壁（ロボットが障害物と認識する箇所、.pgm ファイルの黒い部分）の高さを 1m に設定する
- maze.pgm ファイルを走査して連続する黒い部分を見つけ出し、黒い部分の座標 / 大きさの情報を書き込む
- maze.world ファイル作成の際、+y -> +x, -x -> +y の座標変換と中心座標の調整を行っている
- Footer 部分を書き込む
- custom\_nav2\_bringup/worlds/maze.world ファイルを書き込む

下記のコマンドでスクリプトを実行します。

```
$ python3 convert_pgm_to_world.py
```

上記の作業により、RViz で使う.pgm,.yaml ファイルと Gazebo で使う.world ファイルの準備が整いました。

## launch ファイルの変更

上記の地図の準備を整えた状態で、launch/tb3\_simulation\_launch.py ファイルに以下の変更を加えます。

- ロボットの開始位置の座標を (-2.0, -0.5) から原点 (0.0, 0.0) にしている
- RViz で使うマップ情報を turtlebot3\_world.yaml から maze.yaml に変更
- Gazebo で使うマップ情報を world\_only.model から maze.world に変更

## TurtleBot シミュレーションの実行

以下のコマンドで、TurtleBot シミュレーションを実行します。

```
$ docker exec -it ros2_nav2 bash
/workspace$ colcon build
/workspace$ source install/setup.bash
/workspace$ ros2 launch custom_nav2_bringup tb3_simulation_launch.py headless:=False
```

以下が表示されます。

![](https://www.remma.net/wp-content/uploads/2025/03/Screenshot-from-2025-03-02-10-12-39-1024x640.png)

RViz and Gazebo are launched

RViz の “2D Pose Estimate” でロボットの位置を Gazebo の位置と合わせます。

![](https://www.remma.net/wp-content/uploads/2025/03/Screenshot-from-2025-03-02-12-53-25-1024x640.png)

Set initial pose

“Nav 2 Goal” でゴール地点を定めてロボットを移動させます。

![](https://www.remma.net/wp-content/uploads/2025/03/Screenshot-from-2025-03-02-10-15-25-1024x640.png)

Set goal

移動が完了しました。

![](https://www.remma.net/wp-content/uploads/2025/03/Screenshot-from-2025-03-02-10-16-04-1024x640.png)

The robot reached the goal

操作画面のスクリーンキャプチャは以下です。

マップを立体的に見ると以下のようになります。白い部分が.world ファイルで指定した “壁” になっています。

![](https://www.remma.net/wp-content/uploads/2025/03/Screenshot-from-2025-03-02-10-41-19-1024x645.png)

Gazebo screenshot

RViz の座標系は (x, y, z) = (赤, 緑, 青) の右手系で表されています（ [参考](https://qiita.com/Ninagawa123/items/d65e92877246e649cca6) ）。下の画像だと、x 軸が画面上、y 軸が画面左、z 軸が画面に向かってくる方向となります。

![](https://www.remma.net/wp-content/uploads/2025/03/Screenshot-from-2025-03-02-13-17-47-820x1024.png)

RViz coordinate system

## まとめ

自分で GIMP で作成したマップ上で TurtleBot シミュレーションが動くことを確認できました。ちょっと Gazebo の動作が遅いので、ここは今後の改善ポイントです（現在.world ファイルが 10,000 行くらいあるのでこれを短くしていく必要があるかと思います）。