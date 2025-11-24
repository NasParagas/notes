---
title: "Rust メモ: Voldemort パターン"
source: "https://nossie531.github.io/doc/rust/voldemort.xhtml"
author:
published:
created: 2025-11-24
description:
tags:
  - "clippings"
---
Voldemort パターンは Rust でたまに使われるデザインパターンである。

クレート外に公開する型やトレイトについて、名指しでの利用を禁止する。

## 基本

以下を満たす型やトレイトが Voldemort パターンの適用対象になる。

- クレート外に公開されないモジュールに所属している。
- クレート外に公開される他アイテムの構成要素になっている。

## サンプル

以下では、 `my_lib` クレートの `MagicNo` 型が Voldemort パターンの適用対象になる。  
そのため、 `my_lib` クレートが提供する以外の方法では、 `MagicNo` の操作は行えない。

my\_app/src/main.rs

```rust
use my_lib::*;

fn main() {
    print_magic_no(get_magic_no());
}
```

my\_lib/src/lib.rs

```rust
mod private {
    pub struct MagicNo(pub i32);
}

pub fn print_magic_no(val: private::MagicNo) {
    println!("Magic number is {}.", val.0);
}

pub fn get_magic_no() -> private::MagicNo {
    private::MagicNo(42)
}
```

## 補足: 公開指定の意味

Voldemort パターンでは、"非公開" モジュール内のアイテムが、名指しこそできなくとも、他クレートに "公開" されている。この文言の不一致はどう解釈すれば良いのだろうか？

まず、モジュールの公開指定は、モジュール自身の公開有無にのみ関係し、そこに含まれる各アイテムの公開有無とは関係ない。そして、各アイテムの公開有無については、そのアイテム自身の公開指定が制御する。つまり、非公開モジュールは、それを経た名前空間での名指しを禁止する効果しかない…と考えると合点がいく。

もちろん殆どの場合、公開するなら名指しも許可する運用となるが、Voldemort パターンはその数少ない例外となる。

## 応用

上で紹介した例の他にも、いくつか使い方がある。

## Sealed トレイトパターン

トレイトの基底となるトレイトに Voldemort パターンを適用する事で、新たなトレイトの実装を禁止する。詳しくは『 [Sealed トレイトパターン](https://nossie531.github.io/doc/rust/sealed_trait.xhtml) 』を参照。

## トレイトメソッドの仮想的な非公開化 (欠点あり)

トレイトメソッドを非公開にする事はできないが、Voldemort 型をダミーの引数や型引数として使えば、仮想的にそれが実現できる。

ただし、これには欠点が多い。まず、無駄な引数が増える。また、IDE のコード補完で実際には使えない候補が提示されるようになる。

用途としては『 [dyn 互換の確認](https://nossie531.github.io/doc/rust/dyn_or_not.xhtml) 』のような特殊なパターンがある。一方、トレイトのデフォルト実装から呼ばれる共通処理のそこそこ分かりやすい置場としても使えるが、これには前述の欠点が勝るだろう。

## rustdoc

Voldemort パターンを適用したアイテムは、rustdoc では文書化されない。

ただし、それを構成要素とするアイテムの宣言部には名前が残る。この名前はもはや名指しできないが、それを含むために制限される操作がある事の暗示や、Voldemort パターンが適用されたその他のアイテムとの区別には役立つ。