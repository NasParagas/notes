---
title: "Ubuntuのパッケージを壊してしまった場合"
source: "https://tech.fusic.co.jp/posts/2022-12-06-ubuntu-packages-fix/"
author:
published:
created: 2025-08-26
description: "Ubuntuを使用する時、パッケージをインストール出来なくなる事がありますが、その時の対応方法について説明します。"
tags:
  - "clippings"
---
![Author](https://tech.fusic.co.jp/logo.png) Teodor TOSHKOV

2022/12/15

## パッケージが見つからない場合

### エラーメッセージ

```
$ sudo apt-get install <パッケージ名>

Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
E: Unable to locate package <パッケージ名>
```

または

```
E: Package '<パッケージ名>' has no installation candidate
```

### 解決方法

第一歩として `sudo apt-get update` をしましょう。

パッケージがまだ見つからない場合、Ubuntuのデフォルトパッケージリスト（下記）に入っていないを意味します。

```
/etc/apt/sources.list
```

以下のコマンドでリストを追加できますが、適切なパッケージはインストールしたいパッケージによって異なります。 Googleで検索することをおすすめします。

```
$ sudo apt-key adv --fetch-keys <パッケージリストのURL>
$ sudo apt-get update
```

## パッケージのコンフリクトがあった場合

パッケージのコンフリクトがあった場合、既にインストールされているパッケージの依存関係ツリーとインストールしようとしているパッケージの依存関係ツリーのバージョン非互換です。そのため、パッケージをインストールする時のログを注意し、確認する事で新たな問題が発生してしまう可能性を減らせます。

### エラーメッセージ

以下のようなエラーメッセージが表示された場合、パッケージのコンフリクトがある事を意味します。

```
$ sudo apt-get install <パッケージ名>

Some packages could not be installed. This may mean that you have
requested an impossible situation or if you are using the unstable
distribution that some required packages have not yet been created
or been moved out of Incoming.
The following information may help to resolve the situation:
n
The following packages have unmet dependencies:
 nvidia-jetpack : Depends: nvidia-jetpack-runtime (= 5.0.1-b118) but it is not going to be installed
                  Depends: nvidia-jetpack-dev (= 5.0.1-b118) but it is not going to be installed
E: Unable to correct problems, you have held broken packages.
```

### aptで解決する方法

コンフリクトを解決出来ない事もありますが、 `apt` の全ての問題解決コマンドを試してみる事をおすすめします。その他のパッケージの問題もある場合に役に立ちます。

```
$ sudo apt-get update
$ sudo apt-get clean
$ sudo apt-get autoremove
$ sudo apt-get autoclean
$ sudo apt-get --fix-broken install
$ sudo apt-get upgrade
$ sudo apt-get dist-upgrade
```

自分で1つずつの関連のパッケージの適切なバージョンをインストールする事でコンフリクトを解決できますが、関連のパッケージの関連のパッケージなども含むと数パッケージから数百パッケージのバージョンを自分で修正する必要があるかもしれません。

残念ながら、 `apt` はコンフリクトに得意ではありませんが、その代わりの `aptitude` というツールがあります。

### aptitudeについて

`aptitude` が `apt` と違い、コンフリクトがあった時、複数の解決方法を表示してくれます。 それでもコンフリクトが解決されない場合もありますが、得られる情報が `apt` より多いため、おすすめします。

`aptitude` がUbuntuのデフォルトパッケージマネージャーではないため、インストールする必要があります。

```
$ sudo apt-get install aptitude
```

`aptitude` でのパッケージのインストールコマンドが `apt` でのコマンドと同じであり、使いやすいだと思います。

```
$ sudo aptitude install <パッケージ名>
```

コマンド実行の結果の例を紹介いたします。

```
The following NEW packages will be installed:
  nvidia-container{ab} nvidia-jetpack nvidia-jetpack-dev{a} nvidia-jetpack-runtime{a} 
0 packages upgraded, 4 newly installed, 0 to remove and 0 not upgraded.
Need to get 117 kB of archives. After unpacking 795 kB will be used.
The following packages have unmet dependencies:
 nvidia-container : Depends: nvidia-docker2 (= 2.10.0-1) but 2.11.0-1 is installed
                    Depends: libnvidia-container-tools (= 1.9.0-1) but 1.11.0-1 is installed
                    Depends: nvidia-container-toolkit (= 1.9.0-1) but 1.11.0-1 is installed
                    Depends: libnvidia-container1 (= 1.9.0-1) but 1.11.0-1 is installed
                    Depends: nvidia-container-runtime (= 3.9.0-1) but 3.11.0-1 is installed
The following actions will resolve these dependencies:

     Keep the following packages at their current version:
1)     nvidia-container [Not Installed]                   
2)     nvidia-jetpack [Not Installed]                     
3)     nvidia-jetpack-dev [Not Installed]                 
4)     nvidia-jetpack-runtime [Not Installed]             

Accept this solution? [Y/n/q/?] n
```

最初紹介されるコンフリクトの解決方法が `apt` と同様であるため、 `n` を記入し、スキップします。

```
The following actions will resolve these dependencies:

     Remove the following packages:                                                  
1)     nvidia-container-toolkit-base [1.11.0-1 (bionic, now)]                        

     Downgrade the following packages:                                               
2)     libnvidia-container-tools [1.11.0-1 (bionic, now) -> 1.9.0-1 (bionic, stable)]
3)     libnvidia-container1 [1.11.0-1 (bionic, now) -> 1.9.0-1 (bionic, stable)]     
4)     nvidia-container-runtime [3.11.0-1 (bionic, now) -> 3.9.0-1 (bionic, stable)] 
5)     nvidia-container-toolkit [1.11.0-1 (bionic, now) -> 1.9.0-1 (bionic, stable)] 
6)     nvidia-docker2 [2.11.0-1 (bionic, now) -> 2.10.0-1 (bionic, stable)]          

Accept this solution? [Y/n/q/?] Y
```

この例で次に紹介された解決方法が `downgrade` でした。 変更されるパッケージを確認し、問題がなさそうだっただめ `Y` を記入しました。

```
The following packages will be DOWNGRADED:
  libnvidia-container-tools libnvidia-container1 nvidia-container-runtime nvidia-container-toolkit nvidia-docker2 
The following NEW packages will be installed:
  nvidia-container{a} nvidia-jetpack nvidia-jetpack-dev{a} nvidia-jetpack-runtime{a} 
The following packages will be REMOVED:
  nvidia-container-toolkit-base{a} 
0 packages upgraded, 4 newly installed, 5 downgraded, 1 to remove and 0 not upgraded.
Need to get 1,817 kB of archives. After unpacking 4,485 kB will be freed.
Do you want to continue? [Y/n/?] Y
```

変更されるパッケージの再度の確認の上 `Y` を記入しますとパッケージが実際にインストールし始めます。

```
Get: 1 https://nvidia.github.io/libnvidia-container/stable/ubuntu18.04/arm64  nvidia-docker2 2.10.0-1 [5,536 B]
...
```

#### 途中で得られるエラー

ただし、途中で以下のようなエラーが表示されることもあります。

```
debconf: delaying package configuration, since <依存パッケージの名前> is not installed
```

その場合はそのパッケージを事前にインストールし、 `aptitude install` を再度実行する必要があります。

```
$ sudo apt-get install <依存パッケージの名前> -y
$ sudo aptitude install <パッケージ名>
```

または、以下のなエラーメッセージも表示される事があります。

```
trying to overwrite '/etc/.../config.toml', which is also in package <依存パッケージの名前> <パッケージのバージョン>
```

その場合、事前に<依存パッケージの名前>を削除してから、または全ての依存パッケージをアンインストールしてから `aptitude install` します。

```
$ sudo apt-get remove <依存パッケージの名前> -y
$ sudo aptitude install <パッケージ名>
```

## まとめ

Ubuntuのパッケージ管理が自由であるため、 `apt` のパッケージの依存関係ツリーを壊してしまう事があります。

本記事ではOSを再インストールせずに問題を解決する方法をいくつもの紹介させていただきました。

`apt` が対応出来ない場合、情報が多いため、 `aptitude` の使用をおすすめします。

この記事を参考に、いくつもの問題の解決に役に立てるよう願っています。

![Teodor TOSHKOV](https://tech.fusic.co.jp/logo.png)

## Teodor TOSHKOV

I am an intern at Fusic, a company in Fukuoka, Japan. From 2022, I will be joining the Machine Learning team. I develop mostly deep learning models, using PyTorch and Tensorflow.