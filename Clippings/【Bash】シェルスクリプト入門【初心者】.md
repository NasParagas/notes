---
title: "【Bash】シェルスクリプト入門【初心者】"
source: "https://qiita.com/flowernotfound/items/38b27f9d9af52925782b"
author:
  - "[[flowernotfound]]"
published: 2023-07-20
created: 2025-09-10
description: "目次 はじめに 対象者 シェルスクリプトとは？ Bashシェルとその基本コマンド シェルスクリプトの作成と実行 変数と環境変数 制御構造（if文、forループ、whileループなど） 関数 エラーハンドリングとデバッグ 最後に はじめに こんにちは。 最近Bashとシェ..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiitaにログインして、便利な機能を使ってみませんか？

あなたにマッチした記事をお届けします

便利な情報をあとから読み返せます

[ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fflowernotfound%2Fitems%2F38b27f9d9af52925782b&realm=qiita) [新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fflowernotfound%2Fitems%2F38b27f9d9af52925782b&realm=qiita)

この記事は最終更新日から1年以上が経過しています。

## 目次

[はじめに](https://qiita.com/flowernotfound/items/#%E3%81%AF%E3%81%98%E3%82%81%E3%81%AB)  
[対象者](https://qiita.com/flowernotfound/items/#%E5%AF%BE%E8%B1%A1%E8%80%85)  
[シェルスクリプトとは？](https://qiita.com/flowernotfound/items/#%E3%82%B7%E3%82%A7%E3%83%AB%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%97%E3%83%88%E3%81%A8%E3%81%AF%EF%BC%9F)  
[Bashシェルとその基本コマンド](https://qiita.com/flowernotfound/items/#Bash%E3%82%B7%E3%82%A7%E3%83%AB%E3%81%A8%E3%81%9D%E3%81%AE%E5%9F%BA%E6%9C%AC%E3%82%B3%E3%83%9E%E3%83%B3%E3%83%89)  
[シェルスクリプトの作成と実行](https://qiita.com/flowernotfound/items/#%E3%82%B7%E3%82%A7%E3%83%AB%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%97%E3%83%88%E3%81%AE%E4%BD%9C%E6%88%90%E3%81%A8%E5%AE%9F%E8%A1%8C)  
[変数と環境変数](https://qiita.com/flowernotfound/items/#%E5%A4%89%E6%95%B0%E3%81%A8%E7%92%B0%E5%A2%83%E5%A4%89%E6%95%B0)  
[制御構造（if文、forループ、whileループなど）](https://qiita.com/flowernotfound/items/#%E5%88%B6%E5%BE%A1%E6%A7%8B%E9%80%A0%EF%BC%88if%E6%96%87%E3%80%81for%E3%83%AB%E3%83%BC%E3%83%97%E3%80%81while%E3%83%AB%E3%83%BC%E3%83%97%E3%81%AA%E3%81%A9%EF%BC%89)  
[関数](https://qiita.com/flowernotfound/items/#%E9%96%A2%E6%95%B0)  
[エラーハンドリングとデバッグ](https://qiita.com/flowernotfound/items/#%E3%82%A8%E3%83%A9%E3%83%BC%E3%83%8F%E3%83%B3%E3%83%89%E3%83%AA%E3%83%B3%E3%82%B0%E3%81%A8%E3%83%87%E3%83%90%E3%83%83%E3%82%B0)  
[最後に](https://qiita.com/flowernotfound/items/#%E6%9C%80%E5%BE%8C%E3%81%AB)

## はじめに

こんにちは。  
最近Bashとシェルスクリプトに触れる機会があったので、基礎的な部分を簡潔に書いていこうと思います。  
これからシェルに触れる方のお役に立てば幸いです。

## 対象者

この記事は以下のような方々に向けて書かれています：

1. 「Bashってなんだ？」という方
2. 「Linuxのコマンドがわからない...」という方
3. 「コマンドは多少わかるけど、スクリプトは書けないよ...」という方

## シェルスクリプトとは？

シェルスクリプトは、一連のコマンドを記述したファイルで、実行することでコマンドラインでの操作を自動化することが可能です。  
ファイル操作、プログラムの実行、テキスト処理など、様々なタスクを自動化することが可能です。

シェルスクリプトの中身は、シェルが解釈・実行するコマンド（ls, cdなど）の集まりです。  
普段は手打ちで実行するようなコマンドを一度にまとめて実行することができ、作業の効率化や自動化に役立ちます。

## 例

非常に簡単な例ですが、たとえば以下のシェルスクリプトを実行すると、現在のディレクトリの内容を表示し、その後に日付と時刻を表示します。

```bash
#!/bin/bash
echo "現在のディレクトリの内容:"
ls
echo "現在の日付と時刻:"
date
```

このスクリプトを実行すると、まず `ls` コマンドにより現在のディレクトリの内容が表示され、次に `date` コマンドにより現在の日付と時刻が表示されます。

## Bashシェルとその基本コマンド

Bashは広く使われているシェルので、LinuxやMacで標準的に使用されています。  
シェルにはzshやcshなどいくつか種類がありますが、とりあえずbashを学んでおけば問題はないです。

まず、bashの基本的なコマンドを見ていきましょう。

- `ls`: 現在のディレクトリの内容を表示します。
	```bash
	$ ls
	file1.txt  file2.txt  directory1  directory2
	```
- `cd`: ディレクトリを移動します。例えば、 `cd Documents` とすると、Documentsディレクトリに移動します。
	```bash
	$ cd Documents
	$ pwd
	/home/user/Documents
	```
- `pwd`: 現在のディレクトリのフルパスを表示します。
	```bash
	$ pwd
	/home/user
	```
- `touch`: 新しいファイルを作成します。例えば、 `touch test.txt` とすると、test.txtという名前の新しいファイルが作成されます。
	```bash
	$ touch test.txt
	$ ls
	test.txt
	```
- `echo`: 引数として与えられたテキストを表示します。例えば、 `echo "Hello, world!"` とすると、"Hello, world!"と表示されます。
	```bash
	$ echo "Hello, world!"
	Hello, world!
	```

## シェルスクリプトの作成と実行

シェルスクリプトを作成するには、まずテキストエディタを使用してコマンドを含むテキストファイルを作成します。  
せっかくシェルスクリプトの勉強をするので、vimを使ってみるのもいいですね。  
ちなみに `vim` で起動できます。  
シェルスクリプトのファイル名は任意ですが、一般的には `.sh` の拡張子を持つことが多いです。

シェルスクリプトの最初の行にはshebang（シバン）と呼ばれる決まり文句を記述する必要があります。  
これはスクリプトがどのシェルを使用して実行するべきかを指定するものです。  
例えばBashシェルを使用する場合、shebangは `#!/bin/bash` となります。  
異なるシェル間でスクリプトを実行する際に、異なる動作をしないよう書いておきます。

では、簡単にシェルスクリプトの形式を紹介します。

```bash
#!/bin/bash
# これはコメントです
echo "Hello, world!"
```

このスクリプトは、 `echo` コマンドを使用して "Hello, world!" を表示します。  
`#` から始まる行はコメントとして扱われ、実行時には無視されます。

次に、作成したシェルスクリプトを実行するには、まず実行権限を付与する必要があります。  
実行権限を設定するには `chmod` コマンドを使用します。  
`chmod` はパーミッションに問題が生じた際にお世話になることがあるので、なんとなく覚えておくと役に立つかもしれません。

```bash
$ chmod +x script.sh
```

この `chmod` コマンドは、ファイルのパーミッション（許可）を変更するためのコマンドです。  
`+x` オプションは、実行権限を付与することを意味します。これにより、 `script.sh` に実行権限が付与されます。

パーミッションには、読み取り（r）、書き込み（w）、実行（x）の3種類があり、それぞれユーザー（u）、グループ（g）、その他（o）の3つのカテゴリに対して設定することができます。  
例えば、 `chmod u+x script.sh` とすると、ユーザーに対して `script.sh` の実行権限を付与します。

また、パーミッションは数字で表すことも可能です。読み取り、書き込み、実行の各パーミッションはそれぞれ4、2、1の値を持ち、これらを足し合わせてパーミッションを表現します。  
例えば、 `chmod 755 script.sh` とすると、ユーザーには読み取り（4）、書き込み（2）、実行（1）の全ての権限（合計7）が付与され、グループとその他には読み取り（4）と実行（1）の権限（合計5）が付与されます。

パーミッションを設定したら、以下のように実行します。

```bash
$ ./script.sh
Hello, world!
```

## 変数と環境変数

シェルスクリプトでは、他の言語と同様に変数を使用してデータを保存し参照することができます。  
変数を定義するには、等号（=）を使用して変数名とその値を指定します。  
等号の前後にはスペースを入れてはいけません。

以下に、変数の定義と参照の例を書いてみます。

```bash
#!/bin/bash
greeting="Hello, world!"
echo $greeting
```

このスクリプトでは、 `greeting` という変数に"Hello, world!"という文字列を代入し、 `echo` コマンドを使用してその値を表示しています。  
変数を参照する際には、変数名の前に$を付けます。

環境変数は、シェル全体で利用可能な変数です。環境変数は通常、大文字で表記されます。環境変数を定義するには、 `export` コマンドを使用します。

以下に、環境変数の定義と参照の例を示します。

```bash
#!/bin/bash
export GREETING="Hello, world!"
echo $GREETING
```

このスクリプトでは、 `GREETING` という環境変数に"Hello, world!"という文字列を代入し、その後で `echo` コマンドを使用してその値を表示しています。

環境変数は、シェル全体で利用可能な変数なので、異なるプロセスにも引き継がれます。  
例えば新規にbashを起動してそちらでも参照できるということです。

なかなか理解しにくいものなので、有効範囲が違うんだな、程度に理解しておけばいいと思います。

## 制御構造（if文、forループ、whileループなど）

Bashシェルスクリプトでは、if文、forループ、whileループなどの制御構造を使用できます。

### if文

if文は、指定した条件が真（true）である場合に、特定のコードを実行します。  
また、else節を使用して、条件が偽（false）である場合に別のコードを実行することもできます。おなじみですね。  
以下はif文とelse節の基本的な形式です。

```bash
#!/bin/bash
number=10
if [ $number -gt 5 ]; then
    echo "Number is greater than 5."
else
    echo "Number is not greater than 5."
fi
```

このスクリプトでは、 `number` という変数に10を代入し、その値が5より大きいかどうかを確認しています。  
`number` の値が5より大きければ、"Number is greater than 5."と表示し、そうでなければ、"Number is not greater than 5."と表示します。

### forループ

forループは、指定した回数だけ特定のコードを繰り返し実行します。これも皆さんおなじみでしょう。  
以下はforループの基本的な形式です。

```bash
#!/bin/bash
for i in 1 2 3 4 5
do
   echo "Looping ... number $i"
done
```

このスクリプトでは、 `i` という変数を1から5までの値に順に設定し、各値について"Looping... number $i"と表示します。

### whileループ

もちろんwhileも使えます。これは指定した条件が真（true）である間、特定のコードを繰り返し実行します。  
以下はwhileループの基本的な形式です。

```bash
#!/bin/bash
count=1
while [ $count -le 5 ]
do
   echo "Count: $count"
   let count=count+1
done
```

このスクリプトでは、 `count` という変数に1を代入し、その値が5以下である間、"Count: $count"と表示し、 `count` の値を1増やします。

ところで、今回 `-le` や `-gt` といった見慣れぬものが出てきましたね。  
ということでbashの比較演算子について少し触れておきます。

### 比較演算子

制御構造でよく使われるのが比較演算子です。  
今回のスクリプトでは `-le` や `-gt` といった比較演算子が出てきました。これらは数値の比較に使われます。  
以下にBashでよく使われる比較演算子をいくつか紹介します。

- `-eq`: 等しい
- `-ne`: 等しくない
- `-gt`: より大きい
- `-ge`: 以上
- `-lt`: より小さい
- `-le`: 以下

## 関数

Bashシェルスクリプトでは関数を定義して特定のコードをまとめることができます。  
関数は、一連のコマンドを一つの単位としてまとめ、名前をつけて呼び出すことができます。  
関数を用いることでコードの再利用性と可読性が向上します。

関数を定義するには、 `function` キーワードを使用します。  
以下は関数の定義と呼び出しの例です。

```bash
#!/bin/bash
function greet {
    echo "Hello, $1"
}
greet "world"
```

このスクリプトでは、 `greet` という名前の関数を定義し、その中で `echo` コマンドを使用して"Hello, $1"と表示しています。  
`$1` は、関数に渡された第一引数を表します。その後で、 `greet` 関数を呼び出し、"world"という引数を渡しています。  
実行結果として、"Hello, world"と表示されます。

関数は、複数の引数を取ることができます。  
引数は `$1` 、 `$2` 、 `$3` などと記述して受け取ります。  
以下に、複数の引数を取る関数の例を示します。

```bash
#!/bin/bash
function greet {
    echo "Hello, $1. Today is $2."
}
greet "world" "Monday"
```

このスクリプトでは、 `greet` 関数は2つの引数を取ります。  
第一引数は `$1` で、第二引数は `$2` で参照します。その後で、 `greet` 関数を呼び出し、"world"と"Monday"という2つの引数を渡しています。  
その結果"Hello, world. Today is Monday."と表示されます。

また、関数内で定義された変数は、その関数の中でのみ利用可能なローカル変数となります。

## 最後に

以上がBashシェルスクリプトの基礎的な内容となります。  
この記事がBashシェルスクリプトを始めるきっかけになれば幸いです。

修正点等ございましたらございましたら遠慮なくご指摘ください。

[0](https://qiita.com/flowernotfound/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fflowernotfound%2Fitems%2F38b27f9d9af52925782b&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fflowernotfound%2Fitems%2F38b27f9d9af52925782b&realm=qiita)

[9](https://qiita.com/flowernotfound/items/38b27f9d9af52925782b/likers)

いいねしたユーザー一覧へ移動

9