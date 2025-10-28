---
title: "Compose で GPU アクセスの有効化 — Docker-docs-ja 24.0 ドキュメント"
source: "https://docs.docker.jp/compose/gpu-support.html"
author:
published:
created: 2025-08-26
description:
tags:
  - "clippings"
---
## Compose で GPU アクセスの有効化

Docker ホストに GPU デバイスが入っており、かつ、 Docker デーモンで適切な設定がある場合、 Compose サービスで GPU デバイス予約を定義できます。このために、 [必要条件](https://docs.docker.jp/config/containers/resource_constraints.html#resource-constraints-gpu) のインストールがまだであれば、終えておく必要があります。

以下のセクションにある例では、 サービス用コンテナが GPU デバイスにアクセスするため、Docker Compose での設定方法に焦点を絞ります。 `docker-compose` と `docker compose` 、どちらのコマンドも使えます。

## ¶

Docker Compose v1.27.0 以上では、2.x と 3.x バージョンの全てのプロパティを統合した Compose Specification スキームを使うように切り替わりました。これにより、サービス用コンテナが GPU にアクセスできるようにするための、 [runtime](https://docs.docker.jp/compose/compose-file/compose-file-v2.html#compose-file-runtime) サービス プロパティを再び使えるようになります。しかし、こちらを使うと、 GPU デバイスにある特定のプロパティを制御できません。

```yaml
services:
  test:
    image: nvidia/cuda:10.2-base
    command: nvidia-smi
    runtime: nvidia
```

## ¶

Docker Compose v1.28.0 以上では、Compose Specification の [device](https://github.com/compose-spec/compose-spec/blob/master/deploy.md#devices) 構造の定義を使い、 GPU 指定の定義が可能です。これにより、以下のデバイス プロパティに任意の値を指定できるため、 GPU 指定を細かく制御できるようになります。

- [capabilities](https://github.com/compose-spec/compose-spec/blob/master/deploy.md#capabilities) - 文字列のリストとして値を指定（例： `capabilities: [gpu]` ）。このフィールドを Compose ファイル内で指定する必要がある。そうしなければ、サービスのデプロイ時、エラーが返る。
- [count](https://github.com/compose-spec/compose-spec/blob/master/deploy.md#count) - 予約すべき GPU デバイス（ホストが保持している GPU 数）を整数値として指定するか `all` 値で指定。
- [device\_ids](https://github.com/compose-spec/compose-spec/blob/master/deploy.md#device_ids) - ホスト上で予約する GPU デバイスの ID を表す文字列を値として指定。デバイス ID はホスト上で `nvidia-smi` の出力で確認できる。
- [driver](https://github.com/compose-spec/compose-spec/blob/master/deploy.md#driver) - 文字列の値として指定（例： `driver: 'nvidia'` ）
- [options](https://github.com/compose-spec/compose-spec/blob/master/deploy.md#options) - ドライバ固有のオプションを表す キーバリューの組み合わせ (key-value pairs)

注釈

`capabilities` フィールドの指定が必須です。そうしなければ、サービスのデプロイ時にエラーが返ります。 `count` と `device_ids` は互いに矛盾します。フィールドでは、一度に一方しか指定できません。

各プロパティに関する情報は、 [Compose Specification](https://github.com/compose-spec/compose-spec/blob/master/deploy.md#devices) の `deploy` セクションをご覧ください。

この Compose ファイル例は、1 GPU デバイスにアクセスするサービスを実行：

```yaml
services:
  test:
    image: nvidia/cuda:10.2-base
    command: nvidia-smi
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1
              capabilities: [gpu]
```

Docker Compose で実行：

```bash
$ docker-compose up
Creating network "gpu_default" with the default driver
Creating gpu_test_1 ... done
Attaching to gpu_test_1
test_1  | +-----------------------------------------------------------------------------+
test_1  | | NVIDIA-SMI 450.80.02    Driver Version: 450.80.02    CUDA Version: 11.1     |
test_1  | |-------------------------------+----------------------+----------------------+
test_1  | | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
test_1  | | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
test_1  | |                               |                      |               MIG M. |
test_1  | |===============================+======================+======================|
test_1  | |   0  Tesla T4            On   | 00000000:00:1E.0 Off |                    0 |
test_1  | | N/A   23C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
test_1  | |                               |                      |                  N/A |
test_1  | +-------------------------------+----------------------+----------------------+
test_1  |
test_1  | +-----------------------------------------------------------------------------+
test_1  | | Processes:                                                                  |
test_1  | |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
test_1  | |        ID   ID                                                   Usage      |
test_1  | |=============================================================================|
test_1  | |  No running processes found                                                 |
test_1  | +-----------------------------------------------------------------------------+
gpu_test_1 exited with code 0
```

`count` か `device_ids` が設定されていなければ、デフォルトではホスト上で利用可能な GPU すべてを使います。

```yaml
services:
  test:
    image: tensorflow/tensorflow:latest-gpu
    command: python -c "import tensorflow as tf;tf.test.gpu_device_name()"
    deploy:
      resources:
        reservations:
          devices:
            - capabilities: [gpu]
```

```bash
$ docker-compose up
Creating network "gpu_default" with the default driver
Creating gpu_test_1 ... done
Attaching to gpu_test_1
test_1  | I tensorflow/stream_executor/platform/default/dso_loader.cc:48] Successfully opened dynamic library libcudart.so.10.1
```

マシン上に複数の GPU を取り付けている場合、 `device_ids` フィールドによって対象となる GPU デバイスの指定ができ、 `count` によってサービス用コンテナに割り当てる GPU デバイス数の上限も指定できます。 `count` がホスト上で利用可能な GPU 数よりも超えた場合、デプロイはエラーが出て失敗します。

```bash
$ nvidia-smi
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 450.80.02    Driver Version: 450.80.02    CUDA Version: 11.0     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  Tesla T4            On   | 00000000:00:1B.0 Off |                    0 |
| N/A   72C    P8    12W /  70W |      0MiB / 15109MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
|   1  Tesla T4            On   | 00000000:00:1C.0 Off |                    0 |
| N/A   67C    P8    11W /  70W |      0MiB / 15109MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
|   2  Tesla T4            On   | 00000000:00:1D.0 Off |                    0 |
| N/A   74C    P8    12W /  70W |      0MiB / 15109MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
|   3  Tesla T4            On   | 00000000:00:1E.0 Off |                    0 |
| N/A   62C    P8    11W /  70W |      0MiB / 15109MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
```

GPU-0 と GPU-3 デバイスのみアクセスできるようにする：

```yaml
services:
  test:
    image: tensorflow/tensorflow:latest-gpu
    command: python -c "import tensorflow as tf;tf.test.gpu_device_name()"
    deploy:
      resources:
        reservations:
          devices:
          - driver: nvidia
            device_ids: ['0', '3']
            capabilities: [gpu]
```

```bash
$ docker-compose up
...
Created TensorFlow device (/device:GPU:0 with 13970 MB memory -> physical GPU (device: 0, name: Tesla T4, pci bus id: 0000:00:1b.0, compute capability: 7.5)
...
Created TensorFlow device (/device:GPU:1 with 13970 MB memory) -> physical GPU (device: 1, name: Tesla T4, pci bus id: 0000:00:1e.0, compute capability: 7.5)
...
gpu_test_1 exited with code 0
```

参考

Enabling GPU access with Compose | Docker Documentation

[https://docs.docker.com/compose/gpu-support/](https://docs.docker.com/compose/gpu-support/)