---
title: "YOLO11でWebカメラから物体検出してみる"
source: "https://qiita.com/kawai-yuuki/items/843e186b88257bc8cc56"
author:
  - "[[kawai-yuuki]]"
published: 2024-10-11
created: 2025-08-21
description: "はじめに これまでyoloをまともに動かしたことがなかったのでやってみます。とりあえず物体検出をローカルで。 今年に入ってv9からv10、さらに11と次々にリリースされており、現在最新のyoloはYOLO11になるそうです。なぜかversionのvが抜けています。 ドキ..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## はじめに

これまでyoloをまともに動かしたことがなかったのでやってみます。とりあえず物体検出をローカルで。  
今年に入ってv9からv10、さらに11と次々にリリースされており、現在最新のyoloはYOLO11になるそうです。なぜかversionのvが抜けています。

## ドキュメントを読んでみる

yolov10までは日本語で読めるのですが、11は英語のみでgoogle翻訳も何故か反応無し。頑張って読むしかない。（結局DeepL使いました）  
詳しい解説まではしませんが、前モデルと比較して図のように速度と精度が上がっているようです。どうやらyolov8からネットワークのパラメータ数を削減したのにもかかわらず、精度が向上しているとか。

[![a311a4ed-bbf2-43b5-8012-5f183a28a845.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3470204/9bd5eb96-9c24-1cf2-7fc7-3bae678585e2.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3470204%2F9bd5eb96-9c24-1cf2-7fc7-3bae678585e2.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=3bf7adb16f380973be84fd0ab1e52747)

## 環境の詳細

使用したPC環境です。

| 項目 | バージョン |
| --- | --- |
| CPU | Ryzen5 3600 |
| GPU | RTX 3060 |
| OS | Ubuntu22.04 |
| webカメラ | オーム電機 WB-CA200N |

ライブラリなど

| 項目 | バージョン |
| --- | --- |
| Python | 3.10.12 |
| ultralytics | 8.3.9 |
| opencv-python | 4.9.0.80 |

## 動かしてみる

### UltralyticsとOpenCVをpipでインストール

```text
pip install ultralytics
```

今回はwebカメラの映像を使ってリアルタイム推論させます。よってOpenCVもインストール。

```text
pip install opencv-python
```

### Pythonのスクリプト

とりあえずこんな感じ。（ChatGPT作）  
物体検出を行うyolo11で最も小さいモデルであるyolo11n.ptを選択しています。

yolo11\_detection.py

```python
import cv2
from ultralytics import YOLO

# YOLOv11のモデルをロード
model = YOLO("yolo11n.pt") 

# Webカメラの起動
cap = cv2.VideoCapture(0)

while cap.isOpened():
    ret, frame = cap.read()
    if not ret:
        break

    # YOLOで物体検出を行う
    results = model(frame)

    # 結果をフレームに描画して表示
    annotated_frame = results[0].plot()

    # ウィンドウに描画した結果を表示
    cv2.imshow("YOLO Detection", annotated_frame)

    # 'q'を押すと終了
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()
```

### 実行

```bash
python3 yolo11_detection.py
```

机にあるものを適当に選んで検出させてみました。MacBookは閉じたままでも検出できていました。

[![YOLO Detection_screenshot_11.10.2024.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3470204/f8f768ab-e8b4-8a58-d56b-8c7b25eab203.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3470204%2Ff8f768ab-e8b4-8a58-d56b-8c7b25eab203.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=f48ef81fcb7659311e8b748b58f13cce)

## 平均のFPSを求めてみる

モデルの大きさごとに平均のFPSを求めてみました。

以下のPythonスクリプトはQキーが押されたとき、それまでの平均FPSを計算し、出力してから推論を終了するように改変したものです。（ChatGPT作）

yolo11\_detection.py

```python
import cv2
import time
from ultralytics import YOLO

# YOLOv11のモデルをロード
model = YOLO("yolo11n.pt") # ここを使用したいモデル名に変更し、実験した

# Webカメラの起動
cap = cv2.VideoCapture(0)

# FPS計算のための変数
frame_count = 0
total_time = 0

while cap.isOpened():
    start_time = time.time()  # フレーム処理開始時間を記録

    ret, frame = cap.read()
    if not ret:
        break

    # YOLOで物体検出を行う
    results = model(frame)

    # 結果をフレームに描画して表示
    annotated_frame = results[0].plot()

    # ウィンドウに描画した結果を表示
    cv2.imshow("YOLO Detection", annotated_frame)

    # フレーム処理にかかった時間を計算
    frame_time = time.time() - start_time
    total_time += frame_time
    frame_count += 1

    # 'q'を押すと終了
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

# 平均FPSを計算
if frame_count > 0:
    average_fps = frame_count / total_time
    print(f"Average FPS: {average_fps:.2f}")

cap.release()
cv2.destroyAllWindows()
```

推論を実行し、しばらくカメラを動作させました。ロードするモデルを変更し、結果を比較してみます。  
結果は以下の表のようになりました。

| モデル名 | 平均FPS |
| --- | --- |
| yolo11n.pt | 32.11 |
| yolo11s.pt | 31.99 |
| yolo11m.pt | 31.81 |
| yolo11l.pt | 31.77 |
| yolo11x.pt | 31.33 |

モデルごとにもう少し変化があると思っていましたが、結果としては微々たる誤差でした。FPSの計測は参考程度にお願いします。

ちなみに、最も大きいモデルであるyolo11x.ptでは以下の図のような推論結果でした。  
先程のyolo11n.ptと比較し、テーブル（ダイニング用ですが）が検出できていたり、スマホの識別スコアが上がっていたりしていました。精度には違いがありそうです。

そのうち他のタスクでも検証してみます。  
[![YOLO Detection_screenshot_11.10.2024_2.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3470204/0f9ce1c0-47af-4599-55d5-6e946894cbc9.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F3470204%2F0f9ce1c0-47af-4599-55d5-6e946894cbc9.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=890564784fcfa5fd9541f7948ceccf4a)

[0](https://qiita.com/kawai-yuuki/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fkawai-yuuki%2Fitems%2F843e186b88257bc8cc56&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fkawai-yuuki%2Fitems%2F843e186b88257bc8cc56&realm=qiita)

[13](https://qiita.com/kawai-yuuki/items/843e186b88257bc8cc56/likers)

いいねしたユーザー一覧へ移動

12