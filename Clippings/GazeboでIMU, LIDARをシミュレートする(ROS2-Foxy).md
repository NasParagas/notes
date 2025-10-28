---
title: "GazeboでIMU, LIDARをシミュレートする(ROS2-Foxy)"
source: "https://nutritionfoodtech.com/2022/10/18/gazebo%E3%81%A7imu-lidar%E3%82%92%E3%82%B7%E3%83%9F%E3%83%A5%E3%83%AC%E3%83%BC%E3%83%88%E3%81%99%E3%82%8Bros2-foxy/#toc4"
author:
  - "[[Kame-SanSun]]"
published: 2022-10-18
created: 2025-08-26
description: "はじめに 前回はディファレンシャルドライブとカメラを Gazebo 上でシミュレートする方法を説明しました。 https://nutritionfoodtech.com/2022/10/15/gazebo%e3%81%a7%e3%83%87"
tags:
  - "clippings"
---
## はじめに

[前回](https://nutritionfoodtech.com/2022/10/15/gazebo%e3%81%a7%e3%83%87%e3%82%a3%e3%83%95%e3%82%a1%e3%83%ac%e3%83%b3%e3%82%b7%e3%83%a3%e3%83%ab%e3%83%89%e3%83%a9%e3%82%a4%e3%83%96%e3%80%81%e3%82%ab%e3%83%a1%e3%83%a9%e3%82%92%e3%82%b7%e3%83%9f/) はディファレンシャルドライブとカメラを Gazebo 上でシミュレートする方法を説明しました。

![](https://nutritionfoodtech.com/wp-content/uploads/2022/10/gazebo3-e1668928975559-160x90.png)

今回は SLAM(自己位置推定) に必要な IMU と LIDAR を Gazebo 上でシミュレートする方法について説明します。

コードの説明が多くなりますが、最後には視覚的に分かりやすくできたらと思います。

## 前提条件

前提条件は以下の通りです。

- ディファレンシャルドライブとカメラを Gazebo 上でシミュレートできている
- IMU と LIDAR の URDF が記述されている

Gazebo プラグインの説明は [Gazebo公式サイト](https://classic.gazebosim.org/tutorials?tut=ros_gzplugins#Tutorial:UsingGazebopluginswithROS) にもあります。

## IMUをGazebo上でシミュレート (URDF作成)

first\_robot.urdf に、IMU のプラグインを追加していきます。  
[前回](https://nutritionfoodtech.com/2022/10/15/gazebo%e3%81%a7%e3%83%87%e3%82%a3%e3%83%95%e3%82%a1%e3%83%ac%e3%83%b3%e3%82%b7%e3%83%a3%e3%83%ab%e3%83%89%e3%83%a9%e3%82%a4%e3%83%96%e3%80%81%e3%82%ab%e3%83%a1%e3%83%a9%e3%82%92%e3%82%b7%e3%83%9f/) 、カメラで説明した部分は省略します。

まずは、全体のコードを示します。

```
<!-- imu plugin -->
<gazebo reference="imu_link">
    <sensor name="gazebo_imu" type="imu">
        <gravity>true</gravity>
        <visualize>true</visualize>
        <update_rate>60</update_rate>
        <always_on>true</always_on>
        <plugin filename="libgazebo_ros_imu_sensor.so" name="imu_plugin">
            <ros>
                <namespace>/first_robot</namespace>
                <remapping>~/out:=imu</remapping>
            </ros>
            <initial_orientation_as_reference>false</initial_orientation_as_reference>
            <output_type>sensor_msgs/Imu</output_type>
            <frame_name>imu_link</frame_name>
        </plugin>
        <imu>
            <angular_velocity>
                <x>
                    <noise type="gaussian">
                        <mean>0.0</mean>
                        <stddev>2e-04</stddev>
                        <bias_mean>0.0000075</bias_mean>
                        <bias_stddev>0.0000008</bias_stddev>
                    </noise>
                </x>
                <y>
                    <nosie type="gaussian">
                        <mean>0.0</mean>
                        <stddev>2e-04</stddev>
                        <bias_mean>0.0000075</bias_mean>
                        <bias_stddev>0.0000008</bias_stddev>
                    </nosie>
                </y>
                <z>
                    <nosie type="gaussian">
                        <mean>0.0</mean>
                        <stddev>2e-04</stddev>
                        <bias_mean>0.0000075</bias_mean>
                        <bias_stddev>0.0000008</bias_stddev>
                    </nosie>
                </z>
            </angular_velocity>
            <linear_acceleration>
                <x>
                    <nosie type="gaussian">
                        <mean>0.0</mean>
                        <stddev>1.7e-02</stddev>
                        <bias_mean>0.001</bias_mean>
                        <bias_stddev>0.001</bias_stddev>
                    </nosie>
                </x>
                <y>
                    <nosie type="gaussian">
                        <mean>0.0</mean>
                        <stddev>1.7e-02</stddev>
                        <bias_mean>0.001</bias_mean>
                        <bias_stddev>0.001</bias_stddev>
                    </nosie>
                </y>
                <z>
                    <nosie type="gaussian">
                        <mean>0.0</mean>
                        <stddev>1.7e-02</stddev>
                        <bias_mean>0.001</bias_mean>
                        <bias_stddev>0.001</bias_stddev>
                    </nosie>
                </z>
            </linear_acceleration>
        </imu>
    </sensor>
</gazebo>
```

上から順に、説明していきます。

```
<gravity>true</gravity>
<visualize>true</visualize>
```

<gravity>はシミュレーション空間に重力が働いてるかどうか指定します。  
<visualize>は Gazebo 上で IMU のを表示するかどうか指定します。  
※私の場合、true にして Gazebo で確認しましたが、表示されませんでした。

```
<plugin filename="libgazebo_ros_imu_sensor.so" name="imu_plugin">
  <ros>
    <namespace>/first_robot</namespace>
    <remapping>~/out:=imu</remapping>
  </ros>
  <initial_orientation_as_reference>false</initial_orientation_as_reference>
  <output_type>sensor_msgs/Imu</output_type>
  <frame_name>imu_link</frame_name>
</plugin>
```

Imu の プラグイン部分ですが、上記の場合、imu\_link の位置にて慣性を計測します。  
計測結果は /first\_robot/imu の topic として出力されます。  
出力内容は<output\_type>タグで指定します。 sensor\_msgs/Imu と記述されていますが、  
これは [ROS公式サイト](http://docs.ros.org/en/api/sensor_msgs/html/msg/Imu.html) に書いてある通りの決められた定義で出力されます。

<initial\_orientation\_as\_reference>は、公式サイトに記載してありませんでしたが、試してみたところ true にすると初期位置をIMUの原点としているように見えます。  
各パラメータの実験は別の記事にまとめますので、今回はひとまず false としておいてください。

```
<nosie type="gaussian">
  <mean>0.0</mean>
  <stddev>1.7e-02</stddev>
  <bias_mean>0.001</bias_mean>
  <bias_stddev>0.001</bias_stddev>
</nosie>
```

bias は、偏りを表します。平均の偏りを0.1以内、標準偏差の偏りを0.001にします。

以上で、IMU の設定は完了です。

## LIDARをGazebo上でシミュレート (URDF作成)

次に、LIDAR を Gazebo 上でシミュレートするためのプラグインをURDFに追加します。

まずは、全体のコードです。

```
<!-- lidar plugin -->
<gazebo reference="lidar_link">
    <sensor name="gazebo_lidar" type="ray">
        <always_on>true</always_on>
        <visualize>true</visualize>
        <update_rate>60</update_rate>
        <plugin filename="libgazebo_ros_ray_sensor.so" name="gazebo_lidar">
            <ros>
                <remapping>~/out:=scan</remapping>
            </ros>
            <output_type>sensor_msgs/LaserScan</output_type>
            <frame_name>lidar_link</frame_name>
        </plugin>
        <ray>
            <scan>
                <horizontal>
                    <samples>360</samples>
                    <resolution>1.000000</resolution>
                    <min_angle>0.000000</min_angle>
                    <max_angle>3.140000</max_angle>
                </horizontal>
            </scan>
            <range>
                <min>0.150</min>
                <max>6.0</max>
                <resolution>0.015</resolution>
            </range>
            <noise>
                <type>gaussian</type>
                <mean>0.0</mean>
                <stddev>0.01</stddev>
            </noise>
        </ray>
    </sensor>
    <material>Gazebo/DarkGrey</material>
</gazebo>
```

上から説明していきます。

```
<output_type>sensor_msgs/LaserScan</output_type>
<frame_name>lidar_link</frame_name>
```

<output\_type>は IMU にもあるタグですが、今回は sensor\_msgs/LaserScan を指定します。  
sensor\_msgs/LaserScan の詳細は [こちらの公式サイト](http://docs.ros.org/en/noetic/api/sensor_msgs/html/msg/LaserScan.html) に記載されています。  

```
<scan>
  <horizontal>
    <samples>360</samples>
    <resolution>1.000000</resolution>
    <min_angle>0.000000</min_angle>
    <max_angle>3.140000</max_angle>
  </horizontal>
</scan>
```

<scan>タグは LIDAR のスキャンの設定を行います。  
<horizontal>タグは水平方向のスキャンの内容を設定します。

<samples>タグはスキャン点の数のように見えますが、違います。  
samples の数値は、<min\_angle>~<max\_angle> の中にいくつの点があるかを指定します。

<resolution>タグはスキャン点の補間の割合を選択します。  
詳細は [Gazeboの公式サイト](https://classic.gazebosim.org/tutorials?tut=guided_i1) にありますが、例えば 10 にすると、samples x resolution = 3600となり、0.1° 毎にスキャン点が生成されます。ですが、1° のうち 9 点は補間された点になります。  
<resolution>は特別な条件が必要でない限り、1 にしておきましょう。

<min\_angle>と<max\_angle>は LIDAR のスキャン角度を表します。rad 表記となります。

```
<range>
  <min>0.150</min>
  <max>6.0</max>
  <resolution>0.015</resolution>
</range>
```

<range>は LIDAR のスキャン距離を指定します。  
<min><max>はそれぞれ最小検出距離、最大検出距離を表します。  
<resolution>は直線距離の分解能を表します。が、数値を変化させても視覚的な変化がなかったのでこちらも今後検証していきます。

以上で URDF への記述は完了です。

## IMU, LIDAR をGazebo上でシミュレート

長くなりましたが、Gazebo上でシミュレートする方法を説明します。

[前回の記事](https://nutritionfoodtech.com/2022/10/15/gazebo%e3%81%a7%e3%83%87%e3%82%a3%e3%83%95%e3%82%a1%e3%83%ac%e3%83%b3%e3%82%b7%e3%83%a3%e3%83%ab%e3%83%89%e3%83%a9%e3%82%a4%e3%83%96%e3%80%81%e3%82%ab%e3%83%a1%e3%83%a9%e3%82%92%e3%82%b7%e3%83%9f/) で launch ファイルを作成済みだとは思いますが、念のため載せておきます。  
first\_gazebo.launch.py が launch フォルダにあるか確認してください。

あとはいつも通りターミナル上でパッケージをビルドします。

```
cd ~/ros_ws
colcon build --packages-select ros2_first_test
source ~/.bashrc
ros2 launch ros2_first_test first_gazebo.launch.py
```

Rviz2 と Gazebo が起動します。

![](https://nutritionfoodtech.com/wp-content/uploads/2022/10/gazebo6-1024x579.png)

Gazebo 上ではこのように表示されていれば正常に動作しています。

Rviz2 で LIDAR を表示する手順を説明します。  
Add > By topic > LaserScan をクリックしてください。

![](https://nutritionfoodtech.com/wp-content/uploads/2022/10/Rviz9-1024x782.png)

また、障害物がないと LIDAR のレーザーが反応しませんので、Gazebo 上に障害物を設置します。

![](https://nutritionfoodtech.com/wp-content/uploads/2022/10/Gazebo7.png)

上記画像の赤丸をクリックしてください。その後、LIDAR の青色の円内でもう一度クリックしてください。すると、Gazebo 上に箱が現れます。

![](https://nutritionfoodtech.com/wp-content/uploads/2022/10/Gazebo8.png)

この時、Rvizを確認してみましょう。

![](https://nutritionfoodtech.com/wp-content/uploads/2022/10/Gazebo9.png)

黄色丸部分のように赤色の点群が生成されているはずです。

これで、LIDAR のシミュレートは完了です。  
今回の Rviz2 の設定は今後も使用するので rviz フォルダ内に first\_robot.rviz として保存しておいてください。  
保存方法は [以前の記事](https://nutritionfoodtech.com/2022/10/14/urdf%e3%82%92rviz2%e3%81%a8gazebo%e3%81%a7%e8%a1%a8%e7%a4%ba%e3%81%99%e3%82%8b-ros2-foxy/) に記載されています。

## おわりに

GazeboでIMU, LIDARをシミュレートする方法を説明しました。  
ようやく、一区切りです。  
[次回](https://nutritionfoodtech.com/2022/10/24/gazebo%e3%82%b7%e3%83%9f%e3%83%a5%e3%83%ac%e3%83%bc%e3%83%88%e7%92%b0%e5%a2%83%e3%81%a7topic%e3%81%ae%e7%a2%ba%e8%aa%8d%e3%83%bb%e5%8f%96%e5%be%97%e3%83%bb%e5%87%a6%e7%90%86%e6%96%b9%e6%b3%95ros2-fox/) から少し短く区切って投稿していきます。

[次回](https://nutritionfoodtech.com/2022/10/24/gazebo%e3%82%b7%e3%83%9f%e3%83%a5%e3%83%ac%e3%83%bc%e3%83%88%e7%92%b0%e5%a2%83%e3%81%a7topic%e3%81%ae%e7%a2%ba%e8%aa%8d%e3%83%bb%e5%8f%96%e5%be%97%e3%83%bb%e5%87%a6%e7%90%86%e6%96%b9%e6%b3%95ros2-fox/) は、簡易的なシミュレーション環境を構築したので ロボットへの色設定や、ROS2-Foxy の topic や node の説明、そしてそれらの確認方法を説明しようと思います。  
余裕があればそれらを Python で処理する方法についても解説できればと考えています。

![](https://nutritionfoodtech.com/wp-content/uploads/2022/10/Gazebo10-e1668929066168-160x90.png)

タイトルとURLをコピーしました