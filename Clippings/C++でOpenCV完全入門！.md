---
title: "C++でOpenCV完全入門！"
source: "https://zenn.dev/turing_motors/articles/daf3e8829f0967"
author:
  - "[[Zenn]]"
published: 2023-01-27
created: 2025-08-21
description:
tags:
  - "clippings"
---
[Tech Blog - Turing](https://zenn.dev/p/turing_motors) [Publicationへの投稿](https://zenn.dev/faq#what-is-publication)

114

5[tech](https://zenn.dev/tech-or-idea)

![](https://storage.googleapis.com/zenn-user-upload/46ddb26dc20a-20221221.jpg)

この記事は「自動運転システムをエッジデバイスに組み込むための技術」を3回に分けて紹介するTURINGのテックブログ連載の第1回の記事「C++でOpenCV完全入門！」です。  
第2回の「 [OpenCVをNPPにした結果→10倍高速に！](https://zenn.dev/turing_motors/articles/14a6ea47794535) 」、第3回の「 [詳解V4L2 (video for linux 2)](https://zenn.dev/turing_motors/articles/programming-v4l2) 」もぜひご覧ください！

## はじめに

こんにちは。完全自動運転EVを開発するベンチャー企業、 [TURING株式会社](https://www.turing-motors.com/) でインターンをしている東大工学部3年の [井上信多郎](https://twitter.com/shin0805__) です。

我々人類は、車を運転するにあたって多くの情報を目から取り入れています。目から取り入れた情報を元に、アクセル・ブレーキ・ハンドルを操作しています。  
自動運転の場合、その目に相当するセンサがカメラであり、カメラから得た情報を元に車を運転することになります。カメラから得る情報とは、ずばり画像です。画像の中から信号、標識、前方車両などの必要なものを正確に発見するには、画像の中から注目したい部分を切り取ったり、見やすいように回転させたりする必要があります。つまり、画像を自由自在に扱う技術が自動運転を実現するには不可欠ということです。

画像を扱うライブラリにOpenCVがあります。OpenCVを使うことで、画像に対する様々な操作や処理を簡単に行うことができるようになります。  
この記事は、OpenCVの入門記事です。以下のステップで、OpenCVの基本的な使い方を説明します。

- STEP 0: 環境構築
- STEP 1: 画像を表示してみよう
- STEP 2: 画像を白黒にしてみよう
- STEP 3: 直線を検出してみよう
- STEP 4: Webカメラを使ってみよう
- STEP 5: Webカメラの出力に変換を施そう
- STEP 6: ホモグラフィ変換をやってみよう

以上のステップを追うことによって、TURINGの自動運転でAIが推論をするまでに行なっている、フロントガラスに取り付けたカメラから画像を受け取り、適切な処理を施す流れを実装ベースで理解することができるようになります。

OpenCVは主にC++とPythonで用いられますが、今回はC++での使い方を取り上げます。  
登場するソースコードは全てGitHubに上げているので、参考にしてください。

## STEP 0: 環境構築

OpenCVを使うために、 `libopencv-dev` を以下のコマンドでインストールします。

```bash
sudo apt install libopencv-dev
```

また、コンパイルのために `cmake` と `g++` を使うので以下のコマンドでインストールします。

```bash
sudo apt install cmake
sudo apt install g++
```

上記、環境構築では、開発環境としてUbuntuを採用しています。WindowsやmacOSの場合は以下のリンクを参照してください。

- Windows: [OpenCV 4.6.0 のインストール，動作確認（Windows 上）](https://www.kkaneko.jp/tools/win/opencv46.html)
- macOS: [C++ opencv 環境構築 (Mac) - Qiita](https://qiita.com/toshi_machine/items/908e9be57e8afa629159)

## STEP 1: 画像を表示してみよう

OpenCVは画像を簡単に扱うことができるライブラリです。

手始めに、好きな画像を表示するコードを書いてみましょう。

```cpp
#include <opencv2/opencv.hpp>

int main()
{
    // image.pngをimgに代入
    cv::Mat img = cv::imread("../image.jpg");

    // imgの表示
    cv::imshow("img", img);

    // キーが押されるまで待機
    cv::waitKey(0);

    return 0;
}
```

- `cv::Mat` とは、画像を保存できる変数の型です。
- `cv::imread` という関数で、画像ファイルを `cv::Mat` へ変換します。実行場所から画像への相対パスを引数に持ちます。
- `cv::imshow` という関数で、 `cv::Mat` を表示することができます。

コンパイルするためにCMakeLists.txtを書きます。

```cpp
cmake_minimum_required(VERSION 2.8.3)
project(step1)
set(SOURCE_FILE main.cpp)

# OpenCVのビルド環境の設定ファイルを読み込む
find_package(OpenCV REQUIRED)

# OpenCV関係のインクルードディレクトリのパスを設定
include_directories(
    ${OpenCV_INCLUDE_DIRS}
)

# 実行ファイル名を設定
add_executable(main
    ${SOURCE_FILE}
)

# リンクするライブラリを読み込む
target_link_libraries(main
    ${OpenCV_LIBRARIES}
)
```

このような構成になりました。image.jpgが表示したい画像です。

```bash
.
├── CMakeLists.txt
├── image.jpg
└── main.cpp
```

コンパイルして、実行してみましょう。

```bash
mkdir build
cd build
cmake ..
make
./main
```

以下のように、画像が表示されました。

![](https://storage.googleapis.com/zenn-user-upload/2cf1bd050dff-20221221.png)

任意のキーを押して終了できます。

## STEP 2: 画像を白黒にしてみよう

カラー画像を白黒画像にしてみます。

```cpp
#include <opencv2/opencv.hpp>

int main()
{
    // image.pngをimgに代入
    cv::Mat img = cv::imread("../image.jpg");

    // imgを白黒にしてimg_binに代入
    cv::Mat img_bin;
    cv::cvtColor(img, img_bin, cv::COLOR_BGR2GRAY);

    // imgの表示
    cv::imshow("img_bin", img_bin);

    // キーが押されるまで待機
    cv::waitKey(0);

    return 0;
}
```

- `cv::cvtColor` とは、画像の色を別の形式へ変換するものです。元の画像、変換後の画像、変換方法の3つを引数に取ります。

コンパイルして実行すると、白黒になった画像が表示されました。

![](https://storage.googleapis.com/zenn-user-upload/6e333c2a43b6-20221221.png)

ここで、OpenCVが画像をどのように `cv::Mat` に保存しているのかを考えてみましょう。

画像は画素が集まってできたもので、画素一つ一つが色の情報を持っています。色はBGRの3つの値（Blue, Green, Red）で表現されることが多いです。縦の画素数をrows、横の画素数をcolsと呼びます。

![](https://storage.googleapis.com/zenn-user-upload/f401155fe71a-20221221.png)

つまり、BGR画像は3つの行列で保存できるということです。いくつの行列で保存しているかをチャンネル数と呼び、BGR画像ならチャンネル数は3ということになります。  
一方、白黒画像では1つの画素には明るさの程度しか保存されていません。つまり、チャンネル数は1ということになります。

以下のコードを追記して、チャンネル数を確認してみましょう。

```cpp
// チャンネル数の確認
std::cout << "img     : " << img.channels() << " channel" << std::endl;
std::cout << "img_bin : " << img_bin.channels() << " channel" << std::endl;
```

実行結果は以下のようになり、確かにチャンネル数が3から1になったことが確認できます。

```bash
img     : 3 channel
img_bin : 1 channel
```

`cv::Mat` では、要素の型とチャンネル数を合わせてtypeを表現します。  
今回のBGR画像では、要素の型はサイズが8ビットのunsinged char、チャンネル数は3なので、typeは `CV_8UC3` と表現されます。同様に白黒画像は `CV_8UC1` です。

ちなみに、BGR画像を保存した `cv::Mat` を先頭アドレスから順番に展開していくと、以下のように並んでいます。

![](https://storage.googleapis.com/zenn-user-upload/5738761a98cc-20230128.png)

また、以下のように並んでいるとも考えることができ、その場合では画像1行あたりの画素数をstepと呼びます。

![](https://storage.googleapis.com/zenn-user-upload/3393cdc76aa9-20230128.png)

## STEP 3: 直線を検出してみよう

色を変更することにとどまらず、OpenCVを使えば様々なことが関数1つでできます。ここでは、画像から直線を検出してみましょう。

まずは、Canny法によるエッジ検出をしてみます。たった1行で書けます。

```cpp
cv::Canny(img, img_canny, 500.0, 700.0);
```

- `cv::Canny` でCanny法によるエッジ検出ができます。元画像、出力画像、閾値2つ、の計4つの引数を持ちます。
- [cv::Cannyの公式ドキュメント](https://docs.opencv.org/4.x/dd/d1a/group__imgproc__feature.html#ga04723e007ed888ddf11d9ba04e2232de)

以下のような画像になりました。画像のエッジが検出できていることがわかります。

![](https://storage.googleapis.com/zenn-user-upload/59482311c9f3-20221221.png)

次にこの画像に対して、ハフ変換を用いることで、直線を検出してみます。同じく1行です。

```cpp
cv::HoughLines(img_canny, lines, 0.5, CV_PI / 360, 80);
```

- `cv::HoughLines` でハフ変換ができます。元画像、直線の保存先、距離分解能、角度分解能、閾値の計5つの引数を持ちます。
- [cv::HoughLinesの公式ドキュメント](https://docs.opencv.org/4.x/dd/d1a/group__imgproc__feature.html#ga46b4e588934f6c8dfd509cc6e0e4545a)

検出した直線を画像に重ね書きして、表示してみましょう。

```cpp
std::vector<cv::Vec2f>::iterator it = lines.begin();
for (; it != lines.end(); ++it) {
  float rho = (*it)[0], theta = (*it)[1];
  cv::Point pt1, pt2;
  double a = cos(theta), b = sin(theta);
  double x0 = a * rho, y0 = b * rho;
  pt1.x = cv::saturate_cast<int>(x0 + 1000 * (-b));
  pt1.y = cv::saturate_cast<int>(y0 + 1000 * (a));
  pt2.x = cv::saturate_cast<int>(x0 - 1000 * (-b));
  pt2.y = cv::saturate_cast<int>(y0 - 1000 * (a));
  // 直線描画
  cv::line(img, pt1, pt2, cv::Scalar(0, 0, 255), 3, cv::LINE_AA);
}
```

- `cv::line` で2点間を通る直線を描くことができます。

![](https://storage.googleapis.com/zenn-user-upload/92ff59c5f7b7-20221221.png)

画像中の直線を検出して表示することができました。

## STEP 4: Webカメラを使ってみよう

OpenCVを使えば、Webカメラにアクセスして、画像を取得することができます。

```cpp
#include <opencv2/opencv.hpp>

int main() {
  cv::Mat img;
  // /dev/video0でVideoCaptureを宣言
  cv::VideoCapture cap(0);

  while (true) {
    // カメラの画像をimgに代入
    cap.read(img);

    // imgの表示
    cv::imshow("VideoCapture", img);

    // escで終了
    unsigned char key = cv::waitKey(2);
    if (key == '\x1b') break;
  }

  return 0;
}
```

- `cv::VideoCapture` を使うことでカメラにアクセスできるようになります。コンストラクタの引数でデバイス番号を取ります。
- `cv::VideoCapture::read` でカメラの画像を読み出すことができます。引数に画像の代入先を取ります。

コンパイルして実行すると、Webカメラの画像がリアルタイムで表示されます。

![](https://storage.googleapis.com/zenn-user-upload/393d64178552-20221221.png)

escキーを押すと終了します。

## STEP 5: Webカメラの出力に変換を施そう

Webカメラから取得した画像も、同じ `cv::Mat` であることに変わりないので、これまでに述べてきたような画像処理を施すことができます。

例えば以下のように、Webカメラから取得した画像に対して直線検出を行い、その結果から水平な5本の直線を選択することで、楽譜の五線譜を認識することができます。

```cpp
for (; it != lines.end(); ++it) {
  float rho = (*it)[0], theta = (*it)[1];
  cv::Point pt1, pt2;
  double a = cos(theta), b = sin(theta);
  double x0 = a * rho, y0 = b * rho;

  // 認識点の描画
  cv::circle(dst_img, cv::Point(x0, y0), 2, cv::Scalar(255, 0, 0), -1, cv::LINE_AA);

  // 一本の線で複数の線を読まないようにする and 水平な線に近いものを読む
  if (std::hypot(x0 - past_x0, y0 - past_y0) >= 16 && abs(theta - CV_PI / 2) < 0.1) {
    // 認識した五線の描画
    pt1.x = cv::saturate_cast<int>(x0 + 1300 * (-b));
    pt1.y = cv::saturate_cast<int>(y0 + 1300 * (a));
    pt2.x = cv::saturate_cast<int>(x0 - 1300 * (-b));
    pt2.y = cv::saturate_cast<int>(y0 - 1300 * (a));
    cv::line(dst_img, pt1, pt2, cv::Scalar(0, 0, 255), 3, cv::LINE_AA);
    if (line_y_index < 10) {
      line_x[line_y_index] = x0;
      line_y[line_y_index] = y0;
      line_th[line_y_index] = theta;
      line_y_index++;
    }
  }
  past_x0 = x0;
  past_y0 = y0;
}
```

その周辺で音符を探索し、その座標に見合った音階の音を鳴らせば、Webカメラで認識した楽譜をリアルタイムに音へ変換するシステムを作ることができます。

<iframe src="https://www.youtube-nocookie.com/embed/MTcwfKafDHc" allow="accelerometer; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen=""></iframe>

ここまで来れば、OpenCVを使って様々なことが実現できると思います。

## STEP 6: ホモグラフィ変換をやってみよう

最後に実際にTURINGでも活用している技術の基本的な使い方を紹介したいと思います。

TURINGでは、カメラから取得した画像に適切な変換を施し、AIによる推論を行うことで自動運転を行っています。その変換の1つにホモグラフィ変換があります。ホモグラフィ変換とは、2つの画像平面の対応関係を行列式で表し、片方の画像平面をもう一方に変換することです。

![](https://storage.googleapis.com/zenn-user-upload/43ca12a21a7b-20230125.png)

（図は [OpenCV: Basic concepts of the homography explained with code](https://docs.opencv.org/4.0.0/d9/dab/tutorial_homography.html) より引用）

同じ場所に設置したカメラでも実際に見てみると微妙に見え方がずれてしまうことがあります。AIの入力として画像を使う場合、このような映像のズレは少ないほうがAIの予測精度が向上することが多いです。ホモグラフィ変換を用いることで、カメラの取り付け方による見え方の誤差を吸収することができます。ここではOpenCVを使って、2枚の画像の特徴点を検出し、それらのマッチングを行い、ホモグラフィ変換行列の算出から変換までの手順を説明します。

変換したい画像（Source画像）と画角を合わせたい画像（Target画像）を用意し、4ステップでホモグラフィ変換をおこないます。

1. それぞれの画像で特徴点を抽出。ここでは [SIFT](https://docs.opencv.org/4.0.0/d2/dca/group__xfeatures2d__nonfree.html#gace6ee881f1443844fee1091169976208) という手法を用いて特徴点の抽出と記述子(Descriptor)の算出を行っています。
	```cpp
	// SIFTによるキーポイントの抽出
	std::vector<cv::KeyPoint> keypoints_source, keypoints_target;
	cv::Ptr<cv::Feature2D> f2d = cv::xfeatures2d::SiftFeatureDetector::create();
	// キーポイントを見つける
	f2d->detect(img_gray_source, keypoints_source);
	f2d->detect(img_gray_target, keypoints_target);
	// キーポイントのDescriptor（特徴ベクトルを計算する）
	cv::Mat descriptors_source, descriptors_target;
	f2d->compute(img_gray_source, keypoints_source, descriptors_source);
	f2d->compute(img_gray_target, keypoints_target, descriptors_target);
	```
2. 類似している特徴点をマッチング。 [Brute-force descriptor matcher（BFMatcher）](https://docs.opencv.org/4.0.0/d3/da1/classcv_1_1BFMatcher.html) の中のk最近傍法を用いて特徴点のマッチングを行い、良い感じにマッチした特徴点のみを使うようにしています。
	```cpp
	// BFMatcherを使ってDescriptorのマッチングを行う
	cv::BFMatcher matcher;
	// k最近防法を用いたマッチング
	std::vector<std::vector<cv::DMatch>> knn_matches;
	matcher.knnMatch(descriptors_source, descriptors_target, knn_matches, 20);
	// Loweのratio testでマッチングをフィルタリングする
	const float ratio_thresh = .75f;
	std::vector<cv::DMatch> matches;
	for (size_t i = 0; i < knn_matches.size(); i++) {
	  if (knn_matches[i][0].distance <
	      ratio_thresh * knn_matches[i][1].distance) {
	    matches.push_back(knn_matches[i][0]);
	  }
	}
	// フィルタリングされたマッチングのキーポイントを求める
	std::vector<cv::Point2f> obj;
	std::vector<cv::Point2f> scene;
	for (size_t i = 0; i < matches.size(); i++) {
	  obj.push_back(keypoints_source[matches[i].queryIdx].pt);
	  scene.push_back(keypoints_target[matches[i].trainIdx].pt);
	}
	```
3. マッチングさせた特徴点の組み合わせから3x3のホモグラフィ変換行列を算出しています。
	```cpp
	// ホモグラフィ変換行列の推定
	cv::Mat masks;
	cv::Mat H = cv::findHomography(obj, scene, masks, cv::RANSAC, 3);
	```
4. 算出したホモグラフィ変換行列を使って画像の射影変換をします。
	```cpp
	// 算出したホモグラフィ変換行列を用いてperspective変換
	cv::Mat img_transformed;
	cv::warpPerspective(img_source, img_transformed, H, img_transformed.size());
	```

マッチングの結果（左がSource画像、右がTarget画像）

![](https://storage.googleapis.com/zenn-user-upload/1bd6b932e265-20230125.jpg)

変換後の画像（左のSource画像が回転、拡大縮小して右のTarget画像と同じような姿勢になっている）

![](https://storage.googleapis.com/zenn-user-upload/1c1c17c3d11e-20230125.jpg)

ここではキーポイントの計算にSIFTを、キーポイントのマッチングにk最近傍法を用いましたが他にもOpenCVに様々な関数が用意されています。（この辺りはOpenCVのバージョンによって変わることがあるので使うときは公式ドキュメントを参照してください。）

- **[Feature Detection and Description](https://docs.opencv.org/4.0.0/d5/d51/group__features2d__main.html)**
- **[Descriptor Matchers](https://docs.opencv.org/4.0.0/d8/d9b/group__features2d__match.html)**

さらに最近はDeep Learningを用いた手法もあり、特徴点が抽出しづらい画像でも簡単にホモグラフィ変換行列の算出ができるようになってきました。

AIの推論用以外にも車の周囲に取り付けたカメラ映像をつなげてサラウンドビューを表示するといった技術にもホモグラフィ変換行列は活用されています。TURINGでも試しにサラウンドビューを作ってみたりしてました。

![](https://storage.googleapis.com/zenn-user-upload/ae623c235385-20230125.png)

このようにTURINGではカメラから取得した映像を用いて様々な技術開発に取り組んでいます。

## おわりに

読んでいただきありがとうございました。OpenCVを使えば、画像を簡単に取り扱うことができる上に、様々な変換も関数1つで実行できるということがわかったと思います。

しかしながら、車に搭載するようなエッジデバイスではCPUの性能が低い上に使えるメモリも極小なので、OpenCVでカメラから画像を受け取り変換を施すということを行っていては、処理が間に合いません。

TURINGでは、このような問題を解決するためにNPP(NVIDIA Performance Primitives)やv4l2(video for linux 2)を駆使して、OpenCVを使うことなく高速な画像処理を実現しました。「自動運転システムをエッジデバイスに組み込むための技術」第2回の記事ではNPPを使った高速な画像処理、第3回の記事ではv4l2を使ったカメラへのアクセス技術を紹介します。ぜひそちらもご覧ください。

完全自動運転を実現するには、最高に賢い頭脳を担うAIを開発するのはもちろんのこと、今回ご紹介した画像処理や、さらにそれをエッジデバイスで行う低レイヤな技術など、本当に幅広い分野のテクノロジーが必要です。

そのような広く大きな課題へ挑戦したい！という方は、是非、TURINGへ。

## 問い合わせ先

TURINGでの開発業務にご興味をお持ち頂けた方は、 [求人一覧](https://herp.careers/v1/turing) 、 [Wantedly](https://www.wantedly.com/companies/turing-motors/projects) をご覧下さい。

その他、気になる点や質問等ございましたら、お気軽にTwitterのDMをお送りください。共同代表山本、青木どちらもDMを開放しております。([@issei\_y](https://twitter.com/issei_y), [@aoshun7](https://twitter.com/aoshun7))

114

5

114

5