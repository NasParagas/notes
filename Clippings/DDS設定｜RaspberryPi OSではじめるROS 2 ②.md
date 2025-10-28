---
title: "DDS設定｜RaspberryPi OSではじめるROS 2 ②"
source: "https://zenn.dev/array/books/raspi_os_de_hajimeru_ros2_2/viewer/p00_06_network#dds%E8%A8%AD%E5%AE%9A%E3%81%AE%E5%88%87%E3%82%8A%E6%9B%BF%E3%81%88"
author:
  - "[[Zenn]]"
published:
created: 2025-08-26
description:
tags:
  - "clippings"
---
このチャプターの目次

1. [現在使用されているDDSの確認](https://zenn.dev/array/books/raspi_os_de_hajimeru_ros2_2/viewer/#%E7%8F%BE%E5%9C%A8%E4%BD%BF%E7%94%A8%E3%81%95%E3%82%8C%E3%81%A6%E3%81%84%E3%82%8Bdds%E3%81%AE%E7%A2%BA%E8%AA%8D)
2. [CyclonDDSのビルドと設定](https://zenn.dev/array/books/raspi_os_de_hajimeru_ros2_2/viewer/#cyclondds%E3%81%AE%E3%83%93%E3%83%AB%E3%83%89%E3%81%A8%E8%A8%AD%E5%AE%9A)
3. [ビルド](https://zenn.dev/array/books/raspi_os_de_hajimeru_ros2_2/viewer/#%E3%83%93%E3%83%AB%E3%83%89)
4. [ロード](https://zenn.dev/array/books/raspi_os_de_hajimeru_ros2_2/viewer/#%E3%83%AD%E3%83%BC%E3%83%89)
5. [DDS設定の切り替え](https://zenn.dev/array/books/raspi_os_de_hajimeru_ros2_2/viewer/#dds%E8%A8%AD%E5%AE%9A%E3%81%AE%E5%88%87%E3%82%8A%E6%9B%BF%E3%81%88)
	1. [FastRTPSの設定](https://zenn.dev/array/books/raspi_os_de_hajimeru_ros2_2/viewer/#fastrtps%E3%81%AE%E8%A8%AD%E5%AE%9A)
	2. [CycloneDDSの設定](https://zenn.dev/array/books/raspi_os_de_hajimeru_ros2_2/viewer/#cyclonedds%E3%81%AE%E8%A8%AD%E5%AE%9A)
6. [ドメインIDの設定](https://zenn.dev/array/books/raspi_os_de_hajimeru_ros2_2/viewer/#%E3%83%89%E3%83%A1%E3%82%A4%E3%83%B3id%E3%81%AE%E8%A8%AD%E5%AE%9A)
	1. [ドメインIDに設定可能な値](https://zenn.dev/array/books/raspi_os_de_hajimeru_ros2_2/viewer/#%E3%83%89%E3%83%A1%E3%82%A4%E3%83%B3id%E3%81%AB%E8%A8%AD%E5%AE%9A%E5%8F%AF%E8%83%BD%E3%81%AA%E5%80%A4)
	2. [ドメインIDの設定](https://zenn.dev/array/books/raspi_os_de_hajimeru_ros2_2/viewer/#%E3%83%89%E3%83%A1%E3%82%A4%E3%83%B3id%E3%81%AE%E8%A8%AD%E5%AE%9A-1)
7. [ローカルホストのみの通信設定](https://zenn.dev/array/books/raspi_os_de_hajimeru_ros2_2/viewer/#%E3%83%AD%E3%83%BC%E3%82%AB%E3%83%AB%E3%83%9B%E3%82%B9%E3%83%88%E3%81%AE%E3%81%BF%E3%81%AE%E9%80%9A%E4%BF%A1%E8%A8%AD%E5%AE%9A)

ここでは、ROS 2の通信に使用されるDDS（Data Distribution Service）の設定を行います。

DDS（Data Distribution Service）とは、リアルタイム・分散システムの構築をターゲットにしたマルチキャスト通信の一つであり、初のオープンなミドルウェアの国際標準です。

アプリケーション自体は分離していながら、パブリッシュ・サブスクライブ通信を行うための探索や接続を自動的に行う機能を備えています。速度・品質については、 Quality of Service (QoS)をユーザが選択することで調整することができます。

  

ROS 2のDDSは、DDS実装レイヤで抽象化されており、オプションでDDSの実装を変更することができます。

![](https://storage.googleapis.com/zenn-user-upload/edb2fb872e8e-20240715.png)

ROS 2 JazzyのDDSは、eProsima Fast DDS（FastRTPS）がデフォルトで使用されています。

  

DDSはマルチキャスト通信であるため、コンピュータ感が自動的に他のコンピュータのノードを探索・通信することは想定内の動作です。

一方で、他のユーザのノード・トピックや他のDDS実装が混在した場合、想定外の挙動や不具合の原因となるため、設定によって分離が必要です。

  

## 現在使用されているDDSの確認

以下のコマンドで、現在使用されているDDSの実装を確認できます。

```bash
DISTRO=jazzy
source /opt/ros/${DISTRO}/setup.bash

ros2 doctor --report
```

出力されたメッセージのうち、 `RMW MIDDLEWARE` の部分を確認します。

ここでは、 `rmw_fastrtps_cpp` が使用されていることがわかります。

この設定を確認したら、まずはCycloneDDSをビルドします。

## CyclonDDSのビルドと設定

新しく `cyclonedds_ws/` というワークスペースを作成します。

```bash
mkdir -p ~/cyclonedds_ws/src
cd ~/cyclonedds_ws/src
git clone https://github.com/ros2/rmw_cyclonedds.git -b jazzy
```

## ビルド

ビルドします。

```bash
cd ~/cyclonedds_ws
DISTRO=jazzy

source /opt/ros/${DISTRO}/setup.bash
colcon build
```

## ロード

あらかじめ環境変数を設定しておきます。

```bash
source ~/cyclonedds_ws/install/setup.bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
```

最後の行までうまく実行できた時点で、 `ros2 doctor --report` を実行すると、次のような出力を得られます。

`rmw_cyclonedds_cpp` に変更されていれば、正しく切り替わっています。

  

## DDS設定の切り替え

DDS実装の切り替えは、Linuxの環境変数 `RMW_IMPLEMENTATION` を設定することで行います。

ここでは、FastRTPSとCycloneDDSの切り替え方法を説明します。

### FastRTPSの設定

FastRTPSを使用する場合は、以下のように設定します。

```bash
export RMW_IMPLEMENTATION=rmw_fastrtps_cpp
```

### CycloneDDSの設定

CycloneDDSを使用する場合は、以下のように設定します。

```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
```

## ドメインIDの設定

DDSでは、異なる論理ネットワークで物理ネットワークを共有するために、ドメインIDを設定して、同じドメインIDを持つノード同士で通信を行います。

全てのROS 2ノードはデフォルトで0がドメインIDとして設定されています。同じネットワークで異なるグループのノードの干渉を避けるため、異なるドメインIDを設定することが推奨されます。

### ドメインIDに設定可能な値

環境変数 `ROS_DOMAIN_ID` を0 ~ 232の範囲で設定できます。ただし、プラットフォーム固有の問題を全て回避するための安全な値は 0 ~ 101です。

詳しい説明は、 [ROS\_DOMAIN\_ID (jazzy)](https://docs.ros.org/en/jazzy/Concepts/Intermediate/About-Domain-ID.html) をご覧ください。

また、隣り合う `ROS_DOMAIN_ID` を設定する場合、ノードをたくさん作成して接続すると干渉が発生する可能性があるため、IDを離すように設定してください。

### ドメインIDの設定

ドメインIDを設定するには、以下のように環境変数を設定します。

```bash
# ドメインIDを30に設定
export ROS_DOMAIN_ID=30
```

  

## ローカルホストのみの通信設定

ROS 2の通信は、デフォルトでマルチキャスト通信を使用しますが、これをローカルホストのみに制限することも可能です。

> ROS\_LOCALHOST\_ONLYはjazzy以降は非推奨となりました。

設定は次のように行います。

```bash
export ROS_AUTOMATIC_DISCOVERY_RANGE=LOCALHOST
```

設定を無効にする場合は、次のように設定します。

```bash
export ROS_AUTOMATIC_DISCOVERY_RANGE=SUBNET
# または、 unset ROS_AUTOMATIC_DISCOVERY_RANGEß
```