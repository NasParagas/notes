---
title: "Rustで時間計測をするには"
source: "https://qiita.com/moriai/items/2e39357fc34c54f6de49"
author:
  - "[[moriai]]"
published: 2020-12-28
created: 2025-10-29
description: "はじめに Rust でメソッドの実行時間を測定したいと思ったので、調べてみました。小ネタですが Rustその2 Advent Calendar 2020 24日目 アドベントカレンダーが空いていたので、埋めておきます。 環境 とりあえず、手持ちで一番安定している環境とい..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiitaにログインして、便利な機能を使ってみませんか？

あなたにマッチした記事をお届けします

便利な情報をあとから読み返せます

[ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fmoriai%2Fitems%2F2e39357fc34c54f6de49&realm=qiita) [新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fmoriai%2Fitems%2F2e39357fc34c54f6de49&realm=qiita)

この記事は最終更新日から3年以上が経過しています。

[24日目](https://qiita.com/advent-calendar/2020/rust2)

[Rust 2 Advent Calendar2020](https://qiita.com/advent-calendar/2020/rust2)

[@moriai (Shiisaa Moriai)](https://qiita.com/moriai)

## Rustで時間計測をするには

最終更新日 投稿日 2020年12月28日

## はじめに

Rust でメソッドの実行時間を測定したいと思ったので、調べてみました。小ネタですが [Rustその2 Advent Calendar 2020](https://qiita.com/advent-calendar/2020/rust2) 24日目 アドベントカレンダーが空いていたので、埋めておきます。

## 環境

とりあえず、手持ちで一番安定している環境ということで。

- MacBook Pro (Retina, 15-inch, Mid 2015)
	- Processor: 2.8 GHz Quad-Core Intel Core i7-4980HQ
	- Memory: 16 GB 1600 MHz DDR3
- macOS 10.15.7 (19H114)
- rustc 1.48.0 (7eac88abb 2020-11-16)

## crate std

測定対象のメソッドを呼ぶ前と呼んだ後で `std::time::Instant::now()` を呼びだし、 `duration_since()` メソッドで duration を求めるのが簡単で分かりやすい方法だと思います。

`std::time::Instant` のドキュメントによれば、次のようなシステムコールを呼んでいるそうです。私が今のところ使ったことがあるプラットフォームだけ抽出しておきます。

| プラットフォーム | システムコール |
| --- | --- |
| UNIX | clock\_gettime(CLOCK\_MONOTONIC) |
| macOS | mach\_absolute\_time |
| WASI | \_\_wasi\_clock\_time\_get(CLOCKID\_MONOTONIC) |
| Windows | QueryPerformanceCounter |

## crate quanta

最近の x86 系の CPU であれば、CPU 内に TSC カウンタというリアルタイムクロックで駆動されるカウンタがありますので、それを直接呼び出すものです。CPU 内のレジスタを読み出すだけなので、システムコールを呼び出すよりは格段にオーバーヘッドが少ないのではと思ってしまいます。オーバーヘッド `crate quanta` のドキュメントによれば、TSC カウンタの読み出しオーバーヘッドは 11ns 程度らしいので、意外とオーバーヘッドがあるのねぇ、と思ってしまいます。

## やってみよう！

とりあえず、カウンタを１万回呼び出し、その平均値を求めましょう。

src/main.rs

```rust
use std::time::Instant;
use quanta::Clock;

fn main() {
    let mut clock = Clock::new();
    const N:u32 = 1_000_000;

    let istart = Instant::now();
    let mut istop = istart;
    for _ in 1..N {
        istop = Instant::now();
    }
    println!("std::time:Instant::now() overhead = {:?}",
        istop.duration_since(istart));

    let start = clock.now();
    let mut stop = start;
    for _ in 1..1_000_000 {
        stop = clock.now();
    }
    println!("quanta::clock::now() overhead = {:?}",
        stop.duration_since(start));
}
```

```console
$ cargo r --quiet --release
std::time::Instant::duration 15.614537ms
quanta::Instant::duration 13.580445ms
```

私の環境だと、何度やってもこの程度でした。1,000,000回呼び出しているので、std と quanta のオーバーヘッドはそれぞれ 15.6ns, 13.6ns ぐらいのようです。

## おわりに

`crate quanta` を使った方が 2.0ns ほどオーバーヘッドが小さいようです。

今回は、こんなところで。

[0](https://qiita.com/moriai/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fmoriai%2Fitems%2F2e39357fc34c54f6de49&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fmoriai%2Fitems%2F2e39357fc34c54f6de49&realm=qiita)

[![moriai](https://qiita-user-profile-images.imgix.net/https%3A%2F%2Favatars1.githubusercontent.com%2Fu%2F5388032%3Fv%3D4?ixlib=rb-4.0.0&auto=compress%2Cformat&lossless=0&w=128&s=511847dcbaefbd4d4c3ddc55362d2327)](https://qiita.com/moriai)

[

## @moriai(Shiisaa Moriai)

](https://qiita.com/moriai)

Linux, Darwin のカーネルを触ったり, Rust, Swift, C で軽くプログラムしたり, Elastic Stack で可視化したり, OSの将来を夢見てみたり. 低レイヤは Real-Time Mach や FreeBSD で学びました.

[RSS](https://qiita.com/moriai/feed)

この記事は以下の記事からリンクされています

- [Rustler で行儀の良い NIF を書く](https://qiita.com/kobae964/items/9b8e78b8b0f3ca0f7e19) の [コメント](https://qiita.com/kobae964/items/9b8e78b8b0f3ca0f7e19#comment-0dbe5e56eb2208575994)

## Qiita Conference 2025 Autumn 11月5日(水)~7日(金)開催！

![](https://cdn.qiita.com/assets/public/official_campaigns/qiita_conference_2025_autumn/image-conference_2025_autumn_ogp-d35d2500cd0420d93f2ed69a8d162d74.png)

Qiita Conferenceは、AI時代のエンジニアに贈るQiita最大規模のテックカンファレンスです！

基調講演ゲスト(敬称略)

piacere、牛尾 剛、Esteban Suarez、和田 卓人、seya、ミノ駆動、市谷 聡啓、からあげ、岩瀬 義昌、まつもとゆきひろ、みのるん and more…

[イベント詳細を見る](https://qiita.com/official-campaigns/conference/2025-autumn)

[10](https://qiita.com/moriai/items/2e39357fc34c54f6de49/likers)

いいねしたユーザー一覧へ移動

9