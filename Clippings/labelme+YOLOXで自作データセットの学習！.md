---
title: "labelme+YOLOXで自作データセットの学習！"
source: "https://zenn.dev/opamp/articles/d3878b189ea256"
author:
  - "[[Zenn]]"
published: 2022-01-10
created: 2025-08-26
description:
tags:
  - "clippings"
---
26[tech](https://zenn.dev/tech-or-idea)

前回記事の終わりで「YOLOXの記事書きたいなぁ・・・」と言っていましたが、今回はそのYOLOXを実際に動かしてみたいと思います！

ただ、「COCOデータセットを使ってYOLOXを学習」だと ~~みんなやってる~~ 味気ないので、自作データセットを用いてYOLOXの学習を行ってみたいと思います！

## 環境

前回の記事で作成したDocker環境内で学習していきたいと思います！

## 画像データにラベル付けを行う

YOLOXを学習させるためにはデータセットが必要となります。今回はデータセットを自前で作成するところから始めていきます。  
以降の作業は、画像データがすでに存在する（すでに撮影済み）であると仮定して進めていきます。

今回はアノテーションによく利用される **labelme** というツールを用いてアノテーションします。

## labelmeのインストール

labelmeはローカルの環境にインストールします。

```bash
pip install labelme
```

`Successfully installed` と表示されればインストールは完了です！

## labelmeの起動

labelmeを利用する際は、ターミナルにて

```bash
labelme
```

と入力することで、GUIが立ち上がります。こんな感じですね。  
![](https://storage.googleapis.com/zenn-user-upload/108b67640f39-20220104.png)

アノテーションを開始するには、左のメニューバーから `OpenDir` を選択し、画像が保存されているフォルダを選択します。  
フォルダを選択すると、保存されている画像が表示されると思います。  
筆者環境では、仕事猫の検出を行ってみたいと思います。  
![](https://storage.googleapis.com/zenn-user-upload/6b6a7d529f39-20220104.png)

## アノテーション

今回は物体検出なので長方形のアノテーションになります。  
画像上で右クリックするとメニューが表示されますが、その中から `Create Rectangle` を選択します。  
カーソルが十字に変化したら、検出したい物体の左上をクリックします。すると長方形が描画されると思うので、検出したい物体をすべて囲めるようにマウスを動かし、もう一度クリックします。  
![](https://storage.googleapis.com/zenn-user-upload/f9600f8f5fa4-20220105.png)

すると新たなウィンドウが表示されます。このウィンドウは今囲った対象のラベルを選択する画面です。ラベルをまだ登録していない場合は、 `Enter object label` に対象のラベル名を入力、すでに存在する場合は下から選択し `ok` をクリックします。

これでアノテーションは完了です！ほかの画像に対しても同様に行いましょう！（遠い目）

アノテーションが終われば、学習に利用するデータと検証に利用するデータを別々のフォルダに格納して保存しておきます。  
![](https://storage.googleapis.com/zenn-user-upload/ea0b58f5edf8-20220107.png)  
また、利用したクラスを記載したtextデータを作成しておきましょう。

classes.txt

```bash
__ignore__
class1
class2
class3
```

## アノテーションデータの変換

続いてデータセットの変換を行います。まずは変換の際に必要となる `pycocotools` のインストールから始めましょう。  
こちらはwindowsにインストールします。

```bash
pip install pycocotools
```

もしインストールの際に以下のようなエラーが発生した場合は、

```bash
error: Microsoft Visual C++ 14.0 or greater is required. Get it with "Microsoft C++ Build Tools": https://visualstudio.microsoft.com/visual-cpp-build-tools/
```

にアクセスし、インストールラーをダウンロードします。

続いてに従いながら準備を進めてください！再起動してくださいと出ると思うので、必ず再起動をして下さい。（セットアップが終わりません・・・）

インストールが終われば、続いてをクローンします。

```bash
git clone https://github.com/wkentaro/labelme
```

クローンが終わると、いよいよ変換に移ります。  
変換には `labelme2coco.py` というものを使います。 `labelme2coco.py` は `/labelme/examples/instance_segmentation` に入っていますので、そこまで移動しましょう！

移動先に作成したデータ類を移動させます。こんな感じですね。  
![](https://storage.googleapis.com/zenn-user-upload/5422bd44e963-20220109.png)

では変換していきましょう！変換は

```bash
python labelme2coco.py [データセットのフォルダ] [保存名] --labels classes.txt
```

のように書くことができます。  
筆者の環境ですと、

```bash
python labelme2coco.py 01.train train_dataset --labels classes.txt
python labelme2coco.py 02.val val_dataset --labels classes.txt
```

のようになります。

次に生成されたデータセットをYOLOX用に変更します。  
まずはじめにフォルダを作成します。

```bash
mkdir -p dataset/train2017/
mkdir -p dataset/val2017/
mkdir -p dataset/annotations
```

続いて生成された学習データセットの中にある画像(`JPEGImages` の中の画像)を `dataset/train2017/` に移動します。  
同様に検証データセットの中にある画像を `dataset/val2017/` へ移動します。  
最後に各データセットフォルダの中にある `annotations.json` を `train_annotations.json` 、 `val_annotations.json` に変更し、 `dataset/annotations` に格納します。  
ここまででフォルダ構成は次のようになっています。

```bash
dataset
├── train2017
│   └── 画像.jpg ... (学習用画像群)
└── val2017
│   └── 画像.jpg ... (評価用画像軍)
└── annotations
　　├── train_annotations.json
　　└── val_annotations.json
```

最後にアノテーションデータの修正を行います。  
`dataset` フォルダ内に `fix_annot.py` ファイルを作成し、以下を記述します。

fix\_annot.py

```python
import os 
import glob

annot_list = glob.glob(os.path.join('./annotations/','*.json'))

for path in annot_list:
    with open(path , 'r' ,encoding = 'utf-8') as f:
        jf = json.load(f)
    for data in jf['images']:
        data['file_name'] = data['file_name'].split('\\')[-1]
    with open(path , 'w' ,encoding = 'utf-8') as f:
        json.dump(jf , f)
```

これでデータセットは完成です！

## YOLOXのインストール

続いてYOLOXのインストールを行います。ここからはDockerで作業していきます。  
YOLOXのGithubサイトからインストール方法を確認。ちょっと変更・・・。

```bash
git clone https://github.com/Megvii-BaseDetection/YOLOX
cd YOLOX
pip3 install -U pip && pip3 install -r requirements.txt
pip3 install -v -e .  # or  python3 setup.py develop
pip3 install cython; pip3 install 'git+https://github.com/cocodataset/cocoapi.git#subdirectory=PythonAPI'
```

このコマンドで一通り入ります！

## 学習定義ファイルの作成

今回は小さなモデル、YOLOX-Sモデルで実装していきます！  
YOLOXは学習の際に **学習内容を定義したファイル** を必要とします。  
YOLOXの学習定義ファイルは `YOLOX/yolox/exp/yolox_base.py` を親クラスとした構築されます。  
まずは `yolox_base.py` を確認してみましょう。

yolox\_base.py

```python
self.num_classes = 80
        self.depth = 1.00
        self.width = 1.00
        self.act = 'silu'

        # ---------------- dataloader config ---------------- #
        # set worker to 4 for shorter dataloader init time
        self.data_num_workers = 4
        self.input_size = (640, 640)  # (height, width)
        # Actual multiscale ranges: [640-5*32, 640+5*32].
        # To disable multiscale training, set the
        # self.multiscale_range to 0.
        self.multiscale_range = 5
        # You can uncomment this line to specify a multiscale range
        # self.random_size = (14, 26)
        self.data_dir = None
        self.train_ann = "instances_train2017.json"
        self.val_ann = "instances_val2017.json"

        # --------------- transform config ----------------- #
        self.mosaic_prob = 1.0
        self.mixup_prob = 1.0
        self.hsv_prob = 1.0
        self.flip_prob = 0.5
        self.degrees = 10.0
        self.translate = 0.1
        self.mosaic_scale = (0.1, 2)
        self.mixup_scale = (0.5, 1.5)
        self.shear = 2.0
        self.enable_mixup = True

        # --------------  training config --------------------- #
        self.warmup_epochs = 5
        self.max_epoch = 300
        self.warmup_lr = 0
        self.basic_lr_per_img = 0.01 / 64.0
        self.scheduler = "yoloxwarmcos"
        self.no_aug_epochs = 15
        self.min_lr_ratio = 0.05
        self.ema = True

        self.weight_decay = 5e-4
        self.momentum = 0.9
        self.print_interval = 10
        self.eval_interval = 10
        self.exp_name = os.path.split(os.path.realpath(__file__))[1].split(".")[0]

        # -----------------  testing config ------------------ #
        self.test_size = (640, 640)
        self.test_conf = 0.01
        self.nmsthre = 0.65
```

`dataloader config` には学習データの配置場所、使用するCPU数や画像サイズなどが定義されています。  
`transform config` には学習時のデータ拡張の方法が定義されています。  
`training config` には学習時の戦略が書かれています。（最大Epoch数や学習率など）  
`testing config` にはテスト時の設定が記載されています。

今回は `dataloader config` のみ変更していきます。

独自のモデル設定を作る前に、ひな形の設定をコピーしましょう。  
ひな形の設定は `YOLOX/exp/default` に保存されています。

![](https://storage.googleapis.com/zenn-user-upload/935dfb12c579-20220108.png)

この中から今回は `yolox_s.py` を `YOLOX` 直下にコピーします。  
ではコピーしたファイルを編集していきます。  
以下のコード内のコメントを参考にしながら設定を変更してください！

これが完成すれば、いよいよ学習です！

## YOLOXの学習

では学習していきましょう！  
学習には `YOLOX/tools/train.py` が必要なので、 `YOLOX` の直下にコピーしておきましょう。

```bash
cp ./tools/train.py ./
```

また学習済みモデルを利用する際は、先にダウンロードしておきます。  
GithubのBenchmarkのところからダウンロードできます。  
YOLOX-sを使う場合は以下のコマンドでも大丈夫です！

```bash
wget https://github.com/Megvii-BaseDetection/YOLOX/releases/download/0.1.1rc0/yolox_s.pth
```

学習は以下のコマンドで行います！

```bash
python train.py -f yolox_s.py -d 1 -b 16 --fp16 -o -c ./yolox_s.pth --cache
```

引数の説明は

- `-f` ：設定ファイルのパス
- `-d` ：利用するGPUの数
- `-b` ：バッチサイズ
- `-o` ：学習前に利用するGPUメモリを予約する
- `-c` ：学習済みモデルのパス
- `--fp16` ：畳み込み層などの計算をfloat16で行う。精度は若干落ちるが、学習が高速になる
- `--cache` ：学習済みデータをYOLOXが高速に読み出せる形に変形する。学習データを変更する際はデータセットフォルダ内に生成されたキャッシュデータを削除しないと、データが変更されないので注意が必要。

もしOpenCVのエラーが出力された場合は

```bash
apt-get install -y libgl1-mesa-dev
apt-get install -y libglib2.0-0
```

を実行してみてください。

無事学習が開始され

```bash
2022-01-09 14:46:09 | INFO     | yolox.core.trainer:126 - args: Namespace(batch_size=16, cache=True, ckpt='./yolox_s.pth', devices=1, dist_backend='nccl', dist_url=None, exp_file='yolox_s.py', experiment_name='yolox_s', fp16=True, machine_rank=0, name=None, num_machines=1, occupy=True, opts=[], resume=False, start_epoch=None)
2022-01-09 14:46:09 | INFO     | yolox.core.trainer:127 - exp value:
╒══════════════════╤═════════════════════════════════════════════════════════╕
│ keys             │ values                                                  │
╞══════════════════╪═════════════════════════════════════════════════════════╡
│ seed             │ None                                                    │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ output_dir       │ './YOLOX_outputs'                                       │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ print_interval   │ 10                                                      │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ eval_interval    │ 1                                                       │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ num_classes      │ 3                                                       │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ depth            │ 0.33                                                    │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ width            │ 0.5                                                     │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ act              │ 'silu'                                                  │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ data_num_workers │ 4                                                       │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ input_size       │ (640, 640)                                              │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ multiscale_range │ 5                                                       │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ data_dir         │ './datasets/dataset/images'                             │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ train_ann        │ './datasets/dataset/annotations/train_annotations.json' │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ val_ann          │ './dataset/dataset/annotations/val_annotations.json'    │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ mosaic_prob      │ 1.0                                                     │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ mixup_prob       │ 1.0                                                     │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ hsv_prob         │ 1.0                                                     │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ flip_prob        │ 0.5                                                     │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ degrees          │ 10.0                                                    │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ translate        │ 0.1                                                     │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ mosaic_scale     │ (0.1, 2)                                                │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ mixup_scale      │ (0.5, 1.5)                                              │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ shear            │ 2.0                                                     │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ enable_mixup     │ True                                                    │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ warmup_epochs    │ 5                                                       │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ max_epoch        │ 300                                                     │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ warmup_lr        │ 0                                                       │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ basic_lr_per_img │ 0.00015625                                              │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ scheduler        │ 'yoloxwarmcos'                                          │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ no_aug_epochs    │ 15                                                      │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ min_lr_ratio     │ 0.05                                                    │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ ema              │ True                                                    │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ weight_decay     │ 0.0005                                                  │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ momentum         │ 0.9                                                     │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ exp_name         │ 'yolox_s'                                               │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ test_size        │ (640, 640)                                              │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ test_conf        │ 0.01                                                    │
├──────────────────┼─────────────────────────────────────────────────────────┤
│ nmsthre          │ 0.65                                                    │
╘══════════════════╧═════════════════════════════════════════════════════════╛
```

のような画面が出れば成功です！あとは気長に待ちましょう・・・。

## 推論する！

ではお待ちかねの推論タイムです！  
`YOLOX` フォルダ内に `YOLOX_outputs/yolox_s` が作成されていると思います。（ `yolox_s` 部分は設定ファイルの名前によって変化します。例：設定ファイルをylx\_s.pyにしていると、 `YOLOX_outputs/ylx_s` になります。）

その中から `best_ckpt.pth` を `YOLOX` 直下にコピーします。

```bash
cp ./YOLOX_outputs/yolox_s/best_ckpt.pth/ ./
```

では推論してみましょう！

```bash
cp tools/demo.py ./
python demo.py image -f [設定ファイル].py --device gpu --fp16 --path [検査画像までのパス] -c ./best_ckpt.pth --save_result
```

![](https://storage.googleapis.com/zenn-user-upload/bd64964d1098-20220110.jpg)

・・・。精度はよくないですね・・・。学習の感じを見ていると早々に過学習している傾向にあったので、22枚の画像ではだめでした・・・。  
多分YOLOX-Sはこのデータセットに対して大きすぎるモデルだったのでしょう。Nanoとか使えばよかったのかな？

あと、この画像を見てもらうとわかる通り、表示されるラベルがおかしいことになってます。（ `car` って出てますね）

これはCOCOデータセットのラベルが利用されていることが原因です。  
では最後にこれを修正しましょう。

![](https://storage.googleapis.com/zenn-user-upload/90bcdce6bfb0-20220110.jpg)

変化しましたね！（見切れててすみません・・・。お手元の画像で確認してもらえればと思います！）

## まとめ

今回はlabelmeとYOLOXで自作データセットの学習にチャレンジしました！  
YOLOv5は使いやすいためいろいろな人が利用していますが、YOLOXはYOLOv5と比べると使いにくいのでまだまだ日本語記事が少ないイメージです。

この記事が皆様の開発の一助になれば幸いです！

ではまた！次はCenterNetの実装記事を書きたいな・・・

26

26