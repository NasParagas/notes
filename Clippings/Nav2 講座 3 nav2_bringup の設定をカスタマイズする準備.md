---
title: "Nav2 講座 3: nav2_bringup の設定をカスタマイズする準備"
source: "https://www.remma.net/?p=7458"
author:
  - "[[remma_tech_12]]"
published: 2025-02-25
created: 2025-11-07
description: "ROS 2 に関して筆者は以下の本で学びました。ROS1 との比較を含めかなりわかりやすく ROS 2 について書かれており、おすすめの一冊です。本記事では nav2_bringup をコピーして custom_nav2_bringup と"
tags:
  - "clippings"
---
ROS 2 に関して筆者は以下の本で学びました。ROS1 との比較を含めかなりわかりやすく ROS 2 について書かれており、おすすめの一冊です。

[

![](https://s0.wordpress.com/mshots/v1/https%3A%2F%2Famzn.to%2F4luacyG?w=160&h=90)

](https://amzn.to/4luacyG "Amazon.co.jp")

本記事では nav2\_bringup をコピーして custom\_nav2\_bringup というオリジナルパッケージを作成し、設定をカスタマイズしていきます。

※ 本記事はすでに [この記事](https://www.remma.net/?p=7216) の内容を実施していることを前提としています。

## 過去記事

- [Nav2 講座 1: docker 上に ROS 2 環境を構築し TurtleBot simulation を動かす](https://www.remma.net/?p=7216)
- [Nav2 講座 2: Rust で ROS 2 ノードを作る: footprint の動的変更](https://www.remma.net/?p=7263)

## PC 環境

- Thinkpad X1 Intel Core i7
- Ubuntu 24.04

## 準備

[この github repository](https://github.com/remmaTech12/ros2_nav2) を pull して、Docker コンテナを立ち上げ、コンテナ内に入りましょう。

```
$ git clone git@github.com:remmaTech12/ros2_nav2.git
$ git checkout article_7458
$ docker compose up -d --build
$ docker exec -it ros2_nav2 bash
```

コンテナの中で以下を実行します。

```
/workspace& mkdir -p src
/workspace$ cp -r /opt/ros/humble/share/nav2_bringup src
/workspace$ cd src
/workspace/src$ mv nav2_bringup custom_nav2_bringup
/workspace/src$ cd custom_nav2_bringup
```

リネームした custom\_nav2\_bringup ディレクトリの中に LICENCE というファイルを作り、以下を入力して保存します。

/workspace/src/custom\_nav2\_bringup に CMakeLists.txt ファイルを作成し、以下を記載します。

package.xml でパッケージ名を nav2\_bringup から custom\_nav2\_bringup に変更します。

tb3\_simulation\_launch.py を修正し、share ディレクトリ（設定ファイル等を配置するディレクトリ）を変更します。これにより、/workspace/install/custom\_nav2\_bringup/share/custom\_nav2\_bringup 配下の設定ファイルがロードされます。

この状態で Docker コンテナ内から以下のコマンドを叩くと TurtleBot シミュレーションが起動することを確かめられると思います。パッケージ名はあえて、custom\_nav2\_bringup（今回コピーしてリネームしたパッケージ名）にしています。

```
/workspace$ cd /workspace
/workspace$ colcon build
/workspace$ source install/setup.bash
/workspace$ ros2 launch custom_nav2_bringup tb3_simulation_launch.py headless:=False
```

## ライセンスについて

nav2 の各パッケージのライセンスに関しては [こちら](https://github.com/ros-navigation/navigation2/blob/main/LICENSE) に明記されている通り、package.xml に記載されているそうです。

> Portions of this repository are available under one of the following licenses
> 
> SPDX-ID:  
> \* LGPL-2.1-or-later  
> \* Apache-2.0  
> \* BSD-3-Clause  
> \* Apache-2.0 AND BSD-3-Clause
> 
> Please see the package.xml file for each package to see the specific license for that package.
> 
> [https://github.com/ros-navigation/navigation2/blob/main/LICENSE](https://github.com/ros-navigation/navigation2/blob/main/LICENSE)

私達が編集する nav2\_bringup パッケージを見てみると、Apache-2.0 ライセンスとなっていることがわかります（ [GitHub link](https://github.com/ros-navigation/navigation2/blob/4694386872365851c840737515f89e3d409c1d9f/nav2_bringup/package.xml#L10) ）。

```
<license>Apache-2.0</license>
```

このライセンスは、 [こちらの記事](https://qiita.com/ikari_mirudake/items/2081acccb50ed9c078f7) にも記載されている通り、再頒布が許可されたライセンスです。今回のプロジェクトでは以下のように簡単に NOTICE ファイルを作成して、変更点を明記しておきました。

```
This project includes software developed by Remma.

Modifications:
- 2025.02-24: Change the package name.
```

## ちょっとした変更を加える

/workspace/src/custom\_nav2\_bringup/params/nav2\_params.yaml のパラメータにちょっとした変更を加えた上で、変更が反映されているかを確認します。local\_costmap の inflation\_radius を小さくするという変更を行いました。

![](https://www.remma.net/wp-content/uploads/2025/02/Screenshot-from-2025-02-25-20-59-58-1024x861.png)

変更前

![](https://www.remma.net/wp-content/uploads/2025/02/Screenshot-from-2025-02-25-20-59-00-1024x861.png)

変更後

実際に inflation\_radius が小さくなったことが確かめられました！