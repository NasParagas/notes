---
title: "ROS2向けVSCodeテンプレート"
source: "https://zenn.dev/techkind/articles/2106071600_ros2_vscode_template"
author:
  - "[[Zenn]]"
published: 2021-06-07
created: 2025-08-21
description:
tags:
  - "clippings"
---
15[idea](https://zenn.dev/tech-or-idea)

## はじめに

ROS2を開発するにあたり、開発環境としてVSCodeを使用する方は多いのではないでしょうか。

豊富な拡張機能により、VSCode devcontainerを使用してDocker環境を構築するのも用意ですし、ROS2で使用されるC++やPythonといった言語周りの設定もjsonファイル一つで設定できるので、個人的にはVSCodeなしでは生きていけない体にむしろ開発されてしまいました。笑

ROS2はパッケージと呼ばれるモジュール単位でプログラムが管理され、パッケージごとにGitのリポジトリも分けていたりすると思います。

そうなると、それぞれのリポジトリごとにVSCode環境を作成したりしますが、大体同じ設定になるのに、いちいち他のパッケージからVSCodeの設定をコピーしてくるのも結構面倒だなと感じていました。

なので、共通部分はテンプレートとしてまとめて管理しておきたいなと思い、作成したので紹介します。

## 想定環境

テンプレートを使用する上での環境は以下を想定しています。

- Ubuntu 20.04
- ROS2 Foxy

## テンプレートの中身

備忘録程度のものですが、テンプレートはGitHubで管理しています。

間違っている箇所がございましたら、コメントなどで指摘ください。

VSCodeの拡張機能としては、

- Python
	- [https://marketplace.visualstudio.com/items?itemName=ms-python.python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
- C/C++
	- [https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)
- ROS
	- [https://marketplace.visualstudio.com/items?itemName=ms-iot.vscode-ros](https://marketplace.visualstudio.com/items?itemName=ms-iot.vscode-ros)

あたりは、テンプレートを使用するうえでにインストールしておくと良いと思います。

テンプレートの中身は、以下の4つのファイルです。

1. .gitignore
	- Gitでバージョン管理しないフォルダ/ファイルの設定を行う
2. .vscode/c\_cpp\_properties.json
	- C/C++周りの設定を行う
3. .vscode/settings.json
	- VSCodeの基本設定を行うファイルだが、ここではPython周りの設定を行う
4. .vscode/tasks.json
	- VSCodeでタスクを実行する際の設定を行う

それぞれの中身について紹介します。

## .gitignore

`.gitignore` の中身は以下の通りです。

```
build/*
install/*
log/*
```

パッケージをビルドすると、ビルドを実行したフォルダ直下に `build` 、 `install` 、 `log` フォルダが生成されます。  
これらはバージョン管理の必要がないので、管理対象から除外しておきます。

## c\_cpp\_properties.json

`c_cpp_properties.json` には以下を記述しています。

c\_cpp\_properties.json

```
{
    "configurations": [
        {
            "name": "Linux",
            "includePath": [
                "${workspaceFolder}/**",
                "/opt/ros/foxy/include/**",
                "/usr/include/**"
            ],
            "defines": [],
            "compilerPath": "/usr/bin/gcc",
            "cStandard": "c99",
            "cppStandard": "c++14",
            "intelliSenseMode": "gcc-x64"
        }
    ],
    "version": 4
}
```

このファイルにはC++で開発するうえでの設定を行います。  
`includePath` にはROS2の開発に必要なヘッダーファイルを読み込むためのパスを指定します。別パッケージのカスタムメッセージを使用する場合などには、そのパッケージへのパスを適宜ここに書いたりします。  
`cStandard` と `cppStandard` はコンパイラのバージョンを指定しますが、ROS2 FoxyはC++14およびC99規格に準拠したコンパイラを対象としているので [^1] 、このように記述しておきます。

`defines` の中身は空っぽですが、ビルドオプションを設定する際はここに記述します。例えば、

c\_cpp\_properties.json

```
{
    "configurations": [
        {
            // ~ (中略) ~

            "defines": [
              "_DEBUG"
            ],

            // ~ (中略) ~
        }
    ],
    "version": 4
}
```

と記載した場合、C++コード内部の `#ifdef _DEBUG` が有効になり、インテリセンスもオプションに合わせた形で補完が効くようになります。

## settings.json

`settings.json` には必要最小限の設定として以下を記述しています。

settings.json

```
{
    "editor.tabSize": 4,
    // Autocomplete from ros python packages
    "python.autoComplete.extraPaths": [
        "/opt/ros/foxy/lib/python3.8/site-packages/"
    ],
    "python.analysis.extraPaths": [
        "/opt/ros/foxy/lib/python3.8/site-packages/"
    ],
}
```

ROS2のモジュールをimportする際に、補完が効くように必要なパスを指定します。

## tasks.json

`tasks.json` にはビルドする際に必要な設定を記述しています。

tasks.json

```
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        // Build tasks
        {
            "label": "build",
            "detail": "Build workspace (default)",
            "type": "shell",
            "command": "colcon build --cmake-args -DCMAKE_BUILD_TYPE=Release --catkin-skip-building-tests --symlink-install",
            "group": {
                "kind": "build",
                "isDefault": true
            }
        },
    ]
}
```

`command` にビルドを実行する際のコマンドを記述します。ROS2は `colcon` コマンドを用いてビルドしますので `colcon build` のリリース時のコマンドを記述しています。  
コマンドの引数についてはいろいろありますので [^2] 、各自必要な引数に適宜修正していただければと思います。

## まとめ

簡単ではありますが、ROS2の開発をする際の、VSCodeのテンプレートでした。

ROS2は遊んでいて楽しいですね。myCobot [^3] とか何かロボットを動かしたくなります。

実際に何かものが動く楽しさってソフトだけでは得られない喜びがありますよね。

ROS2を使用する人の助けになる記事になっていれば幸いです。

脚注

15

15

[^1]: [https://docs.ros.org/en/foxy/Guides/Ament-CMake-Documentation.html#compiler-and-linker-options](https://docs.ros.org/en/foxy/Guides/Ament-CMake-Documentation.html#compiler-and-linker-options)

[^2]: [https://colcon.readthedocs.io/en/released/reference/verb/build.html](https://colcon.readthedocs.io/en/released/reference/verb/build.html)

[^3]: [https://www.elephantrobotics.com/en/myCobot-en/](https://www.elephantrobotics.com/en/myCobot-en/)