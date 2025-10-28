---
title: "Rvizを使用してロボットの状態を可視化する(gazebo)"
source: "https://qiita.com/N622/items/f1c5dffa37e095116119"
author:
  - "[[N622]]"
published: 2023-12-19
created: 2025-08-28
description: "概要 rivizとは rvizとはROS2付属の可視化ツールです。gazeboがシミュレーターであり、現実でのロボットの挙動を再現することに重きをおいている一方で、rvizは特定のトピックにフォーカスした可視化を行うことができますー例えばLiDARから得られた障害物の..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiitaにログインして、便利な機能を使ってみませんか？

[ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2FN622%2Fitems%2Ff1c5dffa37e095116119&realm=qiita) [新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2FN622%2Fitems%2Ff1c5dffa37e095116119&realm=qiita)

この記事は最終更新日から1年以上が経過しています。

## 概要

### rivizとは

　rvizとはROS2付属の可視化ツールです。gazeboがシミュレーターであり、現実でのロボットの挙動を再現することに重きをおいている一方で、rvizは特定のトピックにフォーカスした可視化を行うことができますー例えばLiDARから得られた障害物の位置を表示したり、ロボットの向きやジョイントの位置関係のみを表示したりなどです。さらに、slam toolboxやnavigation2などの有用なツールがrvizの使用を前提に作られており、これらのツールを使えるようになるという意味でもrvizを習得することは有意義です。

### この記事の内容

　この記事ではrvizはじめましての人を想定して、rvizの基本的な使い方を説明します。簡単ににrvizの機能を体験するために、本記事では [前回](https://qiita.com/N622/items/9d89f77d85d9da0af29e) 作成したロボットを少し改良して用います。ただし、 [gazebo公式ドキュメント](https://gazebosim.org/docs/fortress/getstarted) と重複する内容は基本的に書きませんーその都度公式ドキュメントへのリンクを貼ります。また、本記事を完了して実際に自分自身の開発を始める前に、 **公式ドキュメントに目を通しておくことー少なくとも目次を見て、何が書いてあるのかを何となく把握しておくこと、を強く推奨します。**  
この記事で扱った内容を実装したコードは [私のgithub](https://github.com/OsawaKousei/2DRoboPrac_ws) に公開しています。今回の内容に相当する部分はリンク先のレポジトリの `gz_ws/src/nav_dev` に収められています。  
また、記事の内容に誤りや不備等ございましたら、ご指摘いただけると幸いです。

### 注意事項

　扱うロボットのモデルがurdf形式で書かれておらず、gazeboの標準機能でsdf形式をトレースして使う場合はいくつか変換できないsdfの記法があることに注意が必要です。確認した限り、以下のものは変換できません。

- CADファイルの読み込み：故に、ロボットのモデルをrvizで表示することはできなくなります
- ball形式のジョイント：ball形式のジョイントはtfに反映されません
- sensorタグ：センサーのついているリンクは(正しく書かれている限り)動作するので、それほど問題にはなりません

　残念ながら2023/12/3現在、rviz2ーROS2を意識する場合はこのように呼ぶ、に関するまとまったドキュメントは整理されていないようです。 [ROS1時代のドキュメント](http://wiki.ros.org/rviz) はありますが、ROS1を使ったことがある人でないと理解は難しいでしょう。但し、 [ROS2のドキュメント](http://docs.ros.org/en/humble/Tutorials/Intermediate/URDF/Building-a-Visual-Robot-Model-with-URDF-from-Scratch.html) には僅かですがrviz2に関連する記述があります。また、ROSのバージョンに依存しない簡単な [ユーザーガイド](https://docs.ros.org/en/indigo/api/rviz/html/user_guide/index.html#) も発見できました。

## 動作環境・バージョン

### 動作環境

Ubuntu22.04　：デュアルブートした実機PC  
VScode　：コーディングとターミナルの操作は基本的にVScodeで行った

### バージョン

ROS2: humble　  
gazebo: ignition fortless　：ROS2公式がhumbleとの連携相手として推奨するバージョン

## 前提条件

以下のような読者を想定して記事を執筆しました  
・上記の動作環境またはそれと同等の機能を持つ環境を有すること  
・ 基本的なubuntuの操作が理解できること  
・ROS2についての基本的な知識があること  
・gazeboの基本的な知識があること

## コンテンツ

## rvizとの連携の準備をする

### LiDARの取り付け

　rvizではセンサーデータを取得してそれを可視化することができます。これは特にLiDARなどのフィーバックを人間が読み取ることの難しいセンサーを用いた開発において威力を発揮します。ですが先ず、LiDARデータをpublishしてくれるロボットが必要です。前回作成したロボットモデルにLiDARを取り付け、gazeboからLiDARデータがpublishされるようにしましょう。  
　sensorタグの扱いについては [gazebo公式チュートリアル](https://gazebosim.org/docs/fortress/sensors) を参考にして下さい。具体的にはロボットに以下のlinkを追加してジョイントを定義すれば完了です。poseタグの値などは、自分が使いたいロボットに合わせて変更して下さいー **LIDARから出る光線がロボットの他のパーツに干渉しないように注意して下さい** 。また、円柱を用いて簡単なLiDARのモデリングを行っていますが、無くても問題ありません。

```text
<!-- LIDAR -->
<link name="base_scan">
  <pose>0.15 0 0.06 0 0 0</pose>
  <inertial>
    <inertia>
      <ixx>0.001</ixx>
      <ixy>0</ixy>
      <ixz>0</ixz>
      <iyy>0.001</iyy>
      <iyz>0</iyz>
      <izz>0.001</izz>
    </inertia>
    <mass>0.114</mass>
  </inertial>
  <collision name="lidar_sensor_collision">
    <geometry>
          <cylinder>
              <radius>0.02</radius>
              <length>0.05</length>
          </cylinder>
        </geometry>
  </collision>
  <visual name="lidar_sensor_visual">
    <geometry>
          <cylinder>
              <radius>0.02</radius>
              <length>0.05</length>
          </cylinder>
        </geometry>
    <material>
      <diffuse> 0.145098039 0.156862745 0.164705882 1</diffuse>
      <ambient> 0.145098039 0.156862745 0.164705882 1</ambient>
      <specular>0.145098039 0.156862745 0.164705882 1</specular>
    </material>
  </visual>
  <sensor name="gpu_lidar" type="gpu_lidar">
    <topic>scan</topic>
    <ignition_frame_id>base_scan</ignition_frame_id>
    <update_rate>5</update_rate>
    <ray>
      <scan>
        <horizontal>
          <samples>360</samples>
          <resolution>1</resolution>
          <min_angle>0</min_angle>
          <max_angle>6.28</max_angle>
        </horizontal>
      </scan>
      <range>
        <min>0.120000</min>
        <max>20.0</max>
        <resolution>0.015000</resolution>
      </range>
    </ray>
    <always_on>true</always_on>
    <visualize>true</visualize>
  </sensor>
</link>
<joint name="lidar_joint" type="fixed">
  <parent>base_link</parent>
  <child>base_scan</child>
</joint>
```

　１つだけ、公式チュートリアルも書いてない(2023/12/3)重要なことを説明します。  
必ず `ignition_frame_id` を定義して下さい。これの定義はセンサータグ配下で以下のように行います。  
`<ignition_frame_id>base_scan</ignition_frame_id>`  
パラメータにはsensorタグが付いているlinkの名前を指定して下さい。これは、tfの出力とセンサーデータの出力を同時に行うときに不可欠な役割を果たします。これを指定していないとtfのフレームとsensorのフレームがずれてしまい、rvizがLiDARデータを認識できなくなります。

　さらに、今後のことを考えて以下の修正を加えます。今までは `base_link` が一番上位にあるlinkでしたが、その更に上に `base_footprint` と言うlinkを追加します。以下に示すlink構造になるように修正して下さい。

```text
<model name="LidarRobo">
  <pose>0 0 0 0 0 0</pose>

    <link name="base_footprint"/>

    <link name="base_link">
      .
      .
      .
    </link>

    <joint name="base_joint" type="fixed">
      <parent>base_footprint</parent>
      <child>base_link</child>
    </joint>
```

これで一旦ロボットのモデルは完成です。modelタグ配下の部分を以下に示します。

model.sdfの確認

model.sdf

```text
<model name="LidarRobo">
  <pose>0 0 0 0 0 0</pose>

    <link name="base_footprint"/>

    <link name="base_link">
      <inertial>
        <pose>0.1 0 0 0 0 0</pose>
        <inertia>
          <ixx>4.2111447e-02</ixx>
          <ixy>0</ixy>
          <ixz>0</ixz>
          <iyy>4.2111447e-02</iyy>
          <iyz>0</iyz>
          <izz>7.5254874e-02</izz>
        </inertia>
        <mass>1.3729096e+00</mass>
      </inertial>
      <collision name="base_collision">
        <pose>-0.064 0.08 0.06 0 0 -1.57</pose>
        <geometry>
          <mesh>
            <uri>model://LidarRobo5/meshes/2DRoboPrac.stl</uri>
          </mesh>
        </geometry>
      </collision>
      <visual name="base_visual">
        <pose>-0.064 0.08 0.06 0 0 -1.57</pose>
        <geometry>
          <mesh>
            <uri>model://LidarRobo5/meshes/2DRoboPrac.dae</uri>
          </mesh>
        </geometry>
        <material>
          <diffuse> 0.921568627 0.925490196 0.941176471 1</diffuse>
          <ambient> 0.921568627 0.925490196 0.941176471 1</ambient>
          <specular>0.921568627 0.925490196 0.941176471 1</specular>
        </material>
      </visual>
    </link>

    <joint name="base_joint" type="fixed">
      <parent>base_footprint</parent>
      <child>base_link</child>
    </joint>

    <!-- LIDAR -->
    <link name="base_scan">
      <pose>0.15 0 0.06 0 0 0</pose>
      <inertial>
        <inertia>
          <ixx>0.001</ixx>
          <ixy>0</ixy>
          <ixz>0</ixz>
          <iyy>0.001</iyy>
          <iyz>0</iyz>
          <izz>0.001</izz>
        </inertia>
        <mass>0.114</mass>
      </inertial>
      <collision name="lidar_sensor_collision">
        <geometry>
              <cylinder>
                  <radius>0.02</radius>
                  <length>0.05</length>
              </cylinder>
            </geometry>
      </collision>
      <visual name="lidar_sensor_visual">
        <geometry>
              <cylinder>
                  <radius>0.02</radius>
                  <length>0.05</length>
              </cylinder>
            </geometry>
        <material>
          <diffuse> 0.145098039 0.156862745 0.164705882 1</diffuse>
          <ambient> 0.145098039 0.156862745 0.164705882 1</ambient>
          <specular>0.145098039 0.156862745 0.164705882 1</specular>
        </material>
      </visual>
      <sensor name="gpu_lidar" type="gpu_lidar">
        <topic>scan</topic>
        <ignition_frame_id>base_scan</ignition_frame_id>
        <update_rate>5</update_rate>
        <ray>
          <scan>
            <horizontal>
              <samples>360</samples>
              <resolution>1</resolution>
              <min_angle>0</min_angle>
              <max_angle>6.28</max_angle>
            </horizontal>
          </scan>
          <range>
            <min>0.120000</min>
            <max>20.0</max>
            <resolution>0.015000</resolution>
          </range>
        </ray>
        <always_on>true</always_on>
        <visualize>true</visualize>
      </sensor>
    </link>
    <joint name="lidar_joint" type="fixed">
      <parent>base_link</parent>
      <child>base_scan</child>
    </joint>

    <!-- Wheel left -->
    <link name="wheel_left_link">
      <pose>0.0 0.095 0.023 -1.57 0 0</pose>
      <inertial>
        <inertia>
          <ixx>1.1175580e-05</ixx>
          <ixy>-4.2369783e-11</ixy>
          <ixz>-5.9381719e-09</ixz>
          <iyy>1.1192413e-05</iyy>
          <iyz>-1.4400107e-11</iyz>
          <izz>2.0712558e-05</izz>
        </inertia>
        <mass>0.1</mass>
      </inertial>
      <collision name="wheel_left_collision">
        <geometry>
          <cylinder>
            <radius>0.033</radius>
            <length>0.018</length>
          </cylinder>
        </geometry>
      </collision>
      <visual name="wheel_left_visual">
        <geometry>
          <cylinder>
            <radius>0.033</radius>
            <length>0.018</length>
          </cylinder>
          </geometry>
          <material>
            <ambient>1.0 0.0 0.0 1</ambient>
            <diffuse>1.0 0.0 0.0 1</diffuse>
            <specular>1.0 0.0 0.0 1</specular>
          </material>
      </visual>
    </link>
    <joint name="wheel_left_joint" type="revolute">
      <parent>base_link</parent>
      <child>wheel_left_link</child>
      <axis>
        <xyz>0 0 1</xyz>
      </axis>
    </joint>

    <!-- Wheel right -->
    <link name="wheel_right_link">
      <pose>0.0 -0.095 0.023 -1.57 0 0</pose>
      <inertial>
        <inertia>
          <ixx>1.1175580e-05</ixx>
          <ixy>-4.2369783e-11</ixy>
          <ixz>-5.9381719e-09</ixz>
          <iyy>1.1192413e-05</iyy>
          <iyz>-1.4400107e-11</iyz>
          <izz>2.0712558e-05</izz>
        </inertia>
        <mass>0.1</mass>
      </inertial>
      <collision name="wheel_right_collision">
        <geometry>
          <cylinder>
            <radius>0.033</radius>
            <length>0.018</length>
          </cylinder>
        </geometry>
      </collision>
      <visual name="wheel_right_visual">
        <geometry>
          <cylinder>
            <radius>0.033</radius>
            <length>0.018</length>
          </cylinder>
          </geometry>
          <material>
            <ambient>1.0 0.0 0.0 1</ambient>
            <diffuse>1.0 0.0 0.0 1</diffuse>
            <specular>1.0 0.0 0.0 1</specular>
          </material>
        <material>
          <diffuse> 0.145098039 0.156862745 0.164705882 1</diffuse>
          <ambient> 0.145098039 0.156862745 0.164705882 1</ambient>
          <specular>0.145098039 0.156862745 0.164705882 1</specular>
        </material>
      </visual>
    </link>
    <joint name="wheel_right_joint" type="revolute">
      <parent>base_link</parent>
      <child>wheel_right_link</child>
      <axis>
        <xyz>0 0 1</xyz>
      </axis>
    </joint>

    <link name='caster_back_right_link'>
      <pose>0.15 -0.05 -0.004 -1.57 0 0</pose>
      <inertial>
        <mass>0.001</mass>
        <inertia>
          <ixx>0.00001</ixx>
          <ixy>0.000</ixy>
          <ixz>0.000</ixz>
          <iyy>0.00001</iyy>
          <iyz>0.000</iyz>
          <izz>0.00001</izz>
        </inertia>
      </inertial>
      <visual name='caster_back_right_visual'>
          <geometry>
              <sphere>
                  <radius>0.005</radius>
              </sphere>
          </geometry>
          <material>
              <ambient>0.0 1 0.0 1</ambient>
              <diffuse>0.0 1 0.0 1</diffuse>
              <specular>0.0 1 0.0 1</specular>
          </material>
        </visual>
      <collision name='collision'>
        <geometry>
          <sphere>
            <radius>0.005</radius>
          </sphere>
        </geometry>
      </collision>
    </link>
    <joint name='caster_back_right_joint' type='ball'>
      <parent>base_link</parent>
      <child>caster_back_right_link</child>
    </joint>

    <link name='caster_back_left_link'>
      <pose>0.15 0.05 -0.004 -1.57 0 0</pose>
      <inertial>
        <mass>0.001</mass>
        <inertia>
          <ixx>0.00001</ixx>
          <ixy>0.000</ixy>
          <ixz>0.000</ixz>
          <iyy>0.00001</iyy>
          <iyz>0.000</iyz>
          <izz>0.00001</izz>
        </inertia>
      </inertial>
      <visual name='caster_back_left_visual'>
          <geometry>
              <sphere>
                  <radius>0.005</radius>
              </sphere>
          </geometry>
          <material>
              <ambient>0.0 1 0.0 1</ambient>
              <diffuse>0.0 1 0.0 1</diffuse>
              <specular>0.0 1 0.0 1</specular>
          </material>
        </visual>
      <collision name='collision'>
        <geometry>
          <sphere>
            <radius>0.005</radius>
          </sphere>
        </geometry>
      </collision>
    </link>
    <joint name='caster_back_left_joint' type='ball'>
      <parent>base_link</parent>
      <child>caster_back_left_link</child>
    </joint>

    <!-- Differential drive -->
    <plugin filename="libignition-gazebo-diff-drive-system.so" 
      name="ignition::gazebo::systems::DiffDrive">
      <left_joint>wheel_left_joint</left_joint>
      <right_joint>wheel_right_joint</right_joint>
      <wheel_separation>0.19</wheel_separation>
      <wheel_radius>0.033</wheel_radius>
      <topic>cmd_vel</topic>
    </plugin>

    <!-- Joint state publisher -->
    <plugin filename="libignition-gazebo-joint-state-publisher-system.so" 
      name="ignition::gazebo::systems::JointStatePublisher">
      <topic>joint_states</topic>
    </plugin>

    <!-- Global odometer -->
    <plugin
      filename="ignition-gazebo-odometry-publisher-system"
      name="ignition::gazebo::systems::OdometryPublisher">
      <odom_publish_frequency>50</odom_publish_frequency>
      <odom_topic>/odom</odom_topic>
      <odom_frame>odom</odom_frame>
      <robot_base_frame>base_footprint</robot_base_frame>
      <tf_topic>/odom/tf</tf_topic>
    </plugin>

  </model>
```

### spawn機能を使う準備をする

　今までは、worldタグの中にmodelタグを記述してきました。しかし、この書き方はフィールドとロボットを分離できません。例えば同じフィールド上で何種類かのロボットを順番に試したいとき、従来の方法ではworld.sdfを書き直す必要がありました。でも、面倒くさがる必要はありません、world.sdfに書かれていなくても後からロボットをフィールドに配置することができるんです、そう！spawn機能を使えばね👍。  
　spawn機能を使うために、従来のディレクトリ構成を少し変更しましょう。先ず、modelディレクトリ内にworldsディレクトリを作成して今までのworld.sdfはそこへ移動し、 `nav_slam.sdf` という名前にしましょう。この名前は諸事情があってこう決まっているわけですが、新しいワークスペースで作業している皆さんには関係がないので、好きに命名してくれても構いません。但し、その場合は後々のlaunchファイルのパラメータを少し変更する必要があることに注意して下さい。  
　次に、modelsディレクトリに新しく `LidarRobo` ディレクトリを作成し、その中に `model.sdf` という新規ファイルと `mesh` ディレクトリを格納してください。以下のようなディレクトリ構成になっていればOKです。

`model.sdf` には次のように記述して下さい。

model.sdf

```text
<sdf version='1.8'>
  
</sdf>
```

それができたら、 `nav_slam.sdf` からmodelタグ部分をまるごと切り取り、model.sdfのsdfタグ内に貼り付けます。  
これでspawn機能を使う準備は完了です。

### spawn機能を使う

　どのワールドにどのロボットをspawnさせるかはlaunchファイルで指定します。先ず、フィールドの読み込みですが、これは従来のgazeboの起動方法と同じように行います。

　次に、今指定したワールドにロボットをspawnさせるには以下のように記述します。spawnさせる位置などのパラメータは適当に調整して下さい。

　最後に、return内にこれらの処理を実行する記述をします。

```text
return LaunchDescription([
    ign_resource_path,
    ignition_spawn_entity,
    ign_gz,
                         
    DeclareLaunchArgument(
        'use_sim_time',
        default_value=use_sim_time,
        description='If true, use simulated clock'),

    DeclareLaunchArgument(
        'world_name',
        default_value=world_name,
        description='World name'),

    bridge,
    teleop_node,
])
```

　launchファイルを起動すると、ちゃんとロボットがspawnしていることが分かります。

[![Screenshot from 2023-12-03 21-54-10.png (321.0 kB)](https://img.esa.io/uploads/production/attachments/19744/2023/12/03/148658/7ba9d28e-e739-489e-9b2a-b63c0d3dc539.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fimg.esa.io%2Fuploads%2Fproduction%2Fattachments%2F19744%2F2023%2F12%2F03%2F148658%2F7ba9d28e-e739-489e-9b2a-b63c0d3dc539.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=9e495bb63903cc2590be025f572a6027)

### フィールドを用意する

　LiDARをロボットに取り付けましたが、現状のフィールドには壁がないので、何も読み取ることができません。これは余りにも愚かしいので壁の付いたフィールドを用意しましょう。自分の使いたいフィールドがある読者はそれを使うことができます。そうでない場合は [github](https://github.com/OsawaKousei/2DRoboPrac_ws) から `ros2_ws/src/nav_dev/models/field/mesh/` からフィールドのCADデータをコピーしてきて使いましょう。今回の記事でもこれを使います。  
　新たにmodelsディレクトリ内に `field` ディレクトリを作成し、その中に `mesh` ディレクトリを作成してフィールドのCADデータを入れて下さい。fieldディレクトリ配下に `model.sdf` を作成して以下のように記述します。

```text
<?xml version="1.0" ?>
<sdf version="1.6">
  <model name="my_field" canonical_link='field'>
    <!--動かないエンティティであることを設定-->
    <static>true</static>
    <link name="field">
      <collision name="collision">
        <geometry>
          <mesh>
            <scale>1 1 1</scale>
            <uri>model://field/meshes/HaruRobo2024field.stl</uri>
          </mesh>
        </geometry>
        <surface>
          <friction>
            <ode>
              <mu>1.2</mu>
              <mu2>1.2</mu2>
            </ode>
          </friction>
        </surface>
      </collision>
      <visual name="visual">
        <geometry>
          <mesh>
            <scale>1 1 1</scale>
            <uri>model://field/meshes/HaruRobo2024field.dae</uri>
          </mesh>
        </geometry>
      </visual>
    </link>
  </model>
</sdf>
```

`static` タグを `true` に設定することが重要です。gazebo側はこのモデルをフィールドだと認識する機能を備えていないので、これを指定しなければシミュレーションを開始した瞬間にフィールドも落下し始めます。  
　それができたら、launchファイルにフィールドもspawnさせるよう、追記していきます。

return内に忘れずに追記をしたら、launchファイルを起動しましょう。  
以上の記事の内容を全て実装したlaunchファイルは [github](https://github.com/OsawaKousei/2DRoboPrac_ws) の `ros2_ws/src/nev_dev/launch/nav_spawn.launch.py` にあります。

[![Screenshot from 2023-12-03 22-05-00.png (347.3 kB)](https://img.esa.io/uploads/production/attachments/19744/2023/12/03/148658/459a2fc3-e505-48b2-8b21-d119b5c92139.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fimg.esa.io%2Fuploads%2Fproduction%2Fattachments%2F19744%2F2023%2F12%2F03%2F148658%2F459a2fc3-e505-48b2-8b21-d119b5c92139.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=8543c863934b9923b1e52ec34a79b7c1)

フィールドが表示されていることが分かります。

## gaebo上のロボットの状態をrvizで表示する

### rvizの起動

　単純にrvizを起動するだけなら `rviz2` とターミナルの任意の場所で打てば立ち上げることができます。しかし、そのためにターミナルを１つ割り当てるのはやはり面倒なのでlaunchファイルから起動することにします。

以上のようにすることでlaunchファイルからrvizを起動する設定ができます。但し、この場合rvizの設定ファイルが必要になります。以下に私が作成したテンプレートを示すので、コピペして使って下さい。

rviz設定ファイルテンプレート

nav\_rviz.rviz

```yaml
Panels:
  - Class: rviz_common/Displays
    Help Height: 0
    Name: Displays
    Property Tree Widget:
      Expanded:
        - /Global Options1
        - /TF1/Frames1
        - /TF1/Tree1
        - /Odometry1/Topic1
      Splitter Ratio: 0.5833333134651184
    Tree Height: 778
  - Class: rviz_common/Selection
    Name: Selection
  - Class: rviz_common/Tool Properties
    Expanded:
      - /Publish Point1
    Name: Tool Properties
    Splitter Ratio: 0.5886790156364441
  - Class: rviz_common/Views
    Expanded:
      - /Current View1
    Name: Views
    Splitter Ratio: 0.5
  - Class: nav2_rviz_plugins/Navigation 2
    Name: Navigation 2
Visualization Manager:
  Class: ""
  Displays:
    - Alpha: 0.5
      Cell Size: 1
      Class: rviz_default_plugins/Grid
      Color: 160; 160; 164
      Enabled: true
      Line Style:
        Line Width: 0.029999999329447746
        Value: Lines
      Name: Grid
      Normal Cell Count: 0
      Offset:
        X: 0
        Y: 0
        Z: 0
      Plane: XY
      Plane Cell Count: 10
      Reference Frame: <Fixed Frame>
      Value: true
    - Alpha: 1
      Class: rviz_default_plugins/RobotModel
      Collision Enabled: false
      Description File: ""
      Description Source: Topic
      Description Topic:
        Depth: 5
        Durability Policy: Volatile
        History Policy: Keep Last
        Reliability Policy: Reliable
        Value: /robot_description
      Enabled: false
      Links:
        All Links Enabled: true
        Expand Joint Details: false
        Expand Link Details: false
        Expand Tree: false
        Link Tree Style: Links in Alphabetic Order
      Mass Properties:
        Inertia: false
        Mass: false
      Name: RobotModel
      TF Prefix: ""
      Update Interval: 0
      Value: false
      Visual Enabled: true
    - Class: rviz_default_plugins/TF
      Enabled: true
      Frame Timeout: 15
      Frames:
        All Enabled: false
        base_footprint:
          Value: true
        base_link:
          Value: true
        base_scan:
          Value: true
        odom:
          Value: true
        wheel_left_link:
          Value: true
        wheel_right_link:
          Value: true
      Marker Scale: 1
      Name: TF
      Show Arrows: true
      Show Axes: true
      Show Names: false
      Tree:
        odom:
          base_footprint:
            base_link:
              base_scan:
                {}
              wheel_left_link:
                {}
              wheel_right_link:
                {}
      Update Interval: 0
      Value: true
    - Alpha: 1
      Autocompute Intensity Bounds: true
      Autocompute Value Bounds:
        Max Value: 10
        Min Value: -10
        Value: true
      Axis: Z
      Channel Name: intensity
      Class: rviz_default_plugins/LaserScan
      Color: 255; 255; 255
      Color Transformer: Intensity
      Decay Time: 0
      Enabled: true
      Invert Rainbow: false
      Max Color: 255; 255; 255
      Max Intensity: 0
      Min Color: 0; 0; 0
      Min Intensity: 0
      Name: LaserScan
      Position Transformer: XYZ
      Selectable: true
      Size (Pixels): 3
      Size (m): 0.009999999776482582
      Style: Points
      Topic:
        Depth: 5
        Durability Policy: Volatile
        Filter size: 10
        History Policy: Keep Last
        Reliability Policy: Best Effort
        Value: /scan
      Use Fixed Frame: true
      Use rainbow: true
      Value: true
    - Alpha: 1
      Autocompute Intensity Bounds: true
      Autocompute Value Bounds:
        Max Value: 10
        Min Value: -10
        Value: true
      Axis: Z
      Channel Name: intensity
      Class: rviz_default_plugins/PointCloud2
      Color: 255; 255; 255
      Color Transformer: ""
      Decay Time: 0
      Enabled: true
      Invert Rainbow: false
      Max Color: 255; 255; 255
      Max Intensity: 4096
      Min Color: 0; 0; 0
      Min Intensity: 0
      Name: Bumper Hit
      Position Transformer: ""
      Selectable: true
      Size (Pixels): 3
      Size (m): 0.07999999821186066
      Style: Spheres
      Topic:
        Depth: 5
        Durability Policy: Volatile
        Filter size: 10
        History Policy: Keep Last
        Reliability Policy: Best Effort
        Value: /mobile_base/sensors/bumper_pointcloud
      Use Fixed Frame: true
      Use rainbow: true
      Value: true
    - Alpha: 1
      Class: rviz_default_plugins/Map
      Color Scheme: map
      Draw Behind: true
      Enabled: true
      Name: Map
      Topic:
        Depth: 1
        Durability Policy: Transient Local
        Filter size: 10
        History Policy: Keep Last
        Reliability Policy: Reliable
        Value: /map
      Update Topic:
        Depth: 5
        Durability Policy: Volatile
        History Policy: Keep Last
        Reliability Policy: Reliable
        Value: /map_updates
      Use Timestamp: false
      Value: true
    - Alpha: 1
      Class: nav2_rviz_plugins/ParticleCloud
      Color: 0; 180; 0
      Enabled: true
      Max Arrow Length: 0.30000001192092896
      Min Arrow Length: 0.019999999552965164
      Name: Amcl Particle Swarm
      Shape: Arrow (Flat)
      Topic:
        Depth: 5
        Durability Policy: Volatile
        Filter size: 10
        History Policy: Keep Last
        Reliability Policy: Best Effort
        Value: /particle_cloud
      Value: true
    - Class: rviz_common/Group
      Displays:
        - Alpha: 0.30000001192092896
          Class: rviz_default_plugins/Map
          Color Scheme: costmap
          Draw Behind: false
          Enabled: true
          Name: Global Costmap
          Topic:
            Depth: 1
            Durability Policy: Transient Local
            Filter size: 10
            History Policy: Keep Last
            Reliability Policy: Reliable
            Value: /global_costmap/costmap
          Update Topic:
            Depth: 5
            Durability Policy: Volatile
            History Policy: Keep Last
            Reliability Policy: Reliable
            Value: /global_costmap/costmap_updates
          Use Timestamp: false
          Value: true
        - Alpha: 0.30000001192092896
          Class: rviz_default_plugins/Map
          Color Scheme: costmap
          Draw Behind: false
          Enabled: true
          Name: Downsampled Costmap
          Topic:
            Depth: 1
            Durability Policy: Transient Local
            Filter size: 10
            History Policy: Keep Last
            Reliability Policy: Reliable
            Value: /downsampled_costmap
          Update Topic:
            Depth: 5
            Durability Policy: Volatile
            History Policy: Keep Last
            Reliability Policy: Reliable
            Value: /downsampled_costmap_updates
          Use Timestamp: false
          Value: true
        - Alpha: 1
          Buffer Length: 1
          Class: rviz_default_plugins/Path
          Color: 255; 0; 0
          Enabled: true
          Head Diameter: 0.019999999552965164
          Head Length: 0.019999999552965164
          Length: 0.30000001192092896
          Line Style: Lines
          Line Width: 0.029999999329447746
          Name: Path
          Offset:
            X: 0
            Y: 0
            Z: 0
          Pose Color: 255; 85; 255
          Pose Style: Arrows
          Radius: 0.029999999329447746
          Shaft Diameter: 0.004999999888241291
          Shaft Length: 0.019999999552965164
          Topic:
            Depth: 5
            Durability Policy: Volatile
            Filter size: 10
            History Policy: Keep Last
            Reliability Policy: Reliable
            Value: /plan
          Value: true
        - Alpha: 1
          Autocompute Intensity Bounds: true
          Autocompute Value Bounds:
            Max Value: 10
            Min Value: -10
            Value: true
          Axis: Z
          Channel Name: intensity
          Class: rviz_default_plugins/PointCloud2
          Color: 125; 125; 125
          Color Transformer: FlatColor
          Decay Time: 0
          Enabled: true
          Invert Rainbow: false
          Max Color: 255; 255; 255
          Max Intensity: 4096
          Min Color: 0; 0; 0
          Min Intensity: 0
          Name: VoxelGrid
          Position Transformer: XYZ
          Selectable: true
          Size (Pixels): 3
          Size (m): 0.05000000074505806
          Style: Boxes
          Topic:
            Depth: 5
            Durability Policy: Volatile
            Filter size: 10
            History Policy: Keep Last
            Reliability Policy: Reliable
            Value: /global_costmap/voxel_marked_cloud
          Use Fixed Frame: true
          Use rainbow: true
          Value: true
        - Alpha: 1
          Class: rviz_default_plugins/Polygon
          Color: 25; 255; 0
          Enabled: false
          Name: Polygon
          Topic:
            Depth: 5
            Durability Policy: Volatile
            Filter size: 10
            History Policy: Keep Last
            Reliability Policy: Reliable
            Value: /global_costmap/published_footprint
          Value: false
      Enabled: true
      Name: Global Planner
    - Class: rviz_common/Group
      Displays:
        - Alpha: 0.699999988079071
          Class: rviz_default_plugins/Map
          Color Scheme: costmap
          Draw Behind: false
          Enabled: true
          Name: Local Costmap
          Topic:
            Depth: 1
            Durability Policy: Transient Local
            Filter size: 10
            History Policy: Keep Last
            Reliability Policy: Reliable
            Value: /local_costmap/costmap
          Update Topic:
            Depth: 5
            Durability Policy: Volatile
            History Policy: Keep Last
            Reliability Policy: Reliable
            Value: /local_costmap/costmap_updates
          Use Timestamp: false
          Value: true
        - Alpha: 1
          Buffer Length: 1
          Class: rviz_default_plugins/Path
          Color: 0; 12; 255
          Enabled: true
          Head Diameter: 0.30000001192092896
          Head Length: 0.20000000298023224
          Length: 0.30000001192092896
          Line Style: Lines
          Line Width: 0.029999999329447746
          Name: Local Plan
          Offset:
            X: 0
            Y: 0
            Z: 0
          Pose Color: 255; 85; 255
          Pose Style: None
          Radius: 0.029999999329447746
          Shaft Diameter: 0.10000000149011612
          Shaft Length: 0.10000000149011612
          Topic:
            Depth: 5
            Durability Policy: Volatile
            Filter size: 10
            History Policy: Keep Last
            Reliability Policy: Reliable
            Value: /local_plan
          Value: true
        - Class: rviz_default_plugins/MarkerArray
          Enabled: false
          Name: Trajectories
          Namespaces:
            {}
          Topic:
            Depth: 5
            Durability Policy: Volatile
            History Policy: Keep Last
            Reliability Policy: Reliable
            Value: /marker
          Value: false
        - Alpha: 1
          Class: rviz_default_plugins/Polygon
          Color: 25; 255; 0
          Enabled: true
          Name: Polygon
          Topic:
            Depth: 5
            Durability Policy: Volatile
            Filter size: 10
            History Policy: Keep Last
            Reliability Policy: Reliable
            Value: /local_costmap/published_footprint
          Value: true
        - Alpha: 1
          Autocompute Intensity Bounds: true
          Autocompute Value Bounds:
            Max Value: 10
            Min Value: -10
            Value: true
          Axis: Z
          Channel Name: intensity
          Class: rviz_default_plugins/PointCloud2
          Color: 255; 255; 255
          Color Transformer: RGB8
          Decay Time: 0
          Enabled: true
          Invert Rainbow: false
          Max Color: 255; 255; 255
          Max Intensity: 4096
          Min Color: 0; 0; 0
          Min Intensity: 0
          Name: VoxelGrid
          Position Transformer: XYZ
          Selectable: true
          Size (Pixels): 3
          Size (m): 0.009999999776482582
          Style: Flat Squares
          Topic:
            Depth: 5
            Durability Policy: Volatile
            Filter size: 10
            History Policy: Keep Last
            Reliability Policy: Reliable
            Value: /local_costmap/voxel_marked_cloud
          Use Fixed Frame: true
          Use rainbow: true
          Value: true
      Enabled: true
      Name: Controller
    - Class: rviz_common/Group
      Displays:
        - Class: rviz_default_plugins/Image
          Enabled: true
          Max Value: 1
          Median window: 5
          Min Value: 0
          Name: RealsenseCamera
          Normalize Range: true
          Topic:
            Depth: 5
            Durability Policy: Volatile
            History Policy: Keep Last
            Reliability Policy: Reliable
            Value: /intel_realsense_r200_depth/image_raw
          Value: true
        - Alpha: 1
          Autocompute Intensity Bounds: true
          Autocompute Value Bounds:
            Max Value: 10
            Min Value: -10
            Value: true
          Axis: Z
          Channel Name: intensity
          Class: rviz_default_plugins/PointCloud2
          Color: 255; 255; 255
          Color Transformer: RGB8
          Decay Time: 0
          Enabled: true
          Invert Rainbow: false
          Max Color: 255; 255; 255
          Max Intensity: 4096
          Min Color: 0; 0; 0
          Min Intensity: 0
          Name: RealsenseDepthImage
          Position Transformer: XYZ
          Selectable: true
          Size (Pixels): 3
          Size (m): 0.009999999776482582
          Style: Flat Squares
          Topic:
            Depth: 5
            Durability Policy: Volatile
            Filter size: 10
            History Policy: Keep Last
            Reliability Policy: Reliable
            Value: /intel_realsense_r200_depth/points
          Use Fixed Frame: true
          Use rainbow: true
          Value: true
      Enabled: false
      Name: Realsense
    - Class: rviz_default_plugins/MarkerArray
      Enabled: true
      Name: MarkerArray
      Namespaces:
        {}
      Topic:
        Depth: 5
        Durability Policy: Volatile
        History Policy: Keep Last
        Reliability Policy: Reliable
        Value: /waypoints
      Value: true
    - Angle Tolerance: 0.10000000149011612
      Class: rviz_default_plugins/Odometry
      Covariance:
        Orientation:
          Alpha: 0.5
          Color: 255; 255; 127
          Color Style: Unique
          Frame: Local
          Offset: 1
          Scale: 1
          Value: true
        Position:
          Alpha: 0.30000001192092896
          Color: 204; 51; 204
          Scale: 1
          Value: true
        Value: true
      Enabled: true
      Keep: 100
      Name: Odometry
      Position Tolerance: 0.10000000149011612
      Shape:
        Alpha: 1
        Axes Length: 1
        Axes Radius: 0.10000000149011612
        Color: 255; 25; 0
        Head Length: 0.30000001192092896
        Head Radius: 0.10000000149011612
        Shaft Length: 1
        Shaft Radius: 0.05000000074505806
        Value: Arrow
      Topic:
        Depth: 5
        Durability Policy: Volatile
        Filter size: 10
        History Policy: Keep Last
        Reliability Policy: Reliable
        Value: /odom
      Value: true
  Enabled: true
  Global Options:
    Background Color: 48; 48; 48
    Fixed Frame: base_link
    Frame Rate: 30
  Name: root
  Tools:
    - Class: rviz_default_plugins/MoveCamera
    - Class: rviz_default_plugins/Select
    - Class: rviz_default_plugins/FocusCamera
    - Class: rviz_default_plugins/Measure
      Line color: 128; 128; 0
    - Class: rviz_default_plugins/SetInitialPose
      Covariance x: 0.25
      Covariance y: 0.25
      Covariance yaw: 0.06853891909122467
      Topic:
        Depth: 5
        Durability Policy: Volatile
        History Policy: Keep Last
        Reliability Policy: Reliable
        Value: /initialpose
    - Class: rviz_default_plugins/PublishPoint
      Single click: true
      Topic:
        Depth: 5
        Durability Policy: Volatile
        History Policy: Keep Last
        Reliability Policy: Reliable
        Value: /clicked_point
    - Class: nav2_rviz_plugins/GoalTool
  Transformation:
    Current:
      Class: rviz_default_plugins/TF
  Value: true
  Views:
    Current:
      Angle: -1.5707999467849731
      Class: rviz_default_plugins/TopDownOrtho
      Enable Stereo Rendering:
        Stereo Eye Separation: 0.05999999865889549
        Stereo Focal Distance: 1
        Swap Stereo Eyes: false
        Value: false
      Invert Z Axis: false
      Name: Current View
      Near Clip Distance: 0.009999999776482582
      Scale: 116.60354614257812
      Target Frame: <Fixed Frame>
      Value: TopDownOrtho (rviz_default_plugins)
      X: 0
      Y: 0
    Saved: ~
Window Geometry:
  Displays:
    collapsed: false
  Height: 932
  Hide Left Dock: false
  Hide Right Dock: true
  Navigation 2:
    collapsed: false
  QMainWindow State: 000000ff00000000fd00000004000000000000016a00000348fc020000000afb0000001200530065006c0065006300740069006f006e00000001e10000009b0000005c00fffffffb0000001e0054006f006f006c002000500072006f007000650072007400690065007302000001ed000001df00000185000000a3fb000000120056006900650077007300200054006f006f02000001df000002110000018500000122fb000000200054006f006f006c002000500072006f0070006500720074006900650073003203000002880000011d000002210000017afb000000100044006900730070006c006100790073010000003e00000348000000ca00fffffffb0000002000730065006c0065006300740069006f006e00200062007500660066006500720200000138000000aa0000023a00000294fb00000014005700690064006500530074006500720065006f02000000e6000000d2000003ee0000030bfb0000000c004b0069006e0065006300740200000186000001060000030c00000261fb00000018004e0061007600690067006100740069006f006e002000320000000242000001440000014400fffffffb0000001e005200650061006c00730065006e0073006500430061006d00650072006100000002c6000000c10000002800ffffff000000010000010f0000034afc0200000003fb0000001e0054006f006f006c002000500072006f00700065007200740069006500730100000041000000780000000000000000fb0000000a00560069006500770073000000003d0000034a000000a600fffffffb0000001200530065006c0065006300740069006f006e010000025a000000b200000000000000000000000200000490000000a9fc0100000001fb0000000a00560069006500770073030000004e00000080000002e10000019700000003000004420000003efc0100000002fb0000000800540069006d00650100000000000004420000000000000000fb0000000800540069006d00650100000000000004500000000000000000000004990000034800000004000000040000000800000008fc0000000100000002000000010000000a0054006f006f006c00730100000000ffffffff0000000000000000
  RealsenseCamera:
    collapsed: false
  Selection:
    collapsed: false
  Tool Properties:
    collapsed: false
  Views:
    collapsed: true
  Width: 1545
  X: 329
  Y: 172
```

ディレクトリ構成は以下のようにして下さい

launchファイルにはrvizを起動するための文言を書き足して下さい。  
現段階で以下のようになっていることを想定しています。

launchファイルの確認

nav\_rviz.launch.py

```python
import os
from ament_index_python.packages import get_package_share_directory
from launch import LaunchDescription
from launch.actions import DeclareLaunchArgument
from launch.actions import IncludeLaunchDescription, SetEnvironmentVariable
from launch.launch_description_sources import PythonLaunchDescriptionSource
from launch.substitutions import LaunchConfiguration, PathJoinSubstitution
from launch_ros.actions import Node

def generate_launch_description():
    use_sim_time = LaunchConfiguration('use_sim_time', default='true')
    world_name = LaunchConfiguration('world_name', default='nav_slam_world')
    pkg_share_dir = get_package_share_directory('nav_dev')
    model_path = os.path.join(pkg_share_dir, "models")

    #ignition gazeboがモデルにアクセスできるように設定
    ign_resource_path = SetEnvironmentVariable(
        name='IGN_GAZEBO_RESOURCE_PATH',value=[
        os.path.join("/opt/ros/humble", "share"),
        ":" +
        model_path])

    #ロボットをスポーンさせる設定
    ignition_spawn_entity = Node(
        package='ros_ign_gazebo',
        executable='create',
        output='screen',
        arguments=['-entity', 'LidarRobo',
                   '-name', 'LidarRobo',
                   #ロボットのsdfファイルを指定
                   '-file', PathJoinSubstitution([
                        pkg_share_dir,
                        "models", "LidarRobo", "model.sdf"]),
                    #ロボットの位置を指定
                   '-allow_renaming', 'true',
                   '-x', '0.4',
                   '-y', '0.4',
                   '-z', '0.075',
                   ],
        )
    
    #フィールドをスポーンさせる設定
    ignition_spawn_world = Node(
        package='ros_ign_gazebo',
        executable='create',
        output='screen',
            #フィールドのsdfファイルを指定
        arguments=['-file', PathJoinSubstitution([
                        pkg_share_dir,
                        "models", "field", "model.sdf"]),
                   '-allow_renaming', 'false'],
        )
    
    #ワールドのsdfファイルを設定(worldタグのあるsdfファイル)
    world = os.path.join(pkg_share_dir, "models", "worlds", "nav_slam.sdf")

    #ignition gazeboの起動設定
    ign_gz = IncludeLaunchDescription(
            PythonLaunchDescriptionSource(
                [os.path.join(get_package_share_directory('ros_ign_gazebo'),
                              'launch', 'ign_gazebo.launch.py')]),
            launch_arguments=[('ign_args', [' -r -v 3 ' +
                              world
                             ])])
    
    #ros_ign_bridgeの起動設定
    bridge = Node(
        package='ros_ign_bridge',
        executable='parameter_bridge',
        parameters=[{
            #brigdeの設定ファイルを指定
            'config_file': os.path.join(pkg_share_dir, 'config', 'nav_slam.yaml'),
            'qos_overrides./tf_static.publisher.durability': 'transient_local',
            'qos_overrides./odom.publisher.durability': 'transient_local',
        },{'use_sim_time': use_sim_time}],
        remappings=[
            ("/odom/tf", "tf"),
        ],
        output='screen'
    )
     
    #rviz2の設定フィルのパスを取得
    rviz_config_dir = os.path.join(
        pkg_share_dir,
        'config',
        'nav_rviz.rviz')
    
    #rviz2の起動設定
    rviz2 = Node(
            package='rviz2',
            executable='rviz2',
            name='rviz2',
            arguments=['-d', rviz_config_dir],
            parameters=[{'use_sim_time': use_sim_time}],
            output='screen')
    
   
    
    return LaunchDescription([
        ign_resource_path,
        ignition_spawn_entity,
        ignition_spawn_world,
        ign_gz,
                             
        DeclareLaunchArgument(
            'use_sim_time',
            default_value=use_sim_time,
            description='If true, use simulated clock'),

        DeclareLaunchArgument(
            'world_name',
            default_value=world_name,
            description='World name'),

        bridge,
        
        #
        #rvizを起動するための文言
        #
        rviz2,
    ])
```

以上の設定ファイルを読み込んでlaunchファイルを起動すると、以下のような画面になるかと思います。単に `rviz2` で起動したときと比べて見た目が幾らか変わっているでしょうか？  
[![Screenshot from 2023-12-03 17-10-54.png (172.7 kB)](https://img.esa.io/uploads/production/attachments/19744/2023/12/03/148658/013e6d71-230c-4ea5-8d29-cfcb6437c8f4.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fimg.esa.io%2Fuploads%2Fproduction%2Fattachments%2F19744%2F2023%2F12%2F03%2F148658%2F013e6d71-230c-4ea5-8d29-cfcb6437c8f4.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=d2105c82753830d59db45c313a64df0e)

### LiDARのデータを表示

　実は、Lidarデータの表示はいままでの全てのことが正しく行われていれば簡単にできます。rviz画面左上に `fixed frame` という項目があるかと思います。ここに、sdfファイルのsensorタグで指定していた `ignition frame id` の値を入力して下さい。この記事通りの実装なら `base_scan` です。すると、以下のようにLiDARから取得されたデータが表示され始めるのを確認できるはずです。  
[![Screenshot from 2023-12-03 17-18-16.png (271.5 kB)](https://img.esa.io/uploads/production/attachments/19744/2023/12/03/148658/106d75d4-957a-415f-868a-8607481df375.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fimg.esa.io%2Fuploads%2Fproduction%2Fattachments%2F19744%2F2023%2F12%2F03%2F148658%2F106d75d4-957a-415f-868a-8607481df375.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=bee93bf40925d265382d520378114eed)

### odom

　odometry(オドメトリ)とはロボットの位置と角度を意味します。これをrviz上で取得するためにはsdfモデルに以下のプラグインを書き足す必要があります。

```text
<!-- Global odometer -->
<plugin
  filename="ignition-gazebo-odometry-publisher-system"
  name="ignition::gazebo::systems::OdometryPublisher">
  <odom_publish_frequency>50</odom_publish_frequency>
  <odom_topic>/odom</odom_topic>
  <odom_frame>odom</odom_frame>
  <robot_base_frame>base_footprint</robot_base_frame>
  <tf_topic>/odom/tf</tf_topic>
</plugin>
```

その上でもう一度launchファイルを起動してみましょう。fixed frameを `odom` にすると、以下のような結果が得られるはずです。  
[![Screenshot from 2023-12-03 17-34-53.png (264.2 kB)](https://img.esa.io/uploads/production/attachments/19744/2023/12/03/148658/e061725a-f497-4ab6-bff9-cda5cbaf7dcd.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fimg.esa.io%2Fuploads%2Fproduction%2Fattachments%2F19744%2F2023%2F12%2F03%2F148658%2Fe061725a-f497-4ab6-bff9-cda5cbaf7dcd.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=bf9a3c2131088eae9e195602a619acb2)  
大きい矢印はロボットの向きを表し、小さい矢印はロボットが原点からどれだけ動いているかを表します。

### tf

　tfは上の２つに比べてやや難しい概念です。詳しくは [ROS2公式ドキュメント](https://docs.ros.org/en/humble/Tutorials/Intermediate/Tf2/Introduction-To-Tf2.html) を参照してもらうとして、簡単に言うとそれは「ロボットの各パーツのジョイントの状態を表したもの」と表現できます。これを使えばロボットのモデルをまるごとrviz上に表示しなくても効果的にロボットの状態を把握できるのです。  
　さて、tfをrviz上で取得するには少々凝った準備が必要です。先ず、odomのときと同様に以下のプラグインをsdfモデルに追加する必要があります。

```text
<!-- Joint state publisher -->
<plugin filename="libignition-gazebo-joint-state-publisher-system.so" 
  name="ignition::gazebo::systems::JointStatePublisher">
  <topic>joint_states</topic>
</plugin>
```

次に、 `robot_state_publisher` を起動する必要があります。ターミナルからの起動も可能ですが、launchファイルを使う方法を紹介します。 `robot_state_publisher` は名前の通り、ロボットの状態を発信してくれますが、そのためには使っているロボットの情報を予め渡しておく必要があります。先ず、sdfをurdfに変換するために `xacro` というツールを使います。launchファイルの上部に `import xacro` を追記して下さい。次に、以下の内容をコピペします。

以上の記事の内容を全て実装したlaunchファイルは [github](https://github.com/OsawaKousei/2DRoboPrac_ws) のros2\_ws/src/nev\_dev/launch/nav\_rviz.launch.pyにあります。

このlaunchファイルを起動して、以下のような結果が得られれば成功です。  
[![Screenshot from 2023-12-03 17-51-34.png (185.2 kB)](https://img.esa.io/uploads/production/attachments/19744/2023/12/03/148658/59625839-0638-450f-abc8-798fa0b3ea60.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fimg.esa.io%2Fuploads%2Fproduction%2Fattachments%2F19744%2F2023%2F12%2F03%2F148658%2F59625839-0638-450f-abc8-798fa0b3ea60.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=c52394e54ba3e5ecabf4263205faefa1)

## トラブルシューティング（追記求む）

### 一般論

・とりあえず `sudo apt update` する  
・gazeboのゾンビ化を疑う  
gazeboは乱暴な閉じ方をしたときーそして丁寧に行った場合でもそれなりの頻度で、正常に終了せずプロセスがゾンビ化します  
先ず、 `ps aux|grep ign` で検索をかけます。正しく終了できていれば２つのプロセスが表示されます。  
３つ以上ならプロセスのゾンビ化が発生しています。 `kill -9 <プロセス番号>` で最初と最後のプロセス以外を強制終了します。

プロセスがゾンビ化しているときの代表的な挙動

・モデルやワールドの記述を変更したのに反映されていない  
・何故かロボットが２つ表示される  
・シミュレーションの開始ボタンのオンオフが高速で切り替わる

- tfが正常にpublishされているか確認する
tfの確認

記事の内容通りに実装した場合、tfのTreeはーrvizのパネルにある `tf` で確認できる、以下のようになっているはずです。

```text
base_footprint
    base_link
        base_scan
        wheel_left_link
        wheel_right_link
odom
```

以上のようになっていない場合、sdfファイルにおけるlinkの構成が間違っている可能性があります。

### 個別の対策

Lidarのデータが読み取れない

以下のようなエラーが出ていないか確認して下さい

```text
Message fillter dropping message:~
for reason because the qeue is full
```

もし出ていたら、fixed frameの指定が間違っている可能性が高いです。そのあたりを見直して下さい。  
sdfファイルで適切に `ignition_frame_id` の記述が行われているかも確認して下さい。

## 終わりに

　この記事ではrvizとgazeboの連携を確立し、それを通してrvizの基本的な機能を紹介しました。rvizは拡張性の高いツールでありー自分でプラグインを作成することもできます、これからのロボット開発の大きな助けとなるでしょう。  
　次回は [slam toolboxとnavigation2を用いた自律走行を行う(gz)](https://qiita.com/N622/items/9f372fcd4568342ad8f0) です。これまで行ってきたことの集大成として、gazebo+rvizでSLAMとnavigationに挑戦します。  
これらの記事の全体像が知りたければ、 [ignition gazebo+Rviz2の開発環境構築](https://qiita.com/N622/items/6ca879f8b1f9d667e561) も覗いてみてください。ここにはgazeboとrvizに関する私の書いた記事がまとめられています。  
　また、この記事で扱った内容を実装したコードは [私のgithub](https://github.com/OsawaKousei/2DRoboPrac_ws) に公開しています。今回の内容に相当する部分はリンク先のレポジトリの `gz_ws/src/nav_dev` に収められています。

## 参考文献

[Gazebo - Docs](https://gazebosim.org/docs/fortress/getstarted)  
[ROS 2 Documenttion](https://docs.ros.org/en/humble/index.html)  
[rviz-ROS Wiki](http://wiki.ros.org/rviz)

[0](https://qiita.com/N622/items/#comments)

コメント一覧へ移動

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2FN622%2Fitems%2Ff1c5dffa37e095116119&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2FN622%2Fitems%2Ff1c5dffa37e095116119&realm=qiita)

[3](https://qiita.com/N622/items/f1c5dffa37e095116119/likers)

いいねしたユーザー一覧へ移動

3