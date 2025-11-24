---
title: "Pandasで「野根（None）」という地名が欠損値（NaN）になってしまった話"
source: "https://zenn.dev/mierune/articles/014c283ecd2be6"
author:
  - "[[Zenn]]"
published: 2025-10-29
created: 2025-11-22
description:
tags:
  - "clippings"
---
[MIERUNEのZennブログ](https://zenn.dev/p/mierune) [Publicationへの投稿](https://zenn.dev/faq#what-is-publication)

281

21[tech](https://zenn.dev/tech-or-idea)

## Pandasの read\_csv における欠損値文字列の扱い

Pythonのデータ解析ライブラリ [Pandas](https://pandas.pydata.org/) では、CSVファイルを読み込む際（ `pd.read_csv()` ）、特定の文字列が自動的に `NaN` （欠損値）として扱われるという仕様があります。

ドキュメントによると、デフォルトで以下の値がNaNと解釈されます。

> By default the following values are interpreted as NaN: “ “, “#N/A”, “#N/A N/A”, “#NA”, “-1.#IND”, “-1.#QNAN”, “-NaN”, “-nan”, “1.#IND”, “1.#QNAN”, “<NA>”, “N/A”, “NA”, “NULL”, “NaN”, “None”, “n/a”, “nan”, “null “.

## 実際に起きた問題: 野根（None） という地名

この仕様の影響で、私たちの開発している歴史地図ウェブサービス「れきちず」のデータ処理中に思わぬ問題が発生しました。

データセットの中に「 **野根（のね）** 」という地名があり、その英語表記として **"None"** を付与しました。

ところが、この「None」は、Pandasで読み込むと自動的にNaNとして扱われてしまいました。

その結果、作成したGeoJSONの内容が `name_en: NaN` となってしまいました。この状態で [tippecanoe](https://github.com/felt/tippecanoe) によるベクトルタイル生成を行うと、 `JSON does not allow NaN` というエラーが発生しました。もし事前に欠損値を全てnullや空文字に変換していたら、エラーは出ないため、この事象は発覚しなかったでしょう...

## 解決策: keep\_default\_na=False を設定

この問題は `read_csv()` のオプションを調整することで解決できます。

デフォルトでは `keep_default_na=True` になっているため、これを `False` に設定します。

これにより、デフォルトのNaN解釈リストが無効化され、「None」などの文字列がそのまま文字列として扱われます。

さらに、独自に欠損値とみなしたい文字列を指定したい場合は `na_values` オプションを併用できます。

```
pd.read_csv("input.tsv", keep_default_na=False, na_values=["NaNにしたい文字列"])
```

また、 `na_filter` という、「欠損値の検出」を制御するオプションも用意されています。空文字や `na_values` に該当する値を検出しない場合（データに `NA` の値が全くない場合）、 `na_filter=False` とすることで、大規模データの読み込みでパフォーマンスを改善できる可能性があるそうです。

## 余談: 「野根」は四国の高知にあります

「野根」は、現在の高知県にあります。

![「れきちず」での該当箇所](https://storage.googleapis.com/zenn-user-upload/0b0c9ad42713-20251029.png)

*図: [「れきちず」での該当箇所](https://rekichizu.jp/map?center=134.754558%2C34.108133&zoom=8.17&pitch=0&bearing=0)*

1959年に「野根町」と「甲浦（かんのうら）町」が合併し、「東洋町」が発足しました。東洋町は高知県の最東端に位置し、徳島県と接する自治体です。

私は徳島に住んでいたころ、何度か東洋町を訪れて、宿泊したこともあります。その名の通り東に太平洋を望み、ビーチではサーフィンを楽しむ人の姿も見られる、静かで素敵なところでした。

![東洋町の海岸](https://storage.googleapis.com/zenn-user-upload/39fcb846f98d-20251029.png)

![東洋町でのキャンプ](https://storage.googleapis.com/zenn-user-upload/080ac94a33fd-20251029.png)

室戸岬をぐるっと一周139km走る自転車イベント「安芸・室戸パシフィックライド」では、野根中学校がエイドステーションになっていました。

![安芸・室戸パシフィックライドでの野根中学校エイドステーション](https://storage.googleapis.com/zenn-user-upload/7d452b0abdd2-20251029.png)

このようなかたちで再会するとは思いませんでした 🤭

281

21

### Discussion

![](https://static.zenn.studio/images/drawing/discussion.png)

ログインするとコメントできます

281

21