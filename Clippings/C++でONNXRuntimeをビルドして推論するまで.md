---
title: "C++でONNXRuntimeをビルドして推論するまで"
source: "https://qiita.com/mgmk2/items/c802598b9dbe7a06a425"
author:
  - "[[mgmk2]]"
published: 2022-01-09
created: 2025-08-26
description: "はじめに C++でDNNの推論を行う時のライブラリとして、Pythonで学習を行った時のフレームワーク(PyTorchやTensorFlow)のC++APIをそのまま使う手もありますが、それ以外にONNXRuntimeが有力な候補として上げられます。 筆者の所感ですが、P..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から3年以上が経過しています。

## はじめに

C++でDNNの推論を行う時のライブラリとして、Pythonで学習を行った時のフレームワーク([PyTorch](https://pytorch.org/) や [TensorFlow](https://www.tensorflow.org/))のC++APIをそのまま使う手もありますが、それ以外に [ONNXRuntime](https://onnxruntime.ai/) が有力な候補として上げられます。  
筆者の所感ですが、PyTorchやTensorFlowのC++APIと比べると

- バイナリサイズが小さい
- 静的ライブラリのビルド、リンクが比較的簡単
- 実行速度が同等かそれ以上

といったメリットがあります。

しかし、ビルドから実際に使用するところまでの情報が散逸しており、実際に動かしてみるまで苦労したので、この記事で一通りの流れをまとめます。

## この記事でやること

### ONNXRuntimeをソースからビルド

ONNXRuntimeのC++ライブラリをソースからビルドし、推論アプリケーションで使うために必要なライブラリ群を列挙します。  
ONNXRuntimeライブラリを静的リンクしたい場合、必要なライブラリが複数箇所に散らばっているため注意が必要です。

### ONNXフォーマットのモデルを読み込んで推論を行うC++アプリケーションの例

ONNXフォーマットのモデルの読み込みから推論までを行うコードをC++で書きます。  
今回の例では推論を行うDNNモデルとしてResNet50を使用します。  
pythonでPyTorchからONNXフォーマットに変換しますが、変換元はPyTorchに限らずTensorFlowや他のフレームワークのモデルでもOKです。

### 上記アプリケーションのビルド

ONNXRuntimeライブラリをリンクして推論を行う上記アプリケーションをビルドするため、CMakeLists.txtを書きます。

## この記事でやらないこと

### ONNXRuntimeのEP利用

ONNXRuntimeはそのままでも使用できますが、NVIDIA CUDAやIntel oneDNN等の様々な外部ライブラリを用いることでハードウェアアクセラレーション等の恩恵を受けることができます。  
このような外部ライブラリをExecution Provider(EP)と呼びます。  
EPの利用にはメリットがある反面、作業工程が増えるため、この記事では扱いません。

[Execution Providers - onnxruntime](https://onnxruntime.ai/docs/execution-providers/)  
[Build with different EPs - onnxruntime](https://onnxruntime.ai/docs/build/eps.html)

### ONNXRuntimeの高速化

上記EP利用と同様に、ONNXRuntimeを高速化するための設定について、この記事では扱いません。

[Performance - onnxruntime](https://onnxruntime.ai/docs/performance/)

### ONNXRuntimeを使ったDNNの学習

v1.8.0からONNXRuntimeを使ってDNNの学習を行うことが可能になりました。  
しかし、この記事では推論を行うための流れをまとめるため、学習機能は扱いません。

[ORT Training with PyTorch - onnxruntime](https://onnxruntime.ai/docs/get-started/training-pytorch.html)  
[Build for training - onnxruntime](https://onnxruntime.ai/docs/build/training.html)

### Linux/MacOS対応

筆者の環境がWindowsのみのため、この記事内のコードはWindowsを前提として書いています。  
実際にはCMakeを使い、OS依存の部分を可能な限り排除しているため、Linux/MacOSでも流れの確認は可能ですが、実際の動作は保証しません。  
Linux/MacOSユーザーの方は適宜改変してください。また、記事内の単語についても適宜読み替えてください。

## 環境

この記事ではWindows10とVisual Studio 2019を前提とします。

## ONNXRuntimeのビルドに必要なもの

- CMake >= 3.18
- Python >= 3.6
- Visual Studio >= 2017

## ONNXフォーマットのモデルの出力に必要なもの

- Python3
- PyTorch
- Numpy
- Pillow

## 推論アプリケーションのビルドに必要なもの

- CMake >= 3.15
- OpenCV

OpenCVは画像の読み込みとfloatへの変換に使用しています。  
同等の機能があれば他のライブラリでも大丈夫です。

## 筆者の環境

参考のため筆者の環境を以下に記します。

- Windows10 21H2

### C++

- Visual Studio 2019
- CMake 3.19.1
- OpenCV 4.5.5

### Python

- Miniconda 4.9.2
- Python 3.8.12
- PyTorch 1.10.1
- Numpy 1.20.3
- Pillow 8.4.0

## ONNXRuntimeのビルド

まずはONNXRuntimeをC++でビルドします。

[Build for inferencing - onnxruntime](https://onnxruntime.ai/docs/build/inferencing.html#cpu)

## クローン

ONNXRuntimeを [github](https://github.com/microsoft/onnxruntime) からクローンしましょう。

```shell
>git clone https://github.com/microsoft/onnxruntime.git
>cd onnxruntime
```

適当なバージョンでチェックアウトしておきます。

```shell
>git checkout refs/tags/v1.10.0
```

## ビルドの実行

ビルド用のファイルがあるので、これを実行します。  
CMakeとPythonにパスが通っていること（ `cmake`, `python` コマンドが実行可能なこと）が必要なので、注意してください。  
また、Debug版で7.5GBほど必要なのでこちらも注意です。

Linux/MacOSの場合は `build.bat` を `build.sh` で置き換えてください。

`-h` オプションでオプションの一覧を見ることができます。  
batファイル自体はpythonを実行するためのラッパーになっており、実際に実行されるのは `tools/ci_build/build.py` ([github](https://github.com/microsoft/onnxruntime/blob/v1.10.0/tools/ci_build/build.py))です。  
オプションの処理にはpythonのArgumentParserを使用しているので、上記pythonファイルからもオプションを確認できます。

オプションの数が膨大なので、よく使うものを抜粋しておきます。

| コマンド | 説明 |
| --- | --- |
| \--config CONFIG | `Debug`, `MinSizeRel`, `RelWithDebInfo`, `Release` のいずれか |
| \--build\_shared\_lib | 共有ライブラリをビルドするかどうかのフラグ。これをつけても静的ライブラリはビルドされるので、とりあえずつけておいてOK。 |
| \--skip\_tests | ビルド後のテストをスキップするかどうかのフラグ |
| \--parallel NUM\_JOBS | 並列ビルドを行う。 `NUM_JOBS` を指定しない場合はCPUコア数から自動的に決定される。 |
| \--cmake\_generator GENERATOR | CMakeで使用するGeneratorを指定する。 `Visual Studio 15 2017`, `Visual Studio 16 2019` (デフォルト), `Visual Studio 17 2022`, `Ninja` のいずれか。 Windowsのみ有効。 |
| \--enable\_lto | リンク時最適化を有効化するフラグ |
| \--enable\_msvc\_static\_runtime | MSVCランタイムライブラリを `MTd` (Debug版)もしくは `MT` (それ以外)に指定するフラグ。推論アプリケーションのビルド設定と一致させる必要がある。 |

`--parallel` オプションは基本的にジョブ数を指定せず使用して大丈夫ですが、メモリ不足によってビルドが失敗する場合、CPUコア数より少ない数で直接指定してください。

マシンスペック次第ですが、手元のRyzen5 2500UのノートPCでは、Debug版のビルドにテスト含めて30分ほどかかりました。  
コーヒーでも飲んで気長に待ちましょう。

## 生成物の確認

ビルドが終わると `build/Windows/[CONFIG]` フォルダに諸々のファイルが生成されています。（ `[CONFIG]` は `Debug` 等で置き換えてください。）  
これらのうち、アプリケーションのビルドに必要なものを列挙します。  
適当なフォルダにコピーしておくと後で楽になります。

### 共有ライブラリ

| ファイル | 場所 (`build/Windows/[CONFIG]` 以下) | デバッグ情報(`pdb` ファイル)の場所 |
| --- | --- | --- |
| onnxruntime.dll   onnxruntime.lib | `[CONFIG]` | `[CONFIG]` |

### 静的ライブラリ

依存ライブラリが各所に散らばっているので、漏れがないように注意しましょう。  
`Debug` 版、 `RelWithDebInfo` 版の場合はデバッグ情報(`pdb` ファイル)も忘れずに。

| ファイル | 場所 (`build/Windows/[CONFIG]` 以下) | デバッグ情報(`pdb` ファイル)の場所 |
| --- | --- | --- |
| onnxruntime\_session.lib   onnxruntime\_optimizer.lib   onnxruntime\_providers.lib   onnxruntime\_util.lib   onnxruntime\_framework.lib   onnxruntime\_graph.lib   onnxruntime\_mlas.lib   onnxruntime\_common.lib   onnxruntime\_flatbuffers.lib | `[CONFIG]` | `onnxruntime_*.dir/[CONFIG]` |
| onnx.lib   onnx\_proto.lib | `external/onnx/[CONFIG]` | `external/onnx/onnx.dir/[CONFIG]`   `external/onnx/onnx_proto.dir/[CONFIG]` |
| libprotobuf-lite.lib   (or libprotobuf-lited.lib) | `external/protobuf/cmake/[CONFIG]` | `external/protobuf/cmake/libprotobuf-lite.dir/[CONFIG]` |
| re2.lib | `external/re2/[CONFIG]` | `external/re2/re2.dir/[CONFIG]` |
| flatbuffers.lib | `external/flatbuffers/[CONFIG]` | `external/flatbuffers/flatbuffers.dir/[CONFIG]` |
| cpuinfo.lib | `external/pytorch_cpuinfo/[CONFIG]` | `external/pytorch_cpuinfo/cpuinfo.dir/[CONFIG]` |
| clog.lib | `external/pytorch_cpuinfo/deps/clog/[CONFIG]` | `external/pytorch_cpuinfo/deps/clog/clog.dir/[CONFIG]` |

`onnxruntime_providers.lib` と似た名前の `onnxruntime_providers_shared.lib` がありますが、こちらはEP利用のためのライブラリなので、デフォルトでは必要ありません。

## 推論アプリケーションの作成

ビルドしたONNXRuntimeライブラリ群を使って、実際にResNet50の推論を行うアプリケーションを作成してみます。

## ONNXフォーマットのResNet50モデルの生成

C++コードを書く前に、ONNXフォーマットのResNet50モデルを用意しておきましょう。

[ResNet | PyTorch](https://pytorch.org/hub/pytorch_vision_resnet/)  
[torch.onnx — PyTorch 1.10.1 documentation](https://pytorch.org/docs/stable/onnx.html)

### PyTorchモデルから変換

今回は [PyTorchのResNet50学習済みモデル](https://pytorch.org/hub/pytorch_vision_resnet/) を使用します。  
PyTorchのモデルは、画像のピクセル値を `[0, 255]` から `[0, 1]` に変換し、さらに正規化したものを入力しなければなりません。  
今回は、 `[0, 255]` から `[0, 1]` への変換はC++側で行い、正規化はモデルに含めるようにしました。  
また、出力は確率ではなく `softmax` を通す前の値なので、 `softmax` もモデルに含めるようにしました。

```py
import torch

def get_pytorch_model():
    # ResNet50の学習済みモデルをロード
    model = torch.hub.load('pytorch/vision:v0.10.0', 'resnet50', pretrained=True)

    # 正規化処理とSoftmaxをモデルに連結して新しいモデルを作成
    return torch.nn.Sequential(
        torchvision.transforms.Normalize(mean=[0.485, 0.456, 0.406], std=[0.229, 0.224, 0.225]),
        model,
        torch.nn.Softmax(-1)
    )
```

変換処理は以下のようになります。  
入力の画像サイズは `224x224` で固定としました。  
入力と出力の名前（ `input_names`, `output_names` に指定した文字列）はC++側で推論を行う際に必要なので、覚えておきましょう。

```py
import os
import torchvision

def save_model(output_dir: str):
    # 変換するモデルを取得
    model = get_pytorch_model()

    # ダミーの入力
    dummy_input = torch.randn(1, 3, 224, 224)

    # ONNXフォーマットに変換して出力
    filename = os.path.join(output_dir, 'resnet50.onnx')
    torch.onnx.export(
        model,
        dummy_input,
        filename,
        input_names=['input0'],
        output_names=['output0'],
        dynamic_axes={'input0': {0: 'batch_size'}}
    )
```

[PyTorchのResNet50学習済みモデル](https://pytorch.org/hub/pytorch_vision_resnet/) のページに従い、保存したモデルの出力ラベルも保存しておきます。

```py
def save_imagenet_label(output_dir: str):
    filename = os.path.join(output_dir, 'imagenet_classes.txt')
    torch.hub.download_url_to_file(
        'https://raw.githubusercontent.com/pytorch/hub/master/imagenet_classes.txt', filename)
```

同様に、推論アプリケーションのテストに用いる画像も保存しておきます。  
ダウンロードした画像はサイズが合わないので、 `224x224` に変換した画像もあらかじめ作成しました。

```py
from PIL import Image

def save_image_sample(output_dir: str):
    # 画像をダウンロード
    filename = os.path.join(output_dir, 'dog.jpg')
    torch.hub.download_url_to_file(
        'https://github.com/pytorch/hub/raw/master/images/dog.jpg', filename)

    # 224x224サイズに編集して別途保存
    image = Image.open(filename)
    image = torchvision.transforms.functional.resize(image, size=256)
    image = torchvision.transforms.functional.center_crop(image, output_size=224)
    image.save(os.path.join(output_dir, 'dog_input.png'))
```

全体は以下のようになります。

クリックで展開

output\_resource.py

```py
import os
import argparse

import torch
import torchvision
from PIL import Image

def save_image_sample(output_dir: str):
    # 画像をダウンロード
    filename = os.path.join(output_dir, 'dog.jpg')
    torch.hub.download_url_to_file(
        'https://github.com/pytorch/hub/raw/master/images/dog.jpg', filename)
    
    # 224x224サイズに編集して別途保存
    image = Image.open(filename)
    image = torchvision.transforms.functional.resize(image, size=256)
    image = torchvision.transforms.functional.center_crop(image, output_size=224)
    image.save(os.path.join(output_dir, 'dog_center.jpg'))

def save_imagenet_label(output_dir: str):
    filename = os.path.join(output_dir, 'imagenet_classes.txt')
    torch.hub.download_url_to_file(
        'https://raw.githubusercontent.com/pytorch/hub/master/imagenet_classes.txt', filename)

def get_pytorch_model():
    # ResNet50の学習済みモデルをロード
    model = torch.hub.load('pytorch/vision:v0.10.0', 'resnet50', pretrained=True)

    # 正規化処理とSoftmaxをモデルに連結して新しいモデルを作成
    return torch.nn.Sequential(
        torchvision.transforms.Normalize(mean=[0.485, 0.456, 0.406], std=[0.229, 0.224, 0.225]),
        model,
        torch.nn.Softmax(-1)
    )

def save_model(output_dir: str):
    # 変換するモデルを取得
    model = get_pytorch_model()

    # ダミーの入力
    dummy_input = torch.randn(1, 3, 224, 224)

    # ONNXフォーマットに変換して出力
    filename = os.path.join(output_dir, 'resnet50.onnx')
    torch.onnx.export(
        model,
        dummy_input,
        filename,
        input_names=['input0'],
        output_names=['output0'],
        dynamic_axes={'input0': {0: 'batch_size'}}
    )

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    parser.add_argument('-o', '--output-dir', type=str, required=True)
    args = parser.parse_args()

    output_dir = os.path.normpath(args.output_dir)
    if not os.path.isabs(output_dir):
        output_dir = os.path.join(os.getcwd(), output_dir)

    if not os.path.exists(output_dir):
        os.makedirs(output_dir)

    save_image_sample(output_dir)
    save_imagenet_label(output_dir)
    save_model(output_dir)
```

### PyTorchモデルの推論結果を確認

C++アプリケーションの結果と比較できるように、PyTorchモデルの推論結果を確認しておきましょう。

check\_inference\_result.py

```py
import argparse
import os
import numpy as np
from PIL import Image
import torch
import torchvision

from output_resource import get_pytorch_model

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    parser.add_argument('-i', '--input', type=str, required=True)
    parser.add_argument('-l', '--label', type=str, required=True)
    args = parser.parse_args()

    image_filename = os.path.normpath(args.input)
    label_filename = os.path.normpath(args.label)

    # 画像の読み込み
    assert os.path.exists(image_filename)
    image = Image.open(image_filename)

    assert image.height == 224 and image.width == 224

    # ラベルの読み込み
    with open(label_filename, mode='r') as f:
        labels = [s.strip() for s in f.readlines()]

    # モデルの読み込み
    model = get_pytorch_model()
    model.eval()

    # 画像の前処理
    input_tensor = torchvision.transforms.functional.to_tensor(image)  # [0,1]に変換
    input_tensor = input_tensor.unsqueeze(0)  # CHW -> BCHW

    # 推論を実行
    with torch.no_grad():
        results = model(input_tensor)
        result = results[0].numpy().copy()

    # Top5を表示
    indices = np.argsort(result)[::-1]
    for i in range(5):
        index = indices[i]
        print(f'{i + 1}: {labels[index]} {result[index]}')
```

実際に実行してみます。

```sh
>python python/check_inference_result.py -i resource/dog_input.png -l resource/imagenet_classes.txt
1: Samoyed 0.8732958436012268
2: Pomeranian 0.030270852148532867
3: white wolf 0.019671261310577393
4: keeshond 0.011073519475758076
5: Eskimo dog 0.009204263798892498
```

Samoyedが約87%となり、うまく推論できていそうです。

[サモエド - Wikipedia](https://ja.wikipedia.org/wiki/%E3%82%B5%E3%83%A2%E3%82%A8%E3%83%89)

## OpenCVの導入

今回は画像読み込みにOpenCVを使用します。導入していない場合は先に入れておきましょう。  
Windowsの場合は [公式HPのリリース](https://opencv.org/releases/) または [github](https://github.com/opencv/opencv/releases) からダウンロードして、適当な箇所で展開しておきます。  
opencv\_world\*.dllがあるフォルダにパスを通しておくと後で楽です。  
Macは `homebrew` で導入できるはず。  
Linuxはソースからビルドする感じかな？ ググれば情報見つかると思うので割愛します。

## C++で推論アプリケーションのコードを書く

必要なファイルが揃ったところで、C++で推論アプリケーションのコードを書いていきましょう。

基本的な情報は [ONNXRuntime公式のサンプルコード](https://github.com/microsoft/onnxruntime-inference-examples/blob/main/c_cxx/MNIST/MNIST.cpp) を参考にしています。  
（サンプルコードと言いつつ、WindowsOnlyなコードな上に余計な処理が多くて、もっと何とかならんの…？って思ってしまいました…）

### 画像・ラベルデータ読み込み

最初に画像とラベルデータを読み込む関数を定義しておきましょう。  
画像読み込みにOpenCVを使っていますが、他のライブラリを使っても構いません。  
本質からは外れるので折り畳んでいます。

クリックで展開

```cpp
std::vector<float> loadImage(const std::string& filename)
{
    auto image = cv::imread(filename);

    // OpenCVはBGRの順で読み込むのでRGBに変換
    cv::cvtColor(image, image, cv::COLOR_BGR2RGB);

    // 1次元に変更
    image = image.reshape(1, 1);

    // [0, 255]のuint_8から[0, 1]のfloatに変換
    std::vector<float> vec;
    image.convertTo(vec, CV_32FC1, 1. / 255);

    // HWC -> CHW
    std::vector<float> output;
    for (size_t ch = 0; ch < 3; ++ch) {
        for (size_t i = ch; i < vec.size(); i += 3) {
            output.emplace_back(vec[i]);
        }
    }
    return output;
}

std::vector<std::string> loadLabels(const std::string& filename)
{
    std::vector<std::string> output;

    std::ifstream file(filename);
    if (file) {
        std::string s;
        while (std::getline(file, s)) {
            output.emplace_back(s);
        }
        file.close();
    }

    return output;
}
```

### モデル読み込み

まずはモデル読み込み部分です。  
細かくチューニングするのでなければ簡単です。  
モデルの読み込みに失敗した場合等、 `Ort::Session` のコンストラクタが `Ort::Exception` を投げる可能性があるので、万全を期すならばエラー処理しましょう。

```cpp
const std::wstring modelFile = L"...";

Ort::Env env;
Ort::SessionOptions sessionOptions;

Ort::Session session(nullptr);
try {
    session = Ort::Session(env, modelFile.data(), sessionOptions);
}
catch(Ort::Exception& e) {
    std::cout << e.what() << std::endl;
    return 1;
}
```

### 入出力周りの準備

次に入出力用の配列を確保します。  
予め要素数が分かっているので `std::array` を使っていますが、 `std::vector` でもOKです。（配列として扱えればOK）

```cpp
constexpr int64_t numChannels = 3;
constexpr int64_t width = 224;
constexpr int64_t height = 224;
constexpr int64_t numClasses = 1000;
constexpr int64_t numInputElements = numChannels * height * width;

// 入出力のshape
const std::array<int64_t, 4> inputShape = { 1, numChannels, height, width };
const std::array<int64_t, 2> outputShape = { 1, numClasses };

// 入出力用の配列
std::array<float, numInputElements> input;
std::array<float, numClasses> results;
```

実際に推論を行うためには入出力用配列から `Tensor` を作成する必要があります。  
作成した `Tensor` は入出力用配列のポインタを保持するので、入出力用配列を削除しないように注意です。  
また、 `std::vector` を使う場合は以後メモリの再確保を行わないようにしましょう。

```cpp
// 入出力用のTensor
// 内部で配列の先頭ポインタを保持する
auto memory_info = Ort::MemoryInfo::CreateCpu(OrtDeviceAllocator, OrtMemTypeCPU);
auto inputTensor = Ort::Value::CreateTensor<float>(memory_info, input.data(), input.size(), inputShape.data(), inputShape.size());
auto outputTensor = Ort::Value::CreateTensor<float>(memory_info, results.data(), results.size(), outputShape.data(), outputShape.size());
```

予め入出力配列を用意せずに `Tensor` を作成することもできますが、 `std::array` や `std::vector` で保持した方が何かと便利です。

実際のデータの読み書きは `Tensor` ではなく、入出力配列から行うことができます。  
繰り返し推論を実行する際も、 `Tensor` を何度も作成する必要はなく、入出力配列を弄るだけでOKです。

```cpp
const std::string imageFile = "...";
const std::vector<float> imageVec = loadImage(imageFile); // CHW形式を1次元にした画像データ
assert(imageVec.size() == numInputElements);

// 画像データを入力用配列にコピー
std::copy(imageVec.begin(), imageVec.end(), input.begin());
```

最後に入出力の名前を設定します。  
ONNXフォーマットに出力した際に指定したものを同じ名前にしましょう。  
モデルデータから取得することもできますが、入出力が複数ある場合は順番に注意が必要です。

```cpp
// 入出力の名前
// ONNXモデルの保存時と同じ名前にすること
const std::array<const char*, 1> inputNames = { "input0" };
const std::array<const char*, 1> outputNames = { "output0" };

/*
// 読み込んだモデルデータから入出力名を取得することもできる
// 入出力が複数ある場合は順番に注意
std::vector<const char*> inputNames;
std::vector<const char*> outputNames;

Ort::AllocatorWithDefaultOptions alloc;
for (size_t i = 0; i < session.GetInputCount(); ++i) {
    inputNames.emplace_back(session.GetInputName(i, alloc));
}
for (size_t i = 0; i < session.GetOutputCount(); ++i) {
    outputNames.emplace_back(session.GetOutputName(i, alloc));
}
*/
```

### 推論の実行

ここまでで準備は終わりです。推論を実行しましょう。  
結果を確認する処理も合わせて書いておきます。

```cpp
// 推論を実行
Ort::RunOptions runOptions;
try {
    session.Run(runOptions, inputNames.data(), &inputTensor, 1, outputNames.data(), &outputTensor, 1);
}
catch (Ort::Exception& e) {
    std::cout << e.what() << std::endl;
    return 1;
}

// 結果から順位付け
std::vector<std::pair<size_t, float>> indexValuePairs;
for (size_t i = 0; i < results.size(); ++i) {
    indexValuePairs.emplace_back(i, results[i]);
}
std::sort(indexValuePairs.begin(), indexValuePairs.end(),  { return lhs.second > rhs.second; });

// Top5を表示
const std::string labelFile = "...";
const std::vector<std::string> labels = loadLabels(labelFile); // 出力に対応したラベル
for (size_t i = 0; i < 5; ++i) {
    const auto& [index, value] = indexValuePairs[i];
    std::cout << i + 1 << ": " << labels[index] << " " << value << std::endl;
}
```

### コード全体

以上のコードをまとめると以下のようになります。

クリックで展開

main.cpp

```cpp
# include <fstream>
# include <iostream>
# include <array>
# include <string>

# include <opencv2/core/mat.hpp>
# include <opencv2/imgcodecs.hpp>
# include <opencv2/imgproc.hpp>
# include <onnxruntime/core/session/experimental_onnxruntime_cxx_api.h>

std::vector<float> loadImage(const std::string& filename)
{
    auto image = cv::imread(filename);

    // OpenCVはBGRの順で読み込むのでRGBに変換
    cv::cvtColor(image, image, cv::COLOR_BGR2RGB);

    // 1次元に変更
    image = image.reshape(1, 1);

    // [0, 255]のuint_8から[0, 1]のfloatに変換
    std::vector<float> vec;
    image.convertTo(vec, CV_32FC1, 1. / 255);

    // HWC -> CHW
    std::vector<float> output;
    for (size_t ch = 0; ch < 3; ++ch) {
        for (size_t i = ch; i < vec.size(); i += 3) {
            output.emplace_back(vec[i]);
        }
    }
    return output;
}

std::vector<std::string> loadLabels(const std::string& filename)
{
    std::vector<std::string> output;

    std::ifstream file(filename);
    if (file) {
        std::string s;
        while (std::getline(file, s)) {
            output.emplace_back(s);
        }
        file.close();
    }

    return output;
}

int main()
{
    const std::wstring modelFile = L"...";

    Ort::Env env;
    Ort::SessionOptions sessionOptions;

    Ort::Session session(nullptr);
    try {
        session = Ort::Session(env, modelFile.data(), sessionOptions);
    }
    catch (Ort::Exception& e) {
        std::cout << e.what() << std::endl;
        return 1;
    }

    constexpr int64_t numChannels = 3;
    constexpr int64_t width = 224;
    constexpr int64_t height = 224;
    constexpr int64_t numClasses = 1000;
    constexpr int64_t numInputElements = numChannels * height * width;

    // 入出力のshape
    const std::array<int64_t, 4> inputShape = { 1, numChannels, height, width };
    const std::array<int64_t, 2> outputShape = { 1, numClasses };

    // 入出力用の配列
    std::array<float, numInputElements> input;
    std::array<float, numClasses> results;

    // 入出力用のTensor
    // 内部で配列の先頭ポインタを保持する
    auto memory_info = Ort::MemoryInfo::CreateCpu(OrtDeviceAllocator, OrtMemTypeCPU);
    auto inputTensor = Ort::Value::CreateTensor<float>(memory_info, input.data(), input.size(), inputShape.data(), inputShape.size());
    auto outputTensor = Ort::Value::CreateTensor<float>(memory_info, results.data(), results.size(), outputShape.data(), outputShape.size());

    // 画像データ読み込み
    const std::string imageFile = "...";
    const std::vector<float> imageVec = loadImage(imageFile); // CHW形式を1次元にした画像データ
    assert(imageVec.size() == numInputElements);

    // 画像データを入力用配列にコピー
    std::copy(imageVec.begin(), imageVec.end(), input.begin());

    // 入出力の名前
    // ONNXモデルの保存時と同じ名前にすること
    const std::array<const char*, 1> inputNames = { "input0" };
    const std::array<const char*, 1> outputNames = { "output0" };

    /*
    // 読み込んだモデルデータから入出力名を取得することもできる
    // 入出力が複数ある場合は順番に注意
    std::vector<const char*> inputNames;
    std::vector<const char*> outputNames;

    Ort::AllocatorWithDefaultOptions alloc;
    for (size_t i = 0; i < session.GetInputCount(); ++i) {
        inputNames.emplace_back(session.GetInputName(i, alloc));
    }
    for (size_t i = 0; i < session.GetOutputCount(); ++i) {
        outputNames.emplace_back(session.GetOutputName(i, alloc));
    }
    */

    // 推論を実行
    Ort::RunOptions runOptions;
    try {
        session.Run(runOptions, inputNames.data(), &inputTensor, 1, outputNames.data(), &outputTensor, 1);
    }
    catch (Ort::Exception& e) {
        std::cout << e.what() << std::endl;
        return 1;
    }

    // 結果から順位付け
    std::vector<std::pair<size_t, float>> indexValuePairs;
    for (size_t i = 0; i < results.size(); ++i) {
        indexValuePairs.emplace_back(i, results[i]);
    }
    std::sort(indexValuePairs.begin(), indexValuePairs.end(),  { return lhs.second > rhs.second; });

    // Top5を表示
    const std::string labelFile = "...";
    const std::vector<std::string> labels = loadLabels(labelFile); // 出力に対応したラベル
    for (size_t i = 0; i < 5; ++i) {
        const auto& [index, value] = indexValuePairs[i];
        std::cout << i + 1 << ": " << labels[index] << " " << value << std::endl;
    }
}
```

## CMakeLists.txtを書く

コードが書けたので、ビルドするための `CMakeLists.txt` を書いていきます。

ONNXRuntimeを動的リンクする場合と静的リンクする場合で異なるので、順番に見ていきましょう。

ONNXRuntimeのヘッダー群とライブラリは以下のようなツリーになっているとします。

### 共通部分

まずはおまじない。  
MSVCの場合は `CMAKE_MSVC_RUNTIME_LIBRARY` の設定をONNXRuntimeのビルドの時と合わせるようにしましょう。

CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.15)
project(ORTResnet VERSION 0.1.0)

cmake_policy(SET CMP0048 NEW)

cmake_policy(SET CMP0091 NEW)
set(CMAKE_MSVC_RUNTIME_LIBRARY MultiThreaded$<$<CONFIG:Debug>:Debug>DLL)

set(CMAKE_CXX_STANDARD 17)
```

今回は普通にコンソールアプリケーションです。  
ヘッダーまでは特に問題ありません。

CMakeLists.txt

```cmake
# define target
add_executable(ORTResnet cpp/main.cpp)

if(MSVC)
    target_compile_options(ORTResnet PRIVATE /source-charset:utf-8)
endif()

target_include_directories(ORTResnet PRIVATE ${ORT_ROOT}/include)
```

### 動的リンク

ライブラリのリンク設定を書いていきます。動的リンクの場合は難しいことはないです。

### 静的リンク

問題は静的リンクです。  
静的ライブラリは複数ある上に依存ライブラリもありますが、順番を間違えるとリンクエラーが（大量に）出てしまいます。  
[onnxruntime/cmake\_guideline.md at master · microsoft/onnxruntime](https://github.com/microsoft/onnxruntime/blob/master/docs/cmake_guideline.md#static-library-order-matters)  
[onnxruntime/onnxruntime\_dependencies.png at master · microsoft/onnxruntime](https://github.com/microsoft/onnxruntime/blob/master/docs/onnxruntime_dependencies.png)

静的ライブラリのリンク順はONNXRuntimeビルド時の `onnx_test_runner` プロジェクトを見るのが確実です。（MSVCの場合は `onnx_test_runner.vcxproj` が `build/Windows/[CONFIG]` フォルダにあるはずです。）  
[How to build and use onnxruntime static lib on windows · Issue #1472 · microsoft/onnxruntime](https://github.com/microsoft/onnxruntime/issues/1472#issuecomment-517354249)

Windowsかつ `Debug` 版の場合は、Windows組み込みの `Dbghelp.lib` が別途必要となります。  
また、 `libprotobuf-lite` のみ、 `Debug` 版で末尾に `d` がつくことに注意しましょう。

実際に書いてみると以下のようになります。

CMakeLists.txt

```cmake
target_link_directories(
    ORTResnet
PRIVATE
    ${ORT_ROOT}/lib/$<IF:$<CONFIG:Debug>,Debug,Release>/static
    ${ORT_ROOT}/lib/$<IF:$<CONFIG:Debug>,Debug,Release>/static/external
)

target_link_libraries(
    ORTResnet
PRIVATE
    onnxruntime_session
    onnxruntime_optimizer
    onnxruntime_providers
    onnxruntime_util
    onnxruntime_framework
    onnxruntime_graph
    onnxruntime_mlas
    onnxruntime_common
    onnxruntime_flatbuffers
    onnx
    onnx_proto
    libprotobuf-lite$<$<CONFIG:Debug>:d>
    re2
    flatbuffers
    cpuinfo
    clog
    
    $<$<AND:$<PLATFORM_ID:Windows>,$<CONFIG:Debug>>:Dbghelp>
)
```

この順番でなくとも動く可能性はあります。またMSVC以外では確認していないので順番が異なる可能性があります。  
リンクに失敗する場合は、ONNXRuntimeビルド時の `onnx_test_runner` プロジェクトを確認してください。

以上でONNXRuntimeに関わる部分は終わりです。

### 全体

OpenCVも含めたCMakeLists.txt全体は以下のようになります。

CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.15)
project(ORTResnet VERSION 0.1.0)

cmake_policy(SET CMP0048 NEW)

cmake_policy(SET CMP0091 NEW)
set(CMAKE_MSVC_RUNTIME_LIBRARY MultiThreaded$<$<CONFIG:Debug>:Debug>DLL)

set(CMAKE_CXX_STANDARD 17)

option(ORT_STATIC "Use ONNXRuntime static library." ON)

# check ORT_ROOT existance
if (NOT ORT_ROOT)
    message(FATAL_ERROR "ORT_ROOT must be set")
endif()

get_filename_component(ORT_ROOT_ABS ${ORT_ROOT} ABSOLUTE)
if (EXISTS ${ORT_ROOT_ABS})
else()
    message(FATAL_ERROR "ORT_ROOT does not exists: ${ORT_ROOT}")
endif()

# define target
add_executable(ORTResnet cpp/main.cpp)

if(MSVC)
    target_compile_options(ORTResnet PRIVATE /source-charset:utf-8)
endif()

# ONNXRuntime
target_include_directories(ORTResnet PRIVATE ${ORT_ROOT}/include)

if(ORT_STATIC)
    target_link_directories(
        ORTResnet
    PRIVATE
        ${ORT_ROOT}/lib/$<IF:$<CONFIG:Debug>,Debug,Release>/static
        ${ORT_ROOT}/lib/$<IF:$<CONFIG:Debug>,Debug,Release>/static/external
    )

    target_link_libraries(
        ORTResnet
    PRIVATE
        onnxruntime_session
        onnxruntime_optimizer
        onnxruntime_providers
        onnxruntime_util
        onnxruntime_framework
        onnxruntime_graph
        onnxruntime_mlas
        onnxruntime_common
        onnxruntime_flatbuffers
        onnx
        onnx_proto
        libprotobuf-lite$<$<CONFIG:Debug>:d>
        re2
        flatbuffers
        cpuinfo
        clog
        
        $<$<AND:$<PLATFORM_ID:Windows>,$<CONFIG:Debug>>:Dbghelp>
    )

else()
    target_link_directories(
        ORTResnet
    PRIVATE
        ${ORT_ROOT}/lib/$<IF:$<CONFIG:Debug>,Debug,Release>/shared
    )
    
    target_link_libraries(
        ORTResnet
    PRIVATE
        onnxruntime
    )
endif()

# OpenCV
set(OpenCV_STATIC OFF)
find_package(OpenCV REQUIRED)

target_include_directories(ORTResnet PRIVATE ${OpenCV_INCLUDE_DIRS})
target_link_libraries(ORTResnet PRIVATE ${OpenCV_LIBS})
```

## ビルドの実行

実際にビルドしてみましょう。  
今回は、 `ORT_ROOT` で前述のツリーがあるパス、 `ORT_STATIC` で静的リンクか動的リンクかを管理しています。

```sh
>cmake -B build -DORT_STATIC=ON -D ORT_ROOT=...
>cmake --build build --config Debug
```

ビルドに成功すれば `build/Debug` フォルダに `ORTResnet.exe` という名前の実行ファイルができているはずです。

### 推論アプリケーションの結果を確認

動的リンクの場合は、 `onnxruntime.dll` があるフォルダにパスを通すか、実行ファイルと同じフォルダに `onnxruntime.dll` をコピーして、実行時にロードできるようにしておきましょう。  
また、 `opencv_world*.dll` も同様です。

実際に実行してみます。

```sh
>ORTResnet.exe
1: Samoyed 0.873296
2: Pomeranian 0.0302708
3: white wolf 0.0196712
4: keeshond 0.0110735
5: Eskimo dog 0.00920427
```

`python` で確認した時と同じ結果が得られました。

## おわりに

ONNXRuntimeをビルドしてから推論アプリケーションを作成するまでの流れを一通り追っていきました。  
Execution Providersが絡むともう少し面倒なのですが、モチベがあれば後日別記事にしたいと思います。

この記事で紹介したコード等は以下のリポジトリから確認できます。（一部改変有）  
\[mgmk2/onnxruntime-cpp-example\]\[projgithub\]  
\[projgithub\]: [https://github.com/mgmk2/onnxruntime-cpp-example](https://github.com/mgmk2/onnxruntime-cpp-example)

[0](https://qiita.com/mgmk2/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fmgmk2%2Fitems%2Fc802598b9dbe7a06a425&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fmgmk2%2Fitems%2Fc802598b9dbe7a06a425&realm=qiita)

[19](https://qiita.com/mgmk2/items/c802598b9dbe7a06a425/likers)

いいねしたユーザー一覧へ移動

23