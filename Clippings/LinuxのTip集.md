---
title: "LinuxのTip集"
source: "https://qiita.com/TechnicolorArai/items/06b3ca631faefd90a308"
author:
  - "[[TechnicolorArai]]"
published: 2019-09-10
created: 2025-08-21
description: "コマンド基本+α 省略しない名前 省略しない名前を知っているとちょっとした時に役に立つかも？ cd : change directory pwd : print working directory mv : move cp : copy ls : list ln : ..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から5年以上が経過しています。

## コマンド基本+α

### 省略しない名前

省略しない名前を知っているとちょっとした時に役に立つかも？

- cd: change directory
- pwd: print working directory
- mv: move
- cp: copy
- ls: list
- ln: link

### teeでリダイレクト

リダイレクトと言えば `>` であるがこれだと実行中の標準出力には何もでない。  
teeコマンドを使うと標準出力に表示しつつ `tee hoge.log` でhoge.logにも書き込む [^1] 。  
名前の由来は標準出力を受けて２つの出力先に分けるのがアルファベットのTに似ているから。  
時間がかかって実行状況を見たい `make` などのコマンドに使うと便利

## 権限(Permission)について

`4:読み取り可` 、 `2:書き込み可` 、 `1:実行可` の合計値で表す。  
基本のデフォルトはディレクトリは `755` 、ファイルは `644` 。  
ディレクトリに実行権限(+x(1))がないとディレクトリ内で新しいファイルなどが作れない [^2] 。  
数字は順にユーザー、グループ、それ以外。グループとそれ以外に書き込み権限(+w(2))がないのは `umask` でマスクしているため [^3] 。

## bash起動時シェル

.bash\_profileと.bashrcがある、ubuntuではさらに.bash\_aliaseがある。(Ubuntuではシステムのユーザー用設定が.bashrcにあるためユーザーによる追加用に新たに作られたと思われる)  
読み込み順は.bash\_profile>.bashrc>.bash\_aliase、

.bash\_profile

```bash
# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi
```

のように記述されている(.bash\_aliase)も同様。  
`bash` を打つと`.bashrc` を起動することができる。

## よく使うオプションの意味

### \-P: 物理的位置(/ルート)からの表示

```shell-session
$ pwd -P
```

シンボリックリンクを多用してる時に絶対位置が知りたい時に使う。  
逆は `-L`:論理的位置をたどるだがデフォルトなので使わない。  
他のコマンドでも使えるがあまり使う余地はないと思う。

### \-r, --recursive

再帰的の意味でサブディレクトリを辿る。

### \-f, --force

強制的の意味で一定のエラーなどを無視して進む。  
パッケージ管理システム等で壊れた時に強制的に進めて(治す)ときなどに使う。  
`rm -f` とするとパーミッションがなくても強制的にファイルを削除してしまう。

### \-p, --preserve\[=ATTR\_LIST\]

保存する、留めるの意味。 `-a, --archive` = `dR` はサブディレクトリやリンクなどもすべてできる限りそのまま保存しようとする。  
`-p` は所有者、パーミッション、タイムスタンプなどファイルの情報のみ保存する

### \-v, --verbose

言葉が多い、冗長の意味。時間がかかるコマンドを流す時に状況を把握したい時に使うといい  
`rsync` コマンドで容量が大きいファイルを流す時は `--progress` をつけると何%転送したかまで表示してくれる。

### \-maxdepth N (find), --max-depth N (du)

`find` と `du` でオプションが違うので注意、最小値の `min~` もある。

```shell-session
$ find ./ -maxdepth N ${options}
$ du --max-depth N ./
```

## エイリアス(別名)

`aliases ${別名}="${本名}"` で登録可能、 `unaliases ${別名}"で削除。 emacs` 使いだと `aliases emacs="emacs -nw"` などと登録しておくと便利。  
Ubuntuだと `ls` 系コマンドは以下のエイリアスがあった(多分他でもある)。

ls\_aliases

```bash
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'
```

## リソース管理系コマンド

### df: ディスクの空き容量を表示する

**パーティション** ごとの空き容量を表示する。ディレクトリごとはできない。

### du: ファイル(ディレクトリ)の使用量を表示する

### ps プロセス確認

`ps aux` とするとほぼすべてのプロセスとそれが使うリソースが得られる。  
`a`:端末を持つ全てのプロセスを表示する。 `x`:端末を持たない全てのプロセスを表示する。 `u`:cpu、メモリの使用率などを追加する。である  
その他にも `--sort` 等のコマンドもある [^4]

### top プロセスをリアルタイム表示

リアルタイムの表示なので端末を専有される、終了はq。詳しい使い方はこちら [^5] 。  
CPUやMENは使用率(%)で表示してくれる。

### vmstat

IO、メモリ、CPUの状況など全体的な情報を得る `vmstat ${秒}` 等で監視できる。  
CPUは時間の割合らしい。

## findテクニック

### バックアップファイルの削除

emacsが自動保存する `#*#` 、viの自動保存する `*.swp` 、自動バックアップファイル `*~` 等が作成される。これらをor `-o` で判定して-deleteで削除。  
エイリアス `clean` 等に登録しておくと便利かも

clean

```bash
find \( -name "#*#" -o -name "*~" -o -name "*.swp" \) -delete
```

### リンク切れファイル

`find -xtype l` 、 `-delete` オブションをつけると削除できる。

### \-execオプション

\-execをつけると結果を次のコマンドに引き渡せる場所は `{}` で指定する。  
コマンドの終了はセミコロンですがシェルに解釈されないようにエスケープをつけて `\;`になります。  
プラス `+` にすると `{}` の場所にまとめて展開されます [^8] 。一回のコマンドで終わるので高速になりますが2つめの引数で意味が変わる場合 `cp` などでは事故を起こす可能性があります。

```shell-session
$ find ./ -type f -exec chmod 644 {} \+
$ fine ./ -type d -exec chmod 755 {} \;
$ find ./ -name "*.txt" -exec cp {} \;
cp: './back_up/hoge.txt' と 'back_up/hoge.txt' は同じファイルです # findが複数回走るためコピーされたファイルも列挙される。
$ find ./ -maxdepth 1 -name "*.txt" -exec cp {} \; # -maxdepthでサブディレクトリは除外しているのでエラーはでない。
```

### 時間オプション

## Tipや罠

### unlinkはrmの別名

なので実体をしてしまうと実体から削除される。 `rm` も `-r` を付けないとディレクトリは削除しない、ディレクトリのシンボリックリンクは削除しないのでほぼ同じ。

.bash\_aliase

```bash
function unlink_aliase(){
    if [ -L $1 ]; then
        unlink $1
    else
        echo "$1はシンボリックリンクではありません"
    fi
}

alias unlink="unlink_aliase"
```

このように別名コマンドを作ってそれをエイリアスで上書き登録することで実体ファイルの削除は防げる。

### パーティションの作成や変更

root権限が必要でファイルシステムに直接手を入れるのでデータ破損リスクはかなり高い。  
`fdisk` を使うがコマンドラインで使いづらい [^10] 。GUIだとGParted [^11] がよく使われる。１，２回やったことがあるけれどもいずれもGPartedを使った。

### extundelete: 最終手段

Ubuntuだと `apt` に存在する。あくまでも最終手段、IOが多いとファイルシステム自体から消えて手遅れになるよう。

[2](https://qiita.com/TechnicolorArai/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2FTechnicolorArai%2Fitems%2F06b3ca631faefd90a308&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2FTechnicolorArai%2Fitems%2F06b3ca631faefd90a308&realm=qiita)

[7](https://qiita.com/TechnicolorArai/items/06b3ca631faefd90a308/likers)

いいねしたユーザー一覧へ移動

4

[^1]: [teeとリダイレクト](https://qiita.com/matyapiro31/items/6ba3d1d431d8df3788a5)

[^2]: [実行権限のみ欠けたディレクトリ(rw-)の挙動](https://qiita.com/TechnicolorArai/items/%E5%AE%9F%E8%A1%8C%E6%A8%A9%E9%99%90%E3%81%AE%E3%81%BF%E6%AC%A0%E3%81%91%E3%81%9F%E3%83%87%E3%82%A3%E3%83%AC%E3%82%AF%E3%83%88%E3%83%AA\(rw-\)%E3%81%AE%E6%8C%99%E5%8B%95)

[^3]: [Linuxのファイル権限でつかうumask値のuってなんだろね?](https://qiita.com/papapa_paper/items/5af5d53a02190f37bf82)

[^4]: [【 ps 】コマンド――実行中のプロセスを一覧表示する](http://www.atmarkit.co.jp/ait/articles/1603/28/news022.html)

[^5]: [topコマンドの使い方](https://qiita.com/k0kubun/items/7368c323d90f24a00c2f)

[^6]: [vmstat](https://qiita.com/ryotaro76/items/4e273be640d2716a7b91)

[^7]: [リンク切れのシンボリックリンクを探す方法](https://qiita.com/kawaz/items/ba0e75b6828bd3951e52)

[^8]: [find の -exec optionの末尾につく; と + の違い。](https://qiita.com/ironsand/items/0aed2ffca295706cab69)

[^9]: [unlinkはリンクを削除するだけのコマンドではない](https://qiita.com/Kyou13/items/4a6742bf1cf260d96b29)

[^10]: [fdiskでDisk容量を拡張する](https://qiita.com/2or3/items/501f206a6091a4ce895f)

[^11]: [GPartedでパーティションを操作するには](http://www.atmarkit.co.jp/flinux/rensai/linuxtips/825usegparted.html)

[^12]: [Linuxにて削除(rm)してしまったファイルを復元する【CentOS】](https://qiita.com/satoken0417/items/c1535b7d3322ac0d69ca)