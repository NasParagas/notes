[Managing Dependencies with rosdep — ROS 2 Documentation: Humble documentation](https://docs.ros.org/en/humble/Tutorials/Intermediate/Rosdep.html)

### 何？
パッケージの依存関係の管理をするためのツール
rosdepはあくまでシステムや依存関係を確認し、どのパッケージをインストールすればいいか判断するツールで、実際のインストールはシステムのパッケージマネージャーに任せる(`apt`とか)

依存関係を見つける際に`package.xml`を使用する。ここに記載される依存関係を`rosdep keys`と呼び、手動で記載する必要がある


### よく使われるタグの説明
[ここ](https://docs.ros.org/en/humble/Tutorials/Intermediate/Rosdep.html)の機械翻訳
#### `<depend>`
- **ビルド時と実行時の両方**で必要となる依存関係を指定します。
- C++パッケージでは、迷ったときはこれを使うのが無難です。
- **Pythonパッケージでは基本的に使わず、`<exec_depend>` を使います**（ビルドフェーズがないため）。
#### `<build_depend>`
- **ビルド時のみ**に必要な依存関係を指定します。
- インストール後にその依存が不要である場合に使用します。
- ただし、ビルドに使った依存先のヘッダファイルなどを他のパッケージで利用する場合は、次の `<build_export_depend>` も必要です。
#### `<build_export_depend>`
- あなたのパッケージが提供する**ヘッダファイルや CMake 設定ファイル**が、他のパッケージからも使われる場合に必要です。
- 一般的に、エクスポートするライブラリが内部で使用する依存ライブラリは `<depend>` で指定するのが安全です（実行時にも必要だから）。
#### `<exec_depend>`
- 実行時に必要な依存関係（**共有ライブラリ、実行ファイル、Pythonモジュール、launchスクリプトなど**）を指定します。
#### `<test_depend>`
- **テスト実行時のみ**に必要な依存関係を指定します。
- `<build_depend>`, `<exec_depend>`, `<depend>` に指定した依存と重複させないようにしましょう。

### package_xmlにkeyとして何を書けばよいか
機械翻訳
#### ① ROSパッケージの場合

依存先が**ROSベースのパッケージ**で、**ROSエコシステムにリリース済み**であれば（例：`nav2_bt_navigator`）、**そのパッケージ名をそのままキーとして使用**できます。

リリース済みパッケージの一覧は、以下の GitHub リポジトリで確認できます：

📍 [https://github.com/ros/rosdistro](https://github.com/ros/rosdistro)  
例：`humble/distribution.yaml`（ROS 2 Humble の場合）
🔎 注釈：  
「ROSエコシステムにリリースされている」とは、そのパッケージが `rosdistro` データベースの `<distro>/distribution.yaml` に記載されていることを意味します（例：`humble/distribution.yaml` など）。

---

#### ② システム依存（非ROSパッケージ）の場合

依存先が ROS でない一般的なライブラリの場合（「システム依存」と呼ばれる）、適切な **rosdep キー** を探す必要があります。

以下の2つのファイルが役立ちます：

- `rosdep/base.yaml`：apt などの **システムパッケージ**の依存関係
    
- `rosdep/python.yaml`：**Pythonモジュール**の依存関係
    

---

#### キーの例：`doxygen`

たとえば、あるパッケージが `doxygen` に依存しているとしましょう（良いドキュメントは大事です！）。  
この場合、`rosdep/base.yaml` で `doxygen` を検索すると、以下のように見つかります：

```
doxygen:   
arch: [doxygen]   
debian: [doxygen]   
fedora: [doxygen]   
freebsd: [doxygen]   
gentoo: [app-doc/doxygen]   
macports: [doxygen]   
nixos: [doxygen]   
openembedded: [doxygen@meta-oe]  
opensuse: [doxygen]   
rhel: [doxygen]   
ubuntu: [doxygen]
```

この情報から、あなたの `package.xml` ファイルに記載すべき **rosdepキー** は `doxygen` であるとわかります。

このキーを使えば、各OSの適切なパッケージマネージャー用に自動的に変換され、インストールされます。


## 使い方
機械翻訳
### rosdep のインストール
`sudo apt-get install python3-rosdep`

### rosdep の初期化と更新

`sudo rosdep init`
`rosdep update`

- `rosdep init`：ローカルの初期設定ファイルを作成します
    
- `rosdep update`：ローカルに `rosdistro` のインデックスをキャッシュします
    

> 🔁 時々 `rosdep update` を実行して、インデックスを最新に保つと良いでしょう。

---

### 依存関係のインストール

依存関係のインストールは、一般的には **複数のパッケージを含むワークスペース**に対してまとめて行われます。  
ルートディレクトリにいる状態で、`src/` フォルダにソースコードがある場合、次のように実行します：

bash

コードをコピーする

`rosdep install --from-paths src -y --ignore-src`

各オプションの意味は以下の通りです：

- `--from-paths src`：`src` フォルダ内の `package.xml` を対象にする
    
- `-y`：インストールの際に確認プロンプトをすべて「はい」にする
    
- `--ignore-src`：ソースコードがすでに存在するパッケージの依存関係をインストールしない
    

---

### その他の使い方

より詳しい使い方を知りたい場合は以下を試してください：

bash

コードをコピーする

`rosdep -h`

または、公式ドキュメントを参照してください：

📖 [rosdep ドキュメント (ROS公式)](http://docs.ros.org/en/independent/api/rosdep/html/)