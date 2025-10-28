---
title: "ROSにおけるパッケージ管理ツールであるvcstoolとrosdepの紹介とその使い方"
source: "https://memoteki.net/archives/3296"
author:
  - "[[Tiryoh]]"
published: 2023-12-05
created: 2025-08-26
description: "ROSのチュートリアルを進めていると、vcsやrosdepコマンドを使うことがあると思います。今回はこれらのパッケージ管理ツールとその使い方について紹介します。"
tags:
  - "clippings"
---
ROSのチュートリアルを進めていると、 `vcs` や `rosdep` コマンドを使うことがあると思います。今回はこれらのパッケージ管理ツールとその使い方について紹介します。

この記事は [ROS 2 Advent Calendar 2023](https://qiita.com/advent-calendar/2023/ros2) の12/5の記事として投稿しています。

すごくざっくり書くと、 `vcs` コマンドを提供するvcstoolはソースからビルドするパッケージを管理するツール、 `rosdep` コマンドを提供するrosdepはバイナリインストールするパッケージを管理するツールです。両方使えるととても便利です。

目次
1. [vcstool](https://memoteki.net/archives/#index_id0)
	1. [vcstoolを使うために必要なこと](https://memoteki.net/archives/#index_id1)
	2. [vcstoolの使い方](https://memoteki.net/archives/#index_id2)
2. [rosdep](https://memoteki.net/archives/#index_id3)
	1. [rosdepを使うために必要なこと](https://memoteki.net/archives/#index_id4)
	2. [rosdepの使い方](https://memoteki.net/archives/#index_id5)
3. [vcstoolとrosdepをあわせた使い方](https://memoteki.net/archives/#index_id6)
4. [まとめ](https://memoteki.net/archives/#index_id7)
5. [参考・関連リンク](https://memoteki.net/archives/#index_id8)

## vcstool

vcstoolは元Open Roboticsの [@drik-thomas](https://github.com/dirk-thomas) さんが開発したパッケージ管理ツールです。GitやMercurialなどでバージョン管理をしている複数のリポジトリを便利に管理するために開発されたようです。

![](https://opengraph.githubassets.com/62016396f5b0ece44144bd1b5bce14a91fc8a75ff962d3dedfb21c39de64e583/dirk-thomas/vcstool)

vcstoolはROS 2から標準のパッケージ管理ツールになりました。ROS 1ではwstoolという別のツールが標準で使われていましたが、今はもう非推奨になっています。vcstoolはこのwstool用のファイルである`.rosinstall` 形式（ [REP-126](https://www.ros.org/reps/rep-0126.html) にて定義されています）にも対応しています。コマンドの読み替えが必要にはなりますが、vcstoolはwstoolの代わりになると言えそうです。

今回はwstoolとvcstoolの違いについては省略しますが、過去の記事で少し触れているので、興味のある方はぜひご確認ください。

![](https://memoteki.net/wp-content/uploads/2019/12/524dc69c36b9705a8b79dfc0e4c77314-300x151.png)

### vcstoolを使うために必要なこと

vcstoolを使うためには、

1. vcstool自体のインストール
2. vcstoolに読み込ませるためのパッケージリスト

が必要です。

vcstoolはROS 2がインストールされている環境であれば、 `sudo apt install python3-vcstool` でインストールすることができます。その他のインストール方法は公式ページを参照してください。

![](https://opengraph.githubassets.com/62016396f5b0ece44144bd1b5bce14a91fc8a75ff962d3dedfb21c39de64e583/dirk-thomas/vcstool)

vcstoolでパッケージをまとめてダウンロードできるようにするためには、下記のようにvcstoolに読み込ませるためのパッケージリストを用意する必要があります。下記のようにROSパッケージ内で一緒に管理するケースがよく見る例です。

[https://github.com/rt-net/raspimouse\_slam\_navigation\_ros2/blob/main/.ci.rosinstall](https://github.com/rt-net/raspimouse_slam_navigation_ros2/blob/main/.ci.rosinstall)

[https://github.com/ROBOTIS-GIT/turtlebot3/blob/ros2/turtlebot3.repos](https://github.com/ROBOTIS-GIT/turtlebot3/blob/ros2/turtlebot3.repos)

[https://github.com/OUXT-Polaris/dynamixel\_hardware\_interface/blob/master/dependency.repos](https://github.com/OUXT-Polaris/dynamixel_hardware_interface/blob/master/dependency.repos)

### vcstoolの使い方

私がよく使っているvcstoolのコマンドをいくつか紹介します。vcstoolとrosdepをあわせて便利に使う方法は最後に紹介します。

#### パッケージをまとめてダウンロードしてくるとき

`mypackage.repos` ファイルに各種パッケージが記載されている場合に、 `src` ディレクトリにダウンロードしてくるとき

```
vcs import src < mypackage.repos
```

https://example.com/mypackage.repos に公開されているファイルに各種パッケージが記載されている場合に、 `src` ディレクトリにダウンロードしてくるとき

```
vcs import src --input https://example.com/mypackage.repos
```

#### パッケージをリスト化するとき

`src` ディレクトリに各種パッケージがある場合に、 `mypackage.repos` ファイルに書き出すとき

```
vcs export src > mypackage.repos
```

#### パッケージをまとめて更新するとき

`src` ディレクトリに各種パッケージがある場合に、まとめて更新（git pull）するとき

```
vcs pull src
```

## rosdep

rosdepはOpen Roboticsが中心となってメンテナンスをしている、依存パッケージをまとめてインストールするためのパッケージ管理ツールです。

![](https://opengraph.githubassets.com/c721ee5cb7dce4cc629fc7cfa320e945937483183b3ac884c429550fb99012d8/ros-infrastructure/rosdep)

下記の”rosdistro”という名前のリポジトリに数多くのソフトウェア・ライブラリが登録されており、rosdepコマンドを実行するとこのリストを参照してシステムにマッチした依存パッケージをダウンロード・インストールします。例えばですが、Pythonの数値計算ライブラリとして有名なnumpyも、rosdepを使ってインストールすることができます。

![](https://opengraph.githubassets.com/4ce7fcf4b7f4d3d23253d6b7a32586dba6425ae4a1d7090198c6dab6f52a50e5/ros/rosdistro)

### rosdepを使うために必要なこと

rosdepを使うためには、

1. rosdep自体のインストール
2. rosdepが参照する”rosdistro”リポジトリに目的とするソフトウェア・ライブラリがリストされていること
3. ROSパッケージの `package.xml` に依存パッケージが定義されていること

が必要です。

rosdepはROS 2がインストールされている環境であれば、 `sudo apt install python3-rosdep` でインストールすることができます。

“rosdistro”リポジトリへの追加方法と `package.xml` の記載方法は今回は割愛します。

“rosdistro”リポジトリについては、CONTRIBUTING.mdに記載があります。

[https://github.com/ros/rosdistro/blob/master/CONTRIBUTING.md](https://github.com/ros/rosdistro/blob/master/CONTRIBUTING.md)

`package.xml` の記載については、 [@srs](https://qiita.com/srs) さんのQiitaの記事が詳しいです。

![](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-user-contents.imgix.net%2Fhttps%253A%252F%252Fcdn.qiita.com%252Fassets%252Fpublic%252Farticle-ogp-background-afbab5eb44e0b055cce1258705637a91.png%3Fixlib%3Drb-4.0.0%26w%3D1200%26blend64%3DaHR0cHM6Ly9xaWl0YS11c2VyLXByb2ZpbGUtaW1hZ2VzLmltZ2l4Lm5ldC9odHRwcyUzQSUyRiUyRnFpaXRhLWltYWdlLXN0b3JlLnMzLmFtYXpvbmF3cy5jb20lMkYwJTJGMjU0NDQyJTJGcHJvZmlsZS1pbWFnZXMlMkYxNTM5NDg3MDE5P2l4bGliPXJiLTQuMC4wJmFyPTElM0ExJmZpdD1jcm9wJm1hc2s9ZWxsaXBzZSZiZz1GRkZGRkYmZm09cG5nMzImcz03YzgyMDNhNWE5NGQ2Y2RmMWVjMDg2YThiNjk3ODI5Ng%26blend-x%3D120%26blend-y%3D467%26blend-w%3D82%26blend-h%3D82%26blend-mode%3Dnormal%26s%3D8164212ebbb45249b3b4e233e24af312?ixlib=rb-4.0.0&w=1200&fm=jpg&mark64=aHR0cHM6Ly9xaWl0YS11c2VyLWNvbnRlbnRzLmltZ2l4Lm5ldC9-dGV4dD9peGxpYj1yYi00LjAuMCZ3PTk2MCZoPTMyNCZ0eHQ9Uk9TJUU4JUFDJTlCJUU1JUJBJUE3OTclMjBDTWFrZUxpc3QudHh0JUUzJTgxJUE4cGFja2FnZS54bWwlRTMlODElQUUlRTYlOUIlQjglRTMlODElOEQlRTYlOTYlQjkmdHh0LWFsaWduPWxlZnQlMkN0b3AmdHh0LWNvbG9yPSUyMzFFMjEyMSZ0eHQtZm9udD1IaXJhZ2lubyUyMFNhbnMlMjBXNiZ0eHQtc2l6ZT01NiZ0eHQtcGFkPTAmcz1lMjJmMDhkMzEyNjdjZGQyNTRmYjdlZWU0ZWQyNzg0Nw&mark-x=120&mark-y=112&blend64=aHR0cHM6Ly9xaWl0YS11c2VyLWNvbnRlbnRzLmltZ2l4Lm5ldC9-dGV4dD9peGxpYj1yYi00LjAuMCZ3PTgzOCZoPTU4JnR4dD0lNDBzcnMmdHh0LWNvbG9yPSUyMzFFMjEyMSZ0eHQtZm9udD1IaXJhZ2lubyUyMFNhbnMlMjBXNiZ0eHQtc2l6ZT0zNiZ0eHQtcGFkPTAmcz01M2QxOGUzMzZlNDMxMzI5ZjlkYTgxOTk1MmFjOTRlMQ&blend-x=242&blend-y=480&blend-w=838&blend-h=46&blend-fit=crop&blend-crop=left%2Cbottom&blend-mode=normal&s=b97e3076b48287a9880465acec06a076)

### rosdepの使い方

私がよく使っているrosdepのコマンドを紹介します。vcstoolとrosdepをあわせて便利に使う方法は最後に紹介します。

`src` ディレクトリに各種パッケージがある場合に、それらの依存パッケージをまとめてインストールするとき

```
rosdep install -riy --from-paths src
```

`-r` はエラーが出ても無視するオプション、 `-i` はすでにソースコードがダウンロードしてあるパッケージはスキップするオプション、 `-y` は全部に対してyesとするオプションです。

`-y` についてだけ補足します。今回紹介するか少し迷ったのですが、実際には使っている人も多いだろうということで紹介します。Ubuntuで実行する場合、 `rosdep install （中略）` と実行すると、裏で `sudo apt-get install （中略）` が実行されます。これに対してすべてyesと回答する設定が `-y` オプションです。 `package.xml` の中身をよくチェックしてないまま実行すると、最悪の場合は意図してないパッケージがインストールされて **環境が壊れたりすることも想定されます** ので、ご注意ください。 `-y` のオプションを指定しない場合は、 `sudo apt-get install` で「インストールしますか？はい・いいえ」のプロンプトで止まりますので、都度回答する必要があります。

その他のオプションはあまり使っていませんが、 `rosdep install --help` と実行すると多数出てきます。他にも「このオプション便利だよ！」などありましたらSNSやコメント等で教えていただけると嬉しいです！

rosdep install –help
```
$ rosdep install --help
Usage: rosdep [options]  

Commands:

rosdep check ...
  check if the dependencies of package(s) have been met.

rosdep install ...
  download and install the dependencies of a given package or packages.

rosdep db
  generate the dependency database and print it to the console.

rosdep init
  initialize rosdep sources in /etc/ros/rosdep.  May require sudo.

rosdep keys ...
  list the rosdep keys that the packages depend on.

rosdep resolve 
  resolve  to system dependencies

rosdep update
  update the local rosdep database based on the rosdep sources.

rosdep what-needs ...
  print a list of packages that declare a rosdep on (at least
  one of) 

rosdep where-defined ...
  print a list of yaml files that declare a rosdep on (at least
  one of) 

rosdep fix-permissions
  Recursively change the permissions of the user's ros home directory.
  May require sudo.  Can be useful to fix permissions after calling
  "rosdep update" with sudo accidentally.

Options:
  -h, --help            show this help message and exit
  --os=OS_NAME:OS_VERSION
                        Override OS name and version (colon-separated), e.g.
                        ubuntu:lucid
  -c SOURCES_CACHE_DIR, --sources-cache-dir=SOURCES_CACHE_DIR
                        Override /home/ubuntu/.ros/rosdep/sources.cache
  -v, --verbose         verbose display
  --version             print just the rosdep version, then exit
  --all-versions        print rosdep version and version of installers, then
                        exit
  --reinstall           (re)install all dependencies, even if already
                        installed
  -y, --default-yes     Tell the package manager to default to y or fail when
                        installing
  -s, --simulate        Simulate install
  -r                    Continue installing despite errors.
  -q                    Quiet. Suppress output except for errors.
  -a, --all             select all packages
  -n                    Do not consider implicit/recursive dependencies.  Only
                        valid with 'keys', 'check', and 'install' commands.
  -i, --ignore-packages-from-source, --ignore-src
                        Affects the 'check', 'install', and 'keys' verbs. If
                        specified then rosdep will ignore keys that are found
                        to be catkin or ament packages anywhere in the
                        ROS_PACKAGE_PATH, AMENT_PREFIX_PATH or in any of the
                        directories given by the --from-paths option.
  --skip-keys=SKIP_KEYS
                        Affects the 'check' and 'install' verbs. The specified
                        rosdep keys will be ignored, i.e. not resolved and not
                        installed. The option can be supplied multiple times.
                        A space separated list of rosdep keys can also be
                        passed as a string. A more permanent solution to
                        locally ignore a rosdep key is creating a local rosdep
                        rule with an empty list of packages (include it in
                        /etc/ros/rosdep/sources.list.d/ before the defaults).
  --filter-for-installers=FILTER_FOR_INSTALLERS
                        Affects the 'db' verb. If supplied, the output of the
                        'db' command is filtered to only list packages whose
                        installer is in the provided list. The option can be
                        supplied multiple times. A space separated list of
                        installers can also be passed as a string. Example:
                        \`--filter-for-installers "apt pip"\`
  --from-paths          Affects the 'check', 'keys', and 'install' verbs. If
                        specified the arguments to those verbs will be
                        considered paths to be searched, acting on all catkin
                        packages found there in.
  --rosdistro=ROS_DISTRO
                        Explicitly sets the ROS distro to use, overriding the
                        normal method of detecting the ROS distro using the
                        ROS_DISTRO environment variable. When used with the
                        'update' verb, only the specified distro will be
                        updated.
  --as-root=INSTALLER_KEY:
                        Override whether sudo is used for a specific
                        installer, e.g. '--as-root pip:false' or '--as-root
                        "pip:no homebrew:yes"'. Can be specified multiple
                        times.
  --include-eol-distros
                        Affects the 'update' verb. If specified end-of-life
                        distros are being fetched too.
  -t DEPENDENCY_TYPES, --dependency-types=DEPENDENCY_TYPES
                        Dependency types to install, can be given multiple
                        times. Choose from {'buildtool', 'doc', 'build',
                        'exec', 'build_export', 'buildtool_export', 'test'}.
                        Default: all except doc.
```

## vcstoolとrosdepをあわせた使い方

vcstoolとrosdepをあわせた使い方として、ここでは具体的にTurtlebot3のシミュレーション用パッケージとその依存パッケージをまとめてインストールする場合の手順を紹介します。ROS 2 Humbleを想定します。

まず、ROSのワークスペースに移動して、vcsコマンドでTurtlebot3の関連パッケージをまとめてダウンロードしてきます。

```
cd ~/ros2_ws
vcs import src --input https://raw.githubusercontent.com/ROBOTIS-GIT/turtlebot3/ros2/turtlebot3.repos
```

次にrosdepコマンドで依存パッケージをまとめてダウンロードしてきます。

```
rosdep install -riy --from-paths src
```

最後にビルドが通れば完了です。

```
colcon build --symlink-install
```

メンテナンスが途中だったり、依存パッケージの更新があったりするとビルドが通らなかったりすることもありますが、基本的にはこのやり方でソースコードをダウンロードし、その依存パッケージをまとめてインストールすることができます。

CIの設定やDockerfileでこれらを活用すれば、「それぞれのツールが参照する先のリストや設定ファイルさえ整えておけばよい」という状態でメンテナンスすることができ、とても便利です。  
（そうじゃないと、パッケージ更新のたびにインストールスクリプトを更新するというような事態になりかねません……そうなったらメンテナンス大変です……）

## まとめ

ROSにおけるパッケージ管理ツールであるvcstoolとrosdepの紹介とその使い方を、よく使っているコマンドと一緒に紹介しました。それぞれのツールが参照する先のリストや設定ファイルが整っていれば、1つずつインストールする場合と比較して少ないコマンドで必要なパッケージをまとめてインストールすることができます。

vcstoolとrosdepを使いやすくするためにも、ROSパッケージには`.repos` ファイルを置き、 `package.xml` とあわせて定期的にメンテナンスしていきましょう！この記事がvcstoolとrosdepを活用していくきっかけになれば幸いです。

## 参考・関連リンク

![](https://memoteki.net/wp-content/uploads/2019/12/524dc69c36b9705a8b79dfc0e4c77314-300x151.png)

![](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-user-contents.imgix.net%2Fhttps%253A%252F%252Fcdn.qiita.com%252Fassets%252Fpublic%252Fadvent-calendar-ogp-background-7940cd1c8db80a7ec40711d90f43539e.jpg%3Fixlib%3Drb-4.0.0%26w%3D1200%26blend64%3DaHR0cHM6Ly9xaWl0YS11c2VyLXByb2ZpbGUtaW1hZ2VzLmltZ2l4Lm5ldC9odHRwcyUzQSUyRiUyRnBicy50d2ltZy5jb20lMkZwcm9maWxlX2ltYWdlcyUyRjI0MzY4MDU0NTMlMkYydndpd2tlaTBjMWNlZ2NsbGQ0Nl9ub3JtYWwuanBlZz9peGxpYj1yYi00LjAuMCZhcj0xJTNBMSZmaXQ9Y3JvcCZtYXNrPWVsbGlwc2UmYmc9RkZGRkZGJmZtPXBuZzMyJnM9M2YwZjgxZmNjN2UzZDgyYmUzMzQ3YmYzODY0ZDk1NDE%26blend-x%3D120%26blend-y%3D467%26blend-w%3D82%26blend-h%3D82%26blend-mode%3Dnormal%26s%3D746578cb2a137614ed589888ce1742d4?ixlib=rb-4.0.0&w=1200&fm=jpg&mark64=aHR0cHM6Ly9xaWl0YS11c2VyLWNvbnRlbnRzLmltZ2l4Lm5ldC9-dGV4dD9peGxpYj1yYi00LjAuMCZ3PTk2MCZoPTMyNCZ0eHQ9cm9zZGVwJUU0JUJEJUJGJUUzJTgxJThBJUUzJTgxJTg2JUUzJTgyJTg4JnR4dC1hbGlnbj1sZWZ0JTJDdG9wJnR4dC1jb2xvcj0lMjMzQTNDM0MmdHh0LWZvbnQ9SGlyYWdpbm8lMjBTYW5zJTIwVzYmdHh0LXNpemU9NTYmdHh0LXBhZD0wJnM9ODY4MWQxZmI1ZDJkNGI2NmM0YTliNTZkMjlkZTkyNjI&mark-x=120&mark-y=112&blend64=aHR0cHM6Ly9xaWl0YS11c2VyLWNvbnRlbnRzLmltZ2l4Lm5ldC9-dGV4dD9peGxpYj1yYi00LjAuMCZ3PTgzOCZoPTU4JnR4dD0lNDBzdHJ2JnR4dC1jb2xvcj0lMjMzQTNDM0MmdHh0LWZvbnQ9SGlyYWdpbm8lMjBTYW5zJTIwVzYmdHh0LXNpemU9MzYmdHh0LXBhZD0wJnM9ZDRmYWY2YmFhODkzOGNjNjAzNzcxYzIyODVhNzZjOTY&blend-x=242&blend-y=480&blend-w=838&blend-h=46&blend-fit=crop&blend-crop=left%2Cbottom&blend-mode=normal&s=a3082fb1bf7f7b4608ea9456c7594ee8)

![](https://opengraph.githubassets.com/f91811c399704a25d981a958e62734aaea80e67c3a888d43ec86f2367eca7c76/ros/rosdistro/issues/25432)

| ↑ 1 | `wstool update` や `wstool merge` コマンドなどで馴染みがあるかもしれません。 |
| --- | --- |
| ↑ 2 | [recommend / use vcstool instead of wstool · Issue #25432 · ros/rosdistro · GitHub](https://github.com/ros/rosdistro/issues/25432)   [https://github.com/ros/rosdistro/issues/25432](https://github.com/ros/rosdistro/issues/25432) |

- [Git for Windowsのお勧めインストール手順](https://memoteki.net/archives/5586)
- [Ubuntu 20.04と最新Intel CPUでRVizを動かすDocker設定](https://memoteki.net/archives/9255)