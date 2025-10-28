---
title: "curl コマンドの基本"
source: "https://zenn.dev/skrikzts/articles/8fe062618f3fa8"
author:
  - "[[Zenn]]"
published: 2024-09-18
created: 2025-08-26
description:
tags:
  - "clippings"
---
12

3[tech](https://zenn.dev/tech-or-idea)

## はじめに

このページでは、 `curl` コマンドについて記述します。

## curl コマンドとは？

`curl` （Client URL）は、コマンドラインからHTTPリクエストを送信するためのツールです。URLを指定してデータを取得したり、データを送信したりすることができます。 `curl` はHTTPだけでなく、FTPやSMTPなどの他のプロトコルもサポートしています。

## インストール方法

多くのLinuxディストリビューションやmacOSには `curl` が標準でインストールされています。インストールされていない場合は、以下のコマンドでインストールできます。

### Ubuntu/Debian系:

```bash
sudo apt update
sudo apt install curl
```

### CentOS/RHEL系:

```bash
sudo yum install curl
```

### macOS (Homebrewを使用):

```bash
brew install curl
```

## 基本的な使い方

### 1\. HTTPリクエストを使用してデータを取得する

`curl` の最も基本的な使い方は、URLを指定してデータを取得することです。

```bash
curl http://example.com
```

上記のコマンドは、 `http://example.com` からデータを取得し、標準出力に表示します。

### 2\. レスポンスをファイルに保存する

`-o` オプションを使用して、レスポンスをファイルに保存することができます。

```bash
curl -o output.html http://example.com
```

このコマンドは、 `example.com` のHTMLコンテンツを `output.html` という名前のファイルに保存します。

### 3\. リクエストヘッダーを表示する

`-I` オプションを使用すると、サーバーのレスポンスヘッダーのみを表示できます。

```bash
curl -I http://example.com
```

このコマンドは、 `HTTP` ヘッダー情報（ステータスコード、コンテンツタイプなど）を表示します。

### 4\. POSTリクエストを送信する

#### 1\. POSTリクエストの基本

`-X POST` オプションを使用すると、POSTリクエストを送信できます。データを送信するには、 `-d` オプションを使用します。

```bash
curl -X POST -d "name=John&age=25" http://example.com/post
```

このコマンドは、 `name` と `age` というデータを `POST` メソッドで `http://example.com/post` に送信します。

#### 2\. JSON形式のリクエストボディを使用したPOSTリクエスト

APIへのリクエストでは、JSON形式のデータを送信することが一般的です。その場合、 `-H` オプションを使用して `Content-Type` ヘッダーを指定し、 `-d` オプションでJSONデータを送信します。

```bash
curl -X POST -H "Content-Type: application/json" -d '{"name": "John", "age": 25}' http://example.com/post
```

このコマンドは、 `{"name": "John", "age": 25}` というJSONデータを `http://example.com/post` に対してPOSTリクエストとして送信します。 `Content-Type: application/json` ヘッダーを指定することで、サーバーにJSONデータとして処理するよう指示しています。

#### 3\. ファイルを使用したリクエストボディの送信

リクエストボディの内容が多い場合や複雑な場合、 `-d` オプションの代わりにファイルを使用してデータを送信することができます。以下の例では、 `data.json` というファイルの内容をリクエストボディとして送信します。

まず、 `data.json` というファイルを作成し、以下のような内容を記述します：

```json
{
  "name": "John",
  "age": 25
}
```

次に、 `curl` コマンドでそのファイルを読み込み、POSTリクエストを送信します。

```bash
curl -X POST -H "Content-Type: application/json" -d @data.json http://example.com/post
```

このコマンドは、 `data.json` ファイルの内容を読み込み、 `http://example.com/post` に対してJSONデータとして送信します。

## よく使われるオプションのまとめ

| オプション | 説明 |
| --- | --- |
| `-o` | 出力をファイルに保存 |
| `-O` | リモートファイル名を使用して保存 |
| `-I` | ヘッダーのみを表示 |
| `-X` | リクエストメソッドの指定 |
| `-d` | POSTリクエストで送信するデータ |
| `-H` | リクエストヘッダーの追加 |
| `-u` | ユーザー名とパスワードの指定 |
| `-L` | リダイレクトを追跡 |

詳細なオプションと使用方法については、公式ドキュメントを参照してください: [curl公式ドキュメント](https://curl.se/docs/)

## コマンドの比較

`curl` と同様にHTTPリクエストを行うツールとしては、 `wget` があります。

### curl vs wget

- `curl`:
	- デフォルトでは標準出力に出力される。
	- HTTPヘッダーの表示や、POSTリクエストの送信、プロトコルのサポートが豊富。
	- URLのリクエストだけでなく、APIテストや複雑なデータの送信も可能。
- `wget`:
	- デフォルトでファイルをダウンロードする。
	- 再帰的なダウンロードが可能。
	- 一般的に `wget` は単純なファイルのダウンロードに向いている。

## まとめ

`curl` は、HTTPリクエストを行うための非常に強力で柔軟なコマンドラインツールです。ファイルのダウンロードからAPIのテストまで幅広い用途で使用されます。他のツールと比較しても、多機能でプロトコルのサポートも豊富です。 `curl` を使いこなすことで、様々なネットワーク操作がより簡単になります。

12

3

12

3