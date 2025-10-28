---
title: "ROS2とOpenCVによる画像処理入門1"
source: "https://qiita.com/Nkn4ryu/items/861998cb768ddaad18af"
author:
  - "[[Nkn4ryu]]"
published: 2024-04-19
created: 2025-08-21
description: "はじめに ロボット開発のチームでは，自動運転時の画像処理や物体認識に関わる部分の開発を任されることになりました．今回はチュートリアルとして，C++およびopenCVを用いてランダム画像の生成と，生成画像を画面に出力するノードを別々に作成しました． 目次 概要 パッケ..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## はじめに

ロボット開発のチームでは，自動運転時の画像処理や物体認識に関わる部分の開発を任されることになりました．今回はチュートリアルとして，C++およびopenCVを用いてランダム画像の生成と，生成画像を画面に出力するノードを別々に作成しました．

## 目次

1. [概要](https://qiita.com/Nkn4ryu/items/#Abst)
2. [パッケージ作成](https://qiita.com/Nkn4ryu/items/#Chapter1)
3. [Publisher：ランダム画像の生成](https://qiita.com/Nkn4ryu/items/#Chapter2)
4. [Subscriber：画像の出力](https://qiita.com/Nkn4ryu/items/#Chapter2)
5. [動作確認](https://qiita.com/Nkn4ryu/items/#Chapter3)
6. [参考文献](https://qiita.com/Nkn4ryu/items/#reference)

## 概要

　ROS2における機能単位はノードです． [^1] ROS2では複数のノードを1つのプロセスで動かすことができ，その実行単位をターゲットと呼びます．ノードひとつひとつに機能を実装することで大きなプロセスを作成することができます．

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3665619/1356093f-f7bc-27c3-4443-e9bd2150db13.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3665619%2F1356093f-f7bc-27c3-4443-e9bd2150db13.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=f9698374bac51965659b3af4c072404b)  
※引用　Eureka Box（ユーリカボックス）【体験版】ROS =連載第2回 [^2]

　今回のチュートリアルでは画像を生成し，その画像を画面に出力するという機能を実装します（＝ターゲット）．  
　具体的には，ノード間でのトピック通信というメッセージ方式を用いて実装します．トピック通信は非同期・単方向の通信です．メッセージを発信する側と受信する側をそれぞれpublisher, subscriberと呼びます．　

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3665619/fb809bd9-ce76-1d8b-ab33-71cd6cb7d9e7.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3665619%2Ffb809bd9-ce76-1d8b-ab33-71cd6cb7d9e7.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=1a7a17489a0dab4283526be437a577fc)  
※引用　Eureka Box（ユーリカボックス）【体験版】ROS =連載第2回 <sup><a href="https://qiita.com/Nkn4ryu/items/#fn-2">2</a></sup>

　今回のチュートリアルでは画像を生成し，その画像を画面に出力するという機能（＝ターゲット）を実装したいので，画像生成をpublisherノード，その画像を受け取って画面に出力するノードをsubscriberとして実装しました．

## パッケージ作成

　今回のチュートリアル用にパッケージを作成します．  
以下のコマンドを実行します．コマンドではパッケージで用いるライブラリなど依存関係を指定することが可能で，

- rclcpp:ROS2の機能をC++で使用する
- std\_msgs,sensor\_msgs:標準的およびセンサー関連のメッセージを扱う
- cv\_bridge，image\_transportは画像データを効率的に変換および転送
- OpenCV:画像の生成および操作

これらのライブラリを指定しています．

```terminal
cd ~/ros2_ws/src
~/ros2_ws/src$ ros2 pkg create my_opencv_demo --dependencies rclcpp std_msgs sensor_msgs cv_bridge image_transport OpenCV
```

このコマンド実行後,以下のように新たなディレクトリとファイルが作成されます．

```bash
ros2_ws/
├── build/            # ビルドされたファイルが格納される
├── install/          # インストールされたファイルが格納される
├── log/              # ビルドのログが格納される
└── src/              # ソースファイルが格納される
    └── my_opencv_demo/　　　 # 新たに作成されたディレクトリ・ファイル
        ├── CMakeLists.txt      # CMakeのビルド設定ファイル
        ├── package.xml         # パッケージのメタデータと依存関係
        ├── include/            # C++ヘッダーファイル用
        │   └── my_opencv_demo/
        ├── src/                # パッケージのソースファイル用　→ここにノードのソースコードを置く
        └── launch/             # launchファイル用
```

これでパッケージ作成の準備が整いました．

## Publiseher:ランダム画像の生成

　画像を生成し，publishするノードを作成していきます．概要にも述べたとおり，このノードは各ピクセルがランダムカラーの画像を500msで生成・パブリッシュします．

ソースコードは以下の折りたたみ部分をご確認ください．MinimalImagePublisherクラスがrclcpp::Nodeを継承しています．このノードクラスでは定期的にランダムカラーの画像を生成し，パブリッシュするノードを定義しています．画像はOpenCVで生成され、cv\_bridgeを通じてROS2のイメージメッセージに変換されてパブリッシュされます．

ソースコード

image\_generate\_pub.cpp

```c++
#include "rclcpp/rclcpp.hpp"
#include "sensor_msgs/msg/image.hpp"
#include "std_msgs/msg/header.hpp"
#include <chrono>
#include <cv_bridge/cv_bridge.h> // cv_bridge converts between ROS 2 image messages and OpenCV image representations.
#include <image_transport/image_transport.hpp> // Using image_transport allows us to publish and subscribe to compressed image streams in ROS2
#include <opencv2/opencv.hpp> // We include everything about OpenCV as we don't care much about compilation time at the moment.

using namespace std::chrono_literals;

class MinimalImagePublisher : public rclcpp::Node {
public:
  MinimalImagePublisher() : Node("opencv_image_publisher"), count_(0) {
    publisher_ =
        this->create_publisher<sensor_msgs::msg::Image>("random_image", 10);
    timer_ = this->create_wall_timer(
        500ms, std::bind(&MinimalImagePublisher::timer_callback, this));
  }

private:
  void timer_callback() {
    // Create a new 640x480 image
    cv::Mat my_image(cv::Size(640, 480), CV_8UC3);

    // Generate an image where each pixel is a random color
    cv::randu(my_image, cv::Scalar(0, 0, 0), cv::Scalar(255, 255, 255));

    // Write message to be sent. Member function toImageMsg() converts a CvImage
    // into a ROS image message
    // Declare the text position
    cv::Point text_position(15, 40);
    cv::Point text_position_2(15, 90);
    // Declare the size and color of the font
    int font_size = 1;
    cv::Scalar font_color(255, 255, 255);

    // Declare the font weight
    int font_weight = 2;

    // Put the text in the image
    cv::putText(my_image, "ROS2 + OpenCV Test Image", text_position, cv::FONT_HERSHEY_COMPLEX, font_size, font_color, font_weight);
    cv::putText(my_image, "Test Image : GxP GxP GxP", text_position_2, cv::FONT_HERSHEY_COMPLEX, font_size, font_color, font_weight);

    msg_ = cv_bridge::CvImage(std_msgs::msg::Header(), "bgr8", my_image)
               .toImageMsg();

    // Publish the image to the topic defined in the publisher
    publisher_->publish(*msg_.get());
    RCLCPP_INFO(this->get_logger(), "Image %ld published", count_);
    count_++;
  }
  rclcpp::TimerBase::SharedPtr timer_;
  sensor_msgs::msg::Image::SharedPtr msg_;
  rclcpp::Publisher<sensor_msgs::msg::Image>::SharedPtr publisher_;
  size_t count_;
};

int main(int argc, char *argv[]) {
  rclcpp::init(argc, argv);
  // create a ros2 node
  auto node = std::make_shared<MinimalImagePublisher>();

  // process ros2 callbacks until receiving a SIGINT (ctrl-c)
  rclcpp::spin(node);
  rclcpp::shutdown();
  return 0;
}
```

## Subscribe:画像の出力

トピック通信によって，Publisherが発信した画像のメッセージを受け取って画面に表示するノードを実装します．

ソースコード

image\_sub\_view.cpp

```c++
#include "rclcpp/rclcpp.hpp"
#include "sensor_msgs/msg/image.hpp"
#include <cv_bridge/cv_bridge.h>
#include <opencv2/highgui/highgui.hpp>
#include <opencv2/imgproc/imgproc.hpp>

class MinimalImageSubscriber : public rclcpp::Node
{
public:
  MinimalImageSubscriber() : Node("opencv_image_subscriber")
  {
    // Initialize subscriber
    subscriber_ = this->create_subscription<sensor_msgs::msg::Image>(
      "random_image", 10, std::bind(&MinimalImageSubscriber::image_callback, this, std::placeholders::_1));
  }

private:
  void image_callback(const sensor_msgs::msg::Image::SharedPtr msg)
  {
    try {
      // Convert ROS image message to OpenCV image
      cv::Mat cv_image = cv_bridge::toCvCopy(msg, "bgr8")->image;

      // Display image
      cv::imshow("Subscribed Image", cv_image);
      cv::waitKey(1); // Wait for a key press for 1 millisecond to allow UI events to process
    } catch (cv_bridge::Exception& e) {
      RCLCPP_ERROR(this->get_logger(), "Could not convert from '%s' to 'bgr8'.", msg->encoding.c_str());
    }
  }

  rclcpp::Subscription<sensor_msgs::msg::Image>::SharedPtr subscriber_;
};

int main(int argc, char *argv[])
{
  rclcpp::init(argc, argv);
  auto node = std::make_shared<MinimalImageSubscriber>();
  rclcpp::spin(node);
  rclcpp::shutdown();
  return 0;
}
```

```text
cv::Mat cv_image = cv_bridge::toCvCopy(msg, "bgr8")->image;
```

この部分では **cv\_bridge::toCvCopy(msg, "bgr8")** 関数が，受け取ったメッセージをOpenCVのcv::Matオブジェクトに変換しています．この変換によってOpenCVを用いて他画像処理を受け取ったノードが自由に行えるようになります．

上記の部分で画像を画面に表示しています．今後，内部で画像変換を行ったり，加工を加えた画像をさらに外部のノードに渡したりすることも考えています．詳細は折りたたみのソースコードをご確認ください．

## 動作確認

まずは作成したパッケージをコンパイル・ビルドします．

```terminal
~/ros2_ws$ colcon build --packages-select my_opencv_demo
Starting >>> my_opencv_demo
Finished <<< my_opencv_demo [6.93s]                     

Summary: 1 package finished [7.31s]
```

無事にビルドが完了しました．次にROS2ワークスペースの環境をセットアップします．このコマンドによって，ビルドされたパッケージが利用可能になります．

```terminal
~/ros2_ws$ source ~/ros2_ws/install/setup.bash
```

実際に二つのノードを起動してプロセスを開始してみましょう．以下のコマンドを実行します．こちらは画像を生成するpublisherノードでした．このノードを起動することで生成した画像がpublishされます．

```terminal
~/ros2_ws$ ros2 run my_opencv_demo minimal_opencv_ros2_node
　// minimal_opencv_ros2_nodeは，画像を生成するノード．
```

次に，ターミナルで他のセッションを開始して，subscriberノードも起動します．先ほどのpubliserノードとトピック通信がうまくできていれば画像が表示されるはずです．

　無事に表示されました．画像は毎秒生成されてpublishされるのでストリーミングデータですが，新たな画像を受け取って表示画像を更新できていることがわかります．

[![スクリーンショット 2024-04-19 14.41.27.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3665619/0e36cad5-c5e7-94c3-4b68-2bfee53e40dd.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3665619%2F0e36cad5-c5e7-94c3-4b68-2bfee53e40dd.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=754054a36b0ee09ce667109432d8da0f)

## 参考文献

- [How to integrate OpenCV with a ROS2 C++ node - The Construct](https://www.theconstruct.ai/how-to-integrate-opencv-with-a-ros2-c-node/)

[1](https://qiita.com/Nkn4ryu/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2FNkn4ryu%2Fitems%2F861998cb768ddaad18af&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2FNkn4ryu%2Fitems%2F861998cb768ddaad18af&realm=qiita)

[3](https://qiita.com/Nkn4ryu/items/861998cb768ddaad18af/likers)

いいねしたユーザー一覧へ移動

4

[^1]: [第2回 ROSとROS2のプログラミング概念|コラム](https://www.eureka-box.com/media/column/a41#link02)

[^2]: