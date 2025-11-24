---
title: "【StereoDepth】stereo depth estimationの基礎理解(Deep Learning編)"
source: "https://qiita.com/minh33/items/6b8d37ce08f85d3a3479"
author:
  - "[[minh33]]"
published: 2020-09-16
created: 2025-09-22
description: "Stereo CameraによるDepth(Disparity)推定についてざっくりと流れをまとめて見ました！ 一般的なstereo matchingについては前の記事をどうぞ https://qiita.com/minh33/items/55717aa1ace9d7f7e..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から3年以上が経過しています。

Stereo CameraによるDepth(Disparity)推定についてざっくりと流れをまとめて見ました！

一般的なstereo matchingについては前の記事をどうぞ  
[https://qiita.com/minh33/items/55717aa1ace9d7f7e7dd](https://qiita.com/minh33/items/55717aa1ace9d7f7e7dd)

## Depth Estimationのプロセス

1\. Feature Extraction =>左右同じNetwork,同じweightを使って特徴取り出す(WxHxC)。ex)intensity, shape,.....etc 2. Cost Volume =>Pixelをshiftする事でDisparity Channelを作る(DxWxHxC)。 Dは任意の値で、取り得る最大Disparity(pixel)を自分で決める。大きすぎると重くなるし、小さすぎると近い物体のマッチングがされない。 3. 3D Feature Matching =>求めた特徴量を(DxWxHxC)を畳み込む事で右と左の特徴量が近い所に大きな値が出るよう学習する(DxWxHx1) 4. Disparity Regression =>(DxWxH)から(1xWxH)に変換して最終的なDisparityを求める 5. Lossを計算する 　　=>Ground TruthにLiDARを使うか左と右の画像をwarpすることでLossを計算する。最近では画像のみで学習するのが多く見られる。

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/482094/1b59d540-4b65-543b-c2d0-adad430a0084.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F482094%2F1b59d540-4b65-543b-c2d0-adad430a0084.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=3016b2303eb2ed075f241a105aedda5a)

## Computing the Stereo Matching Cost with a Convolutional Neural Network(2015)

右と左の画像の特徴量をintensityの代わりにconvolutionすることによって、多チャンネルの特徴量に代替することによって精度を向上  
[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/482094/0415b038-b362-b23b-f7fa-56a4f34bc089.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F482094%2F0415b038-b362-b23b-f7fa-56a4f34bc089.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=4363749ec80efc1d6ea05416faced68b)

## Spatial Pyramid Pooling in Deep Convolutional Networks for Visual Recognition(2015)

近くの物体をmatchingさせるためにはより広範囲のpixelを参照しなければいけない。その解決方法として特徴量マップの解像度を細かいものと粗いものを結合させることにより解決  
[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/482094/1878385a-63bc-e87a-cce7-5cfb7c11d765.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F482094%2F1878385a-63bc-e87a-cce7-5cfb7c11d765.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=d18f3c79c2c3bc194c69e2bb42777ec3)

## End-to-End Learning of Geometry and Context for Deep Stereo Regression(2017)

  
今までと同じように右と左の画像をそれぞれに同じweightを使って畳込み特徴量マップ(WxHxC)を生成。右の画像に対して左の画像のpixelを0~maxDisparity(任意)まで横にshiftさせた特徴量マップ(DxWxHxC)を作る。単純にpixelを横(width方向)にシフトするだけ。3D Convolutionと3D Deconvolutionを1/2,1/4,1/8,1/16,1/32で行う事によって大まかな特徴と細かい特徴を学習できる。ここでの出力が(DxHxW)となる。1次元になったmatchingの値にDisparityを掛けて重み付け平均を取ることで最終的なDisparityを出力する。softArgMinによりsub-pixel accuracyでDisparityを求めることが可能になった。

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/482094/4297c09e-a51c-60f9-b399-e1ca28498225.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F482094%2F4297c09e-a51c-60f9-b399-e1ca28498225.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=d97c1254e6d80f097a6aef196e017a6b)

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/482094/32060acb-7dc1-5918-63c3-87540f1188c0.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F482094%2F32060acb-7dc1-5918-63c3-87540f1188c0.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=490d0d5d83f48fe0d633eef344205214)

## Self-Supervised Learning for Stereo Matching with Self-Improving Ability(2017)

  
今までLossを求めるのにDisparityまたはDepthをLiDARから得ていた。LiDARの密度は画像よりも粗く、LiDARを用いないシステムでもTrainingをする為、右の画像を推定した右のDisparityだけpixelをshiftすることによって、左の画像を擬似的に生成する。生成された左の画像と、元の左の画像をSAD(intensityまたはRGBの差)やSSIM(構造的類似性)を見ることで、Lossを定義出来る。Disparityが正しく推定出来ているのであれば、warpをした画像はほぼ反対の画像と同じになる。

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/482094/3be3f881-984c-b180-76b4-66143c903c27.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F482094%2F3be3f881-984c-b180-76b4-66143c903c27.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=f35661974f82e02cbaadb1da8d1a127b)

[0](https://qiita.com/minh33/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fminh33%2Fitems%2F6b8d37ce08f85d3a3479&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fminh33%2Fitems%2F6b8d37ce08f85d3a3479&realm=qiita)

[6](https://qiita.com/minh33/items/6b8d37ce08f85d3a3479/likers)

いいねしたユーザー一覧へ移動

5