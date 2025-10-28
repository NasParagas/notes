---
title: "OpenCVのDNNモジュールでMNISTをする。(C++編)"
source: "https://qiita.com/yomei_o/items/8a5cffa03e43a7d34b97"
author:
  - "[[yomei_o]]"
published: 2024-10-19
created: 2025-08-26
description: "はじめに OpenCVにはDNNモジュールという、ニューラルネットワークのランタイムライブラリがあります。 このDNNモジュールはいろいろなonnxファイルを読み込んで推論することができます。 OpenCVのMNISTのC++のサンプルコードがないか探してみたんですが、見..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## はじめに

OpenCVにはDNNモジュールという、ニューラルネットワークのランタイムライブラリがあります。  
このDNNモジュールはいろいろなonnxファイルを読み込んで推論することができます。

OpenCVのMNISTのC++のサンプルコードがないか探してみたんですが、見つからなかったので、今回DNNモジュールの使い方の勉強も兼ねてOpenCVでMNISTをやってみたいと思います。

## 準備

OpenCVを使うには事前にcmakeやC++コンパイラ、OpenCVがインストールされていないといけません。

### Linux

Linuxではapt-getでインストールできます。

```text
apt-get install cmake make g++ gcc libopencv-dev
```

### Windows

Windowsでビルドするには以下のサイトをみてcmakeやC++コンパイラやOpenCVを準備をしてください。

WindowsにCmakeを導入  
[https://qiita.com/matskeng/items/c466c4751e1352f97ce6](https://qiita.com/matskeng/items/c466c4751e1352f97ce6)

MinGW-w64のインストール  
[https://qiita.com/yomei\_o/items/2e706a5fa3ac5ffc3a50](https://qiita.com/yomei_o/items/2e706a5fa3ac5ffc3a50)

WindowsでOpenCVをMinGW-w64でビルド  
[https://qiita.com/yomei\_o/items/4d794536e24ffc12e29d](https://qiita.com/yomei_o/items/4d794536e24ffc12e29d)

### Mac

macでも以下のような感じでインストールできます。  
[https://qiita.com/kai\_kou/items/df335eb7ee78229ee46f](https://qiita.com/kai_kou/items/df335eb7ee78229ee46f)

## MNISTの学習済みONNXモデルの取得

MNISTの学習済みONNXモデルは以下のサイトからとってくることができます。  
[https://github.com/onnx/models/tree/main/validated/vision/classification/mnist](https://github.com/onnx/models/tree/main/validated/vision/classification/mnist)

今回はmnist-8.onnxというものを使うことにしました。  
[https://github.com/onnx/models/blob/main/validated/vision/classification/mnist/model/mnist-8.onnx](https://github.com/onnx/models/blob/main/validated/vision/classification/mnist/model/mnist-8.onnx)

## プログラムの作成

適当なディレクトリに「CMakeLists.txt」と「opencv\_mnist.cpp」のファイルを置きます。

opencv\_mnist.cpp

```c++
#include<stdio.h>
#include<iostream>
#include<string>
#include<vector>

#include <opencv2/opencv.hpp>

int main()
{
 unsigned char* p;
 int x=-1, y=-1, n=-1;

 cv::Mat color = cv::imread("tegaki5.png");
 cv::Mat img;
 cv::cvtColor(color, img, cv::COLOR_BGR2GRAY);
 cv::resize(img, img, cv::Size(28, 28));
 img = ~img;

 cv::Mat inpBlob = cv::dnn::blobFromImage(img);
 cv::dnn::Net net = cv::dnn::readNetFromONNX("mnist-8.onnx");

 cv::imwrite("out.png",img);

 net.setInput(inpBlob);
 cv::Mat output = net.forward();

 int i;
 for (i = 0; i < 10; i++) {
  printf("%d : %f \n",i,output.at<float>(i));
 }

 return 0;
}
```

CMakeLists.txt

```text
cmake_minimum_required(VERSION 3.13)
project(test_cmake CXX C)

find_package(OpenCV REQUIRED)

add_executable(test opencv_mnist.cpp)

target_include_directories(test PRIVATE ${OpenCV_INCLUDE_DIRS})
target_link_libraries (test PRIVATE gdi32 comdlg32 ${OpenCV_LIBS})
```

画像は次のものを使用しました。  
tegaki5.png  
[![tegaki5.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3914335/6e93439c-5acb-281a-bed8-9501d37be3b0.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3914335%2F6e93439c-5acb-281a-bed8-9501d37be3b0.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=da5ea90788f3ad8d9db7f67d3a1e2149)

OpenCVのDNNモジュールの使い方は解説するまでもなくとてもシンプルです。  
まず、以下のようにネットワークモデルを読み込みます。

```text
net=cv::dnn::readNetFromONNX("mnist-8.onnx");
```

次に、イメージをfloatの配列に変換します。

```text
blob=cv::dnn::blobFromImage(img);
```

データをネットにセットします。

```text
net.setInput(blob);
```

そして、推論の実行をします。

```text
cv::Mat output = net.forward();
```

## ビルド

LinuxやMacでは先ほど使ったディレクトリでシェルから以下のコマンドを実行すればプログラムがビルドされます。

```text
mkdir build
cd build
cmake ..
make
```

Windowsでビルドする場合はこんな感じでビルドします。

```text
mkdir build
cd build
set OpenCV_DIR=C:\prog\opencv-4.10.0\build\install
cmake -DCMAKE_CXX_COMPILER=x86_64-w64-mingw32-g++ -DCMAKE_C_COMPILER=x86_64-w64-mingw32-gcc -G "MinGW Makefiles" ..
make
```

ビルドが成功すると以下のような感じになります。

```text
make
Consolidate compiler generated dependencies of target test
[ 50%] Building CXX object CMakeFiles/test.dir/opencv_mnist.cpp.obj
[100%] Linking CXX executable test
[100%] Built target test\`\`\`
```

## プログラムの実行

それではビルドしたtestを実行してみます。

```text
./test
0 : -1597.594116
1 : -2466.290771
2 : -1662.498535
3 : 727.113403
4 : -1446.075684
5 : 4535.136719
6 : 1075.813110
7 : -685.803711
8 : -195.729706
9 : 165.380707
```

「5」が一番確立が高く出ました。  
このようにOpenCVのDNNモジュールはとても簡単にonnxを使うことができます。

## おまけ

OpevCVでonnxが使えるようになると、Semantic Segmentationなども簡単にできるようになります。  
Qualcomm社のサイトでは自動運転用のSemantic Segmentationのonnxモデルが公開されています。  
[![s002.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3914335/bd39029b-e300-38e1-91d1-09763047acf6.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3914335%2Fbd39029b-e300-38e1-91d1-09763047acf6.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=dc4906a8e68883f9f88ed6174cebb98f)  
このモデルは最近リリースされたOpevCV4.10のDNNモジュールで動くようになりました。  
これを使えばたった数十行で簡単な自動運転のプログラムを作れます。

DeepLabV3-Plus-MobileNet  
[https://aihub.qualcomm.com/compute/models/deeplabv3\_plus\_mobilenet](https://aihub.qualcomm.com/compute/models/deeplabv3_plus_mobilenet)

OpenCVのDNNモジュールを使ったMNISTでした。

[0](https://qiita.com/yomei_o/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fyomei_o%2Fitems%2F8a5cffa03e43a7d34b97&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fyomei_o%2Fitems%2F8a5cffa03e43a7d34b97&realm=qiita)

[2](https://qiita.com/yomei_o/items/8a5cffa03e43a7d34b97/likers)

いいねしたユーザー一覧へ移動

2