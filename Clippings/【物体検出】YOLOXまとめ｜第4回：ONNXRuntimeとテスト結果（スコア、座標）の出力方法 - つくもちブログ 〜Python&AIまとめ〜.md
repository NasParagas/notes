---
title: "【物体検出】YOLOXまとめ｜第4回：ONNXRuntimeとテスト結果（スコア、座標）の出力方法 - つくもちブログ 〜Python&AIまとめ〜"
source: "https://tt-tsukumochi.com/archives/1228"
author:
  - "[[tt-tsukumochi]]"
published: 2022-03-12
created: 2025-08-26
description: "このシリーズでは物体検出でお馴染みのYOLOシリーズの最新版「YOLOX」について、環境構築から学習の方法まで"
tags:
  - "clippings"
---
このシリーズでは物体検出でお馴染みのYOLOシリーズの最新版「YOLOX」について、環境構築から学習の方法までまとめます。

YOLOXは2021年8月に公開された最新バージョンであり、速度と精度の面で限界を押し広げています。

第4回目である今回は、YOLOXでpytorchモデルをONNX変換する方法と、ONNXRuntimeを実行する方法を紹介します。

Google colabを使用して簡単に物体検出のモデルを実装することができますので、ぜひ最後までご覧ください。

## ONNXとは

ONNX（Open Neural Network Exchange）とは、Microsoftがオープンソースで公開している学習モデルを表現するフォーマット形式のことです。

PyTorch、Tensorflow、 Scikit-learnなどの各種フレームワークで学習したモデルを、別のフレームワークで読み込めるようになります。

CPUやCUDA、TensorRTなど様々なデバイスに対応しており、学習済みモデルの管理や運用が容易になります。

ONNX Runtime はサーバーサイドの推論を高速化するだけでなく、ONNX Runtime for Mobile も提供されています。

さらに2021年9月にはORT Web が新た供されたことで、ブラウザー内での推論が可能になりました。

## YOLOXpytorchモデルをONNXに変換

早速、YOLOXのPytorchモデルをONNXに変換する方法を紹介します。

ここからはGoogle colabを使用して実装していきます。

まずはGPUを使用できるように設定をします。

今回紹介するコードは以下のボタンからコピーして使用していただくことも可能です。

### YOLOX の導入

```python
from google.colab import drive
drive.mount('/content/drive')
%cd ./drive/MyDrive
```

すでにYOLO Xを使用している方はクローンは不要です。

```python
# !git clone https://github.com/Megvii-BaseDetection/YOLOX
%cd YOLOX
```

公式よりcloneしてきます。

```python
!pip install -U pip && pip install -r requirements.txt
!pip install -v -e . 
!pip install cython
!pip install 'git+https://github.com/cocodataset/cocoapi.git#subdirectory=PythonAPI'
```

今回変換する学習済モデルをダウンロードします。

```python
!wget https://github.com/Megvii-BaseDetection/YOLOX/releases/download/0.1.1rc0/yolox_x.pth
```

今回初めてYOLOXを使用する方は、環境構築確認のためテストしてみましょう。

```python
!python tools/demo.py image -n yolox-x -c yolox_x.pth --path assets/dog.jpg --conf 0.25 --nms 0.45 --tsize 640 --save_result --device gpu
```

### 標準のYOLOXモデルをONNXに変換

ここからはYOLOXのモデルをONNXに変換してみましょう。

まずは標準のYOLOXモデルを変換します。

以下の例では先ほどダウンロードした「yolox\_x.pth」を「yolox\_x.onnx」に変換します。

変換する際はサイズを統一する必要がありますので-n をyolox-xとします。

```python
!python tools/export_onnx.py --output-name yolox_x.onnx -n yolox-x -c yolox_x.pth

# -n：ベースとなるモデル名。[yolox-s、m、l、xおよびyolox-nane、yolox-tiny、yolov3]から選択。
# -c：変換前のモデル名
# --output-name：変換後のモデル名
```

変換が終了しました。

## YOLOXでONNXRuntime

先ほど変換したモデルを使ってテストをしてみましょう。

まずは必要なライブラリをインポートします。

```python
import argparse
import os
import cv2
import numpy as np
import pandas as pd
import onnxruntime
from google.colab.patches import cv2_imshow
from yolox.data.data_augment import preproc as preprocess
from yolox.data.datasets import COCO_CLASSES
from yolox.utils import mkdir, multiclass_nms, demo_postprocess, vis
```

今回使用する画像とモデルを指定します。

「image\_path 」にはテストする画像、「model」には先ほど変換したモデル名を入力します。

```python
output_dir ='onnx_out'
image_path = 'yolox_test1.jpeg'
model = 'yolox_x.onnx'
```

テストを実行します。

```python
input_shape = (640,640)
origin_img = cv2.imread(image_path)
img, ratio = preprocess(origin_img, input_shape)
session = onnxruntime.InferenceSession(model)
ort_inputs = {session.get_inputs()[0].name: img[None, :, :, :]}
output = session.run(None, ort_inputs)
predictions = demo_postprocess(output[0], input_shape)[0]
boxes = predictions[:, :4]
scores = predictions[:, 4:5] * predictions[:, 5:]
boxes_xyxy = np.ones_like(boxes)
boxes_xyxy[:, 0] = boxes[:, 0] - boxes[:, 2]/2.
boxes_xyxy[:, 1] = boxes[:, 1] - boxes[:, 3]/2.
boxes_xyxy[:, 2] = boxes[:, 0] + boxes[:, 2]/2.
boxes_xyxy[:, 3] = boxes[:, 1] + boxes[:, 3]/2.
boxes_xyxy /= ratio
dets = multiclass_nms(boxes_xyxy, scores, nms_thr=0.45, score_thr=0.5)
if dets is not None:
    final_boxes, final_scores, final_cls_inds = dets[:, :4], dets[:, 4], dets[:, 5]
    origin_img = vis(origin_img, final_boxes, final_scores, final_cls_inds,
                      0.3, class_names=COCO_CLASSES)

mkdir(output_dir)
output_path = os.path.join(output_dir, os.path.basename(image_path))

cv2.imwrite(output_path, origin_img)
cv2_imshow(origin_img)
```

![](https://tt-tsukumochi.com/wp-content/uploads/2022/03/yolox_test1.jpeg)

ONNXでテストすることができました。

### 物体検出結果（スコアや座標など）を表形式で出力する

先ほどの結果を表形式で表示してましょう。

```python
result = []
[result.extend((final_cls_inds[x],COCO_CLASSES[int(final_cls_inds[x])],final_scores[x],final_boxes[x][0],final_boxes[x][1],final_boxes[x][2],final_boxes[x][3]) for x in range(len(final_scores)))]
df = pd.DataFrame(result, columns = ['class-id','class','score','x-min','y-min','x-max','y-max'])
df
```

以下の表を得ることができました。

![](https://tt-tsukumochi.com/wp-content/uploads/2022/03/fig2-1024x261.png)

## オリジナルモデルでONNXRuntime

次に自分で作成したモデルのONNX変換とテストを試してみましょう。

今回は、以前作成したマスク着用の判定モデルを使用します。

詳細は以下の記事よりご確認ください。

![](https://tt-tsukumochi.com/wp-content/uploads/2022/01/1579924271_jpg.rf_.be5b27c2b2801bccc191e6dbd9bfccca.jpg)

つくもちブログ　〜Python&AIまとめ〜

### 作成したYOLOXモデルをONNXに変換

先ほどと同様に変換をしていきましょう。

変換前のモデルには作成したマスク着用判定モデル、「-f」には学習時に設定したexpsファイルを指定します。

```python
!python tools/export_onnx.py --output-name face_mask.onnx -f exps/example/custom/face_mask.py -c YOLOX_outputs/face_mask/last_epoch_ckpt.pth

# -n：ベースとなるモデル名。モデル名は、[yolox-s、m、l、xおよびyolox-nane、yolox-tiny、yolov3]から選択。
# -c：変換前のモデル名
# --output-name：変換後のモデル名
# -f：学習時に使用したexpsファイル
```

### 作成したYOLOXモデルでテスト

変換したモデルで、テストをしてみましょう。

```python
output_dir ='onnx_out'
image_path = 'OK.jpg'
model = 'face_mask.onnx'
    
input_shape = (640,640)
origin_img = cv2.imread(image_path)
img, ratio = preprocess(origin_img, input_shape)
session = onnxruntime.InferenceSession(model)
ort_inputs = {session.get_inputs()[0].name: img[None, :, :, :]}
output = session.run(None, ort_inputs)
predictions = demo_postprocess(output[0], input_shape)[0]
boxes = predictions[:, :4]
scores = predictions[:, 4:5] * predictions[:, 5:]
boxes_xyxy = np.ones_like(boxes)
boxes_xyxy[:, 0] = boxes[:, 0] - boxes[:, 2]/2.
boxes_xyxy[:, 1] = boxes[:, 1] - boxes[:, 3]/2.
boxes_xyxy[:, 2] = boxes[:, 0] + boxes[:, 2]/2.
boxes_xyxy[:, 3] = boxes[:, 1] + boxes[:, 3]/2.
boxes_xyxy /= ratio
dets = multiclass_nms(boxes_xyxy, scores, nms_thr=0.45, score_thr=0.5)
if dets is not None:
    final_boxes, final_scores, final_cls_inds = dets[:, :4], dets[:, 4], dets[:, 5]
    origin_img = vis(origin_img, final_boxes, final_scores, final_cls_inds,
                      0.3, class_names=COCO_CLASSES)
mkdir(output_dir)
output_path = os.path.join(output_dir, os.path.basename(image_path))
cv2.imwrite(output_path, origin_img)
```

![](https://tt-tsukumochi.com/wp-content/uploads/2022/03/OK-1.jpg)

変換したモデルでマスク着用判定をすることができました。

※画像は [こちら](https://www.pakutaso.com/20210433113post-34497.html) から拝借しています。

## まとめ

最後までご覧いただきありがとうございました。

YOLOXでpytorchモデルをONNX変換する方法とONNXRuntimeを実行する方法を紹介しました。

ONNXのによりYOLOXの活用の幅が広がりそうですね。