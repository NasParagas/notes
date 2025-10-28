---
title: "DDS (Data Distribution Service) とは"
source: "https://www.youtalk.jp/2017/05/28/dds.html"
author:
  - "[[Yutaka Kondo]]"
published: 2017-05-28
created: 2025-10-21
description: "本日はROS2で採用となったミドルウェアであるDDS (Data Distribution Service)について、下記記事の日本語要約をしていきます。"
tags:
  - "clippings"
---
DDS (Data Distribution Service) とは

本日はROS2で採用となったミドルウェアである [DDS (Data Distribution Service)](http://portals.omg.org/dds/) について、下記記事の日本語要約をしていきます。

[http://design.ros2.org/articles/ros\_on\_dds.html](http://design.ros2.org/articles/ros_on_dds.html)

ROS 2がデフォルトで使っている実装であるeProsimaのFastRTPSのライセンスに関しては、記事ではLGPLのままの説明になっていますが、現在はOSRFの働きにより、Apache 2に変更された内容で説明しています。

OpenRTMに対するCORBAのことを思い出すと、感慨深い内容になっています。

## なぜDDSになったか？

ROSの次世代通信システムを考えているとき、まず選択肢に挙がったのは、 [ZeroMQ](http://zeromq.org/), [Protocol Buffers](https://developers.google.com/protocol-buffers/), [zeroconf](http://zeroconf.org/) などのミドルウェアを使ったデータ転送の改善でした。

しかし、最終的に選んだのはエンドツーエンドの通信ミドルウェアであるDDSでした。 DDSのようなエンドツーエンドの通信ミドルウェアの利点は、ROS 2がメンテナンスするコードを減らせること、明確な仕様がドキュメント化されている点です。

DDSはシステムレベルのドキュメントも整備されており、サードパーティがレビューでき、監査でき、高い互換性を持って再実装できます。 これはROSでは実現できなかったことです。

エンドツーエンドの通信ミドルウェアを使う欠点は、その設計にROSが引きずられて動かす必要があることです。 もし、その設計がROSのユースケースに合致しなかったり、不自由だったりすれば、設計からやり直しが必要です。 エンドツーエンドの通信ミドルウェアを採用するということは、その哲学や文化も（ある程度）採用することになり、気軽に選んでいいものではありません。

## DDSとは何か？

DDSはROSのPub/Sub通信とよく似たデータ転送を提供します。 [OMG](http://www.omg.org/) によって策定されたIDL (Interface Description Language) を使って、メッセージを定義し、シリアライズします。 DDSにはROS Serviceと同じリクエスト・レスポンス形式のデータ転送も策定が進んでいます（ [DDS-RPC](http://www.omg.org/spec/DDS-RPC/) ）。

DDSのPub/Sub通信に必要となる送信元、受信先の発見メカニズムは、分散化されています。つまり、ROS masterのようなツールが必要なくなります。 これにより、システムは単一障害点がなくなり、自由度が高くなります。

このような動的な発見メカニズムの代わりに、静的な発見メカニズムを提供するDDSベンダーも複数あります。

DDSは下記のような現場で、すでに必要不可欠な技術となっています。 これらの成功はDDSの設計の信頼性と自由度を証明してくれています。

- 戦艦
- ダムのような大規模施設
- 金融システム
- 宇宙、航空システム
- 電車の線路切り替え機

DDSの配信（訳注：wireの意）仕様である [DDSI-RTPS](http://www.omg.org/spec/DDSI-RTPS/) は非常に自由度が高く、組込み機器のリアルタイムアプリケーションにも統合することができます。

DDSはUDPを使うため、信頼性のある転送や通信ハードウェアを必要としません。 DDS自身が信頼性という車輪（TCPにいくつか機能を追加、削除したもの）を再発明することで、DDSはポータビリティと通信制御性を獲得しました。

Quality of Service (QoS) と呼ぶいくつかのパラメータを使って通信の信頼性を制御します。 リアルタイムのような応答速度を考慮するなら、DDSはUDPのように調整されます。通信の欠損を許すような通信制御もQoSパラメータの変更で対応できます。

DDSはデフォルトではUDPを使いますが、TCPを使う仕様も策定済みであり、すでに2つのベンダーが実装しています。

## ベンダーとライセンス

OMGはDDSの仕様策定の際、いくつかの企業と一緒に策定しました。これらの企業の中には現在、主要なDDSベンダーになっている企業もあります。

- RTI
- PrismTech
- Twin Oaks Software

これらに加えて、DDSの配信仕様であるRTPSへ直接アクセスできる実装を提供するソフトウェアベンダーもあります。この場合、ROSをその上層に実装しやすくなります。

- eProsima

ただし、これらの企業のライセンス形式はROSコミュニティにとっては使いづらいものでした。 LGPLでライセンスするものもありましたが、ROSはBSD形式のライセンスを提供し続けたい姿勢を取っています。 eProsimaのFastRTPSも当初、LGPLライセンスでしたが、現在はApach 2.0に移行し、ROSコミュニティと相性が良いものになりました。

[https://github.com/eProsima/Fast-RTPS](https://github.com/eProsima/Fast-RTPS)

現在では、このFastRTPSがROS 2のデフォルトDDSミドルウェアとして採用されています。

This post is licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) by the author.