---
title: "Linuxでコアダンプを出力する方法"
source: "https://zenn.dev/blackengineer/articles/9c60cdfbc96bf1b769da"
author:
  - "[[Zenn]]"
published: 2020-09-28
created: 2025-08-21
description:
tags:
  - "clippings"
---
13[tech](https://zenn.dev/tech-or-idea)

## 概要

Linuxでコアダンプが出力されない時に確認すべき設定についてまとめます。

## ulimitを設定する

コアファイルの出力サイズが制限されている可能性があるため、ulimitコマンドで確認します。  
ulimitはユーザーが使用できるリソースを制限するコマンドです。  
"ulimit -c"でコアファイルの出力最大サイズが確認できます。（"ulmit -a"でコアサイズ以外のリソース制限情報も含めて全て表示できます）

```
$ ulimit -c
0
```

上記だとコアファイルの出力最大サイズが0になっているため、コアファイルは出力されません。  
最大サイズを変更するには"-c \[size\]"で可能です。sizeにunlimitedを指定すると、サイズ制限なしになります。

```
$ ulimit -S -c unlimited
$ ulimit -c 
unlimited
```

\-Sというオプションはソフトリミットを示しており、-Hをつけるとハードリミットになります。  
ソフトリミットはハードリミットの範囲内のみの設定が可能です。  
ハードリミットで設定すると、一般ユーザはハードリミットの範囲内でしか値を変更する事ができなくなります。  
例えば、"ulimit -H -c 256"とした場合、一般ユーザは0～256までの間の変更しかできなくなり、256以上に変更するためにはroot権限でハードリミットを設定する必要があります。

## core\_patternを設定する

そもそもコアファイルの出力先がどこに設定されているかを確認する必要があります。  
コアファイルの出力名（出力先）を/proc/sys/kernel/core\_patternで設定できるようになっています。

```
$ cat /proc/sys/kernel/core_pattern
core
```

例えば上記設定であればカレントディレクトリ（プログラムの実行ディレクトリ）に"core"という名前でコアファイルが作成されます。  
/tmp配下に出力したいのならば/tmp/coreなどとすれば/tmp配下に出力されるようになります。  
変更するには、/proc/sys/kernel/core\_patternに直接書き込んでも良いですが、sysctlコマンドで設定が可能です。 また、sysctl -aで設定を確認することも可能です。

```
$ sysctl -a |grep core_pattern
kernel.core_pattern = core
$ sudo sysctl -w kernel.core_pattern=/tmp/core
[sudo] password for user:
kernel.core_pattern = /tmp/core
```

コアファイル名には%指定子によって、情報を付与することが可能です。例えば以下などです。

> man core
> 
> %e　実行ファイル名 (パス名のプレフィックスは含まれない)  
> %p　ダンプされるプロセスの PID; そのプロセスが属している PID 名前空間での PID  
> %t　ダンプ時刻、紀元 (Epoch; 1970-01-01 00:00:00 +0000 (UTC)) からの秒数

"/tmp/core\_%e\_%p\_%t"とした場合、次のような名称で出力されます。

```
$ ls /tmp/core*
/tmp/core_a.out_9181_1601219848
```

指定子は他にもあるため、以下を参照してください。

また、core\_patternにはパイプ(|)でプログラムへの標準入力として、コアダンプを渡す方法も用意されています。  
**コアダンプが出力されないときに、core\_patternを見てみたらパイプで謎のプログラムに渡されていた** みたいなこともあるかもしれません。

13

### Discussion

![](https://static.zenn.studio/images/drawing/discussion.png)

ログインするとコメントできます

13