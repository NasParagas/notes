---
title: "意思決定を委ねる：CLIツール「should-i」"
source: "https://zenn.dev/justhiro/articles/5351c337282175"
author:
  - "[[Zenn]]"
published: 2025-11-08
created: 2025-11-10
description:
tags:
  - "clippings"
---
[tech](https://zenn.dev/tech-or-idea)

## はじめに

「今日のランチは何にしよう？」「新しいライブラリを試すべきか？」日常の小さな決断から技術選定まで、私たちは日々無数の選択を迫られています。そんな時、宇宙に判断を委ねてみるのはいかがでしょうか。

今回は、 [yesno.wtf API](https://yesno.wtf/) を使って意思決定をサポートするCLIツール「should-i」をRustで開発しました。この記事では、その実装過程と技術的なポイントを紹介します。

## should-iとは

should-iは、質問を投げかけるとYES/NO/MAYBEで答えてくれるシンプルなCLIツールです。回答と一緒にGIF画像のURLも返してくれるので、視覚的にも楽しめます。

### 使い方

![should-iの実行例](https://storage.googleapis.com/zenn-user-upload/1baa08456559-20251109.png)

#### \--openオプションでGIFをブラウザ表示

`--open` オプションを付けると、回答のGIF画像を自動的にブラウザで開いてくれます：

```bash
$ should-i "take a coffee break" --open
🎲 Asking the universe...
✅ YES! Do it! 🎉
🖼️ https://yesno.wtf/assets/yes/2-5df1b403f2654fa77559af1bf2332d7a.gif
# ブラウザでランダムにGIFを自動的に開きます
```

## 技術スタック

このプロジェクトでは以下のクレートを使用しました：

- **reqwest**: HTTP通信を担当
- **tokio**: 非同期ランタイム
- **serde/serde\_json**: JSONのシリアライズ・デシリアライズ
- **clap**: CLIの引数パーサー
- **anyhow**: エラーハンドリング

## 実装のポイント

### 1\. API通信の実装

yesno.wtf APIはシンプルなRESTful APIで、レスポンスは以下のような構造です：

```json
{
  "answer": "yes",
  "forced": false,
  "image": "https://yesno.wtf/assets/yes/2.gif"
}
```

これをRustの構造体にマッピングします：

```rust
#[derive(Deserialize)]
struct YesNoResponse {
    answer: String,
    image: String,
}
```

reqwestを使った非同期通信は、シンプルかつ型安全に記述できます：

```rust
let response = client
    .get("https://yesno.wtf/api")
    .send()
    .await?
    .json::()
    .await?;
```

### 2\. CLIインターフェースの設計

clapを使うことで、直感的なCLIを簡単に実装できます。derivマクロを活用することで、構造体定義から自動的にパーサーを生成できるのがRustらしい特徴です：

```rust
#[derive(Parser)]
#[command(name = "should-i")]
#[command(about = "A CLI tool to help you make decisions")]
struct Cli {
    /// The question you want to ask
    question: String,
    
    /// Open the GIF image in your browser
    #[arg(short, long)]
    open: bool,
}
```

### 3\. ユーザー体験の向上

単に結果を表示するだけでなく、絵文字を使って視覚的に分かりやすい出力を心がけました：

```rust
match response.answer.as_str() {
    "yes" => println!("✅ YES! Do it! 🎉"),
    "no" => println!("❌ NO! Don't do it! 🚫"),
    "maybe" => println!("🤔 MAYBE... You decide! 🎲"),
    _ => println!("🤷 Unknown response"),
}
```

また、 `--open` オプションでブラウザを自動的に開く機能も実装しました：

```rust
if cli.open {
    if let Err(e) = webbrowser::open(&response.image) {
        eprintln!("⚠️  Failed to open browser: {}", e);
    }
}
```

## エラーハンドリング

Rustの強力な型システムを活かし、anyhowクレートを使って適切なエラーハンドリングを実装しました：

```rust
async fn fetch_decision() -> anyhow::Result {
    let client = reqwest::Client::new();
    let response = client
        .get("https://yesno.wtf/api")
        .send()
        .await
        .context("Failed to connect to yesno.wtf API")?
        .json::()
        .await
        .context("Failed to parse API response")?;
    
    Ok(response)
}
```

`context()` メソッドを使うことで、エラーが発生した際に具体的な状況を伝えられます。

## まとめ

should-iは、シンプルながらもRustのCLIツール開発の基本的な要素を詰め込んだプロジェクトです：

- HTTP通信と非同期処理
- JSONのパース
- CLIの引数処理
- エラーハンドリング
- クロスプラットフォーム対応

「宇宙に判断を委ねる」という遊び心のあるコンセプトですが、実装を通じてRustの実践的な技術を学べました。

迷った時は、ぜひ `should-i` に聞いてみてください！

```bash
$ should-i "write more Rust code"
🎲 Asking the universe...
✅ YES! Do it! 🎉
```

### インストール方法

should-iは [crates.io](https://crates.io/crates/should-i) で公開されており、cargoを使って簡単にインストールできます：

```bash
$ cargo install should-i
```

インストール後は、すぐに使い始められます：

```bash
$ should-i "try this new library"
```

## リンク

10

5

10

5