---
title: "ROS 2: ament_cmake_gtestでSWテストを実施する"
source: "https://zenn.dev/tasada038/articles/4c5faf5be9eb2f"
author:
  - "[[Zenn]]"
published: 2024-09-14
created: 2025-09-01
description:
tags:
  - "clippings"
---
2[tech](https://zenn.dev/tech-or-idea)

海洋ロボコンをやってた人です。  
今回はament\_cmake\_gtestでROS 2のコードをテストしたので、その備忘録として内容を記載しておきます。

ソフトウェアにおいてテスト工程は避けては通れないので、この機会に関連事項の整理も兼ねてまとめておきたいが今回の背景です。あとは、知人/後輩向けに。

ROS 2のテストについて知りたい方は **Section 4** からご覧ください。

また、Section 1. 〜 3.の内容は **すべて公開情報** から抜粋・要約して記載していますので、詳細は各Sectionの参照URLをご覧ください。（※ ディフェンスです）

## 1\. Software Testing Processes

はじめに、ソフトウェアのテストならびに、ソフトウェアテストプロセスについて。

ソフトウェアのテストは下記によると、ANSI/IEEE 1059 規格で「テストとは、ソフトウェア アイテムを分析して、既存の条件と必要な条件 (つまり、欠陥/エラー/バグ) の違いを検出し、ソフトウェア アイテムの機能を評価するプロセス」と定義されています。

ソフトウェアテストプロセスは、例えばA-SPICE（Automotive SPICE）の枠組みの中で、開発プロジェクトにおけるテスト工程を評価・改善するために使用されます。

テスト工程を分けると以下になります。

## 1.1 Unit Testing

`単体テスト` （ユニットテスト、Unit Testing、UT)は各関数、APIの最小単位・機能で検証を実施する。

## 1.2 Component Testing

`コンポーネントテスト` はシステムのコンポーネント / Class単位でテスト実施をすることから、Unit Testingと同義で表されることもあれば、Unit Testingとのスコープの違いで差別化することもある。

## 1.3 Integration Testing

`結合テスト` （インテグレーションテスト）は複数のClass / モジュール間でのテストで、コンポーネント間で正しいデータのやり取り、振る舞いができているかを確認する。

## 2\. Testing Methods

まず、なぜテストが必要か？  
端的に言えば、テストにより対象システムの `安全性（Safety)` と `信頼性（Reliability）` を遵守し、保証できることを確保するためです。

`ISO 26262` のような国際規格に準拠するためには、ユニットテスト、コンポーネントテスト、統合テストなどの段階的なテストが不可欠です。

特に自動車や医療機器、航空宇宙といった安全が最重要視される分野では、テストを通じてシステムの安全性と信頼性を徹底的に確認することが必要不可欠とされているためです。

機能安全に関する内容は以下を参照

Section 2ではSWテストにおけるテスト手法を示します。

## 2.1 White-Box Testing

`ホワイトボックステスト` は、ソフトウェアの内部構造や動作に基づいてテストを行う方法で、意図した仕様書通りに動いているかを確認する。

### Coverage

`カバレッジ（網羅率）` はソフトウェアのコードがどの程度テストされたかを測定する指標で、ソフトウェアの品質やバグの潜在リスクを減らすための指標です。

- 命令網羅:ステートメントカバレッジ (Statement Coverage/C0)

条件分岐の中で、 **真偽関係なく条件パスが1回は通る** と100%になる

- 条件網羅:ブランチカバレッジ（Condition Coverage/C1)

条件分岐の中で **真偽のパスが通る** と100%になる

- MC/DC: MOdified Condition/Decision Coverage

条件分岐の中で **真偽に関する条件値をすべて網羅** した上でパスが通ると100％になる

具体的でわかりやすい参照リンクも記載します。

[ホワイトボックステストのカバレッジの違いを説明できるようになろう](https://qiita.com/ms_/items/d117f3cf7a4fa5b9a1bd)

また、ホワイトボックステスト、ブラックボックステストを始めソフトウェア開発におけるテスト技法については以下を参照してください。

[Vector: ホワイトボックステストとは？](https://www.vector.com/jp/ja/know-how/vj-columns/vj-software-testing/vj-columns2003-02/#)

### 単体テストツール

## 2.2 Black-Box Testing

`ブラックボックステスト` は、ソフトウェアの内部構造に関係なく、入力と出力の関係に基づいてテストを行う方法です。

詳細は、Section 2.2のVector社のコラムを参照してください。

## 2.3 Regression Testing

`リグレッションテスト` は、ソフトウェアの変更や修正が、既存の機能に影響を与えていないかを確認するテストです。

いかにも記載がある通り、変更点に対する影響範囲を確認し、影響がある場合は影響度に応じてSection 2. Testing Methodsを再度実施して影響がないことを確認する、エビデンスを残す必要があります。

各フェーズごとにすべてのテスト工程を再度行うことは、作業工数（タイム/人件費コスト）としても非現実的なので、影響度のある箇所のみテストを行うというイメージです。

[リグレッションテストとは？](https://www.vector.com/jp/ja/know-how/vj-columns/vj-software-testing/vj-columns2003-03/)

## 3\. Risk Assessment

## 3.1 FTA

`Fault Tree Analysis (FTA)` は「故障の木分析」とも呼ばれ、システムやプロセスにおける潜在的な故障や問題の原因を特定し、視覚的に表現する方法です。

下記にも記載があるように、特性要因図など `システム` から `故障原因` を特定します。

[FTA（故障の木解析）とは？作成手順・FMEA・特性要因図との関連解説](https://jss1.jp/column/column_376/)

## 3.2 FMEA

`Failure Modes and Effects Analysis (FMEA)` は「故障モード影響分析」とも呼ばれ、製品やプロセスの設計段階での潜在的な故障モードを特定し、それが及ぼす影響を評価する手法です。

こちらも下記に記載があるように、製品や各機械の `故障状態` から `システム` への潜在的な影響度を **事前に特定** することが目的になります。

[FMEA（故障モード影響度解析）とは？FTAとの違いや実施手順を解説](https://jss1.jp/column/column_375/)

## 3.3 DRBFM

`Design Review Based on Failure Mode (DRBFM)` は「故障モードに基づく設計レビュー」とも呼ばれる設計のデザインレビュー(Design Review: DR)手法で、設計の変更や新しい設計要素がどのように故障モードを引き起こす可能性があるかを評価手法です。

新規点・変更点の故障モードを「心配点」として抽出しDRすることで、故障モードの抜け漏れを無くすことを目的としていると言えます。

DRBFMのフォーマットは以下などでも公開されており、書籍も出ているので現場で使用する際にはインプットとして使用できると思います。

[DRBFMとは？　実践に使える記入例あり！目的と進め方を解説](https://qctoranomaki.com/qms/qa/drbfm/)

[これがトヨタ式DRBFMの正しい進め方](https://perfectmanual.themedia.jp/posts/13157848/)

## 3.4 QC 7 Tools

QC7つ道具（QC 7 Tools: 英)とは、品質管理（Quality Control）において問題解決や改善活動を行うために使用される基本的な7つの手法のことです。以下はその7つの道具です。

- Pareto chart  
	パレート図: 問題や原因を大きい順に並べた棒グラフで、重要な項目を特定する
- Cause-and-effect diagram  
	特性要因図: 問題の原因を整理して視覚化する図で、原因と結果の関係を明確にする
- Histogram  
	ヒストグラム: データの分布状況を棒グラフで表し、ばらつきを確認する
- Check Sheet  
	チェックシート: データ収集のための表形式のツールで、頻度や傾向を把握する
- Scatter diagram  
	散布図: 2つの変数間の相関関係を視覚的に示すグラフ
- Control chart  
	管理図: 製品やプロセスの品質を時系列で管理するために使用し、異常を検出する
- Stratification  
	層別: データを複数の要因ごとに分類して解析し、問題の原因を特定する

[『QC7つ道具』とは？ 手法の説明とソフトウェア開発現場における活用例](https://service.shiftinc.jp/column/8270/)

## 4\. Running Tests in ROS 2

Section 1., 2., 3.の内容をベースに ROS 2ソフトのテスト実施工程を実際に再現してみます。

## 4.1 Install

ROS 2のテスト方法は以下で詳細に語られていますが、ここでは `ament_cmake_gtest` と `lcov` を使用してユニットテスト&カバレッジ表示とコンポテスト、結合テストを実装していきます。

使用するライブラリおよびパッケージは以下です。

terminal

```bash
sudo apt install lcov
```

`lcov` はGCC のカバレッジ テスト ツール `gcov` のグラフィカル フロントエンドで、カバレッジ結果をHTMLベースで確認することができるツールです。

また単体テストフレームワークは `Google C++ Testing Framework: gtest` と `ament` を結合したパッケージである `ament_cmake_gtest` を使用します。CMakeプロジェクトでGoogle Testを使用するための機能が以下で提供されています。

## 4.2 Coding

Unit Testのカバレッジ計測で100%になることを目的に、適当なROS 2 プログラムを作成し、gtestを使用したテストケースを作成します。

cppファイルはユニットテスト等で使いまわしできるようにコンポーネント単位で記載します。  
また、ユニットテストでのカバレッジ計測のため意図的に条件分岐を入れています。

publisher\_component.cpp

```cpp
#include "software_testing_ros2/publisher_component.hpp"

PublisherNode::PublisherNode() : Node("publisher_node")
{
  publisher_ = this->create_publisher<std_msgs::msg::Float32>("float_topic", 10);
}

void PublisherNode::publish_message(float value)
{
  auto msg = std_msgs::msg::Float32();

  if (value > 0.0) {
    RCLCPP_INFO(this->get_logger(), "Publishing positive value: '%f'", value);
    msg.data = value;
  } else if (value < 0.0) {
    RCLCPP_WARN(this->get_logger(), "Publishing negative value: '%f'", value);
    msg.data = value;
  } else {
    RCLCPP_ERROR(this->get_logger(), "Publishing zero value");
    msg.data = 0.0;
  }

  publisher_->publish(msg);
}
```

次にユニットテスト用のソースをtestフォルダ下に作成します。  
`ament_cmake_gtest` のドキュメントに記載のあるように、gtest用のヘッダーファイルをインクルードし、マクロを使用してテストケースを記載していきます。

テストケースの書き方は大きく以下の2つになります。

- 簡単なテスト  
	TEST()
- テストフィクスチャ:複数のテストで同じデータ設定を使う  
	TEST\_F(テストフィクスチャクラス名, テストケース名)

unit\_test\_publisher\_component.cpp

```cpp
#include <gtest/gtest.h>
#include <rclcpp/rclcpp.hpp>
#include <std_msgs/msg/float32.hpp>
#include "software_testing_ros2/publisher_component.hpp"

/******************************************************************************
 * @brief A test fixture for PublisherNode to facilitate testing.
 *
 * Inherits from PublisherNode and provides a method to test message publishing.
 ******************************************************************************/
class PublisherNodeTest : public PublisherNode
{
public:
  using PublisherNode::PublisherNode;
  float last_published_value_;

  PublisherNodeTest() : PublisherNode() {}

/******************************************************************************
   * @brief Tests the publish_message method.
   *
   * Publishes a value and stores it in last_published_value_.
   *
   * @param value The value to publish.
 ******************************************************************************/
  void test_publish_message(float value)
  {
    publish_message(value);
    last_published_value_ = value;
  }
};

/******************************************************************************
 * @brief Tests the PublisherNode class for correct message publishing.
 *
 * This test initializes ROS2, creates a PublisherNodeTest instance,
 * publishes a value, and verifies the published value.
 ******************************************************************************/
TEST(PublisherNodeTest, UnitTesting)
{
  // Initialize ROS2
  int argc = 0;
  char **argv = nullptr;
  rclcpp::init(argc, argv);

  // Create the node instance
  auto node = std::make_shared<PublisherNodeTest>();

  /* ---------- Test Case 1. ----------*/
  /* Publish a positive value */
  node->test_publish_message(5.0);
  EXPECT_EQ(node->last_published_value_, 5.0);

  /* ---------- Test Case 2. ----------*/
  /* Publish a negative value (currently commented out) */
  node->test_publish_message(-3.0);
  EXPECT_EQ(node->last_published_value_, -3.0);

  /* ---------- Test Case 3. ----------*/
  /* Publish a zero value (currently commented out) */
  node->test_publish_message(0.0);
  EXPECT_EQ(node->last_published_value_, 0.0);

  /* Shutdown ROS2 */
  rclcpp::shutdown();
}
```

CMakeLists.txtでは `ament_add_gtest` を追記してテスト対象を登録します。

CMakeLists.txt

```
ament_add_gtest(unit_test_publisher test/unit_test_publisher_component.cpp)
  target_link_libraries(unit_test_publisher
    publisher_component
  )
  ament_target_dependencies(unit_test_publisher
    rclcpp
    std_msgs
  )
```

## 4.3 Testing the ROS 2 code

ここまでできたら、以下で `colcon build` と `colcon test` を実行してテスト実施とカバレッジ結果を確認します。

CUIベースでカバレッジを確認する場合は以下を実行します。

terminal

```bash
colcon test-result --verbose
# Summary: 8 tests, 0 errors, 0 failures, 0 skipped
```

もしエラーの場合は以下のように、failure message　unit\_test\_publisher.gtest.xml  
とどのテスト項目でエラーとなったか表示されます。

![](https://storage.googleapis.com/zenn-user-upload/b177f5e05b81-20240914.png)

terminal

```bash
colcon test --event-handlers console_cohesion+
```

でSummary結果の詳細を確認することも可能です。

`~/test_ws/build/software_testing_ros2/test_results/software_testing_ros2` でxmlレポートとしても確認できます。

![](https://storage.googleapis.com/zenn-user-upload/fae69392659c-20240914.png)

unit\_test\_publisher.gtest.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<testsuites tests="1" failures="0" disabled="0" errors="0" time="0.015" timestamp="2024-09-14T20:07:13" name="AllTests">
  <testsuite name="PublisherNodeTest" tests="1" failures="0" disabled="0" errors="0" time="0.015" timestamp="2024-09-14T20:07:13">
    <testcase name="UnitTesting" status="run" result="completed" time="0.015" timestamp="2024-09-14T20:07:13" classname="software_testing_ros2.PublisherNodeTest" />
  </testsuite>
</testsuites>
```

また、lcovを利用することでカバレッジ結果をHTMLレポートとして記録することもできるので、MC/DCが100%出ない場合は再度テスト実施してソースコードの保証を検討してみてください。（以下インプットのGitHub imgより参照)

![](https://storage.googleapis.com/zenn-user-upload/c4000d2f3d47-20240914.png)

## Reference

[Running Tests in ROS 2 from the Command Line](https://docs.ros.org/en/humble/Tutorials/Intermediate/Testing/CLI.html)

[Unit Testing for ROS2 | ROS2 Developers Open Class #156](https://www.youtube.com/watch?v=t2Jm1Nt49-A)

  

以上です。  
Likeいただけると大変励みになりますので、よろしくお願いいたします。

2