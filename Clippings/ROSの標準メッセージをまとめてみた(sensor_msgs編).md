---
title: "ROSの標準メッセージをまとめてみた(sensor_msgs編)"
source: "https://qiita.com/aquahika/items/6e3753d4498a190bddb1"
author:
  - "[[aquahika]]"
published: 2018-08-14
created: 2025-08-21
description: "ROS対応しているロボットをつくるとき、独自にメッセージを定義せず標準のメッセージを使えるようさくっとまとめてみます。 sensor_msgsのメッセージ一覧 Battery State http://docs.ros.org/api/sensor_msgs/html/..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

[@aquahika (Hikaru Sugiura)](https://qiita.com/aquahika)

最終更新日 投稿日 2018年08月14日

ROS対応しているロボットをつくるとき、独自にメッセージを定義せず標準のメッセージを使えるようさくっとまとめてみます。

## sensor\_msgsのメッセージ一覧

### Battery State

[http://docs.ros.org/api/sensor\_msgs/html/msg/BatteryState.html](http://docs.ros.org/api/sensor_msgs/html/msg/BatteryState.html)  
バッテリーの状態を示すメッセージ。充電状態・健康状態・電池の種類(Lipo/NiMHなど)・容量など

### Camera Info

[http://docs.ros.org/api/sensor\_msgs/html/msg/CameraInfo.html](http://docs.ros.org/api/sensor_msgs/html/msg/CameraInfo.html)  
カメラのメタデータ。歪(ディストーション)データなどを格納。 `image_raw`,`image`,`image_color`,`image_rect`,`image_rect_color` と共につかう。

### ChannelFloat32

[http://docs.ros.org/api/sensor\_msgs/html/msg/ChannelFloat32.html](http://docs.ros.org/api/sensor_msgs/html/msg/ChannelFloat32.html)  
PointCloudで使う？

### CompressedImage

[http://docs.ros.org/api/sensor\_msgs/html/msg/CompressedImage.html](http://docs.ros.org/api/sensor_msgs/html/msg/CompressedImage.html)  
圧縮した画像データ。圧縮方式の種類も格納。(pngやjpgなど)

### FluidPressure

[http://docs.ros.org/api/sensor\_msgs/html/msg/FluidPressure.html](http://docs.ros.org/api/sensor_msgs/html/msg/FluidPressure.html)  
フルード圧力。油圧・水圧・空気圧など。大気圧も格納可能。単位はPascel。圧が変動するか(?)も表現可能。

### Illuminance

[http://docs.ros.org/api/sensor\_msgs/html/msg/Illuminance.html](http://docs.ros.org/api/sensor_msgs/html/msg/Illuminance.html)  
照度。単位はLux。  
光度、輝度、放射照度を表現することは **できない**

### Image

[http://docs.ros.org/api/sensor\_msgs/html/msg/Image.html](http://docs.ros.org/api/sensor_msgs/html/msg/Image.html)  
非圧縮の画像。座標(0,0)は画像の左上。大きさやエンディアン、エンコーディングも格納。

### Imu

[http://docs.ros.org/api/sensor\_msgs/html/msg/Imu.html](http://docs.ros.org/api/sensor_msgs/html/msg/Imu.html)  
Imu(慣性計測装置)のデータ。クォータニオン(四元数)、角速度\[rad/sec\]、加速度\[m/s^2\]を格納。  
**IMUからクォータニオンが出力されていない場合でも使える。詳細は公式ページで**

### JointState

[http://docs.ros.org/api/sensor\_msgs/html/msg/JointState.html](http://docs.ros.org/api/sensor_msgs/html/msg/JointState.html)  
ジョイントの状態を示す。ジョイントの位置または角度(radまたはm)・速さ(rad/sまたはm/s)・負荷(NmまたはN)を格納。

### Joy

[http://docs.ros.org/api/sensor\_msgs/html/msg/Joy.html](http://docs.ros.org/api/sensor_msgs/html/msg/Joy.html)  
ジョイスティックやゲームパッド用。axes(ジョイスティック)、buttons(ボタン)がある  
(筆者メモ)  
ジョイスティックの軸の方向は定められていないが、多くのノードが左下が(X:Y)=(axes\[0\]:axes\[1\])=(1.0,1.0)、右上が(X:Y)=(-1.0,-1.0)になっているぽい？

### JoyFeedBack

[http://docs.ros.org/api/sensor\_msgs/html/msg/JoyFeedback.html](http://docs.ros.org/api/sensor_msgs/html/msg/JoyFeedback.html)  
ジョイスティックにLED・ブザー・振動などでフィードバックを返すためのメッセージ。

### JoyFeedbackArray

[http://docs.ros.org/api/sensor\_msgs/html/msg/JoyFeedbackArray.html](http://docs.ros.org/api/sensor_msgs/html/msg/JoyFeedbackArray.html)  
JoyFeedBackの配列。複数のフィードバックを一度に渡すため。

### LaserEcho

[http://docs.ros.org/api/sensor\_msgs/html/msg/LaserEcho.html](http://docs.ros.org/api/sensor_msgs/html/msg/LaserEcho.html)  
MultiEchoLaserScanメッセージのサブメッセージで個別に使われるべきではない。  
(筆者メモ)  
ガラスなどの透過物が見えるマルチエコー対応のLIDERやLRF用のメッセージかな？

### LaserScan

[http://docs.ros.org/api/sensor\_msgs/html/msg/LaserScan.html](http://docs.ros.org/api/sensor_msgs/html/msg/LaserScan.html)  
LRF/LIDER(おそらく2Dのみ)用のメッセージ。測定角度範囲・角度分解能・スキャンタイム・測定距離範囲・測定結果などを格納。

### MagneticField

[http://docs.ros.org/api/sensor\_msgs/html/msg/MagneticField.html](http://docs.ros.org/api/sensor_msgs/html/msg/MagneticField.html)  
磁気センサー用のデータ。センサーが３軸測定していない場合は無い軸にNaNを入れる。各軸の共分散も格納可能。

### MultiDOFJointState

[http://docs.ros.org/api/sensor\_msgs/html/msg/MultiDOFJointState.html](http://docs.ros.org/api/sensor_msgs/html/msg/MultiDOFJointState.html)  
多自由度ロボット用の状態?

### MultiEchoLaserScan

[http://docs.ros.org/api/sensor\_msgs/html/msg/MultiEchoLaserScan.html](http://docs.ros.org/api/sensor_msgs/html/msg/MultiEchoLaserScan.html)  
マルチエコーレーザースキャナー用のメッセージ。LaserScanのrange(測定結果)がLaserEchoメッセージになって同じ測定軸上に複数点の情報が載せられるようになった版。

### NavSatFix

[http://docs.ros.org/api/sensor\_msgs/html/msg/NavSatFix.html](http://docs.ros.org/api/sensor_msgs/html/msg/NavSatFix.html)  
GNSS用のFixデータ？

### NavSatStatus

[http://docs.ros.org/api/sensor\_msgs/html/msg/NavSatStatus.html](http://docs.ros.org/api/sensor_msgs/html/msg/NavSatStatus.html)  
GNSS衛星のステータス。GPS/GLONASS/Galileoの/Beiduの識別・SBASやGBASによるFixの識別。

### PointCloud

[http://docs.ros.org/api/sensor\_msgs/html/msg/PointCloud.html](http://docs.ros.org/api/sensor_msgs/html/msg/PointCloud.html)  
点群データ。今からは下のPointCloud2推奨？

### PointCloud2

[http://docs.ros.org/api/sensor\_msgs/html/msg/PointCloud2.html](http://docs.ros.org/api/sensor_msgs/html/msg/PointCloud2.html)  
点群データ。現在は上記のPointCloudよりもこちらのほうが主流？

### PointField

[http://docs.ros.org/api/sensor\_msgs/html/msg/PointField.html](http://docs.ros.org/api/sensor_msgs/html/msg/PointField.html)  
PointCloud2の中で使われているメッセージ。詳細省略。

### Range

[http://docs.ros.org/api/sensor\_msgs/html/msg/Range.html](http://docs.ros.org/api/sensor_msgs/html/msg/Range.html)  
一点の距離を測定した結果を格納するメッセージ。測定方法(IRや音),視野角\[rad\]や最大・最低測定可能距離\[m\]、測定結果\[m\]を格納できる。  
PIDやToF,超音波などの測距センサに使える。

### RegionOfInterest

[http://docs.ros.org/api/sensor\_msgs/html/msg/RegionOfInterest.html](http://docs.ros.org/api/sensor_msgs/html/msg/RegionOfInterest.html)  
カメラのROI。カメラ画像の特定の箇所にピントを合わせたりするときの、場所を指定するメッセージ。

### RelativeHumidity

[http://docs.ros.org/api/sensor\_msgs/html/msg/RelativeHumidity.html](http://docs.ros.org/api/sensor_msgs/html/msg/RelativeHumidity.html)  
湿度。1.0が100%。

### Temperature

[http://docs.ros.org/api/sensor\_msgs/html/msg/Temperature.html](http://docs.ros.org/api/sensor_msgs/html/msg/Temperature.html)  
温度。単位は摂氏。

### TimeReference

[http://docs.ros.org/api/sensor\_msgs/html/msg/TimeReference.html](http://docs.ros.org/api/sensor_msgs/html/msg/TimeReference.html)  
外部からの時間の基準。電波時計とかつなげて使う...?

## 参考

- ROS公式のsensor\_msgsのページ  
	[http://wiki.ros.org/sensor\_msgs](http://wiki.ros.org/sensor_msgs)

[0](https://qiita.com/aquahika/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Faquahika%2Fitems%2F6e3753d4498a190bddb1&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Faquahika%2Fitems%2F6e3753d4498a190bddb1&realm=qiita)

[30](https://qiita.com/aquahika/items/6e3753d4498a190bddb1/likers)

いいねしたユーザー一覧へ移動

24