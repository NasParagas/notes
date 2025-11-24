---
title: "カレントディレクトリとは何か  (Unix系OSの場合)"
source: "https://zenn.dev/qnighy/articles/c6653eaf49a21a"
author:
  - "[[Zenn]]"
published: 2025-11-01
created: 2025-11-24
description:
tags:
  - "clippings"
---
144

34[tech](https://zenn.dev/tech-or-idea)

## 結論

カレントディレクトリ (以下cwd) は、以下の2つの組み合わせで管理される、プロセス状態の構成要素です。

- **物理的なcwd**: ディレクトリそのものを指すポインタ。カーネル側で管理される。
- **論理的なcwd**: ディレクトリパス。 `PWD` という名前の環境変数を経由して、ユーザーランドで管理される。

## ポイント

一般論として、ファイルを特定する方法には、ファイルへのポインタを保持する方法とパスを保持する方法の2通りがあります。この2つの方法は一長一短で、様々な場面で振る舞いが異なります。

- ファイルポインタを保持する方法は、同じファイルシステム内でのファイルの **移動やリネーム** を追跡します。一方、パスを保持する場合は移動やリネームを追跡せず、かわりに元の場所に新しくファイルが作られればそちらを参照するようになります。
- ファイルポインタを保持する方法は、ファイルが削除されてもファイルの実体を参照し続けます。一方、パスを保持する場合は削除されたファイルにはアクセスできなくなります。
- ファイルポインタを保持する方法は、 **経路情報** を保持しません。つまり、symlinkなどで同じファイルを複数の方法で参照できるとき、どの方法でアクセスしたかはファイルポインタには記録されていません。いっぽう、パスはこうした情報を保持します。

したがって、cwdにも同様の問題が発生します。

Unix系OSでは、両方の方法を組み合わせることで両者の利点を得ることを目指しています。

## どのように組み合わせているのか

### シェル以外の場合

open(2) などの各種ファイルシステムAPIは、相対パスを物理的なcwdに基づいて解決します。また、 chdir(2) や getcwd(3) は物理的なcwdに対する操作です。多くのプログラミング言語はこうしたAPIに対する通常のバインディングを提供するため、 **Rubyなどのシェル以外のプログラミング言語** を使って書いたプログラムはたいてい、 **物理的なcwd** に対して動作します。

### シェルの場合

いっぽう、 **シェルは異なる動作をします** 。 [POSIX.1-2024のcd (1)](https://pubs.opengroup.org/onlinepubs/9799919799/utilities/cd.html) の説明を要約すると、以下のようになります。 (CDPATH に関する動作等は省略しています)

- `cd` には **物理モード** である `cd -P` と **論理モード** である `cd -L` があり、デフォルトでは論理モードで動作する。
- 物理モードでは以下のように振る舞う。
	1. 物理的なcwdを、物理的なcwdからの相対で更新する。
	2. 論理的なcwdを、物理的なcwdから逆引きして更新する。
- 論理モードでは以下のように振る舞う。
	1. 論理的なcwdを、論理的なcwdからの相対で更新する。
	2. 物理的なcwdを、論理的なcwdを解決することで更新する。 (ただし、解決に失敗したら論理的なcwdの変更もリバートする)

また、 `pwd` にも `pwd -P` と `pwd -L` があり、 `pwd -L` がデフォルトです。

つまり、シェルは **論理的なcwd** に基づいて pwd/cd を行います。ただし、その他の操作は依然として **物理的なcwd** に基づくため、その挙動はハイブリッド的になります。

### シェル起動時の挙動

加えて、 [PWD 変数の項](https://pubs.opengroup.org/onlinepubs/9799919799/utilities/V3_chap02.html#tag_19_05_03) ではざっくり以下のような規定があります。

- シェルの起動時、物理的なcwdと論理的なcwdが一致しなければ、 **物理的なcwd** からパスを逆引きして論理的なcwdを上書きする。

この規定は、cd以外の場面ではシェルが **物理的なcwdを優先して論理的なcwdを同期する** ことを示唆しています (実際にbashやzshで実験してみるとシェルの起動時以外にもこうした挙動になることが確認できます)。

## まとめ

- Unix系OSにおいて、カレントディレクトリは物理/論理の二重の状態として管理されている。
- 論理的なカレントディレクトリはシェルで利用される。
- 物理的なカレントディレクトリと論理的なカレントディレクトリは特定の場面で同期される。

144

34

### Discussion

具体例だとこういう感じですよね

```
user@user-pc:/run/user/1000/cd_check$ mkdir dir1 dir2
user@user-pc:/run/user/1000/cd_check$ ln -s ../dir2 dir1/
user@user-pc:/run/user/1000/cd_check$ find . -exec ls -lAFd {} +
drwxrwxr-x 4 user user 80 11月  2 15:38 ./
drwxrwxr-x 2 user user 60 11月  2 15:39 ./dir1/
lrwxrwxrwx 1 user user  7 11月  2 15:39 ./dir1/dir2 -> ../dir2/
drwxrwxr-x 2 user user 40 11月  2 15:38 ./dir2/
user@user-pc:/run/user/1000/cd_check$ cd -P dir1/dir2/..
user@user-pc:/run/user/1000/cd_check$ cd -L dir1/dir2/..
user@user-pc:/run/user/1000/cd_check/dir1$ cd ..
user@user-pc:/run/user/1000/cd_check$ cd dir1/dir2/..
user@user-pc:/run/user/1000/cd_check/dir1$ cd ..
user@user-pc:/run/user/1000/cd_check$ ls -lAF dir1/dir2/..
合計 0
drwxrwxr-x 2 user user 60 11月  2 15:39 dir1/
drwxrwxr-x 2 user user 40 11月  2 15:38 dir2/
user@user-pc:/run/user/1000/cd_check$
```

cwdまたは親ディレクトリがリネームされた場合、削除された場合、アクセス権限がない場合なども重要ですね。

面白い記事ありがとうございます！  
実際に実験してみましたのですが、期待と異なる結果が得られたので共有です。

bashやzshで以下のように `pwd -P` と `pwd -L` の出力を壊すことができるのですが、実行コマンドのcwdは壊れていませんでした。

```shell
% docker run -it --rm --platform linux/arm64 ubuntu:25.10 bash
root@097f48f55dc5:/# mkdir -p a/b; cd a/b; rm -rf ../../a
root@097f48f55dc5:/a/b# pwd -L
/a/b
root@097f48f55dc5:/a/b# pwd -P
pwd: error retrieving current directory: getcwd: cannot access parent directories: No such file or directory
root@097f48f55dc5:/a/b# pwd -L
pwd: error retrieving current directory: getcwd: cannot access parent directories: No such file or directory
root@097f48f55dc5:/a/b# cd ../..
chdir: error retrieving current directory: getcwd: cannot access parent directories: No such file or directory
root@097f48f55dc5:../..# pwd -P
.
root@097f48f55dc5:../..# pwd -L
../..
root@097f48f55dc5:../..# ls -a
.   .dockerenv  boot  etc   lib    mnt  proc  run   srv  tmp  var
..  bin         dev   home  media  opt  root  sbin  sys  usr
root@097f48f55dc5:../..# ls -l /proc/1/cwd
lrwxrwxrwx 1 root root 0 Nov  5 14:24 /proc/1/cwd -> /
```

これは、シェル上の物理と論理のcwdに加え、カーネル上のプロセスに対するcwdという3種類があることになりますかね？

ログインするとコメントできます

144

34