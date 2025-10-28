# カスタムmsgとsrvの書き方と例

- `msg`と`srv`自体については書いてない

## 参考

- <https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries/Custom-ROS2-Interfaces.html>

## 事前準備

```bash
# 好きなところで
mkdir -p ros2_ws/src
```

## 準備

```bash
cd ./ros2_ws
ros2 pkg create --build-type ament_cmake --license Apache-2.0 tutorial_interfaces
mv ./tutorial_interfaces ./src
mkdir -p ./src/tutorial_interfaces/msg
mkdir -p ./src/tutorial_interfaces/srv
```

## カスタム`msg`

- `msg`フォルダの中に作成
- 拡張子は`.msg`で、ファイル名をパスカルケースで書く
  - なんでパスカルケースじゃないとダメなんだっけ...
- 使える型には制限がある
  - 基本型でも使えないのがあった気が？
  - 他のros2パッケージの方を再利用することもできる(下の例)
    - 例えば(vectorは使えないので)自分で作った座標用のarray msgを保持するためのarrayとか
- 例(`Sphere.msg`)

```txt
geometry_msgs/Point center
float64 radius
```

## カスタム`srv`

- 上述した`msg`と作成場所や命名法、型の制限などは同じ
- `client`から`request`の際に送られてくるものと、`server`から`response`として返すものを区切って記述する
- 例(AddThreeInts.srv)

```txt
int64 a
int64 b
int64 c
---
int64 sum
```

## CMakeLists.txt

- `find_package`に`rosidl_default_generators`と`msg`,`srv`で使用しているパッケージをいれる
- `rosidl_generate_interfaces`に作成した`msg`,`srv`、依存しているパッケージを記入
- 上の例であればこう

```txt
find_package(geometry_msgs REQUIRED)
find_package(rosidl_default_generators REQUIRED)

rosidl_generate_interfaces(${PROJECT_NAME}
  "msg/Sphere.msg"
  "srv/AddThreeInts.srv"
  DEPENDENCIES geometry_msgs
)
```

## packages.xml

- >Because the interfaces rely on rosidl_default_generators for generating language-specific code, you need to declare a build tool dependency on it. rosidl_default_runtime is a runtime or execution-stage dependency, needed to be able to use the interfaces later. The rosidl_interface_packages is the name of the dependency group that your package, tutorial_interfaces, should be associated with, declared using the <member_of_group> tag.
- 上の例であればこう

```xml
<depend>geometry_msgs</depend>
<buildtool_depend>rosidl_default_generators</buildtool_depend>
<exec_depend>rosidl_default_runtime</exec_depend>
<member_of_group>rosidl_interface_packages</member_of_group>
```

## 完成

- 後で本体コードを書くときにincludeが必要になる&そっちのビルド時にはヘッダー情報が存在していないといけないので、先にビルドしておく

```bash
colcon build --packages-select tutorial_interfaces
```

## 確認

```bash
source install/setup.bash
ros2 interface show tutorial_interfaces/msg/Sphere
ros2 interface show tutorial_interfaces/srv/AddThreeInts
```
