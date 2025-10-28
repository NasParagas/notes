---
title: "VirtualBox環境のROS2でWebカメラを使えるようにする"
source: "https://zenn.dev/naonaorange/articles/20210523_virtualbox_ros2_webcam"
author:
  - "[[Zenn]]"
published: 2022-12-25
created: 2025-08-21
description:
tags:
  - "clippings"
---
[tech](https://zenn.dev/tech-or-idea)

VirtualBox環境下でROS2を使用するときに問題になりそうなのはWebカメラなどのデバイス接続がきちんとできるかということです。

この記事ではVirualBox環境下のUbuntu18.04 ROS2 DashingでWebカメラを使えるようにします。

## 前提条件

VirtualBox ExtensionがホストPCとゲストPCにどちらもインストールされていること

## USBをゲストPCに認識させる

VirturalBoxの設定で"USB3.0(xHCI)コントローラー"にチェックが入っていることを確認する。  
私のPCではWebカメラをUSB3.0ポートにさすことになります。  
その場合は下記の設定を行う必要があります。  
![](https://res.cloudinary.com/zenn/image/fetch/s--wZ1WE7CF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/f89ca870a3e4d93fec99a4d2.png%3Fsha%3Db5412f6253135b695db0c3fa56df788bfadd64ae)

## ゲストPCでWebカメラを認識させる

ゲストPCのデバイス設定でWebカメラを選択します。  
これでゲストPCにWebカメラが認識されます。  
![](https://res.cloudinary.com/zenn/image/fetch/s--h5MKRN8---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/ba10de088144807723ece204.png%3Fsha%3Dbae32e2869affeef28fbe5fe8d7d4adceacf3fd9)

## Webカメラの動作確認

とりあえずWebカメラにきちんとアクセスできているか確認しましょう。

```shell
cheese
```

![](https://res.cloudinary.com/zenn/image/fetch/s--vSgcgWwR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/0c98204cdad93b97acb44fc5.png%3Fsha%3D7fafe189580a82c4f751da9f0b85a5a54d8dd3b8)

- ROS2のV4L2用パッケージをインストールする

```shell
sudo apt-get install ros-dashing-v4l2-camera
```

- ROS2プログラムを実行時アクセスできるか確認する

```shell
ros2 run v4l2_camera v4l2_camera_node
```

```shell
ros2 run rqt_image_view rqt_image_view
```

![](https://res.cloudinary.com/zenn/image/fetch/s--nCfFwE5d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/ba8e8fa69b78220bef42c731.png%3Fsha%3D13a42f1fef97effb69c068761c78a5a28301a1dc)

[GitHubで編集を提案](https://github.com/naonaorange/zenn_contents/blob/main/articles/20210523_virtualbox_ros2_webcam.md)

### Discussion

![](https://static.zenn.studio/images/drawing/discussion.png)

ログインするとコメントできます