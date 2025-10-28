---
title: "Gazebo, rviz2でGPUを使うメモ"
source: "https://qiita.com/porizou1/items/f6f66f830759bce3a4db"
author:
  - "[[porizou1]]"
published: 2023-01-09
created: 2025-08-26
description: "ubuntu 環境でNvidiaのGPUマシンでGazeboやrviz2を実行したときに、そのままだとGPUを使ってくれなかったので GPUを使う設定方法のメモ 環境変数の追加 以下を実行して環境変数を設定する 毎回実行するのは面倒なので常にGPU有効にする場合は.bas..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から1年以上が経過しています。

ubuntu 環境でNvidiaのGPUマシンでGazeboやrviz2を実行したときに、そのままだとGPUを使ってくれなかったので  
GPUを使う設定方法のメモ

## 環境変数の追加

以下を実行して環境変数を設定する  
毎回実行するのは面倒なので常にGPU有効にする場合は.bashrcに書いておく

```bash
export __NV_PRIME_RENDER_OFFLOAD=1
export __GLX_VENDOR_LIBRARY_NAME=nvidia
```

## GPUが使われているかの確認

nvidia-smiコマンドを実行して、Gazebo (gzserver, gzclient)、rviz2 (rviz2)のプロセスがあればOK

```bash
$ nvidia-smi
                                                 
+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
|    0   N/A  N/A      1319      G   /usr/lib/xorg/Xorg                 79MiB |
|    0   N/A  N/A      4689    C+G   ...177541916386972095,131072      107MiB |
|    0   N/A  N/A      6184      G   gzserver                            4MiB |
|    0   N/A  N/A      6185      G   gzclient                           78MiB |
|    0   N/A  N/A      6471      G   rviz2                               4MiB |
+-----------------------------------------------------------------------------+
```

[0](https://qiita.com/porizou1/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fporizou1%2Fitems%2Ff6f66f830759bce3a4db&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fporizou1%2Fitems%2Ff6f66f830759bce3a4db&realm=qiita)

[8](https://qiita.com/porizou1/items/f6f66f830759bce3a4db/likers)

いいねしたユーザー一覧へ移動

7