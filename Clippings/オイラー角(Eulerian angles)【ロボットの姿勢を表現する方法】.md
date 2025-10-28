---
title: "オイラー角(Eulerian angles)【ロボットの姿勢を表現する方法】"
source: "https://akinami-robotics.hatenablog.com/entry/2024/05/26/181350"
author:
  - "[[akinami327]]"
published: 2024-05-26
created: 2025-08-26
description: "引用：オイラー角(wikipedia) 関連用語：クォータニオン オイラー角(Eulerian angles) オイラー角は、3次元空間における剛体の姿勢を表現する方法の一つであり、以下の3つの角度で構成されます。 ロール（Roll, φ）：縦軸（X軸）周りの回転 ピッチ（Pitch, θ）：横軸（Y軸）周りの回転 ヨー（Yaw, ψ）：垂直軸（Z軸）周りの回転 オイラー角 回転の順序 オイラー角では、一般に ヨー → ピッチ → ロール の順番で回転させることで姿勢を表現します。 なお、上記以外の順番で回転させることもあり、同じオイラー角でも回転の順番によって最終的な姿勢が異なることに注意し…"
tags:
  - "clippings"
---
![おすすめ](https://cdn-ak.f.st-hatena.com/images/fotolife/a/akinami327/20240524/20240524204629.png)

[![カテゴリ別ロボット用語](https://cdn-ak.f.st-hatena.com/images/fotolife/a/akinami327/20240524/20240524203428.png)](https://akinami-robotics.hatenablog.com/entry/2024/05/26/%E8%A8%98%E4%BA%8B%E3%81%AEURL)

[![ロボティクスの辞書](https://cdn-ak.f.st-hatena.com/images/fotolife/a/akinami327/20240524/20240524202809.png)](https://qiita.com/akinami/items/eb0741b0d9c322e5d5ec)

[![smabo](https://cdn-ak.f.st-hatena.com/images/fotolife/a/akinami327/20240524/20240524201846.png)](https://zenn.dev/akinami/books/f8f2effbc79894)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/akinami327/20240526/20240526180649.png)

引用： オイラー角(wikipedia)

関連用語： [クォータニオン](https://akinami-robotics.hatenablog.com/entry/2024/05/27/204608)

## オイラー角(Eulerian angles)

[オイラー](https://d.hatena.ne.jp/keyword/%A5%AA%A5%A4%A5%E9%A1%BC) 角は、3次元空間における **剛体の姿勢を表現する方法** の一つであり、以下の3つの角度で構成されます。

- **ロール（Roll, φ）** ：縦軸（X軸）周りの回転
- **ピッチ（Pitch, θ）** ：横軸（Y軸）周りの回転
- **ヨー（Yaw, ψ）** ：垂直軸（Z軸）周りの回転
![オイラー角](https://cdn-ak.f.st-hatena.com/images/fotolife/a/akinami327/20240526/20240526180712.png)

[オイラー](https://d.hatena.ne.jp/keyword/%A5%AA%A5%A4%A5%E9%A1%BC) 角

## 回転の順序

[オイラー](https://d.hatena.ne.jp/keyword/%A5%AA%A5%A4%A5%E9%A1%BC) 角では、一般に

- ヨー → ピッチ → ロール

の順番で回転させることで姿勢を表現します。

  

なお、上記以外の順番で回転させることもあり、 **同じ [オイラー](https://d.hatena.ne.jp/keyword/%A5%AA%A5%A4%A5%E9%A1%BC) 角でも回転の順番によって最終的な姿勢が異なる** ことに注意してください。

![回転させる順番で最終的な姿勢が変わる例](https://cdn-ak.f.st-hatena.com/images/fotolife/a/akinami327/20240526/20240526185103.png)

回転させる順番で最終的な姿勢が変わる例

## ロボットにおけるオイラー角

ロボットにおいては、ロボットの姿勢を表現する際に [オイラー](https://d.hatena.ne.jp/keyword/%A5%AA%A5%A4%A5%E9%A1%BC) 角を使用することがあります。

![ロボットにおけるオイラー角](https://cdn-ak.f.st-hatena.com/images/fotolife/a/akinami327/20240526/20240526181000.png)

ロボットにおけるオイラー角

## オイラー角のメリット/デメリット

[オイラー](https://d.hatena.ne.jp/keyword/%A5%AA%A5%A4%A5%E9%A1%BC) 角には以下のようなメリットがあります。

## メリット

- X,Y,Z軸周りに順番に回転するため、 [オイラー](https://d.hatena.ne.jp/keyword/%A5%AA%A5%A4%A5%E9%A1%BC) 角の値をみただけで最終的な姿勢を **直感的に理解しやすい**

## デメリット

- 特定の角度において、 **[ジンバルロック](https://d.hatena.ne.jp/keyword/%A5%B8%A5%F3%A5%D0%A5%EB%A5%ED%A5%C3%A5%AF) （Gimal Lock）** と呼ばれる自由度が1つなくなる現象が起こる
	- 例えば、 **ピッチ角度が±90度の時に、ロール・ピッチのどちらの値を変更しても、Y軸周りに回転** してしまう（自由度が3→2に減少している）

  

このように、 [オイラー](https://d.hatena.ne.jp/keyword/%A5%AA%A5%A4%A5%E9%A1%BC) 角は [ジンバルロック](https://d.hatena.ne.jp/keyword/%A5%B8%A5%F3%A5%D0%A5%EB%A5%ED%A5%C3%A5%AF) という問題があるため、ROSなどでは「 [クォータニオン](https://d.hatena.ne.jp/keyword/%A5%AF%A5%A9%A1%BC%A5%BF%A5%CB%A5%AA%A5%F3) 」とよばれる [ジンバルロック](https://d.hatena.ne.jp/keyword/%A5%B8%A5%F3%A5%D0%A5%EB%A5%ED%A5%C3%A5%AF) が発生しない別の姿勢表現方法を使用することが多いです。

  

なお、 **[オイラー](https://d.hatena.ne.jp/keyword/%A5%AA%A5%A4%A5%E9%A1%BC) 角と [クォータニオン](https://d.hatena.ne.jp/keyword/%A5%AF%A5%A9%A1%BC%A5%BF%A5%CB%A5%AA%A5%F3) は相互に変換が可能** なため、

- 内部的な計算の時は [クォータニオン](https://d.hatena.ne.jp/keyword/%A5%AF%A5%A9%A1%BC%A5%BF%A5%CB%A5%AA%A5%F3) を使う
- 人間が視覚的に確認したい時に、 [オイラー](https://d.hatena.ne.jp/keyword/%A5%AA%A5%A4%A5%E9%A1%BC) 角に変換

といった使い分けがされる事も多いです。

## 参考サイトなど

- [オイラー角(wikipedia)](https://ja.wikipedia.org/wiki/%E3%82%AA%E3%82%A4%E3%83%A9%E3%83%BC%E8%A7%92)
- [Unityのジンバルロックを30秒で体感する](https://qiita.com/fullmated/items/ac62ebd1206a6487232d) ： [ジンバルロック](https://d.hatena.ne.jp/keyword/%A5%B8%A5%F3%A5%D0%A5%EB%A5%ED%A5%C3%A5%AF) について、動画を用いてわかりやすく解説されています。

[« クォータニオン(quaternion)【ロボットの…](https://akinami-robotics.hatenablog.com/entry/2024/05/27/204608) [倒立振子(inverted pendulum)【不安定な振… »](https://akinami-robotics.hatenablog.com/entry/2024/05/25/103253)