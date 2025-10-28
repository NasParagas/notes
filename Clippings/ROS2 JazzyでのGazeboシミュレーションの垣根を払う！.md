---
title: "ROS2 JazzyでのGazeboシミュレーションの垣根を払う！"
source: "https://zenn.dev/dieu_detruit/articles/dd07e44ba53973"
author:
  - "[[Zenn]]"
published: 2024-11-06
created: 2025-08-26
description:
tags:
  - "clippings"
---
10

3[tech](https://zenn.dev/tech-or-idea)

## 概要

- いま、ROS2 Jazzy (Ubuntu 24.04) でシミュレータ事情に関する情報はやや見つけづらくなっています。名前の変更の歴史のせいで検索が死んでいる状態です。
- 「結局なにをどうすればいいんだーー！！」と思ってググったりChatGPTに聞いたりすると、それなりに沼にハマります。
- 結論から言えば新GazeboとそのROS連携のros\_gz\_simを使うとよいのですが、この記事ではその本質情報についてまとめます。
- ROS2でのxacroの使い方の記事としても役に立てると思います。

![](https://storage.googleapis.com/zenn-user-upload/f4d855ed9ced-20241106.png)

### 忙しい人のためのまとめ

- ROS2 Jazzyでのシミュレーションは **ros\_gz\_sim** を使うべきで、そのサンプルはこれです。
- 現在の公式ドキュメントはこれ。([Migrating Gazebo Classic ROS2 Packages](https://gazebosim.org/docs/latest/migrating_gazebo_classic_ros2_packages/) のBefore AfterのAfterが本質情報っぽくて見やすいです)
- そしてここにはURDFから(/robot\_descriptionから)ロボットをspawnする例がないので書いておくと、以下のrosrunをlaunchなりなんなりですればOKです。

```
$ ros2 run ros_gz_sim create -name <モデル名> -topic /robot_description
```

## 理解を助けるためのGazeboの歴史と、残念なこと

- (開発管理者は変遷がありますが) 当初のシミュレータは長らく "Gazebo" として開発されてきました。ROS1時代に広く使われていたのはこれです。
- 2019年から、Open Robotics (旧OSRF) がアーキテクチャを刷新した "Ignition" として別プロジェクトとしての提供を開始。この時点でGazebo(古い)とIgnition(新しい)がありました。
- 2022年、商標の問題からIgnitionの名前が使えなくなり、"Gazebo"に名前を戻しました。(←元凶！)
- 区別のため、
	- 古いかつてのGazeboを **"Gazebo Classic"**
	- 新しいもの(元Ignition)を **"Gazebo"**
- と呼び分けるようになりました。

### 残念なこと

- 名前の呼び分け自体は上記の通り一応うまく行っているのですが、問題は「インターネット上にある過去の記事を書き換えることはできない」ことです。
- いま"Gazebo"(新しい方を意図)して検索しても、古い方の記事が出てきてしまいます。
- ツールやパッケージとして、ROS1 x Gazebo Classic、ROS2 x Gazebo Classic、ROS2 x Ignition、ROS2 x Gazebo(改名後)の4種類があり、やや混迷を極めている状況。
- ChatGPTなども、なかなかまともな返答を返してくれません。辛い。

## 結局、いま使うものはどれ？

- 検索するときは以下のキーワードを念頭に検索するのがオススメです。
	- OSSとして公開されているロボットのROS2パッケージ群を参考にする際にも。
- ただ、まだ最新のものについての記事はあまり多くない印象です。

### 対応するROSパッケージ

- 現在ROS Jazzyで使うもの
	- [**ros\_gz** シリーズ](https://github.com/gazebosim/ros_gz) (ros\_gz\_sim, ros\_gz\_bridge,...)
	- [**gz\_ros2\_control**](https://github.com/ros-controls/gz_ros2_control)
- 使わないもの
	- ros\_gazebo
	- gazebo\_ros2\_control

### 対応するシミュレータ

- FYI: gazeboは本来ROS非依存で使えるツールで、上記ROSパッケージはそのROSとの連携を担っているものです。下記はシミュレータ本体のことを指しています。
- 現在ROS Jazzyで使うもの
	- [gz](https://github.com/gazebosim/gz-sim)
- 使わないもの
	- ign (改名前のIgnition)
	- gazebo (Gazebo Classic)

## ROS2 JazzyでGazeboによる対向2輪のシミュレーション

- 基本は以下のテンプレートを真似すればいいと思いますが、ロボットのspawnをSDFからやる例なので、ここではURDFから (/robot\_descriptionから) やる例を示します。
- diff drive controllerで/cmd\_velから動かせるまでをゴールとします。
- 私の個人的なHCRというロボットを例に書きます。名前は適宜。
- インストール類はどうせ何かしら変わるので、足りないと思ったパッケージをインストールしてください。

### パッケージ (自由ですが、例として)

- hcr\_description
	- sim/real問わず、自分のロボットのURDFの記述
	- ros2\_control用のjointのインターフェース設定 (ROS1でいうURDF内のtransmissionの設定)
- hcr\_gazebo\_bringup
	- sim用のURDFの記述
	- gazeboのros2\_controlに任せる
- hcr\_diff\_drive\_controller
	- sim/real問わず、diff drive controller(差動2輪の速度制御・エンコーダベースのodometry)の立ち上げ

### FYI 前提知識 (読み飛ばしてどうぞ)

- ros2\_controlでの制御系は主に三者からなり、
	- controller\_manager (コントローラの管理)
	- ハードウェアインターフェース (メタな制御器の出力を実ハードに繋ぐ部分)
	- spawner (外部からコントローラを追加する)
- 前の2つはシミュレーション時はGazebo(ros\_gz\_sim)に任せ、実機のときは自分で書くことになります。

### hcr\_description (自分のロボットの定義)

- これはこの記事で解説する本質部分ではないので、ご参考までに。
- xacroの使い方がわからない人は参考になるかも
- 基本的なURDFの書き方は難しいことはないと思うので、 [公式ドキュメント](https://docs.ros.org/en/jazzy/Tutorials/Intermediate/URDF/URDF-Main.html) を参照したりしてください。
- sim/real問わず、このロボットを扱う上で必要な部分を立ち上げるlaunchはこんな感じです。
- 個人的な(ROS1と比べての)詰まりポイントはxacroの使い方でした。substitutionとしてxacroの実行結果(URDF)を得て、遅延評価でrobot\_state\_publisherに渡すみたいですね。なかなか初心者向きではないなという感想です。
- `start_meta_robot.launch.py`

```python
from launch_ros.actions import Node, SetParametersFromFile
from launch_ros.substitutions import FindPackageShare

from launch import LaunchDescription
from launch.actions import DeclareLaunchArgument
from launch.substitutions import (
    Command,
    FindExecutable,
    LaunchConfiguration,
    PathJoinSubstitution,
)

def generate_launch_description() -> LaunchDescription:
    hcr_description_share_path = FindPackageShare("hcr_description")
    model_file_default = PathJoinSubstitution(
        [hcr_description_share_path, "urdf", "hcr.urdf.xacro"]
    )

    # launch arguments
    model_arg = DeclareLaunchArgument("model", default_value=model_file_default)
    sim_arg = DeclareLaunchArgument("sim", default_value="false")

    # substitutions
    model = LaunchConfiguration("model")
    sim = LaunchConfiguration("sim")

    # xacro to urdf
    robot_description = Command(
        [
            PathJoinSubstitution([FindExecutable(name="xacro")]),
            " ",
            model,
        ]
    )

    # load rosparams for hardware settings
    set_hardware_parameters = SetParametersFromFile(
        filename=PathJoinSubstitution(
            [FindPackageShare("hcr_description"), "config", "robot_hardware.yaml"]
        )
    )

    # add joint_state_publisher and robot_state_publisher
    joint_state_publisher_node = Node(
        package="joint_state_publisher",
        executable="joint_state_publisher",
        output="screen",
        parameters=[{"use_sim_time": sim}],
    )
    robot_state_publisher_node = Node(
        package="robot_state_publisher",
        executable="robot_state_publisher",
        output="screen",
        parameters=[{"robot_description": robot_description}],
    )

    return LaunchDescription(
        [
            model_arg,
            sim_arg,
            set_hardware_parameters,
            joint_state_publisher_node,
            robot_state_publisher_node,
        ]
    )
```

- これをincludeして、かつrvizを立てるlaunchです。(必須ではありません)
- `show_robot.launch.py`

```python
import os

from launch import LaunchDescription
from launch.substitutions import LaunchConfiguration, PathJoinSubstitution
from launch.actions import IncludeLaunchDescription, DeclareLaunchArgument
from launch_ros.actions import Node
from launch_ros.substitutions import FindPackageShare

def generate_launch_description() -> LaunchDescription:
    hcr_description_share_path = FindPackageShare("hcr_description")

    # set default value
    model_file_default = PathJoinSubstitution(
        [hcr_description_share_path, "urdf", "hcr.urdf.xacro"]
    )
    rviz_config_default = PathJoinSubstitution(
        [hcr_description_share_path, "config", "urdf.rviz"]
    )

    # launch arguments
    model_arg = DeclareLaunchArgument("model", default_value=model_file_default)
    rviz_config_arg = DeclareLaunchArgument(
        "rviz_config", default_value=rviz_config_default
    )
    use_sim_arg = DeclareLaunchArgument("use_sim", default_value="false")

    # substitutions
    model = LaunchConfiguration("model")
    rviz_config = LaunchConfiguration("rviz_config")
    use_sim = LaunchConfiguration("use_sim")

    # include start_meta_robot.launch.py
    start_meta_robot_launch = IncludeLaunchDescription(
        launch_description_source=PathJoinSubstitution(
            [hcr_description_share_path, "launch", "start_meta_robot.launch.py"]
        ),
        launch_arguments={"model": model, "sim": use_sim}.items(),
    )

    # start rviz2
    rviz2_node = Node(
        package="rviz2",
        arguments=["-d", rviz_config],
        executable="rviz2",
    )

    return LaunchDescription(
        [model_arg, rviz_config_arg, use_sim_arg, start_meta_robot_launch, rviz2_node]
    )
```

- `robot_hardware.yaml` (ROS1ではtransmissionとしてURDFに書いていた内容)

```yaml
ros2_control:
  ros__parameters:
    update_rate: 50
    joints:
      - name: left_wheel_joint
        command_interfaces:
          - velocity
        state_interfaces:
          - velocity
          - position
      - name: right_wheel_joint
        command_interfaces:
          - velocity
        state_interfaces:
          - velocity
          - position
```

### hcr\_gazebo\_bringup (Gazeboの立ち上げ)

- hcr\_gazebo\_bringupのファイルツリーはこんな感じです

```
.
├── config
│   ├── gz_ros2_control.yaml
│   ├── ros_gz_bridge.yaml
│   └── sim.rviz
├── hcr_gazebo_bringup
│   └── __init__.py
├── launch
│   └── simple_sim.launch.py
├── package.xml
├── resource
│   └── hcr_gazebo_bringup
├── setup.cfg
├── setup.py
├── urdf
│   └── hcr_gazebo.urdf.xacro
└── worlds
    └── empty.world
```

- まず、全体を俯瞰すべく、gazeboのシミュレーションを立ち上げるlaunchファイルから。
- やっていることは以下です。
	- hcr\_descriptionにある、joint state publisherとrobot state publisher、rvizの立ち上げのlaunchの呼び出し
	- WaylandではOpenGLのエラーが出るので、X11を使うworkaround
	- gazeboのリソース(SDFやSTLなどの)のパスの設定
	- ros\_gz\_simの立ち上げ (Gazeboシミュレータ自体の立ち上げ)
	- ros\_gz\_bridgeの立ち上げ (GazeboとROS2のブリッジ)
	- /robot\_descriptionから、シミュレーション世界にロボットをspawnさせる
	- diff\_drive\_controllerの立ち上げ (後述)
- `simple_sim.launch.py`

```python
import os

from launch_ros.actions import Node
from launch_ros.substitutions import FindPackageShare

from launch import LaunchDescription
from launch.actions import (
    DeclareLaunchArgument,
    IncludeLaunchDescription,
    SetEnvironmentVariable,
)
from launch.substitutions import LaunchConfiguration, PathJoinSubstitution

def generate_launch_description() -> LaunchDescription:
    hcr_gazebo_bringup_share_path = FindPackageShare("hcr_gazebo_bringup")
    hcr_description_share_path = FindPackageShare("hcr_description")

    # set default value
    model_file_default = PathJoinSubstitution(
        [hcr_gazebo_bringup_share_path, "urdf", "hcr_gazebo.urdf.xacro"]
    )
    rviz_config_default = PathJoinSubstitution(
        [hcr_gazebo_bringup_share_path, "config", "sim.rviz"]
    )

    # launch arguments
    model_arg = DeclareLaunchArgument("model", default_value=model_file_default)
    model_name_arg = DeclareLaunchArgument("model_name", default_value="hcr_sim")
    world_arg = DeclareLaunchArgument("world", default_value="empty.world")
    rviz_config_arg = DeclareLaunchArgument(
        "rviz_config", default_value=rviz_config_default
    )

    # substitutions
    model = LaunchConfiguration("model")
    model_name = LaunchConfiguration("model_name")
    world = LaunchConfiguration("world")
    robot_pos = LaunchConfiguration("robot_pos")
    rviz_config = LaunchConfiguration("rviz_config")

    # show robot (rviz)
    show_robot_launch = IncludeLaunchDescription(
        launch_description_source=PathJoinSubstitution(
            [hcr_description_share_path, "launch", "show_robot.launch.py"]
        ),
        launch_arguments={
            "model": model,
            "rviz_config": rviz_config,
            "sim": "true",
        }.items(),
    )

    # use x11 to avoid opengl error on ogre
    # (WaylandだとOpenGLのエラーが出てしまったので、Xwaylandでごまかします)
    set_qt_qpa_platform_env = SetEnvironmentVariable("QT_QPA_PLATFORM", "xcb")

    # set gazebo resource path
    # gazeboのmodel(SDF)のリソースのパスを通します。
    # URDFの中で参照しているSTLファイルなどにもパスを通す必要があるので、
    # hcr_description/..にパスを通します
    # (URDF内でmodel://hcr_description/meshes/...とアクセスしているため)
    hcr_description_path = FindPackageShare("hcr_description")
    gz_sim_resource_path_candidates = [
        PathJoinSubstitution([hcr_gazebo_bringup_share_path, "models"]),
        ":",
        PathJoinSubstitution([hcr_description_path, os.pardir]),
    ]
    set_gz_sim_resource_path_env = SetEnvironmentVariable(
        "GZ_SIM_RESOURCE_PATH",
        gz_sim_resource_path_candidates,
    )

    # start gazebo
    gazebo_share_path = FindPackageShare("ros_gz_sim")
    # -r: 起動したらシミュレータ世界の時計を動かし始める
    # 第1引数: worldへのパス
    gazebo_launch = IncludeLaunchDescription(
        launch_description_source=PathJoinSubstitution(
            [gazebo_share_path, "launch", "gz_sim.launch.py"]
        ),
        launch_arguments={
            "gz_args": [
                "-r ",
                PathJoinSubstitution([hcr_gazebo_bringup_share_path, "worlds", world]),
            ]
        }.items(),
    )
    ros_gz_bridge_node = Node(
        package="ros_gz_bridge",
        executable="parameter_bridge",
        parameters=[
            {
                "config_file": PathJoinSubstitution(
                    [
                        hcr_gazebo_bringup_share_path,
                        "config",
                        "ros_gz_bridge.yaml",
                    ]
                ),
            }
        ],
        output="screen",
    )
    gazebo_model_spawner_node = Node(
        package="ros_gz_sim",
        executable="create",
        name="gazebo_robot_model_spawner",
        output="screen",
        arguments=[
            "-name",
            model_name,
            "-topic",
            "/robot_description",
        ],
    )

    # start diff drive controller
    diff_drive_controller_launch = IncludeLaunchDescription(
        launch_description_source=PathJoinSubstitution(
            [
                FindPackageShare("hcr_diff_drive_controller"),
                "launch",
                "start.launch.py",
            ]
        ),
        launch_arguments={
            "use_sim": "true",
        }.items(),
    )

    return LaunchDescription(
        [
            model_arg,
            model_name_arg,
            world_arg,
            rviz_config_arg,
            show_robot_launch,
            set_qt_qpa_platform_env,
            set_gz_sim_resource_path_env,
            gazebo_launch,
            ros_gz_bridge_node,
            gazebo_model_spawner_node,
            diff_drive_controller_launch,
        ]
    )
```

- ros\_gz\_bridgeのパラメータ
	- (Gazebo内はprotobufでシリアライズしたROSみたいな通信を使っていて、それとROSをブリッジします)
	- 今回はgazeboのシミュレータ内の時刻/clockをROSにブリッジします。
- `ros_gz_bridge.yaml`

```yaml
---
- ros_topic_name: "/clock"
  gz_topic_name: "/clock"
  ros_type_name: "rosgraph_msgs/msg/Clock"
  gz_type_name: "gz.msgs.Clock"
  direction: GZ_TO_ROS
```

- Gazeboシミュレーション用のロボットモデルのURDF
- ロボットの定義自体はhcr\_descriptionにあるURDFをincludeして、Gazebo用のros2\_controlの設定とそれに必要なGazeboのプラグインを記述します。
- `hcr_gazebo.urdf.xacro`

```xml
<?xml version="1.0"?>
<robot name="hcr_sim" xmlns:xacro="http://ros.org/wiki/xacro">
  <xacro:include filename="$(find hcr_description)/urdf/hcr.urdf.xacro" />

  <!-- wheel -->
  <xacro:macro name="wheel_gazebo" params="position">
    <gazebo reference="${position}_wheel_link">
      <material>Gazebo/Red</material>
      <mu1 value="2.5" />
      <mu2 value="2.5" />
    </gazebo>
  </xacro:macro>

  <xacro:wheel_gazebo position="left" />
  <xacro:wheel_gazebo position="right" />

  <!-- caster -->
  <xacro:macro name="caster_gazebo" params="position">
    <gazebo reference="${position}_caster_link">
      <material>Gazebo/Black</material>
      <mu1 value="0.0" />
      <mu2 value="0.0" />
    </gazebo>
  </xacro:macro>

  <xacro:caster_gazebo position="left" />
  <xacro:caster_gazebo position="right" />

  <!-- ros control -->
  <ros2_control name="GazeboSimSystem" type="system">
    <hardware>
      <plugin>gz_ros2_control/GazeboSimSystem</plugin>
    </hardware>
    <joint name="left_wheel_joint">
      <command_interface name="velocity">velocity</command_interface>
      <state_interface name="position">position</state_interface>
      <state_interface name="velocity">velocity</state_interface>
    </joint>
    <joint name="right_wheel_joint">
      <command_interface name="velocity">velocity</command_interface>
      <state_interface name="position">position</state_interface>
      <state_interface name="velocity">velocity</state_interface>
    </joint>
  </ros2_control>

  <gazebo>
    <plugin filename="gz_ros2_control-system" name="gz_ros2_control::GazeboSimROS2ControlPlugin">
      <parameters>$(find hcr_gazebo_bringup)/config/gz_ros2_control.yaml</parameters>
    </plugin>
  </gazebo>
</robot>
```

- ros\_gzで動かしてくれているcontroller managerのパラメータを設定します。(読み込みはURDFでやっている)
- `gz_ros2_control.yaml`

```yaml
controller_manager:
  ros__parameters:
    update_rate: 100  # Hz

    joint_state_broadcaster:
      type: joint_state_broadcaster/JointStateBroadcaster
```

- 自前でやる必要はないけど、拡張できるようにworldファイルも一応。
- `empty.world`

```xml
<?xml version="1.0" ?>
<sdf version="1.6">
  <world name="empty">
    <physics name="1ms" type="ignored">
      <max_step_size>0.001</max_step_size>
      <real_time_factor>1.0</real_time_factor>
    </physics>
    <plugin
      filename="gz-sim-physics-system"
      name="gz::sim::systems::Physics">
    </plugin>
    <plugin
      filename="gz-sim-user-commands-system"
      name="gz::sim::systems::UserCommands">
    </plugin>
    <plugin
      filename="gz-sim-scene-broadcaster-system"
      name="gz::sim::systems::SceneBroadcaster">
    </plugin>
    <plugin
      filename="gz-sim-contact-system"
      name="gz::sim::systems::Contact">
    </plugin>
    <plugin
      filename="gz-sim-imu-system"
      name="gz::sim::systems::Imu">
    </plugin>

    <light type="directional" name="sun">
      <cast_shadows>true</cast_shadows>
      <pose>0 0 10 0 0 0</pose>
      <diffuse>0.8 0.8 0.8 1</diffuse>
      <specular>0.2 0.2 0.2 1</specular>
      <attenuation>
        <range>1000</range>
        <constant>0.9</constant>
        <linear>0.01</linear>
        <quadratic>0.001</quadratic>
      </attenuation>
      <direction>-0.5 0.1 -0.9</direction>
    </light>

    <model name="ground_plane">
      <static>true</static>
      <link name="link">
        <collision name="collision">
          <geometry>
            <plane>
              <normal>0 0 1</normal>
              <size>100 100</size>
            </plane>
          </geometry>
        </collision>
        <visual name="visual">
          <geometry>
            <plane>
              <normal>0 0 1</normal>
              <size>100 100</size>
            </plane>
          </geometry>
          <material>
            <ambient>0.8 0.8 0.8 1</ambient>
            <diffuse>0.8 0.8 0.8 1</diffuse>
            <specular>0.8 0.8 0.8 1</specular>
          </material>
        </visual>
      </link>
    </model>
  </world>
</sdf>
```

### hcr\_diff\_drive\_controller (diff\_drive\_controllerの立ち上げ)

- sim/real両用にしたいので、パッケージとして作って、bringupからlaunchするように構成しています。
- まずlaunchファイル。
- 個人的にまだROS2のrosparamよくわかっていないんですが、controllerのspawnerの引数としてパラメータを渡すと、起動順に依存せずにちゃんと動きました。
	- おそらくこうでなくても動かせるはず
- `start.launch.py`
- diff\_drive\_controllerの設定
- `gazebo.yaml` (real.yamlもパラメータが違うのみ)

```yaml
diff_drive_controller:
  ros__parameters:
    type: "diff_drive_controller/DiffDriveController"

    left_wheel_names: ["left_wheel_joint"]
    right_wheel_names: ["right_wheel_joint"]

    wheel_separation: 0.8082
    wheel_radius: 0.0625

    wheel_separation_multiplier: 1.0
    left_wheel_radius_multiplier: 1.0
    right_wheel_radius_multiplier: 1.0

    odom_frame_id: odom
    base_frame_id: base_link
    pose_covariance_diagonal: [0.0, 0.0, 0.0, 0.0, 0.0, 0.0]
    twist_covariance_diagonal: [0.0, 0.0, 0.0, 0.0, 0.0, 0.0]

    position_feedback: true
    open_loop: true
    enable_odom_tf: true

    cmd_vel_timeout: 0.5 # seconds
    publish_limited_velocity: true
    velocity_rolling_window_size: 10

    linear:
      x:
        has_velocity_limits: true
        max_velocity: 0.85
        min_velocity: -0.85

    angular:
      z:
        has_velocity_limits: true
        max_velocity: 1.5
        min_velocity: -1.5
```

## 最後に

- このLLMの時代、たとえ時系列的に被っていなくとも、同じ名前をつけてはならないという教訓を得ました。難しい。(Gazeboとても便利なので、ありがたく使わせて頂いています…！感謝)
- pythonでのlaunchもなかなかとっつきづらいですね。苦しいCDKって感じがしましたが、使っているうちになるほどという感触を得ました。遅延評価って分かりづらいよね

## その他、最近ROS2を触った人の役に立ちそうなこと

- ROS1でyocs\_cmd\_vel\_muxをつかっていたけど、ROS2では公式から **twist\_mux** として出ているんですね。いい話

10

3

10

3