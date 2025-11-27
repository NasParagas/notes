---
title: "ROS 2のDockerコンテナ間でpub/subするとき、docker runでどのオプションを付ければいいの？"
source: "https://qiita.com/dandelion1124/items/9c0a9c16956bb8fb9065"
author:
  - "[[dandelion1124]]"
published: 2024-07-21
created: 2025-11-07
description: "1. はじめに ROS 2のDockerコンテナ間通信をする方法を検索すると「docker runオプションに--net=host --ipc=hostを付けよう」という言及がWeb上の記事で散見されます。一方、このオプションを付ける理由まで書かれているものが非常に少ない..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から1年以上が経過しています。

## 1\. はじめに

ROS 2のDockerコンテナ間通信をする方法を検索すると「 `docker run` オプションに `--net=host --ipc=host` を付けよう」という言及がWeb上の記事で散見されます。一方、このオプションを付ける理由まで書かれているものが非常に少ないです。理由がよくわからないと非常に気持ち悪いのでもう少し掘り下げて整理していきます。

## 2\. シチュエーション別おすすめdocker runオプション

ユースケースにもよると思うので、本記事ではDDSそのものの比較には言及しないこととします。気になる方は [https://discourse.ros.org/t/ros-2-alternative-middleware-report/33771](https://discourse.ros.org/t/ros-2-alternative-middleware-report/33771) を読んでみてください。

本記事では基本的にROS 2 HumbleのデフォルトであるFast DDSを使うとして、シチュエーション別 `docker run` オプションを記載します。

ここでは例として `--shm-size=512mb` として、共有メモリサイズを512MBとしていますが、ご自身のユースケースに応じて適宜調整してください。

## 3\. 動作確認条件

以下に動作確認で用いた環境、動作確認内容について列挙します。

### 3.1 動作確認に用いた環境

- ホストOS：Ubuntu 22.04(x86\_64)
- Docker CLI
	- docker-ce 24.0.6
- ROS 2公式Dockerイメージ [ros:humble-ros-core](https://hub.docker.com/layers/library/ros/humble-ros-core/images/sha256-eb1e1df640e98ef44dae24f9f7ef51489efcc548e776bbf48d0bee1e1edd5465) を用いた
- ROS 2ディストリビューション：Humble
- DDS
	- Fast DDS
		- ros-humble-fastrtps 2.6.8
		- ros-humble-rmw-fastrtps-cpp 6.2.6
		- ros-humble-rmw-fastrtps-shared-cpp 6.2.6
	- Cyclone DDS
		- ros-humble-cyclonedds 0.10.4
		- ros-humble-rmw-cyclonedds-cpp 1.3.4

### 3.2 動作確認内容

- 同一マシン上で2つのDockerコンテナを起動し、片方のコンテナでpub、もう片方のコンテナでsubを行った
- pubコマンドは以下の通り

```shell
ros2 topic pub --rate 1 /turtle1/cmd_vel geometry_msgs/msg/Twist "{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 1.8}}"
```

- subコマンドは以下の通り

```shell
ros2 topic echo /turtle1/cmd_vel
```

### 3.3 docker runオプション

今回、以下のパターンで検証していきます。

- \--net=bridge
- \--net=host
- \--net=host --ipc=host

詳細なオプションは以降に記載します。

#### 3.3.1 net=bridge

`--net` オプションのデフォルト値である、 `--net=bridge` を指定します。

```bash
docker run --rm -it \
           ros:humble-ros-core \
           bash
```

#### 3.3.2 net=host

`--net=host` を指定します。

```bash
docker run --rm -it \
           --net=host \
           ros:humble-ros-core \
           bash
```

#### 3.3.3 net=host ipc=host

`--net=host` 、 `--ipc=host` を指定します。

```bash
docker run --rm -it \
           --net=host \
           --ipc=host \
           ros:humble-ros-core \
           bash
```

## 4\. 動作確認結果

同一マシン上の2つのコンテナ間でROS 2のpub/subができるかどうかの確認結果を下表に示します。ここでは比較対象としてCyclone DDSの場合の結果も併せて記載しています。

| DDS\\docker runオプション | net=bridge | net=host | net=host   ipc=host |
| --- | --- | --- | --- |
| Fast DDS | OK | NG | OK |
| Cyclone DDS | OK | OK | OK |

- OK：同一マシン上の2つのコンテナ間でROS 2のpub/subができる
- NG：同一マシン上の2つのコンテナ間でROS 2のpub/subができない
	- ただし、 `ros2 topic list` でトピック一覧には表示される

この結果からFast DDSかつ `net=host` オプションのみの場合を除いて、同一PC上でROS 2のDockerコンテナを複数起動して、コンテナ間でROS 2のpub/subができていることがわかります。

さて、 `docker run` オプションやDDSの違いでなぜこのような挙動の差異が生じるのでしょうか？もう少し掘り下げて見ていきましょう。

## 5\. 前提知識

ここでは詳細な確認をする前に必要な前提知識をおさらいしていきます。

### 5.1 docker runオプション

#### 5.1.1 netオプション

`docker run` のnetオプションでネットワークドライバを指定できます（ [https://docs.docker.com/network/drivers/](https://docs.docker.com/network/drivers/) 参照）。 [https://zenn.dev/suiudou/articles/14249a918ec93b#hostネットワーク](https://zenn.dev/suiudou/articles/14249a918ec93b#host%E3%83%8D%E3%83%83%E3%83%88%E3%83%AF%E3%83%BC%E3%82%AF) の図がわかりやすいでしょう。

- \--net=bridge
	- [https://docs.docker.com/network/drivers/bridge/](https://docs.docker.com/network/drivers/bridge/) に説明があります。
- \--net=host
	- [https://docs.docker.com/network/drivers/host/](https://docs.docker.com/network/drivers/host/) に説明があります。つまり、 `--net=host` ではホストと同一のネットワークインターフェースを使用することになります。

#### 5.1.2 ipcオプション

`docker run` のipcオプションでDockerコンテナにおけるプロセス間通信（Inter-Process Communication）の名前空間を変更することができます。 [https://docs.docker.com/reference/cli/docker/container/run/#ipc](https://docs.docker.com/reference/cli/docker/container/run/#ipc) に以下の説明があります。

| Value | Description |
| --- | --- |
| "" | Use daemon's default. |
| "none" | Own private IPC namespace, with /dev/shm not mounted. |
| "private" | Own private IPC namespace. |
| "shareable" | Own private IPC namespace, with a possibility to share it with other containers. |
| "container:" | Join another ("shareable") container's IPC namespace. |
| "host" | Use the host system's IPC namespace. |

`--ipc=host` の場合、プロセス間通信においてホストと同一の名前空間が用いられることになります。また、ipcオプションを明示的に指定しない場合については [https://docs.docker.com/reference/cli/docker/container/run/#ipc](https://docs.docker.com/reference/cli/docker/container/run/#ipc) に以下の記載があります。

> If not specified, daemon default is used, which can either be "private" or "shareable", depending on the daemon version and configuration.

デーモンのバージョン、コンフィグレーションにもよりますが、デフォルト値となる `private` 、 `shareable` のいずれかが使用されるとあります。そこで [https://docs.docker.jp/engine/reference/commandline/dockerd.html](https://docs.docker.jp/engine/reference/commandline/dockerd.html) を読むと

> \--default-ipc-mode string  
> Default mode for containers ipc ("shareable" | "private") (default "private")

とあり、デフォルトのIPCモードは `private` とあります。そのため、IPCの名前空間はコンテナ毎に独立していることになります。

### 5.2 Fast DDS

ここではFast DDSのTransportについて簡単におさらいしていきます。Fast DDSでは以下のTransportが実装されています。  
[https://fast-dds.docs.eprosima.com/en/latest/fastdds/transport/transport.html](https://fast-dds.docs.eprosima.com/en/latest/fastdds/transport/transport.html)

- UDPv4 Transport
- UDPv6 Transport
- TCPv4 Transport
- TCPv6 Transport
- Shared Memory Transport

Fast DDSのTransport実装を図示したものを以下に示します（ [https://fast-dds.docs.eprosima.com/en/latest/\_images/transport\_comparison.svg](https://fast-dds.docs.eprosima.com/en/latest/_images/transport_comparison.svg) から引用）。

[![](https://qiita-user-contents.imgix.net/https%3A%2F%2Ffast-dds.docs.eprosima.com%2Fen%2Flatest%2F_images%2Ftransport_comparison.svg?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=5cb0c34dbf26298b39250867869bd731)](https://qiita-user-contents.imgix.net/https%3A%2F%2Ffast-dds.docs.eprosima.com%2Fen%2Flatest%2F_images%2Ftransport_comparison.svg?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=5cb0c34dbf26298b39250867869bd731)

本記事ではFast DDSにてデフォルトで有効になっている以下のTransportに着目します。

- UDPv4 Transport
- Shared Memory Transport

#### 5.2.1 UDPv4 Transport

- IPv4経由のUDPデータグラム通信
- 特定のトランスポート構成が指定されていない場合、デフォルトで新しいDomainParticipantにこのトランスポートが作成される
- **Fast DDSではUDPv4 Transportがデフォルトで有効になっている**
	- [https://fast-dds.docs.eprosima.com/en/latest/fastdds/transport/udp/udp.html#enabling-udp-transport](https://fast-dds.docs.eprosima.com/en/latest/fastdds/transport/udp/udp.html#enabling-udp-transport) 参照
- 詳細は [https://fast-dds.docs.eprosima.com/en/latest/fastdds/transport/udp/udp.html](https://fast-dds.docs.eprosima.com/en/latest/fastdds/transport/udp/udp.html) 参照

#### 5.2.2 Shared Memory Transport

- 同一PC内の通信でOSが提供する共有メモリを介してデータを転送
- 特定のトランスポート構成が指定されていない場合、デフォルトで新しいDomainParticipantにこのトランスポートが作成される
- **Fast DDSではShared Memory Transportがデフォルトで有効になっている**
	- [https://fast-dds.docs.eprosima.com/en/latest/fastdds/transport/shared\_memory/shared\_memory.html#enabling-shared-memory-transport](https://fast-dds.docs.eprosima.com/en/latest/fastdds/transport/shared_memory/shared_memory.html#enabling-shared-memory-transport) 参照
- 詳細は [https://fast-dds.docs.eprosima.com/en/latest/fastdds/transport/shared\_memory/shared\_memory.html](https://fast-dds.docs.eprosima.com/en/latest/fastdds/transport/shared_memory/shared_memory.html) 参照

また、同時に複数のtransportが有効になっている場合の挙動については [https://fast-dds.docs.eprosima.com/en/latest/fastdds/transport/shared\_memory/shared\_memory.html](https://fast-dds.docs.eprosima.com/en/latest/fastdds/transport/shared_memory/shared_memory.html) に記載があります。

> In case that several transports are enabled, the discovery traffic is always performed using the UDP/TCP transport, even if the SHM transport is enabled in both participants running in the same machine. This may cause discovery issues if one or several of the participants only has SHM enabled and other participants use some other transport at the same time. Also, when two participants on the same machine have SHM transport enabled, the user data communication between them is automatically performed by SHM transport only. The rest of the enabled transports are not used between those two participants.

## 6\. 調査

ここでは「4. 動作確認結果」で得られた結果とDocker、DDSの処理の対応関係についてもう少し掘り下げて見ていきます。

### 6.1 同一PC上で-net=hostのみだとコンテナ間でpub/subできないのはなぜ？（Fast DDS）

[https://fast-dds.docs.eprosima.com/en/latest/fastdds/transport/shared\_memory/shared\_memory.html](https://fast-dds.docs.eprosima.com/en/latest/fastdds/transport/shared_memory/shared_memory.html) に

> –ipc=host: This option shares the host’s shared memory mechanism with the containers. Without it, Fast DDS will identify both containers as the same host, but since they will have separate shared memory spaces, they will not be able to communicate with one another.

という説明があります。 `--ipc` オプションを明示的に指定しない場合、コンテナ間で共有メモリの名前空間が別になります。そのため、コンテナ間でShared Memory Transportを使って通信できない状態になっており、コンテナ間でROS 2のpub/subできなくなっています。

### 6.2 同一PC上で-net=hostのみだと別コンテナのトピックは見えるのはなぜ？（Fast DDS）

「4. 動作確認結果」で

> - NG：同一マシン上の2つのコンテナ間でROS 2のpub/subができない
> 	- ただし、 `ros2 topic list` でトピック一覧には表示される

とありました。「 `ros2 topic list` でトピック一覧には表示される」のはなぜでしょうか？これは「5.2.2 Shared Memory Transport」で説明した以下の処理に起因しています。

> In case that several transports are enabled, the discovery traffic is always performed using the UDP/TCP transport, even if the SHM transport is enabled in both participants running in the same machine.

Fast DDSではUDPv4 Transport、Shared Memory Transportがデフォルトで有効になっています。また、このケースではdiscovery trafficはUDPv4 Transportで行われているため、コンテナ間で共有メモリの名前空間が同じかどうかは関係ありません。そのため、 `ros2 topic list` でトピック一覧には表示されることになります。

### 6.3 同一PC上で--net=hostのみでもコンテナ間でpub/subできるのはなぜ？（Cyclone DDS）

[https://github.com/ros2/rmw\_cyclonedds/blob/rolling/shared\_memory\_support.md](https://github.com/ros2/rmw_cyclonedds/blob/rolling/shared_memory_support.md) に以下の説明があります。

> This rmw\_cyclonedds implementation uses cyclonedds which includes support for fast Shared Memory data transfer based on iceoryx. Shared Memory is disabled by default but can be enabled easily by providing a cyclonedds.xml configuration file.

Cyclone DDSでは共有メモリを使ったデータ転送はデフォルトで無効化されているため、 `--ipc=host` を付けなくてもコンテナ間でROS 2のpub/subができています。

### 6.4 同一PC上で--ipc=hostなしでもShared Memory Transportを使ってコンテナ間でpub/subできるか？（Fast DDS）

オプションは以下の通りです。前述した通り、コンテナ間で共有メモリの名前空間が別になっていたため、Fast DDSではコンテナ間でROS 2のpub/subができていませんでした。

```bash
docker run --rm -it \
           --net=host \
           ros:humble-ros-core \
           bash
```

そのため、 `docker run` オプションに `--volume=/dev/shm:/dev/shm:rw` 、共有メモリのデバイスファイルをマウントすることでコンテナ間でROS 2のpub/subができるようになります。

```bash
docker run --rm -it \
           --volume=/dev/shm:/dev/shm:rw \
           --net=host \
           ros:humble-ros-core \
           bash
```

### 6.5 net=bridgeのみだとpub/subできていたのはなぜ？（Fast DDS）

**《こちらは調査中です》**

「4. 動作確認結果」で `--net=bridge` のとき、IPC関連のオプションなしでもpub/subできていました。この挙動の理由について調べてみます。

#### 6.5.1 IPC名前空間を調べる

`--net=bridge` でコンテナを2つ起動してIPC名前空間を調べてみます。コンテナ(1)の結果は以下の通りです。

```bash
$ ls -l /proc/self/ns/ipc
lrwxrwxrwx 1 humble humble 0 Jul 21 04:37 /proc/self/ns/ipc -> 'ipc:[4026533262]'
```

コンテナ(2)の結果は以下の通りです。

```bash
$ ls -l /proc/self/ns/ipc
lrwxrwxrwx 1 humble humble 0 Jul 21 04:37 /proc/self/ns/ipc -> 'ipc:[4026533374]'
```

2つのコンテナでIPC名前空間は同一にはなっていないようです。

#### 6.5.2 /dev/shmをマウントしない

`docker run` オプションで `--ipc=none` として `/dev/shm` をマウントしないようにしてコンテナ間でpub/subを確認してみます。

Shared Memory Transportの初期化で失敗していますが、UDPv4 Transportでpub/subできているようです。

#### 6.5.3 FASTDDS\_BUILTIN\_TRANSPORTSを変更する

[https://fast-dds.docs.eprosima.com/en/latest/fastdds/env\_vars/env\_vars.html#fastdds-builtin-transports](https://fast-dds.docs.eprosima.com/en/latest/fastdds/env_vars/env_vars.html#fastdds-builtin-transports) を参考にして環境変数FASTDDS\_BUILTIN\_TRANSPORTSを変更してコンテナ間のpub/sub成否を確認します。結果は以下の通りです。

| FASTDDS\_BUILTIN\_TRANSPORTS | コンテナ間のpub/sub成否 |
| --- | --- |
| DEFAULT | OK |
| UDPv4 | OK |
| SHM | NG |

この結果からUDPv4 Transportでpub/subできているようです。

### 6.6 同一PC上で--ipc=hostなしでもコンテナ間でpub/subできるか？（Fast DDS）

同一PC上のコンテナ間でFast DDS、Shared Memory Transportを使うために `--ipc=host` が必要です。逆に言うとShared Memory Transportを使わないようにし、UDP Transportのみを使うようにすれば `--ipc=host` を付けなくてもよくなります。

[https://fast-dds.docs.eprosima.com/en/latest/fastdds/env\_vars/env\_vars.html#fastdds-builtin-transports](https://fast-dds.docs.eprosima.com/en/latest/fastdds/env_vars/env_vars.html#fastdds-builtin-transports) を参考に以下のコマンドを実行してUDPv4 Transportを使うようにします。  
※ [https://tech.aptpod.co.jp/entry/2023/09/25/170000](https://tech.aptpod.co.jp/entry/2023/09/25/170000) にあるようにUDP Transportを使うようなコンフィグレーションファイル (`fastrtps-profile.xml`)を用いる方法もあります。

```shell
export FASTDDS_BUILTIN_TRANSPORTS=UDPv4
```

このようにすることで `--ipc=host` なしでもコンテナ間でROS 2のpub/subができるようになります。

### 6.7 --net=hostオプションは要らない？

ここまでの説明で `--ipc=host` だけ付けていればよくて `--net=host` は要らないのでは？と思われる方が居るかもしれません。ROS 2でpub/subするコンテナが同一マシン内だけであれば `--net=host` は要らない（＝デフォルトの `--net=bridge` を使う）ので問題ありませんが、ROS 2でpub/subするコンテナが同一ネットワーク上の異なるPC間にある場合、 `--net=bridge` ではpub/subできません。このような使い方をする場合は `--net=host` が必要になります。

### 6.8 Dockerコンテナで共有メモリの割り当てはどうなっているの？

Dockerドキュメントの [https://docs.docker.jp/engine/reference/commandline/dockerd.html](https://docs.docker.jp/engine/reference/commandline/dockerd.html) で

> \--default-shm-size bytes  
> Default shm size for containers (default 64MiB)

とあり、デフォルトは共有メモリのサイズは64MiBとなっています。そのため、このサイズだと不十分なユースケースであれば `docker run` の `--shm-size` オプションで共有メモリの割り当てを増やすと良いでしょう。  
[https://docs.docker.com/engine/reference/run/#runtime-constraints-on-resources](https://docs.docker.com/engine/reference/run/#runtime-constraints-on-resources)

`--shm-size` オプションで共有メモリを1GB割り当てるオプションを以下に示します。

```bash
--shm-size=1gb
```

## 参考URL

- [https://fast-dds.docs.eprosima.com/en/latest/docker/shm\_docker.html](https://fast-dds.docs.eprosima.com/en/latest/docker/shm_docker.html)
- [https://github.com/docker/for-linux/issues/637](https://github.com/docker/for-linux/issues/637)
- [https://github.com/eProsima/Fast-DDS/issues/2956](https://github.com/eProsima/Fast-DDS/issues/2956)
- [https://stackoverflow.com/questions/65900201/troubles-communicating-with-ros2-node-in-docker-container](https://stackoverflow.com/questions/65900201/troubles-communicating-with-ros2-node-in-docker-container)
- [https://fast-dds.docs.eprosima.com/en/latest/fastdds/ros2/ros2\_configure.html](https://fast-dds.docs.eprosima.com/en/latest/fastdds/ros2/ros2_configure.html)
- [https://fast-dds.docs.eprosima.com/en/latest/fastdds/transport/shared\_memory/shared\_memory.html](https://fast-dds.docs.eprosima.com/en/latest/fastdds/transport/shared_memory/shared_memory.html)
- [https://cyclonedds.io/docs/cyclonedds/latest/shared\_memory/shared\_memory.html](https://cyclonedds.io/docs/cyclonedds/latest/shared_memory/shared_memory.html)

[0](https://qiita.com/dandelion1124/items/#comments)

コメント一覧へ移動

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fdandelion1124%2Fitems%2F9c0a9c16956bb8fb9065&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fdandelion1124%2Fitems%2F9c0a9c16956bb8fb9065&realm=qiita)

[17](https://qiita.com/dandelion1124/items/9c0a9c16956bb8fb9065/likers)

いいねしたユーザー一覧へ移動

9