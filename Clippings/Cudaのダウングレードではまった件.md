---
title: "Cudaのダウングレードではまった件"
source: "https://qiita.com/rionet/items/33d52e40a67b9c7bcba3"
author:
  - "[[rionet]]"
published: 2023-08-23
created: 2025-08-26
description: "Cudaのダウングレードに関する備忘録 Cudaの再インストールで最新のcudaがインストールされてしまう現象の解決のための備忘録。 WSL2 - Ubuntu 22.04.3 LTS cuda version 12.2 -> 11.8 先に結論 aptのsources..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から1年以上が経過しています。

## Cudaのダウングレードに関する備忘録

Cudaの再インストールで最新のcudaがインストールされてしまう現象の解決のための備忘録。

WSL2 - Ubuntu 22.04.3 LTS  
cuda version 12.2 -> 11.8

## 先に結論

aptのsources.list.dに最新のcudaを指すアーカイブリストが残っていたため

```shell
$ cd /etc/apt/sources.list.d/
$ ls
archive_uri-https_developer_download_nvidia_com_compute_cuda_repos_wsl-ubuntu_x86_64_-jammy.list
cuda-wsl-ubuntu-11-8-local.list
.....
```

上記の"archive\_uri-https\_developer\_download\_nvidia\_com\_compute\_cuda\_repos\_wsl-ubuntu\_x86\_64\_-jammy.list"を削除して再インストール実行

```shell
$ sudo rm -rf archive_uri-https_developer_download_nvidia_com_compute_cuda_repos_wsl-ubuntu_x86_64_-jammy.list
$ sudo apt update
Get:1 file:/var/cuda-repo-wsl-ubuntu-11-8-local  InRelease [1575 B]
Get:1 file:/var/cuda-repo-wsl-ubuntu-11-8-local  InRelease [1575 B]
Hit:2 http://security.ubuntu.com/ubuntu jammy-security InRelease
Hit:3 http://archive.ubuntu.com/ubuntu jammy InRelease
Hit:4 http://archive.ubuntu.com/ubuntu jammy-updates InRelease
Get:5 http://archive.ubuntu.com/ubuntu jammy-backports InRelease [109 kB]
Fetched 109 kB in 2s (53.7 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
All packages are up to date.
$ sudo apt install -y cuda
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  alsa-topology-conf alsa-ucm-conf at-spi2-core ca-certificates-java cuda-11-8 cuda-cccl-11-8
  cuda-command-line-tools-11-8 cuda-compiler-11-8 cuda-cudart-11-8 cuda-cudart-dev-11-8
  cuda-cuobjdump-11-8 cuda-cupti-11-8 cuda-cupti-dev-11-8 cuda-cuxxfilt-11-8 cuda-demo-suite-11-8
  cuda-documentation-11-8 cuda-driver-dev-11-8 cuda-gdb-11-8 cuda-libraries-11-8
....
```

確認

ちなみに nvidia-smiは

```shell
$ nvidia-smi
Wed Aug 23 10:09:43 2023
+---------------------------------------------------------------------------------------+
| NVIDIA-SMI 535.98.01              Driver Version: 536.99       CUDA Version: 12.2     |
|-----------------------------------------+----------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |         Memory-Usage | GPU-Util  Compute M. |
|                                         |                      |               MIG M. |
|=========================================+======================+======================|
|   0  NVIDIA GeForce RTX 3070        On  | 00000000:01:00.0  On |                  N/A |
|  0%   52C    P8              12W / 220W |    583MiB /  8192MiB |      2%      Default |
|                                         |                      |                  N/A |
+-----------------------------------------+----------------------+----------------------+

+---------------------------------------------------------------------------------------+
| Processes:                                                                            |
|  GPU   GI   CI        PID   Type   Process name                            GPU Memory |
|        ID   ID                                                             Usage      |
|=======================================================================================|
|    0   N/A  N/A        22      G   /Xwayland                                 N/A      |
+---------------------------------------------------------------------------------------+
```

'nvidia-smi'のCUDA VersionはDriverが対応する最新のVersionを示すらしい。  
'nvcc -V'を使って確認するのが正解。

### 後で思えば．．．

'apt update'で不要なものが表示されていた．．．

```shell
$ sudo apt update
Get:1 file:/var/cuda-repo-wsl-ubuntu-11-8-local  InRelease [1575 B]
Get:1 file:/var/cuda-repo-wsl-ubuntu-11-8-local  InRelease [1575 B]
　　　　　　　　　　　　　　↓↓↓↓↓↓↓
Hit:2 https://developer.download.nvidia.com/compute/cuda/repos/wsl-ubuntu/x86_64  InRelease 
　　　　　　　　　　　　　　↑↑↑↑↑↑↑　コレ!!
Hit:3 http://security.ubuntu.com/ubuntu jammy-security InRelease
Hit:4 http://archive.ubuntu.com/ubuntu jammy InRelease
Hit:5 http://archive.ubuntu.com/ubuntu jammy-updates InRelease
Get:6 http://archive.ubuntu.com/ubuntu jammy-backports InRelease [109 kB]
Fetched 109 kB in 2s (50.2 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
All packages are up to date.
```

インストール・再インストールを何度も試していたので、どこかで紛れ込んだのだと思いますが、これが最新のものを指しているとは気づきませんでした．．．

## 以下は通常のcuda削除から再インストールの手順の備忘録

### cudaの削除

以下のサイトの情報を参考にしました。  
[https://qiita.com/harmegiddo/items/86b295ccf96eff489e02](https://qiita.com/harmegiddo/items/86b295ccf96eff489e02)

```shell
sudo apt-get --purge remove nvidia*
sudo apt-get --purge remove cuda*
sudo apt-get --purge remove cudnn*
sudo apt-get --purge remove libnvidia*
sudo apt-get --purge remove libcuda*
sudo apt-get --purge remove libcudnn*
sudo apt-get autoremove
sudo apt-get autoclean
sudo apt-get update
sudo rm -rf /usr/local/cuda*
```

### cudaの再インストール

Nvidiaのサイトの手順に従う  
（最後の"apt update", "apt install"を実行する前にsource.list.dの確認をすることが大事かと）

[0](https://qiita.com/rionet/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Frionet%2Fitems%2F33d52e40a67b9c7bcba3&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Frionet%2Fitems%2F33d52e40a67b9c7bcba3&realm=qiita)

[8](https://qiita.com/rionet/items/33d52e40a67b9c7bcba3/likers)

いいねしたユーザー一覧へ移動

4