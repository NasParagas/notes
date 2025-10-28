---
title: "docker buildコマンドのオプション"
source: "https://web-learn.hatenablog.com/entry/2024/08/17/201201"
author:
  - "[[tomotomo1129]]"
published: 2024-08-17
created: 2025-08-26
description: "docker buildコマンドのオプション docker buildコマンドのオプション docker build コマンドは Docker イメージを作成するために使用されます。 以下は、docker build コマンドの主なオプションの一覧です：1. -t または --tag イメージに名前とタグを付けます。 形式は name:tag で、タグを指定しない場合は latest がデフォルトで使用されます。 docker build -t my-image:latest . 2. -f または --file Dockerfile の場所を指定します。 デフォルトでは、Dockerfile…"
tags:
  - "clippings"
---
- [docker buildコマンドのオプション](https://web-learn.hatenablog.com/entry/2024/08/17/#docker-build%E3%82%B3%E3%83%9E%E3%83%B3%E3%83%89%E3%81%AE%E3%82%AA%E3%83%97%E3%82%B7%E3%83%A7%E3%83%B3)

### docker buildコマンドのオプション

docker build コマンドは Docker イメージを作成するために使用されます。  
以下は、docker build コマンドの主なオプションの一覧です：

1\. **\-t または --tag**  
イメージに名前とタグを付けます。  
形式は name:tag で、タグを指定しない場合は latest がデフォルトで使用されます。

```sh
docker build -t my-image:latest .
```

2\. **\-f または --file**  
Dockerfile の場所を指定します。  
デフォルトでは、Dockerfile という名前のファイルが使用されます。

```sh
docker build -f Dockerfile.dev .
```

3\. **\--build-arg**  
ビルド時に引数を指定します。  
これにより、Dockerfile 内で ARG 命令を使用して、ビルド時のパラメータを変更できます。

```sh
docker build --build-arg MY_VAR=value .
```

4\. **\--no-cache**  
キャッシュを使用せずにビルドします。  
通常、Docker は以前のビルド結果をキャッシュして再利用しますが、このオプションを使うと新しくビルドします。

```sh
docker build --no-cache .
```

5\. **\--rm**  
ビルド中に生成された中間コンテナを自動的に削除します。  
デフォルトで有効です。  
無効にするには、--rm=false を使用します。

```sh
docker build --rm=false .
```

6\. **\--target**  
マルチステージビルドを使用して、指定したビルドステージまでビルドします。  
これにより、特定のビルドステージだけをビルドすることができます。

```sh
docker build --target stage2 .
```

7\. **\--ssh**  
ビルドコンテキスト内で SSH エージェントを利用できるようにします。  
このオプションは、SSH 鍵をビルドプロセスに渡すのに役立ちます。

```sh
docker build --ssh default .
```

8\. **\--network**  
ビルド中のネットワークモードを指定します。  
これにより、ビルドプロセスが特定のネットワーク設定で実行されます。

```sh
docker build --network host .
```

9\. **\--platform**  
ビルドするイメージのプラットフォームを指定します。  
これにより、特定のアーキテクチャ向けにイメージをビルドできます。

```sh
docker build --platform linux/amd64 .
```

10\. **\--quiet または -q**  
ビルドプロセスの出力を抑え、最終的なイメージ ID のみを表示します。

```sh
docker build -q .
```

これらのオプションは、ビルドのカスタマイズや効率的な管理を可能にします。  
ビルドの設定に応じて、適切なオプションを選択してください。