---
title: "Dockerコンテナ上でUSBモジュールを使う方法"
source: "https://qiita.com/Dorebom/items/e8dee38dbb6be3890c1c"
author:
  - "[[Dorebom]]"
published: 2021-03-02
created: 2025-08-26
description: "Dockerコンテナ上でBWT901CL(IMUセンサ)を使おうとして，調べたことをまとめる． やらないといけないことは以下の2点． docker runでホストとコンテナ間でUSBデバイスのフォルダを共有する． chmodで実行権限を与える． 1. ホストとコン..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から3年以上が経過しています。

Dockerコンテナ上でBWT901CL(IMUセンサ)を使おうとして，調べたことをまとめる．

やらないといけないことは以下の2点．

1. `docker run` でホストとコンテナ間でUSBデバイスのフォルダを共有する．
2. `chmod` で実行権限を与える．

## 1\. ホストとコンテナ間でUSBデバイスの共有

そのときの `docker run` 用のシェルスクリプトは以下のとおり

run.sh

```shell
USER_NAME=dorebom
WORKSPACE=workspace/ros2docker/ros2_ws

docker run --rm -it --privileged \
        --gpus all \
        --device=/dev/ttyUSB0:/dev/ttyUSB0 \
        -v /tmp/.X11-unix:/tmp/.X11-unix \
        -v /etc/localtime:/etc/localtime \
        -v /home/$USER_NAME/$WORKSPACE:/home/developer/ros2_ws \
        -e DISPLAY=$DISPLAY \
        --name ros2test \
        ros2docker:foxy
```

ここで，今回のUSBデバイスのアドレスは `/dev/ttyUSB0` であったので， `--device` オプションで共有．

\[NOTE\]その他のオプションは，Dockerコンテナ上でGPUを使うためと，ROSのGazeboなどのGUIをホスト側で利用するためのものであり，また後日解説する．

## 2\. 実行権限を与える

このままでは，コンテナ内でUSBデバイスを認識できても実行できないので権限を与える．

```bash
$ sudo chmod 777 /dev/ttyUSB0
```

ここで，私の環境ではDockerコンテナ内でユーザアカウントを利用しているため， `sudo` を使用している．

## 参考にしたページ

[0](https://qiita.com/Dorebom/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2FDorebom%2Fitems%2Fe8dee38dbb6be3890c1c&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2FDorebom%2Fitems%2Fe8dee38dbb6be3890c1c&realm=qiita)

[6](https://qiita.com/Dorebom/items/e8dee38dbb6be3890c1c/likers)

いいねしたユーザー一覧へ移動

5