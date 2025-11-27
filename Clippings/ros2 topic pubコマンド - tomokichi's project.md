---
title: "ros2 topic pubコマンド - tomokichi's project"
source: "https://scrapbox.io/tomokun-35135655/ros2_topic_pub%E3%82%B3%E3%83%9E%E3%83%B3%E3%83%89"
author:
published:
created: 2025-11-07
description:
tags:
  - "clippings"
---
r o s 2 t o p i c p u b コ マ ン ド

簡 単 に 具 体 的 な 数 字 で デ バ ッ グ を 行 い た い 時 に 使 う

r o s 2 t o p i c l i s t

ト ピ ッ ク の 名 前 を 知 る

[h t t p s:/ / d o c s.r o s.o r g / e n / f o x y / T u t o r i a l s / B e g i n n e r \- C L I \- T o o l s / U n d e r s t a n d i n g \- R O S 2 \- T o p i c s / U n d e r s t a n d i n g \- R O S 2 \- T o p i c s.h t m l # r o s 2 \- t o p i c \- p u b](https://docs.ros.org/en/foxy/Tutorials/Beginner-CLI-Tools/Understanding-ROS2-Topics/Understanding-ROS2-Topics.html#ros2-topic-pub)

r o s 2 t o p i c p u b < t o p i c \_ n a m e \> < m s g \_ t y p e \> ' < a r g s \> '

公 式 に は こ の よ う に 書 い て い る

m s g \_ t y p e と は 、 a r g s と は 、 何 を 入 れ れ ば い い の だ

さ ら に a r g s に は y a m l 形 式 で 書 け と あ る

m s g \_ t y p e に は 、 I n t 3 2 な ら I n t 3 2 だ け で は 不 十 分

[h t t p:/ / d o c s.r o s.o r g / e n / n o e t i c / a p i / s t d \_ m s g s / h t m l / m s g / I n t 3 2.h t m l](http://docs.ros.org/en/noetic/api/std_msgs/html/msg/Int32.html)

s t d \_ m s g s / I n t 3 2 と 書 か な く て は な ら な い

a r g s に は p u b l i s h し た い 値 を 入 れ る 。 し か し 値 だ け で は 不 十 分

i n t 3 2 d a t a

y a m l 形 式 な の で d a t a と い う の を 使 っ て 表 記 す る 必 要 が あ る

d a t a:1 0

ス ペ ー ス も 重 要 。 そ し て ' ' で 囲 う

' d a t a:1 0 '

以 上 か ら

/ i m g \_ n u m b e r と い う ト ピ ッ ク に i n t 3 2 の 型 で 1 0 と い う 値 を 送 り た い 場 合 は こ う な る

`[ros2topicpub](https://scrapbox.io/api/code/tomokun-35135655/ros2_topic_pub%E3%82%B3%E3%83%9E%E3%83%B3%E3%83%89/ros2%20topic%20pub)`

```
ros2 topic pub /img_number std_msgs/Int32 'data: 10'
```