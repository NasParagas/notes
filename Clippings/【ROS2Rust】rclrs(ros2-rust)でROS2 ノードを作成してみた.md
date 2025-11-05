---
title: "【ROS2/Rust】rclrs(ros2-rust)でROS2 ノードを作成してみた"
source: "https://qiita.com/koichi_baseball/items/4e4d5892781683a64398"
author:
  - "[[koichi_baseball]]"
published: 2024-12-15
created: 2025-11-05
description: "はじめに この記事は、ROS2 Advent Calendarの16日目の記事です！ 今回はROS2のRustライブラリの1つであるrclrustを使用してROS2ノードを作成してみたのでそれをまとめます。基本的にはこの記事と公式のREADMEを参考にしています。 ..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=3538023)

## はじめに

この記事は、ROS2 Advent Calendarの16日目の記事です！

今回はROS2のRustライブラリの1つであるrclrustを使用してROS2ノードを作成してみたのでそれをまとめます。基本的には [この記事](https://foxglove.dev/blog/first-steps-using-rust-with-ros2) と公式のREADMEを参考にしています。

ROS2のRustライブラリについては [@MrBearing(岡本 拓海)](https://qiita.com/MrBearing) さんが以下のようにまとめてくれているのでぜひ参考にしてください。

## 環境

以下の環境で動作を確認しています。

| 項目 | バージョン |
| --- | --- |
| Ubuntu | 22.04 |
| ROS | Humble |
| rustc | 1.82.0 |

## 環境構築

公式のREADMEに沿って環境構築をしていきます。

```bash
sudo apt install -y git libclang-dev python3-pip python3-vcstool # libclang-dev is required by bindgen
# Install these plugins for cargo and colcon:
pip install git+https://github.com/colcon/colcon-cargo.git
pip install git+https://github.com/colcon/colcon-ros-cargo.git

mkdir -p workspace/src && cd workspace
git clone https://github.com/ros2-rust/ros2_rust.git src/ros2_rust
vcs import src < src/ros2_rust/ros2_rust_humble.repos
. /opt/ros/humble/setup.sh
colcon build
```

ここまでで、以下のpubliser/subscriberの動作確認ができていれば無事環境は整いました。

## ROSノードの作成

今回は、文字列を受け取ってその文字数をpublishするノードを作成します。

## パッケージの作成

`ros2 pkg create` コマンドは対応していないようなので、 `cargo new` でパッケージを作成する必要があります。

```bash
cd ~/rust_ws/src
cargo new string_length_node
cd string_length_node
```

## 依存関係の追加

`package.xml` は自動生成されないので自分で作成します。

package.xml

```xml
<package format="3">
  <name>string_length_node</name>
  <version>0.0.0</version>
  <description>TODO: Package description</description>
  <maintainer email="user@todo.todo">user</maintainer>
  <license>TODO: License declaration</license>

  <depend>rclrs</depend>
  <depend>std_msgs</depend>

  <export>
    <build_type>ament_cargo</build_type>
  </export>
</package>
```

Cargo.tomlにも以下を追加します。

```toml
[dependencies]
rclrs = "*"
std_msgs = "*"
```

## ノードの作成

main.rsを以下のように編集します。  
このノードは、 `std_msgs::msg::String` 型のデータを `string_topic` トピックで `subscribe` し、その文字列の長さを `std_msgs::msg::UInt8` 型で `string_length` トピックへpublishします。

main.rs

```rust
// Rust imports
use std::sync::{Arc, Mutex};
// rclrs related imports
use std_msgs::msg::String as StringMsg;
use std_msgs::msg::UInt8 as UInt8Msg;

// Define the struct for the node
struct StringLengthNode {
    node: Arc<rclrs::Node>,
    _subscription: Arc<rclrs::Subscription<StringMsg>>,
    data: Arc<Mutex<Option<StringMsg>>>,
    publisher: Arc<rclrs::Publisher<UInt8Msg>>,
}

// Define the implementation of the node.
impl StringLengthNode {
// This function is called when creating the node.
    fn new(context: &rclrs::Context)-> Result<Self, rclrs::RclrsError>{
        let node = rclrs::Node::new(context, "string_length_node")?;
        let data = Arc::new(Mutex::new(None));
        let data_cb = Arc::clone(&data);
        let _subscription = node.create_subscription(
            "string_topic", rclrs::QOS_PROFILE_DEFAULT,
            move |msg: StringMsg| {
                *data_cb.lock().unwrap() = Some(msg);
            },
        )?;
        let publisher = node.create_publisher("string_length", rclrs::QOS_PROFILE_DEFAULT)?;
        // Return Ok with the constructed node
        Ok(Self{
            node,
            _subscription,
            publisher,
            data,
        })
    }
    // This function is called when publishing
    fn publish(&self) -> Result<(), rclrs::RclrsError> {
        // Get the latest data from the subscription
        if let Some(s) = &*self.data.lock().unwrap() {
            let mut length_msg = UInt8Msg { data: 0 };
            length_msg.data = s.data.len() as u8;
            self.publisher.publish(length_msg)?;
        }
        Ok(())
    }
} // impl StringLengthNode

fn main() -> Result<(), rclrs::RclrsError>{
    println!("Hello, world! - String length node.");
    // Create the rclrs context.
    let context = rclrs::Context::new(std::env::args())?;
    // Create a node and a clone. The first one will subscribe and the clone will publish
    let string_length_node = Arc::new(StringLengthNode::new(&context)?);
    let string_length_publish_node = Arc::clone(&string_length_node);
    // Thread for timer to publish
    std::thread::spawn(move || -> Result<(), rclrs::RclrsError> {
        loop {
            use std::time::Duration;
            std::thread::sleep(Duration::from_millis(1000));
            string_length_publish_node.publish()?;
        }
    });
    // Spin the subscription node
    rclrs::spin(Arc::clone(&string_length_node.node))
}
```

## コンパイル&動作確認

いつも通り `colcon build` と `ros2 run` コマンドでできます。

```bash
cd ~/rust_ws/
colcon build
```

```bash
# In a new terminal
. ./install/setup.sh
ros2 run string_length_node string_length_node
# In a new terminal
. ./install/setup.sh
ros2 topic pub /string_topic std_msgs/msg/String "{data: Hello}"
# In a new terminal
. ./install/setup.sh
ros2 topic echo /string_length
```

`ros2 topic echo /string_length` のターミナルが以下のようになっていれば動作確認完了です！

```text
$ ros2 topic echo /string_length
data: 5
---
data: 5
---
data: 5
```

また、 `ros2 topic pub /string_topic std_msgs/msg/String "{data: Hello}"` でのpublishする文字列の文字数を変えると `string_length` トピックの中身も変わります。

## ROSノードの簡単な解説

まず最初にノードの構造体を定義します。Rustには継承がないため、rclcppやrclpyなどのようにNodeから継承はすることができませんが、構造体のメンバとしては定義することができます。ノード、Subscription、Subscribeしたデータ、publisherをメンバにもつ構造体を定義します。

ノードの構造体

```rust
struct StringLengthNode {
    node: Arc<rclrs::Node>,
    _subscription: Arc<rclrs::Subscription<StringMsg>>,
    data: Arc<Mutex<Option<StringMsg>>>,
    publisher: Arc<rclrs::Publisher<UInt8Msg>>,
}
```

Subscribeしたデータを保持するために、以下の対応をする必要があります。

1. `data: Arc<Mutex<Option<StringMsg>>>` で宣言した `data` への参照を作成
2. Subscriptionのコールバック関数内で `data` を扱うために、新しい参照を作成(そのままだとRustの所有権のルールに反する)
3. コールバック関数を作成し、外部の変数(`data_cb`)を所有する必要があるので `move` を使用
4. `Mutex` で変数をロックしてSubscribeした値を保存

Subscription

```rust
let data = Arc::new(Mutex::new(None));
        let data_cb = Arc::clone(&data);
        let _subscription = node.create_subscription(
            "string_topic", rclrs::QOS_PROFILE_DEFAULT,
            move |msg: StringMsg| {
                *data_cb.lock().unwrap() = Some(msg);
            },
        )?;
```

rclrsには `create_timer` のようなタイマーの設定はないため自分でスレッドを作成してループさせます。

main関数内

```rust
std::thread::spawn(move || -> Result<(), rclrs::RclrsError> {
        loop {
            use std::time::Duration;
            std::thread::sleep(Duration::from_millis(1000));
            string_length_publish_node.publish()?;
        }
    });
```

## 所感

`ros2 pkg create` が使えないことや、それに伴って `package.xml` を自分で作成しなければならないことなど、まだC＋＋やPythonのノードのように気軽には作成できない部分もありました。今後に期待したいです！

また、公式のドキュメントにもある通り、Subscribeしたデータの取り扱いなどがRustの言語仕様によって少し見慣れないものになりました。

## 最後に

まだまだほかの言語と比べるとRust×Roboticsの情報や事例は少ないですが今後色々触っていきたいです。

## 参考情報

[0](https://qiita.com/koichi_baseball/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

## Qiita Conference 2025 Autumn 11月5日(水)~7日(金)開催！

![](https://cdn.qiita.com/assets/public/official_campaigns/qiita_conference_2025_autumn/image-conference_2025_autumn_ogp-d35d2500cd0420d93f2ed69a8d162d74.png)

Qiita Conferenceは、AI時代のエンジニアに贈るQiita最大規模のテックカンファレンスです！

基調講演ゲスト(敬称略)

piacere、牛尾 剛、Esteban Suarez、和田 卓人、seya、ミノ駆動、市谷 聡啓、からあげ、岩瀬 義昌、まつもとゆきひろ、みのるん、 Null-Sensei

[イベント詳細を見る](https://qiita.com/official-campaigns/conference/2025-autumn)

## Qiita Advent Calendar 開催！

[7](https://qiita.com/koichi_baseball/items/4e4d5892781683a64398/likers)

いいねしたユーザー一覧へ移動

3