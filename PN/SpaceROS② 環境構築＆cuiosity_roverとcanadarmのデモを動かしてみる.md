※画像を見やすいように更新
2025年7月10日時点の情報です
まだSpaceROS自体が開発中ということもあって、一年前の記事だったとしても手順通りに進まないという状況です。この記事の情報もできるだけ最新を保つようにします

前提
-- 
- Ubuntu 24.04 LTS(CPU)
	- 多分GPUはdocker上でのGUI表示がちょっとめんどい

SpaceROSのデモ自体はDocker上で動かすことになるので、仮想環境上のubuntuでもそこまでやることは変わらないと思っています


環境構築手順
--
### 最終構築環境
- ROS2 jazzy
- Gazebo Hermonic
- Docker  28.3.1, build 38b7060
- Earthly
- SpaceROS 2025-04

### ROS2 jazzyのインストール
SpaceROSのdockerコンテナにROS2もインストールされるので必須ではないが、`rocker`のインストールの際に必要だそうなので一応(どうせそのうち使うし)
インストール~動作確認は以下のリンク先の手順に従ってください
https://docs.ros.org/en/jazzy/Installation/Ubuntu-Install-Debs.html

### Gazeboのインストール
参考: https://gazebosim.org/docs/latest/getstarted/
```bash
sudo apt-get install ros-jazzy-ros-gz
```
動作確認
```bash
gz sim shapes.sdf
```

### docker engineのインストール
参考: https://www.kagoya.jp/howto/cloud/container/dockerubuntu/

```bash
sudo apt update
```
必要なパッケージのインストール
```bash
sudo apt install -y ca-certificates curl gnupg
```

公式のGPGキーを保存するディレクトリを作成
```bash
sudo install -m 0755 -d /etc/apt/keyrings
```
※ちなみにこれは`/etc/apt/keyrings`というディレクトリを、所有者は読み/書き/実行(4+2+1=7), グループとその他のユーザーには読み/実行(4+0+1=5)を許可する権限設定をして作成するというコマンド
参考: https://qiita.com/yummy888/items/510f82976780151855f8
※GPGはよくわかってない。正規のインストール先かを判断するための公開鍵暗号方式をつかった認証方法(ないし認証するための鍵)みたいな感じ？

GPGキーのダウンロードと保存、権限設定
```bash
curl -f -L https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
sudo apt update
```
※
`-f  = --fail`
HTTPエラー（404など）が発生した場合に、何も出力せずに即座に終了する。エラーページの内容をパイプで次のコマンドに渡してしまうのを防ぐ。
`-L = --location`
サーバーがリダイレクト（別のURLへの転送）を指示してきた場合に、その新しいURLに追従してダウンロードを続行する
`|` : 前のコマンドの出力を次のコマンドに渡すパイプ
`--dearmor`: バイナリ変換
`-o`: 出力先の設定
`chmod a+r`: すべてのユーザーに読み取りを許可

dockerインストール
```bash
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

docker動作確認
```bash
docker -v
docker run hello-world
```


### Earthlyのインストール
参考: https://earthly.dev/get-earthly

Earthly自体の説明は以下リンクなどを参照。
https://zenn.dev/kesin11/articles/7f4eed7cabf38d

今回はdocker imageの作成につかってる(はず)
以下を実行
```bash
sudo /bin/sh -c 'wget https://github.com/earthly/earthly/releases/latest/download/earthly-linux-amd64 -O /usr/local/bin/earthly && chmod +x /usr/local/bin/earthly && /usr/local/bin/earthly bootstrap --with-autocomplete'
```

動作確認
```bash
earthly github.com/earthly/hello-world+hello
```
地球みたいなのが生成されればok

### SpaceROSのDockerコンテナ環境の構築
dockerパッケージ取得
```bash
git clone https://github.com/space-ros/docker.git
```

moveit2とspace_robotsのdocker image作成
`docker/moveit2/build.sh`の56行目の`sudo apt update`を以下のように書き換える
```bash
bash -c "\
curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key \
| gpg --dearmor \
| sudo tee /usr/share/keyrings/ros-archive-keyring.gpg > /dev/null && \
echo 'deb [signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu noble main' \
| sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null && \
sudo apt-get update"
```
やってることはopen_roboticsの期限切れgpgキーの更新のはず

```bash
cd ./docker/moveit2
./build.sh
```
※CPUが強いPCを使うとメモリを馬ほど食うので注意
社用PCで30GB近く食います
Dockerfile内の`colcon build`の設定でcpuの仕様個数を変えれるらしいので、ダメそうなときは試してみてください

```bash
cd ../space_robots 
./build.sh
```

二つ合わせて1.5時間ぐらいかかります

`rocker`のインストール
```bash
sudo apt-get install python3-rocker
```


**コンテナ起動前にやること**

`space_robots/run.sh`内`#start the container`以降の記述を以下のように変更
```sh
docker run -it \  
--name $CONTAINER_NAME \  
--network host \  
-e DISPLAY \  
-e TERM \  
-e QT_X11_NO_MITSHM=1 \  
-e XDG_RUNTIME_DIR \  
--device /dev/dri \  
$IMG_NAME
```

ターミナルで以下を実行
```bash
xhost +local:root
```

コンテナ起動
```bash
cd docker/space_robots
./run.sh
```

コンテナ内でのgazebo動作確認
コンテナ内のターミナル①で以下を実行(CPU想定)
```bash
sudo apt-get update  
sudo apt-get install -y mesa-utils  
glxinfo | grep "OpenGL renderer"
# OpenGL renderer string: Mesa Intel(R) HD Graphics 530 (SKL GT2)
```
これっぽい表示が出ればok

※not faund的な表示が出たとき
一旦下の項目のデモを試してみてください。
何も出なかったら(gazeboが表示されなかったら)、`run.sh`の中身や`- xhost +local:root`、OpenGLあたりがこのdocker上でGUIが表示されない問題のキーワードだと思ってます。
`rviz2`を起動しようとしたら出てくるエラーメッセージも参考になると思います

デモ動かすぞ！
--
`/home/spaceros-user/demos_ws`がデモの入っているワークスペース（多分`spaceros-user`直下に自分でいろいろ作るときに使うwsを作る）
docker上のファイルをVSCodeで見てみると、`/home/spaceros-user/demos_ws/src/demos`の中身はおおよそ以下のようなディレクトリ構成になっている
```
.  
├── canadarm2  
├── curiosity_rover  
├── LICENSE  
├── README.md 
├── ros_trick  
└── space_ros_memory_allocation_demo
```
ここの`canadarm2`と`curiosity_rover`がデモらしいデモっぽい（allocatorのも気になる）

### curiosity_roverのデモを動かしてみよう
curiosity_roverフォルダの構成を見てみるとこんな感じで三つのパッケージがある
```
.  
├── curiosity_description  
├── curiosity_gazebo  
├── curiosity_rover_demo  
├── README.md
├── ...
```
descriptionはデモ内で使うモデルなどを定義してるっぽい。gazeboの方がワールドとかモデルの構築をしてるっぽくて、最後のがrover自体の各パーツのnodeの記述がある感じ
下二つのパッケージにはlaunchファイルもあって、以降はそこにあるlaunchファイルを使っています

コンテナ内のターミナル①で以下を実行
```bash
source /home/spaceros-user/demos_ws/install/setup.bash  
ros2 launch curiosity_gazebo curiosity_gazebo.launch.py
```
gazebo上でcuriosity_roverがゆっくり落ちてくるはずです
左上の更新ボタンを押すとroverについてるカメラからの映像を表示してくれます
![[_ATTACHMENT/Screenshot from 2025-07-14 19-05-51.png]]

ホスト側のターミナル二つそれぞれで起動済みのコンテナに入る(②、③とします)
```bash
docker exec -it openrobotics_space_robots_demo bash  
```

②で以下を実行
```bash
source /home/spaceros-user/demos_ws/install/setup.bash 
ros2 launch curiosity_rover_demo mars_rover.launch.py
```
さっき落ちてきたrover自体のnodeを起動してます
  
③で以下を実行
```bash
docker exec -it openrobotics_space_robots_demo bash
source /home/spaceros-user/demos_ws/install/setup.bash
ros2 service call /move_forward std_srvs/srv/Empty
```
ゆ～っくり前に進んでいきます。ここで送れるserviceは`curiosity_rover`内のREADMEにいくつか例があります（左右に曲がったりアームを動かしたり...）
本物のcuriosityの撮った写真は下のリンクから見れます↓
https://mars.nasa.gov/msl/multimedia/raw-images/

### canadarmのデモを動かしてみよう
同様の手順で動かせます
動かすとこんな感じ↓
![[_ATTACHMENT/result.gif]]