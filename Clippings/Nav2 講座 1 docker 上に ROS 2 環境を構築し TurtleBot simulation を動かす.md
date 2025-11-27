---
title: "Nav2 講座 1: docker 上に ROS 2 環境を構築し TurtleBot simulation を動かす"
source: "https://www.remma.net/?p=7216"
author:
  - "[[remma_tech_12]]"
published: 2025-02-20
created: 2025-11-07
description: "ROS 2 に関して筆者は以下の本で学びました。ROS1 との比較を含めかなりわかりやすく ROS 2 について書かれており、おすすめの一冊です。前回記事では、ubuntu 上での docker 環境構築について説明しましたが、今回は do"
tags:
  - "clippings"
---
ROS 2 に関して筆者は以下の本で学びました。ROS1 との比較を含めかなりわかりやすく ROS 2 について書かれており、おすすめの一冊です。

[

![](https://s0.wordpress.com/mshots/v1/https%3A%2F%2Famzn.to%2F4luacyG?w=160&h=90)

Amazon.co.jp

amzn.to

](https://amzn.to/4luacyG "Amazon.co.jp")

[前回記事](https://www.remma.net/?p=7176) では、ubuntu 上での docker 環境構築について説明しましたが、今回は docker 上で ROS 2 環境を構築し、TurtleBot のシミュレーションを動かしていきます。 [こちらの Qiita 記事](https://qiita.com/koichi_baseball/items/61aab066e3eada3160ad) を参考にしました。

## TurtleBot シミュレーション実行

筆者の環境は以下です。

- Thinkpad X1 Intel Core i7
- Ubuntu 24.04

docker-compose.yml と Dockerfile を用意します。 [github](https://github.com/remmaTech12/ros2_nav2/tree/article_7216) にこれらは載せているので、clone してもよいです（筆者動作確認済み branch は `article_7216` であることにご注意ください）。

上記ファイルがあるディレクトリで docker container をビルドします（所要時間 11 分程度）。

```
docker compose up -d --build
```

Docker コンテナに X サーバーへのアクセスを許可するため、ホスト環境で以下のコマンドを実行してください。

```
xhost +
```

docker container の中に入り、TurtleBot の Gazebo シミュレータを起動します。

```
docker exec -it ros2_nav2 bash
export TURTLEBOT3_MODEL=waffle
export GAZEBO_MODEL_PATH=$GAZEBO_MODEL_PATH:/opt/ros/$ROS_DISTRO/share/turtlebot3_gazebo/models
ros2 launch turtlebot3_gazebo turtlebot3_world.launch.py
```

以下が表示されたら（所要時間 3 分程度です。Gazebo が TurtleBot3 のモデルやワールドファイルをダウンロードしたり、物理エンジンやセンサ等のプラグインを初期化したりするのに結構時間がかかります。）

![](https://www.remma.net/wp-content/uploads/2025/02/Screenshot-from-2025-02-19-23-59-25-1024x645.png)

Ctrl-C で Gazebo を終了し、下記コマンドを実行します。

```
ros2 launch nav2_bringup tb3_simulation_launch.py headless:=False
```

以下の画面が表示されるはずなので、

![](https://www.remma.net/wp-content/uploads/2025/02/Screenshot-from-2025-02-19-18-28-56-1024x640.png)

動画のように 2D Pose Estimate でロボットの自己位置を合わせましょう。Nav2 Goal をクリックしてゴールの位置を指定すれば、TurtleBot がゴールに向かって動き出すのを確認できるはずです。

## docker 関連ファイル説明

docker 関連ファイルのそれぞれの行がどのような意味なのかを説明します。

### docker-compose.yml

Docker コンテナの GUI（X11）をホストと安全に共有する（rviz 等が docker container 内から開けるようになります）。

```
environment:
      - DISPLAY
      - QT_X11_NO_MITSHM=1
      - XAUTHORITY=/root/.Xauthority
    volumes:
      - /tmp/.X11-unix:/tmp/.X11-unix  # X11 ソケットの共有
      - $XAUTHORITY:/root/.Xauthority  # X11 認証情報の共有
```

### Dockerfile

ベースは ROS 2 humble

```
FROM ros:humble
```

必要な ROS の機能のインストール

```
RUN apt update && apt install -y \
    vim \
    ros-humble-rviz2 \
    ros-humble-navigation2 \
    ros-humble-nav2-bringup \
    ros-humble-turtlebot3-gazebo
```

docker container 起動時に毎回 setup.bash を実行しなくて済むようにする。

```
RUN echo "source /opt/ros/humble/setup.bash" >> /root/.bashrc
```![](https://www.remma.net/wp-content/themes/cocoon/images/no-image-160.png)

[

ubuntu 24.04 で docker 環境を構築する

](https://www.remma.net/?p=7176 "ubuntu 24.04 で docker 環境を構築する")[![](https://www.remma.net/wp-content/uploads/2025/02/Screenshot-from-2025-02-20-19-34-13-120x68.png)

Nav2 講座 2: Rust で ROS 2 ノードを作る: footprint の動的変更

](https://www.remma.net/?p=7263 "Nav2 講座 2: Rust で ROS 2 ノードを作る: footprint の動的変更")

[Home](https://www.remma.net/)

[ROS](https://www.remma.net/?cat=11)

- - [Articles](https://www.remma.net/)
	- [Profile](https://www.remma.net/?page_id=56)
	- [Works](https://www.remma.net/?page_id=265)
	- [Contact](https://www.remma.net/?page_id=276)
	- [Privacy Policy](https://www.remma.net/?page_id=3)
- [Home](https://www.remma.net/)
- 
- Top
-