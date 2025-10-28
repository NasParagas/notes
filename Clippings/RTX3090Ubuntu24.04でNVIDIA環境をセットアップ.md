---
title: "RTX3090/Ubuntu24.04でNVIDIA環境をセットアップ"
source: "https://qiita.com/genmai99/items/c0e6ffda4e23394bccd8"
author:
  - "[[genmai99]]"
published: 2024-08-15
created: 2025-08-26
description: "はじめに 私は機械学習用にRTX3090のデスクトップPCを1台持っており、定期的にメンテナンスを行なっています。 今回、RTX3090環境へUbuntu24.04をインストールしたので、その作業内容をメモとして公開します。 前回は基本的なRTX3090環境でのUbun..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から1年以上が経過しています。

## はじめに

私は機械学習用にRTX3090のデスクトップPCを1台持っており、定期的にメンテナンスを行なっています。  
今回、RTX3090環境へUbuntu24.04をインストールしたので、その作業内容をメモとして公開します。

[前回](https://qiita.com/genmai99/items/501ace251eaefbcb3649) は基本的なRTX3090環境でのUbuntu24.04セットアップを行いました。  
今回はNVIDIAの環境を整えます。

なお、関連する記事は下記の通りです。

- [RTX3090環境へのUbuntu24.04セットアップ](https://qiita.com/genmai99/items/501ace251eaefbcb3649)
- (本記事)RTX3090/Ubuntu24.04でNVIDIA環境をセットアップ
- [Githubの初期設定](https://qiita.com/genmai99/items/ff700738bb87dfbbba14)
- [画像認識の環境構築とVSCodeの設定](https://qiita.com/genmai99/items/3d02a722772e1135518d)
- [私のVSCodeのカスタマイズ](https://qiita.com/genmai99/items/5e4cc56420f51a80828a)

## 目次

1. [ドライバのインストール](https://qiita.com/genmai99/items/#%E3%83%89%E3%83%A9%E3%82%A4%E3%83%90%E3%81%AE%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB)
2. [CUDA Toolkitのインストール](https://qiita.com/genmai99/items/#cuda-toolkit%E3%81%AE%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB)
3. [pytorchのインストール](https://qiita.com/genmai99/items/#pytorch%E3%81%AE%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB)
4. [最後に](https://qiita.com/genmai99/items/#%E6%9C%80%E5%BE%8C%E3%81%AB)
5. [参考文献](https://qiita.com/genmai99/items/#%E5%8F%82%E8%80%83%E6%96%87%E7%8C%AE)

## ドライバのインストール

- nouveauを無効化

```text
$ echo 'blacklist nouveau' | sudo tee -a /etc/modprobe.d/blacklist-nouveau.conf
$ echo 'options nouveau modeset=0' | sudo tee -a /etc/modprobe.d/blacklist-nouveau.conf
$ cat /etc/modprobe.d/blacklist-nouveau.conf
$ sudo update-initramfs -u
```

- グラフィックボードが認識されているか確認

```text
$ lspci | grep -i nvidia
01:00.0 VGA compatible controller: NVIDIA Corporation GA102 [GeForce RTX 3090] (rev a1)
01:00.1 Audio device: NVIDIA Corporation GA102 High Definition Audio Controller (rev a1)
```

- nvidia ドライバのRecommendを確認
- 今入っているドライバのバージョンを確認

```text
$ dpkg -l | grep nvidia
```

- ここで推奨ではない古いドライバが入っていた場合
	- 古いドライバの削除
		```text
		$ sudo apt --purge remove nvidia-*
		$ sudo apt autoremove
		```
	- 推奨されているドライバをインストール
		```text
		$ sudo apt -y install nvidia-driver-560
		```
	- 再起動
		```text
		$ reboot
		```
- GPUのサマリを表示
	```text
	$ nvidia-smi
	 +-----------------------------------------------------------------------------------------+
	 | NVIDIA-SMI 560.28.03              Driver Version: 560.28.03      CUDA Version: 12.6     |
	 |-----------------------------------------+------------------------+----------------------+
	 | GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
	 | Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
	 |                                         |                        |               MIG M. |
	 |=========================================+========================+======================|
	 |   0  NVIDIA GeForce RTX 3090        Off |   00000000:01:00.0 Off |                  N/A |
	 |  0%   38C    P8             28W /  370W |      15MiB /  24576MiB |      0%      Default |
	 |                                         |                        |                  N/A |
	 +-----------------------------------------+------------------------+----------------------+
	                                                                                          
	 +-----------------------------------------------------------------------------------------+
	 | Processes:                                                                              |
	 |  GPU   GI   CI        PID   Type   Process name                              GPU Memory |
	 |        ID   ID                                                               Usage      |
	 |=========================================================================================|
	 |    0   N/A  N/A      1842      G   /usr/lib/xorg/Xorg                              4MiB |
	 +-----------------------------------------------------------------------------------------+
	```

以上でドライバのインストールが完了しました。  
次に、CUDA Toolkitをインストールします。

## CUDA Toolkitのインストール

- CUDA 12.6のTookitを確認する
	- [こちら](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=24.04&target_type=deb_network) で自分の環境を選択し、実行手順を確認
- aptに追加するキーをダウンロード（Ubuntu24.04用のアドレスであることに注意）

```text
$ wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb
```

- キーをaptに追加

```text
$ sudo dpkg -i cuda-keyring_1.1-1_all.deb
$ sudo apt update
```

- CUDA Toolkitのインストール

```text
$ sudo apt -y install cuda-toolkit-12-6
```

- CUDA Toolkitのパスを.bashrcに追加（これが通っていないと、この後のnvccコマンドが通りません）

```text
$ echo "export PATH=/usr/local/cuda:/usr/local/cuda/bin:$PATH" >> .bashrc
$ echo "export LD_LIBRARY_PATH=/usr/local/lib:/usr/local/cuda/lib64:$LD_LIBRARY_PATH" >> .bashrc
$ source ~/.bashrc
```

- パスが通ったか確認（バージョンが表示されればOK）

## pytorchのインストール

### venvの環境を作る

今回は環境名をtestとします。

- pythonのバージョンを確認し、venvをインストール

```　
$ python3 -V
Python 3.12.3
$ sudo apt install python3.12-venv
```

- workディレクトリを作成し、その中に環境名のディレクトリを作って移動

```text
$ mkdir work
$ mkdir work/test
$ cd work
```

- 仮装環境を作成

```　
$ python3 -m venv test
```

- アクティベート

```text
$ source test/bin/activate
```

- 仮装環境testにインストール

```text
$ pip install --upgrade pip
$ pip install torch torchvision torchaudio
```

- GPUが使える状態になっているか確認(Trueが帰って来ればOK)
- GPU確認に続けて、計算をしてみる(下記の通り'cuda:0'表示されればOK)

```text
>>> array = torch.zeros(4) #長さ4の配列を定義（これはcpu配列）
>>> print(array)
tensor([0., 0., 0., 0.])
>>> array_gpu = array.cuda()
>>> print(array_gpu)
tensor([0., 0., 0., 0.], device='cuda:0')
>>> exit()
```

- ディアクティベートして終了する

```text
$ deactivate
```

これで一通りpytorchを使ったGPU計算環境の動作が確認できました。

## 最後に

RTX3090/Ubunt24.04の計算環境を構築しました。  
確認はpytorchだけ行いましたが、基本的には動作できているので他のプラットフォームでも大丈夫だと思います。

## 参考文献

- [【Ubuntu】Nvidia GPUに対応したCudaドライバをセットアップする \[Techblog#13\]](https://www.folklore.place/techblog/2024/06/30)
- [PyTorchでCUDAが使えないときに確認すべき項目](https://note.com/oyasai123/n/n08ac06ea2691)

[0](https://qiita.com/genmai99/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fgenmai99%2Fitems%2Fc0e6ffda4e23394bccd8&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fgenmai99%2Fitems%2Fc0e6ffda4e23394bccd8&realm=qiita)

[2](https://qiita.com/genmai99/items/c0e6ffda4e23394bccd8/likers)

いいねしたユーザー一覧へ移動

2