```cpp
#include <memory>

#include "rclcpp/rclcpp.hpp"
#include "example_interfaces/srv/add_two_ints.hpp"

void add(const std::shared_ptr<example_interfaces::srv::AddTwoInts::Request> request,
    std::shared_ptr<example_interfaces::srv::AddTwoInts::Response> response) {
        response -> sum = request -> a + request -> b;
        RCLCPP_INFO(rclcpp::get_logger("rclcpp"), "Incoming request\na: %ld" " b: %ld", request -> a, request -> b);
        RCLCPP_INFO(rclcpp::get_logger("rclcpp"), "sending back responce: [%ld]", (long int)response -> sum);
    }

int main(int argc, char **argv) {
    rclcpp::init(argc, argv);
    std::shared_ptr<rclcpp::Node> node = rclcpp::Node::make_shared("add two_ints_server");
    rclcpp::Service<example_interfaces::srv::AddTwoInts>::SharedPtr service =
        node->create_service<example_interfaces::srv::AddTwoInts>("add_two_ints", &add);

    RCLCPP_INFO(rclcpp::get_logger("rclcpp"), "Ready to add two ints.");

    rclcpp::spin(node);
    rclcpp::shutdown;
}
```



```cpp
void add(const std::shared_ptr<example_interfaces::srv::AddTwoInts::Request> request,
    std::shared_ptr<example_interfaces::srv::AddTwoInts::Response> response) 
```
- requestは送られてくるやつで、この関数の中で書き換えたらやばいから`const`で定義
- std::shared_ptrはスマートポインタ

```C++
publisher_ = this->create_publisher<tutorial_interfaces::msg::Num>("topic", 10);
```
この10はキューサイズ
>この `10` は、**パブリッシャが保持するメッセージのバッファ（送信待ちのメッセージ）の最大数**を意味します。
サブスクライバがメッセージを受信するよりも遅い場合、送信が追いつかず、未送信のメッセージがこのキューにたまります。
キューサイズを超えると、**古いメッセージから破棄される**ことになります（送られない）。

```C++
timer_ = this->create_wall_timer(
    std::chrono::milliseconds(500),
    std::bind(&MinimalPublisher::timer_callback, this)
);
```
### 🔸 各パーツの意味

|部分|説明|
|---|---|
|`std::chrono::milliseconds(500)`|500ミリ秒ごとに実行する間隔（= 0.5秒）|
|`std::bind(&MinimalPublisher::timer_callback, this)`|このクラスのメンバ関数 `timer_callback()` を呼び出すようバインド|
|`this->create_wall_timer(...)`|壁時計ベースのタイマー（実世界の時間に従う）を作成|


msg->header.frame_id = "camera_frame"; これはどういう情報？

###### ChatGPT:

これは ROS 2 のメッセージに含まれる **`frame_id`（フレームID）** という情報で、  
**そのデータが「どの座標フレームに属しているか」**を示す**名前付きの識別子**です。

---

## 🧭 `frame_id` とは？

cpp

コードをコピーする

`msg->header.frame_id = "camera_frame";`

この行は「この画像は `camera_frame` という名前の座標系（TFフレーム）に属していますよ」とROSに伝えています。

---

## ✅ 何のために必要？

`frame_id` は主に以下の目的で使われます：

|用途|説明|
|---|---|
|📦 TF（Transform）との連携|座標変換の基準になるフレーム名（例：`camera_frame` → `base_link` などの変換）|
|🧠 センサデータの空間的意味づけ|カメラやLiDARなど、どこに取り付けられているかを区別するため|
|🧩 統合処理（可視化・SLAM・ナビゲーション）|RVizやナビゲーションスタックでの空間配置の判断基準になる|

---

## ✅ 例：複数センサを使う場合

text

コードをコピーする

`/camera/image_raw         → frame_id: "camera_frame" /lidar/scan               → frame_id: "lidar_link" /imu/data                 → frame_id: "imu_link"`

これらの `frame_id` をもとに、TFツリーを作って、**ロボットの各センサの位置関係を明確化**できます。

---

## ✅ 実用Tips

- `camera_frame` という名前は慣習で、実際は URDF（ロボットモデル）で定義したフレーム名に揃えるべきです。
    
- TF2ライブラリでこの `frame_id` をもとに `transform lookup` を行うのがROSの典型的パターンです。
    

---

## 🔧 よくある frame_id の例

| frame_id        | 意味               |
| --------------- | ---------------- |
| `"base_link"`   | ロボット本体の中心座標系     |
| `"camera_link"` | カメラが取り付けられている座標系 |
| `"odom"`        | オドメトリ空間（移動履歴）    |
| `"map"`         | 地図空間（SLAMやナビで使う） |


