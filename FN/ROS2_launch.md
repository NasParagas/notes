[Launch — ROS 2 Documentation: Humble documentation](https://docs.ros.org/en/humble/Tutorials/Intermediate/Launch/Launch-Main.html)
- 同じcppファイルを違うnodeで起動したい場合はnamespaceを変える

### 1. あるNodeが起動したことをトリガーにする (`OnProcessStart`)

これは、先ほど使った`OnProcessExit`の対になる`OnProcessStart`というイベントハンドラを使います。`A`というNodeが起動したことを検知して、`B`というNodeを起動させることができます。
`frame_publisher`が完全に起動してから`frame_sub_detect_pub`を起動する例を以下に示します。
```python
from launch import LaunchDescription
from launch.actions import LogInfo, RegisterEventHandler
from launch.event_handlers import OnProcessStart
from launch_ros.actions import Node

def generate_launch_description():
    # 先に起動させたいNode (トリガーとなるNode)
    frame_publisher_node = Node(
        package="cpp_pubsub",
        executable="frame_publisher",
        name="frame_publisher",
        output="screen"
    )

    # frame_publisher_nodeが起動した後に起動させたいNode
    frame_sub_detect_pub_node = Node(
        package="cpp_pubsub",
        executable="frame_sub_detect_pub",
        name="frame_sub_detect_pub",
        output="screen"
    )

    return LaunchDescription([
        # まずはトリガーとなるNodeをリストに含める
        frame_publisher_node,
        
        # イベントハンドラを登録
        RegisterEventHandler(
            # OnProcessStartイベントハンドラを使用
            event_handler=OnProcessStart(
                # 監視対象のNodeを指定
                target_action=frame_publisher_node,
                # ターゲットが起動した際に実行するアクションを指定
                on_start=[
                    LogInfo(msg='frame_publisherが起動しました。frame_sub_detect_pubを起動します。'),
                    frame_sub_detect_pub_node  # ここで後から起動したいNodeのアクションを記述
                ]
            )
        )
    ])
```

この方法のポイントは、**後から起動したい`frame_sub_detect_pub_node`を`LaunchDescription`のトップレベルのリストには含めず、`OnProcessStart`の`on_start`リストの中に入れる**ことです。これにより、Launchファイルの起動時には`frame_publisher`だけが起動し、その起動が完了したことをトリガーとして`frame_sub_detect_pub`が起動する、という依存関係を明確に定義できます。

### 2. 指定した秒数後に起動する (`TimerAction`)

特定のNodeの起動状態に関係なく、単純に「Launch開始からN秒後」に起動したい場合は、**`TimerAction`** を使います。
例えば、Launch開始から5秒後に`detection_result_subscriber`を起動する例は以下のようになります。

```python
from launch import LaunchDescription
from launch.actions import TimerAction
from launch_ros.actions import Node

def generate_launch_description():
    # すぐに起動するNode
    frame_publisher_node = Node(
        package="cpp_pubsub",
        executable="frame_publisher",
        name="frame_publisher",
        output="screen"
    )

    # 5秒後に起動させたいNode
    delayed_subscriber_node = Node(
        package="cpp_pubsub",
        executable="detection_result_subscriber",
        name="detection_result_subscriber",
        output="screen"
    )

    return LaunchDescription([
        frame_publisher_node,
        
        # TimerActionを使って遅延実行を実現
        TimerAction(
            period=5.0,  # 遅延させたい秒数を浮動小数点数で指定
            actions=[
                delayed_subscriber_node  # 遅延実行したいNodeのアクションをリストで渡す
            ]
        )
    ])
```

この方法も同様に、遅延させたいNodeは`TimerAction`の`actions`リストの中に入れます。`period`に秒数を指定するだけで簡単に遅延起動が実装できます。