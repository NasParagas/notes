とりあえずざっくりとした内容だけです。
今のところリンク集みたいになっちゃってますが、個々の内容については後々追記か別記事にしたいです
次の記事では環境構築手順の説明をしてデモも動かします


#### 注意点

**公式ドキュメントなどが未整備であったり、古い情報が結構残ってます**
例：
-  「For standard ROS 2 topics, one can refer to the ROS 2 Humble documentation.」と記載があるが、SpaceROSの最新対応はROS2 jazzy
- デモの実行方法が公式dockerコンテナ内のREADMEと異なる
- [公式ドキュメント](https://space-ros.github.io/docs/rolling/index.html)の大半がTODO状態
など

この記事はそこらへんに気を付けつつ、公式ドキュメントとネットに転がっている記事を参考に作成していますが、すでに古い情報があるかもしれません。都度追記なり修正なりします




SpaceROSって何？
--
https://space-ros.github.io/docs/rolling/Introduction/About-Space-ROS.html
- 一言でいえば「宇宙(で使える)品質が担保されているROS2」
- 具体的な品質の基準↓
 >It will begin as a subset of ROS 2 and will be aligned with aerospace and NASA standards, such as [DO-178C](https://en.wikipedia.org/wiki/DO-178C) and [NPR7150.2](https://nodis3.gsfc.nasa.gov/displayDir.cfm?t=NPR&c=7150&s=2), so that it can be adopted for high assurance missions
- Open Robotics，NASA，PickNik社が開発を主導
- ROS2の一部の機能を改良したり、品質の担保に必要なツール(静的解析ツールや要件管理ツール)を統合することで、最終的な成果物が宇宙向けロボットに適用可能なものであるように(勝手に？)なってくれることが最終目標だと思う


SpaceROSの目的
--
https://space.ros.org/
https://space-ros.github.io/docs/rolling/Introduction/About-Space-ROS.html

- ROS(2)の宇宙向けロボットへの導入
 - ソフトウェアの再利用性が問題になっていた
 - コミュニティが活発

- ちなみにROS自体にはISS船内で使用するロボットに対しての使用実績がある(https://roscon.jp/2024/presentations/05.pdf)
 >ROS のメリットを宇宙開発に活かすことで，
開発期間やコストの大幅な削減に成功している
・しかし，宇宙ロボットでのROSの採用例は，
宇宙飛行士が居住・サポートできるISS 船内のみ
・ISS 船外の過酷な宇宙環境でROS を用いるには，
高い信頼性と安全性が求められるが，地上のROSは
これらの考慮が不足しており，直接の適用は困難
・ROSは，ロボットの制御，マニピュレーション，
自律移動等に特化したアプリケーションが多く，
信頼性と安全性に関するロボットの健康状態(通信や
電力等)を維持・管理する機能は別途開発が必要

- SpaceROSではISSのような人が制御できる場所ではなく、例えばスペースデブリを除去するミッションのような過酷なミッションをする宇宙機に適用できるようなものを目指す


ROS2との相違点
--
https://space-ros.github.io/docs/rolling/Introduction/How-Space-ROS-Differs.html
（冒頭に「To date, 」と書かれているので、これからもっと機能追加していく可能性もあるかも）

ひとまず用語だけ拾っています。細かい内容も追々記載したいですが、とりあえずは下のリンクが参考になると思います
https://qiita.com/YukiIkeda_robot/items/227380feca2e8c7e886d

### 同じところ
#### NodeやTopicなどのROS2の基本的構造の部分
- むしろそれを宇宙向けロボットにも使えるようにすることで参入障壁を下げたりするのが目的のような気がする

### 異なるところ
#### 静的解析ツールの統合(or統合予定)
https://space-ros.github.io/docs/rolling/Introduction/How-Space-ROS-Differs.html#code-analysis-tools

- どれが統合済みでどれが未統合かはまだ未確認
	- ただ、以前SpaceROSのコミュニティで「公式の手順を使ってIKOSで解析しようとするとエラーが出る。公式もそれを把握している」といった内容の話をしていた気がするので、完璧に統合されているわけではなさそう
- 個々のツールの詳細についてはまだ未調査かつ題目とは内容がずれそうかつ多分一つ一つ記事にできそうなくらいだと思うので省略。上がっている名前と参考になりそうなドキュメントのリンク↓
- Cobra static analyzer
	- [Cobra — Space ROS Documentation: Rolling documentation](https://space-ros.github.io/docs/rolling/Related-Projects/Cobra.html)
	- [Cobra Static Code Analyzer](http://codescrub.com/cobra/index.html)
- IKOS static analyzer
	- [IKOS — Space ROS Documentation: Rolling documentation](https://space-ros.github.io/docs/rolling/Related-Projects/IKOS.html)
	- [Inference Kernel for Open Static (IKOS) Analyzers: A High-Performance Static Analysis Engine to Build Automated Code Analysis Tools for the Formal Verification of Critical Software Properties(ARC-16789-1) \| NASA Software Catalog](https://software.nasa.gov/software/ARC-16789-1)

#### 要件管理ツールの統合(or統合予定)
https://space-ros.github.io/docs/rolling/Introduction/How-Space-ROS-Differs.html#requirements-tools-and-processes
同上
- Doorstop
	- https://space-ros.github.io/docs/rolling/Related-Projects/Doorstop.html
	- https://doorstop.readthedocs.io/en/latest/
- FRET
	- https://space-ros.github.io/docs/rolling/Related-Projects/FRET.html
	- https://software.nasa.gov/software/ARC-18066-1

#### 宇宙システムに適用するうえで必要な機能の追加
https://space-ros.github.io/docs/rolling/Introduction/How-Space-ROS-Differs.html#space-specific-functionality

| C++ PMR memory allocator            | ROS2のコードを改良し、SpaceROSのモジュールとして独自のallocatorを設定できるよう対応。たとえば「非決定的な挙動を避ける」など、要件に応じた設定が可能              |
| ----------------------------------- | ------------------------------------------------------------------------------------------------- |
| Eventing & Telemetry Subsystem(ETS) | ロバストなイベントレポート機能。フライトソフトウェアの監視や計測に使う                                                               |

#### その他SpaceROS特有の機能？など
https://space-ros.github.io/docs/rolling/Introduction/How-Space-ROS-Differs.html#space-specific-functionality


| RTEMS            | RTEMS=Real-Time Executive for Multiprocessor Systems<br>SpaceROSはRTEMSに対応していて、RTEMS上で動作するデモもあるらしい |
| ---------------- | ------------------------------------------------------------------------------------------------- |
| cFS/ROS 2 Bridge | 従来のフライトソフトウェア(cFS)とSpaceROSをブリッジするためのプロジェクトが動いているらしい                                              |


まとめると...
--
- ROS2を宇宙向けロボットでも使えるようにした(する予定)のがSpaceROS
- 基本部分(NodeとかTopicとか)はROS2と同じで、宇宙向けロボットに適用するのに必要な信頼性を向上させるためにいろんなツールを統合したりしている
