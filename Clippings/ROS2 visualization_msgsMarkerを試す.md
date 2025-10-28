---
title: "ROS2: visualization_msgs/Markerを試す"
source: "https://zenn.dev/tasada038/articles/f5ca6732871d8e"
author:
  - "[[Zenn]]"
published: 2023-02-26
created: 2025-08-28
description:
tags:
  - "clippings"
---
2[tech](https://zenn.dev/tech-or-idea)

海洋ロボコンをやってた人です。  
今回は、ROS 2を用いたPCL+Realsenseの3次元点群を用いて、 **visualization\_msgs/Marker** を試してみたので、その備忘録としてまとめました。

モチベーションはGrowing Neural Gas(GNG) を実装する際に使いたかったためです。

自分自身の備忘録としていますが、本学の後輩への引き継ぎとなればとも思っています。

また、何か間違っている箇所等あれば、お気軽にご教授いただけると嬉しく思います。

## 本記事のプログラム

この記事で扱うプログラムのコードはGithubよりクローンしてご活用ください。

ディレクトリは「src/visualization\_marker」です。

## ROS2: visualization\_msgs/Markerとは

はじめに、ROS 2 visualization\_msgs/Markerとはwiki.ros.orgによると様々なシェイプを可視化できるマーカーになります。

> visualization\_msgs/Marker または visualization\_msgs/MarkerArray メッセージで、さまざまなプリミティブ シェイプをプログラムで 3D ビューに追加できる。  
> 引用：　 [http://wiki.ros.org/rviz/DisplayTypes/Marker](http://wiki.ros.org/rviz/DisplayTypes/Marker)

ROS/ROS 2 で予め用意されたシェイプ以外に、独自でシェイプ等を作成する際に役立てます。

**visualization\_msgs/Marker** は、以下のようにMessageが定義されており、uint8型で各シェイプの形状を変更することができます。  
また、定義したシェイプの位置(pose)、スケール(scale)、カラー(color)も自在に変更することができます。

さらに、シェイプを配列として持つ **visualization\_msgs/MarkerArray** を使用することで、一つのtopicで複数のシェイプを送信することが可能です。

本記事では、MarkerArrayを使用して、4種類のシェイプを試してみたので、そのサンプルを紹介します。

## SPHEPE

SPHEPEでは球を表示できます。  
scaleの値を異なる値とすると、球体ではなく楕円になります。  
そのため、球体を表示させる場合はscale.x=scale.y=scale.zとする必要があります。  
marker.typeは「SPHERE=2」です。

![](https://storage.googleapis.com/zenn-user-upload/62f183610886-20230226.png)

## ARROW

ARROWは矢印のシェイプを表示できます。  
scale.xは矢印(線)の長さ、scale.yは矢印(ヘッド)の幅、scale.zは矢印(ヘッド)の高さです。  
矢印を明確に表示する場合は、scale.x > scale.y, scale.zとなるのが自然かと思います。  
※　矢印の線の長さが矢印のヘッドよりも長い方が「→」として綺麗に表示されるため

marker.typeは「ARROW=0」です。

また、ARROW等の向きは、pose.orientation.x/y/z/wの単位クォータニオン(unit quaternion)で表しており

$$
||q|| = \sqrt{{q_x}^2+{q_y}^2+{q_z}^2+{q_w}^2}
$$

を満たす必要があると思います。

rspcl\_marker.cpp

```cpp
// ARROW_r
        visualization_msgs::msg::Marker arrow_r;
        arrow_r.header.frame_id = "camera_color_optical_frame";
        arrow_r.header.stamp = this->now();
        arrow_r.ns = "arrow";
        arrow_r.id = id++;
        arrow_r.type = visualization_msgs::msg::Marker::ARROW;
        arrow_r.action = visualization_msgs::msg::Marker::ADD;
        arrow_r.pose.position.x = geometry_points[i].x;
        arrow_r.pose.position.y = geometry_points[i].y;
        arrow_r.pose.position.z = geometry_points[i].z;
        arrow_r.pose.orientation.x = 0.0;
        arrow_r.pose.orientation.y = 0.0;
        arrow_r.pose.orientation.z = 0.0;
        arrow_r.pose.orientation.w = 1.0;
        arrow_r.scale.x = 0.1;
        arrow_r.scale.y = arrow_r.scale.z = 0.01;
        arrow_r.color.r = 1.0;
        arrow_r.color.g = 0.0;
        arrow_r.color.b = 0.0;
        arrow_r.color.a = 1.0;
        arrow_r.lifetime = rclcpp::Duration(0.0);

        // ARROW_g
        visualization_msgs::msg::Marker arrow_g;
        arrow_g.header.frame_id = "camera_color_optical_frame";
        arrow_g.header.stamp = this->now();
        arrow_g.ns = "arrow";
        arrow_g.id = id++;
        arrow_g.type = visualization_msgs::msg::Marker::ARROW;
        arrow_g.action = visualization_msgs::msg::Marker::ADD;
        arrow_g.pose.position.x = geometry_points[i].x;
        arrow_g.pose.position.y = geometry_points[i].y;
        arrow_g.pose.position.z = geometry_points[i].z;
        arrow_g.pose.orientation.x = 0.0;
        arrow_g.pose.orientation.y = -0.707;
        arrow_g.pose.orientation.z = 0.0;
        arrow_g.pose.orientation.w = 0.707;
        arrow_g.scale.x = 0.1;
        arrow_g.scale.y = arrow_g.scale.z = 0.01;
        arrow_g.color.r = 0.0;
        arrow_g.color.g = 1.0;
        arrow_g.color.b = 0.0;
        arrow_g.color.a = 1.0;
        arrow_g.lifetime = rclcpp::Duration(0.0);
```

![](https://storage.googleapis.com/zenn-user-upload/73370e97ff6e-20230226.png)

## View-Oriented Text

TEXT\_VIEW\_FACINGはテキストを表示できます。  
テキストのスケールはscale.zのみで表現し、この値を大きくすると、フォントサイズが増加します。  
marker.typeは「TEXT\_VIEW\_FACING=9」です。

![](https://storage.googleapis.com/zenn-user-upload/e8b03bdf9fdc-20230226.png)

## LINE\_STRIP

LINE\_STRIPは始点と終点間を繋ぐ線を表示できます。  
スケールはscale.xのみを使用し、線の幅を変更することができます。  
marker.typeは「LINE\_STRIP=4」です。

![](https://storage.googleapis.com/zenn-user-upload/3f54be9dc6e6-20230226.png)

## TRIANGLE\_LIST

TRIANGLE\_LISTは3点間のPointを塗りつぶして三角面を描画します。  
marker.typeは「TRIANGLE\_LIST=11」です。

![](https://storage.googleapis.com/zenn-user-upload/bab14b30fd25-20230410.png)

## Mesh Resource

Rvizでサポートしている、.stl,.dae等を表現できます。  
Meshを使用する場合は、以下をプログラムに追記する必要があります。

```cpp
mesh_marker.mesh_resource = "package://rviz_collada_marker/nil_link_mesh.dae"
mesh_marker.mesh_use_embedded_materials = True
```

marker.typeは「MESH\_RESOURCE=10」です。

こちらは試せていませんが、garemonさんのプログラムを参考にしてみると良いと思います。

  

以上、ご参考にしていただければ幸いです。

2

2