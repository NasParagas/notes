---
title: "ubuntuにCUDA、nvidiaドライバをインストールするメモ"
source: "https://qiita.com/porizou1/items/74d8264d6381ee2941bd"
author:
  - "[[porizou1]]"
published: 2021-01-12
created: 2025-08-26
description: "Nouveau の無効化 sudo gedit /etc/modprobe.d/blacklist-nouveau.conf nouveauの設定ファイルを新規作成して以下を記入して保存する blacklist-nouveau.conf blacklist nouve..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から1年以上が経過しています。

## Nouveau の無効化

```bash
sudo gedit /etc/modprobe.d/blacklist-nouveau.conf
```

nouveauの設定ファイルを新規作成して以下を記入して保存する

blacklist-nouveau.conf

```text
blacklist nouveau
options nouveau modeset=0
```

保存したら以下を実行

```bash
sudo update-initramfs -u
```

## 使用しているGPUの確認

```bash
lspci | grep -i nvidia
```

## 現状入っているCUDA,nvidia-driverの確認

```bash
dpkg -l | grep nvidia
dpkg -l | grep cuda
```

## 現状入っているCUDA nvidiaドライバの削除

参考

```bash
sudo apt-get --purge remove nvidia-*
sudo apt-get --purge remove cuda-*
```

## nvidia-driver のインストール

以下で推奨のドライバを確認

```bash
ubuntu-drivers devices
```

### aptからインストールする方法

インストール（nvidia-driver-460の場合)

```bash
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update
sudo apt install nvidia-driver-460
```

再起動

```bash
sudo reboot
```

以下で確認

```bash
nvidia-smi
```

### .runファイルからインストールする方法

こちらの記事に手順をまとめました

## CUDAのインストール

CUDA Downloadsのページで以下を選択した場合のコマンド

Operating System：Linux  
Architecture：x86\_64  
Distribution：Ubuntu

ubuntu20.04の場合

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-ubuntu2004.pin
sudo mv cuda-ubuntu2004.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/7fa2af80.pub
sudo add-apt-repository "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/ /"
sudo apt-get update
sudo apt-get -y install cuda
```

ubuntu22.04の場合

バージョン指定する場合 （推奨）（指定しない場合最新のCUDAが入る）

```bash
sudo apt-get -y install cuda-11-2
```

~/.bashrcの末尾に以下を追加

```bash
export PATH="/usr/local/cuda/bin:$PATH"
export LD_LIBRARY_PATH="/usr/local/cuda/lib64:$LD_LIBRARY_PATH"
```

バージョンの確認

```bash
nvcc -V
```

## 参考

[2](https://qiita.com/porizou1/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fporizou1%2Fitems%2F74d8264d6381ee2941bd&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fporizou1%2Fitems%2F74d8264d6381ee2941bd&realm=qiita)

[167](https://qiita.com/porizou1/items/74d8264d6381ee2941bd/likers)

いいねしたユーザー一覧へ移動

126