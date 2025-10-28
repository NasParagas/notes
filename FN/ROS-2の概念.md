センサー、モーター、アクチュエーターなど、ロボットを構成する多くのパーツを異なる周期で実時間制御するためのフレームワーク。
	nodeという
## a
ROS2のタスク実行
```
ros2 run <package_name> <executable_name>
```
turtle simなら
```
ros2 run turtlesim turtlesim_node
```

## Node

- イメージ的にはノードと言われて思い浮かぶものそのまま。それぞれに固有の役割がある。各nodeはtopic,service,actionなとを介してデータを送受信できる

## Topic
- データの送り側をpublisher、データの受け取り側をsubscriberという。任意の数のtopicにデータpublishし、任意の数のtopicへのsubscribeができる
- （当然だけど）各nodeの対応するpubとsubは、送受信するデータの構造が同じである必要がある
## service
- clientから明確にcallされたときにのみデータを送信する（送信する側はserver）
>ノードは、ROS 2でサービスを使用して通信できます。トピック（一方向通信のパターンで、ノードが情報を発行し、それを一つ以上のサブスクライバーが受け取る）の場合とは異なり、サービスはリクエスト／レスポンスのパターンであり、クライアントがサービスを提供しているノードにリクエストを送り、そのサービスがリクエストを処理してレスポンスを生成します。
一般的に、サービスは継続的な呼び出しには適していません。トピックやアクションの方がその用途に向いています。

## Parameter
## Action
## Workspace
## Package
[Creating a package — ROS 2 Documentation: Humble documentation](https://docs.ros.org/en/humble/Tutorials/Beginner-Client-Libraries/Creating-Your-First-ROS2-Package.html)

>A package is an organizational unit for your ROS 2 code. If you want to be able to install your code or share it with others, then you’ll need it organized in a package. With packages, you can release your ROS 2 work and allow others to build and use it easily.
Package creation in ROS 2 uses ament as its build system and colcon as its build tool. You can create a package using either CMake or Python, which are officially supported, though other build types do exist.

#### packageの最小構成
- `CMakeLists.txt` file that describes how to build the code within the package
- `include/<package_name>` directory containing the public headers for the package
- `package.xml` file containing meta information about the package
- `src` directory containing the source code for the package
	- C++のソースコードはここに格納する

### commands
pkgの作成例
```
ros2 pkg create --build-type ament_cmake --license Apache-2.0 <package_name>
```
pkgのbuild
```bash
#workspaceのrootで

#workspaceに含まれるすべてのpkgをbuild
colcon build

#特定のpkgのみをbuildx
colcon build --package-select <pkg_name>
```
### その他
- 一つのworkspaceには複数のpackageを含めることができる（ネストすることは出来ない）
	- best practiceはworkspace内のsrcフォルダにpackageをどんどん入れていくこと

## その他
- rosidl_default_generatorsは「ここのやつがmsgとsrvですよ」と示すために必要
- 

## 未整理


[第1回 ROSとは？基本的概念とROS2が必要になった背景\|コラム](https://www.eureka-box.com/media/column/a33)


今実行されているnode, topicの確認
```
ros2 node list
ros2 topic list
```
topicのタイプ？を一緒に表示
```
ros2 topic list -t
```
各nodeの情報取得
```
ros2 node info <node_name>
```
topicでpublishされているデータの逐次表示
```
ros2 topic echo <topic_name>
```
topicのsubとpubの数を表示
```
ros2 topic info <topic_name>
```
期待する(される)データ構造を表示
```
ros2 interface show <type_name>
```
	topicにもserviceにもつかえる
topicにmsgを飛ばす(?)
```
ros2 topic pub <topic_name> <msg_type> '<args>'
```
アクティブなserviceを表示

```
ros2 service list
```
	これまでと同様`-t`でタイプも表示
serviceのtypeを表示
```
ros2 service type <service_name>
```
serviceのtype検索
```
ros2 service find <service_type_name>
```
serviceを呼び出す
```
ros2 service call <service_name> <service_type> <arguments>
```

- nodeがどのようなprameterを持つか表示
```
ros2 param list
```
- nodeのparameterのtypeと現在の値を表示
```
ros2 param get <node_name> <parameter_name>
```
- parameterのset
```
ros2 param set <node_name> <parameter_name> <value>
```
- 現在のparameterを表示
```
ros2 param dump <node_name>
```
- 現在のparameterを保存
```
ros2 param dump <node_name> > <output_name>.yaml
```
- 現在実行中のnodeに保存済みのparameterをload
```
ros2 param load <node_name> <parameter_file>
```
	これだと読み取り専用のparameterを変更することは出来ない
- node起動時にparameterファイルをloadするように
```
ros2 run <package_name> <executable_name> --ros-args --params-file <file_name>
```
	これなら読み取り専用の値も変えることができる
	
- pubされたmsgを記録
```
ros2 bag record <topic_name>
```
- bag file info
```
ros2 bag info <bagfile_path>
```
- bag file replay
```
ros2 bag play <bagfile_name>
```
- get data pub frequency
```
ros2 topic hz <topic_name>
```


- topic
	- nodeがmessageを交換するためのパスとなる。node間でデータを送受信する方法の一つ。pub/sub通信
	- ![[_ATTACHMENT/Pasted image 20250610142855.png]]

- parameter
	- nodeの設定値。値をセットしたり、変更後の値を保存してnode起動時によみこんだりできる

- action
	- 参考：[実習ROS 2 Action通信 #ROS2 - Qiita](https://qiita.com/s-kitajima/items/6f9f544010d49fdbe1d5)
	- ![[_ATTACHMENT/Pasted image 20250610164908.png]]
	- Goal Service: 
		- Request: 「これをしたいです」
		- Responce: 「わかりました」
	- Result Service:
		- Request: 「結果お願いします」
	- Feedback **Topic**: 「今こんな感じです\*N」
		- Responce: 「結果こうなりました」
	- GoalとResultはservice(server/client)、FeedbackはTopic(pub/sub)であることに注意

- `rqt_console`
	- ログを見やすく出力してくれるやつ
```
ros2 run rqt_console rqt_console
```
	
ROS 2’s logger levels are ordered by severity:
> 1. Fatal
>     
> 2. Error
>     
> 3. Warn
>     
> 4. Info
>     
> 5. Debug
>     
>There is no exact standard for what each level indicates, but it’s safe to assume that:
- `Fatal` messages indicate the system is going to terminate to try to protect itself from detriment.
- `Error` messages indicate significant issues that won’t necessarily damage the system, but are preventing it from functioning properly.
- `Warn` messages indicate unexpected activity or non-ideal results that might represent a deeper issue, but don’t harm functionality outright.
- `Info` messages indicate event and status updates that serve as a visual verification that the system is running as expected.
- `Debug` messages detail the entire step-by-step process of the system execution.

The default level is `Info`. You will only see messages of the default severity level and more severe levels.
Normally, only `Debug` messages are hidden because they’re the only level less severe than `Info`. For example, if you set the default level to `Warn`, you would only see messages of severity `Warn`, `Error`, and `Fatal`.
#### 3.1 Set the default logger level[](https://docs.ros.org/en/humble/Tutorials/Beginner-CLI-Tools/Using-Rqt-Console/Using-Rqt-Console.html#set-the-default-logger-level "Link to this heading")

You can set the default logger level when you first run the `/turtlesim` node using remapping. Enter the following command in your terminal:
```
$ ros2 run turtlesim turtlesim_node --ros-args --log-level WARN
```


- launchファイル
	- 自分でどのnodeを(複数)起動してどんなparameterとかを持たせるか決めれるファイル
```
//例
ros2 launch turtlesim multisim.launch.py
```

- その他
	- \/_ros2cli_xxxx はターミナルからデータを取得してたりデータを送信し続けてたりしたらできるやつ？
	- 何かするたびにrqt_graphを更新して何のnodeやtopicが増えてるか確認すると理解が深まる

## ROS workspace
特定の構造を持つディレクトリ
構造
	一般的には、`src`というサブディレクトリが存在し、その中にROSパッケージのソースコードが配置されます。通常、最初はその他のディレクトリは空です。
	**colcon**はアウト・オブ・ソースビルド（ソースコードの外でビルド）を行います。  
	デフォルトでは、`src`ディレクトリと同じ階層に以下のディレクトリを作成します：
	 **build**ディレクトリ：中間ファイルが保存されます。各パッケージごとにサブフォルダが作成され、例えばCMakeがそこで呼び出されます。
	**install**ディレクトリ：各パッケージがインストールされます。デフォルトでは、各パッケージが別々のサブディレクトリにインストールされます。
	**log**ディレクトリ：colconを実行した際のログ情報が保存されます。
```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws

#例
git clone https://github.com/ros2/examples src/examples -b humble
colcon build --symlink-install #ros2_ws/　にbuild,install,logができる
colcon test #installの中に結果が出る

#デモ
source install/setup.bash
ros2 run examples_rclcpp_minimal_subscriber subscriber_member_function

source install/setup.bash
ros2 run examples_rclcpp_minimal_publisher publisher_member_function
```

>ワークスペースをビルドする前に、パッケージの依存関係を解決する必要があります。すでにすべての依存関係がそろっている場合もありますが、毎回クローンした際に依存関係を確認するのがベストプラクティスです。  
長いビルド時間を経て、依存関係の不足に気づいてビルドが失敗するようなことは避けたいですよね。
ワークスペース（`ros2_ws`）のルートディレクトリで、以下のコマンドを実行します：
```
rosdep install -i --from-path src --rosdistro humble -y
```

Underlay, Overlay
underlay上につくったworkspaceがoverlay

>オーバーレイをソースする前に、**ワークスペースをビルドしたターミナルとは別の新しいターミナルを開くことが非常に重要です**。  
同じターミナルでオーバーレイをソースした状態でビルドを行ったり、その逆を行うと、複雑な問題が発生する可能性があります。
新しいターミナルで、まずはメインのROS 2環境を**アンダーレイ**としてソースし、その上にオーバーレイを構築できるようにします：

underlayをソース
```
source /opt/ros/humble/setup.bash
```
overlayをソース
```
source install/local_setup.bash
```
-　overlayとunderlayに同じ名前の機能がある場合、overlayが優先される


## その他
- colcon_cdでパッケージ名を指定すれば、shellのカレントディレクトリをそこに変更してくれる
```
echo "source /usr/share/colcon_cd/function/colcon_cd.sh" >> ~/.bashrc
echo "export _colcon_cd_root=/opt/ros/humble/" >> ~/.bashrc

colcon_cd some_ros_package
```

## 後で読む
## [Setup `colcon` tab completion](https://docs.ros.org/en/humble/Tutorials/Beginner-Client-Libraries/Colcon-Tutorial.html#id15)[](https://docs.ros.org/en/humble/Tutorials/Beginner-Client-Libraries/Colcon-Tutorial.html#setup-colcon-tab-completion "Link to this heading")

The `colcon` command supports command completion for bash and bash-like shells. The `colcon-argcomplete` package must be installed, and [some setup may be required](https://colcon.readthedocs.io/en/released/user/installation.html#enable-completion) to make it work.

## [Tips](https://docs.ros.org/en/humble/Tutorials/Beginner-Client-Libraries/Colcon-Tutorial.html#id16)[](https://docs.ros.org/en/humble/Tutorials/Beginner-Client-Libraries/Colcon-Tutorial.html#tips "Link to this heading")

- If you do not want to build a specific package, then place an empty file named `COLCON_IGNORE` in the directory and it will not be indexed.
    
- If you want to avoid configuring and building tests in CMake packages you can pass: `--cmake-args -DBUILD_TESTING=0`.
    
- If you want to run a single particular test from a package:
    
    $ colcon test --packages-select YOUR_PKG_NAME --ctest-args -R YOUR_TEST_IN_PKG
    

## [Setup `colcon` mixins](https://docs.ros.org/en/humble/Tutorials/Beginner-Client-Libraries/Colcon-Tutorial.html#id17)[](https://docs.ros.org/en/humble/Tutorials/Beginner-Client-Libraries/Colcon-Tutorial.html#setup-colcon-mixins "Link to this heading")

Various command line options are tedious to write and/or difficult to remember.

For example, to change the CMake build type to debug, you normally use:

$ colcon build --cmake-args -DCMAKE_BUILD_TYPE=Debug

To make common command line options easier to invoke this repository makes these “shortcuts” available.

To install the default colcon mixins, run the following:

$ colcon mixin add default https://raw.githubusercontent.com/colcon/colcon-mixin-repository/master/index.yaml
$ colcon mixin update default

Then, try out using the `debug` mixin:

$ colcon build --mixin debug

For more details, see the [colcon mixin repository](https://github.com/colcon/colcon-mixin-repository).

### フォルダ構成は？
[Creating a package — ROS 2 Documentation: Humble documentation](https://docs.ros.org/en/humble/Tutorials/Beginner-Client-Libraries/Creating-Your-First-ROS2-Package.html)


### pubsubをするためのxmlとかcmakeの設定
[Writing a simple publisher and subscriber (C++) — ROS 2 Documentation: Humble documentation](https://docs.ros.org/en/humble/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Cpp-Publisher-And-Subscriber.html)

