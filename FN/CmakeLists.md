# CMakeLists.txtの書き方

## 最小構成

```cmake
cmake_minimum_required(VERSION 3.5)
project(hello CXX)
add_executable(hello hello.cpp)
```

## ビルド&実行方法

下のような構成の場合

```txt
.
├── CMakeLists.txt
└── sampleApp.cpp
```

```bash
mkdir build
cd build
cmake ..
cmake --build .

./hello
```

## いろいろ説明

### cmake_minimum_required

- このCMakeLists.txtを動かすのに最低限必要なバージョンを指定

### project

- 任意のプロジェクト名と使用言語を指定
  - 使用言語の方はコンパイラ探すのに使う

### add_executable

- 生成したい実行ファイル名と、コンパイルしたいソースコードをいれる
- 実行ファイルを複数作りたい場合はこの項目を増やしていく
- ヘッダーは勝手に何とかしてくれる
- ソースファイルが複数ある場合は以下のように書き連ねていく

```cmake
add_executable(2_4_1_vector
    2_4_1_vector.cpp
    2_4_1_user.cpp)
```

### set

- set(CMAKE_CXX_STANDARD 23)
- set(CMAKE_CXX_STANDARD_REQUIRED True)
  - 使用するC++標準を設定し、それを必須にする
