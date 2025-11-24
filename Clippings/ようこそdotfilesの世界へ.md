---
title: "ようこそdotfilesの世界へ"
source: "https://qiita.com/yutkat/items/c6c7584d9795799ee164"
author:
  - "[[yutkat]]"
published: 2019-12-01
created: 2025-11-23
description: "はじめに 少し前から話題になっているが、日本の労働生産性はG7で最も低いらしい。 日本生産性本部資料より https://www.jpc-net.jp/intl_comparison/intl_comparison_2018_press.pdf 日本は人口減少に突入して..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

## Qiitaにログインして、便利な機能を使ってみませんか？

あなたにマッチした記事をお届けします

便利な情報をあとから読み返せます

[ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fyutkat%2Fitems%2Fc6c7584d9795799ee164&realm=qiita) [新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fyutkat%2Fitems%2Fc6c7584d9795799ee164&realm=qiita)

この記事は最終更新日から3年以上が経過しています。

## はじめに

少し前から話題になっているが、日本の労働生産性はG7で最も低いらしい。  
[![seisansei.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/107220/a4c10a1b-173a-d040-a695-222ac74a2fd3.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F107220%2Fa4c10a1b-173a-d040-a695-222ac74a2fd3.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=49c9b73438ab02957109b9a6275663ac)  
日本生産性本部資料より [https://www.jpc-net.jp/intl\_comparison/intl\_comparison\_2018\_press.pdf](https://www.jpc-net.jp/intl_comparison/intl_comparison_2018_press.pdf)

日本は人口減少に突入していることもあって、「作業の効率化」や「自動化・省力化」をいうフレーズをあらゆる業種で聞くようになった。

ITエンジニアは、あらゆる職業の中でも最も効率化、自動化をして生産性を高められるといっても過言ではないだろう。プログラマの三大美徳(「怠惰」「短気」「傲慢」)にもあるように、同じことを何度もやらない、楽をするためにがんばるという生産性を意識した感性が重要視されているからだ。  
生産性を高めることで、勉強する時間が作れたり、新しいことを経験したりするなどしてさらにスキルアップができ、さらに生産性が上がるという好循環を作り出すことができる。

この好循環を作り出すために必要な原動力、そうそれが ***dotfiles*** だ!

## dotfilesとは？

dotfilesとは、ホームディレクトリに置いてあるドット(.)から始まる設定ファイル(.bashrcとか)を管理しているリポジトリのことである。シェルやエディタの設定からアプリケーションの設定まで幅広いものが置かれている。  
dotfilesはGitなどでバージョン管理されており、GitHubで公開されていることが多い。(GitHub以前はオレの.bashrcを晒すぜ！などのブログタイトルでブログにそのまま貼り付けられている形式が多かった)

dotfilesを極めることで、どこで作業していても自分の環境を瞬時にサクっと作り出すことができ(ハッカーみたいでかっこいい)、いきなり快適な作業環境で、ストレスなく、生産性Maxの状態で開発をスタートできる。  
**まさにこれからの時代の働き方である。これからの働き方にはdotfilesが必要なのだ！**

## 特におすすめな人

ここでどのような人がdotfilesを作るべきか具体的な対象を列挙してみよう。

- いろんなサーバー上で開発する人
- dockerとかの中で開発するのが好きな人
- 仕事柄新しいPCで作業することが多い人
- もっと効率的に作業できないかを考えている人
- 自分の環境じゃなきゃ生産性ガタ落ちする人
- パフォーマンス中毒な人
- 数ヶ月に1度そろそろOS入れ替えよかっなーってなる人
- すぐ新しいパソコンが欲しくなる人
- あれ？前も設定したと思うあれどうやるんだっけ？？ってなる人
- 今日は働く気がしないけど仕事してるフリはしなくちゃいけなくて困ってる人
- 育成ゲームが好きだったり、育てがいのあるペットが欲しい人
- 次こそはミスしないぞ！と固く誓っている人
- 怠惰を極めすぎていて同じこと２回以上したくない人
- なんかハッカーっぽいおれおれ環境で周りにドヤ顔したい人
- dotfilerに「そんな非生産的な環境でよく仕事できてるね」って煽られたくない人
- 暇な人

***まぁつまり全エンジニアが対象だ！！！***

## 私の環境

あとの説明部分で環境による差異が発生する可能性が高いため、一応私の使っている環境を書いておく。

- ArchLinux
- Desktop(i3)
- zsh, tmux
- vim(neovim)

ちなみに私のdotfilesはこちら [https://github.com/yutkat/dotfiles](https://github.com/yutkat/dotfiles)  
外部プラグインに依存していないシンプル版はこちら [https://github.com/yutkat/dotfiles-mini](https://github.com/yutkat/dotfiles-mini)

## 今すぐにでも始められる最小dotfilesの作り方

ここからはdotfilesってのはわかったけど、具体的なはじめ方がわからないという人のために *今すぐ* にでも始められる簡単なやり方を書いていく。以下のとおり作業すれば、君も今日からdotfilerの仲間入りだ！

1. GitHubにてdotfilesという名前のリポジトリを作成する(とりあえず、READMEだけでも作る)
2. 自分のPCにdotfilesのリポジトリを `git clone` してくる(ここでは$HOME/dotfilesに置いたと想定する)
3. とりあえず、使っているシェルの設定(~/.bashrc)をdotfiles以下にコピーする
4. 元の~/.bashrcを退避させる `mkdir backup && mv ~/.bashrc backup`
5. dotfilesの.bashrcにシンボリックリンクを貼る `ln -s ~/dotfiles/.bashrc ~`
6. あとはdotfiles以下でコミットしてpushしたら出来上がり
7. 同じように管理したいものdotfilesディレクトリにコピーしてシンボリックリンクを貼っていく

## dotfilesの使い方

### 更新時

1. 作業をしているとき、ふと「なんか生産性落ちてる気がする。。。効率を上げられないかな」と考える・感じる  
	(たとえば、ターミナルでコマンド履歴から前のコマンドを上矢印で探してくるのめんどくさいなーとか)
2. ネットで調べて効率が上がる設定を見つけ出す!  
	(fzfを使い履歴を簡単に検索する方法がヒット。なるほど、履歴をファジーサーチできるようにしてそれをショートカットとして登録すればいいのか！)
3. 該当の方法を設定ファイルに入力する  
	(この場合だとシェルの設定、.bashrc,.zshrcを更新する)
4. シンボリックリンクとなっているはずなので、gitが差分を検知しているはず
5. git add, commit, pushを行いremoteに反映する

### 構築時

1. GitHubから `git clone https://github.com/xxx/dotfiles.git` でdotfilesを持ってくる
2. インストーラー(後述)を起動する
3. はい、もう自分の環境が構築できている！

#### 注意点

複数人でアカウントを共有している環境で作業している場合は他の人の許可を得た上でdotfilesを展開したほうがいい。(他の人がいきなり環境が変わってびっくりしてしまうため)

自分だけで使いたい場合は `export HOME=/your/home/dir` と自分だけのディレクトリにHOMEを変更してそこで使えば他の人への影響を防ぐことができる。(ただしシェルにログインするたび毎回 `export` を実行する必要がある)

あまりガチガチに設定したくない、他の人とも共有したいという環境用に別のdotfilesを作っておくのもおすすめ。たとえば、誰もが使いたいようなgitのステータス情報やコマンドの実行時刻の情報をシェルのプロンプトに出したものを共有用のdotfilesにしておくなど。  
[https://github.com/yutkat/dotfiles-mini](https://github.com/yutkat/dotfiles-mini)

## 何を管理すればよい？

### dotfilesでよく管理されているものの例

- シェルの設定(~/.bashrc, ~/.zshrc, ~/.config/fish, etc)
- エディタの設定(~/.vimrc, ~/.emacs.el, ~/.config/Code/User, etc)
- ターミナルの設定(~/.config/alacritty, etc)
- CLIツールの設定(~/.tmux.conf, ~/.gdbinit, etc)
- アプリの設定(~/.i3, ~/.autokey, etc)
- gitの設定(~/.gitignore\_global, etc)

### dotfilesで管理しにくいもの

- 環境によって動的に内容が変わるもの(シェルスクリプトで自動生成するようにする)
- シンボリックリンクを読み取ってくれないもの、ファイルで上書きしてしまうもの(~/.config/mimeapps.list etc)
- プログラムやプラグインやアプリケーション(管理してもよいが機能更新に追従できなくなるため、パッケージマネージャーやプラグインマネージャーを使ってできるだけ最新版をダウンロードするようにしたほうがいい)

### \[注意\]dotfilesで絶対に管理してはいけないもの

- 認証情報が入っているディレクトリは ***絶対に*** dotfilesにて管理しないこと
	- .ssh,.aws等

## dotfilesのディレクトリレイアウトの例

私が実際にdotfilesでどのようなものを管理しているか抜粋して記載しておく。  
※ 簡略化のために実際の私のdotfilesと変更している箇所がある

```shell
dotfiles
├── .bin/ # dotfilesのインストールスクリプトとよく使う自作スクリプトを格納する
│   ├── make_conkyrc.sh*
│   ├── arch-extra-setup.sh* # メインで使っているOSの追加のインストールスクリプト
│   └── install.sh* # dotfilesのインストールスクリプト
├── .bin.local/ # .localがつくものはその環境でしか使わないものを格納する
│   └── .gitkeep
├── .config/
│   ├── alacritty/ # ターミナルの設定ファイル
│   │   └── alacritty.yml
│   ├── Code/ # みんなが大好きなVSCodeの設定ファイル
│   │   ├── _install.sh* # だけどインストールした直後に設定が作られるためdotfilesとしては管理しずらいのでインストールスクリプトを用意しておく
│   │   └── User/
│   │       ├── keybindings.json
│   │       └── settings.json
│   ├── nvim -> ../.vim/
│   └── systemd/ # ユーザー用のsystemdの常駐設定も管理しておくと便利
│       └── user/
│           └── i3-cycle-focus.service
├── .gdbinit
├── .github/
│   └── workflows/
│       └── test.yml
├── .gitignore
├── .gitignore_global # よく誤コミットしてしまう \`*~\` や \`.vscode\` や \`.DS_Store\` を記載する
├── .gitconfig_shared # よく使うaliasやcolor設定、\`excludesfile = ~/.gitignore_global\` を記載する
├── .i3/ # デスクトップ環境の設定ファイル
│   ├── config
│   └── i3blocks.conf
├── .ideavimrc
├── LICENSE
├── README.md
├── .tmux/
│   ├── conf/ # 行数が増えた設定ファイルで外部ファイルを読み込めるものは設定ごとに分割したほうが見通しがよくなる
│   │   ├── base.tmux*
│   │   ├── bind.tmux*
│   │   ├── color.tmux*
│   │   └── plugin.tmux*
│   └── log/
├── .tmux.conf
├── .vim/
│   ├── after/
│   │   ├── ftplugin/
│   │   └── .gitkeep
│   ├── backup/
│   ├── coc-settings.json
│   ├── ftdetect/
│   ├── ftplugin/
│   ├── info/
│   │   └── .gitkeep
│   ├── init.vim -> ../.vimrc
│   ├── rc/
│   │   ├── autocmd.vim
│   │   ├── base.nvim
│   │   ├── base.vim
│   │   ├── coloring.vim
│   │   ├── command.vim
│   │   ├── display.vim
│   │   ├── init.vim
│   │   ├── keyconfig.vim
│   │   ├── mappings.vim
│   │   ├── pluginconfig.vim
│   │   ├── pluginlist.vim
│   │   └── statusline.vim
│   ├── sessions/
│   │   └── .gitkeep
│   ├── snippets/
│   │   └── .gitkeep
│   ├── swap/ # 使用しているときに一時ファイルができるディレクトリは後述のgitignoreの設定で差分が発生しないように管理する
│   │   └── .gitkeep
│   ├── template/
│   │   └── .gitkeep
│   └── undo/
│       └── .gitkeep
├── .vimrc
├── .xinitrc
├── .Xmodmap
├── .xprofile*
├── .Xresources
├── .xsessionrc
├── .zfunc/
│   └── .gitkeep
├── .zsh/
│   ├── completion/
│   │   └── .gitkeep
│   ├── dircolors
│   └── rc/
│       ├── alias.zsh
│       ├── base.zsh
│       ├── bindkey.zsh
│       ├── commandconfig.zsh
│       ├── completion.zsh
│       ├── function.zsh
│       ├── option.zsh
│       ├── pluginconfig/
│       ├── pluginlist.zsh
│       └── prompt.zsh
├── .zshenv
└── .zshrc
```

- .vimrcや.zshrcなどは長くなったら(個人的には1500行超えるときつくなってくる)役割ごとでファイルを分けたほうが読みやすくなる
- .conkyrc(PCによってコア数等を変更したい),.i3/config(インストールされているパッケージ構成によって動的に生成したい)などのものは、生成するためのシェルスクリプトを.bin/で管理する。
- 個別の環境で設定を変更したい場合に備えて設定ファイル内でローカルの設定ファイルがあれば読み込むようにしておく(.zshrc.local,.vimrc.local)

## dotfiles用の.gitignoreの設定

エディタやシェルにプラグインを入れるなどし始めるとdotfiles管理下のディレクトリに勝手にファイルが作られることが多々ある。他のファイルが追加されても更新があるように見えないようにするため.gitignoreをホワイトリスト形式にする。

## シンプルなdotfilesインストーラーを作ってみよう

dotfilesを作ったが毎回手でシンボリックリンクを貼るのは面倒である。だからdotfiles用のインストーラーを作ったほうがいい。  
インストーラーには、専用ツールやansibleを使う方法、Python,Rubyなどのスクリプト言語を使う方法などいろいろやり方は存在するが、今回は一番簡単なシェルスクリプトを使う方法を紹介しようと思う。

.bin/install.sh

```shell
# !/usr/bin/env bash
set -ue

helpmsg() {
  command echo "Usage: $0 [--help | -h]" 0>&2
  command echo ""
}

link_to_homedir() {
  command echo "backup old dotfiles..."
  if [ ! -d "$HOME/.dotbackup" ];then
    command echo "$HOME/.dotbackup not found. Auto Make it"
    command mkdir "$HOME/.dotbackup"
  fi

  local script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd -P)"
  local dotdir=$(dirname ${script_dir})
  if [[ "$HOME" != "$dotdir" ]];then
    for f in $dotdir/.??*; do
      [[ \`basename $f\` == ".git" ]] && continue
      if [[ -L "$HOME/\`basename $f\`" ]];then
        command rm -f "$HOME/\`basename $f\`"
      fi
      if [[ -e "$HOME/\`basename $f\`" ]];then
        command mv "$HOME/\`basename $f\`" "$HOME/.dotbackup"
      fi
      command ln -snf $f $HOME
    done
  else
    command echo "same install src dest"
  fi
}

while [ $# -gt 0 ];do
  case ${1} in
    --debug|-d)
      set -uex
      ;;
    --help|-h)
      helpmsg
      exit 1
      ;;
    *)
      ;;
  esac
  shift
done

link_to_homedir
git config --global include.path "~/.gitconfig_shared"
command echo -e "\e[1;36m Install completed!!!! \e[m"
```

## dotfilesでもCIしてみよう

dotfilesだってCIで質を高めたい！インストールしているパッケージ名称が変わったり、インストールしたプラグインの依存しているプログラムに変更があったり、インストーラーを変更したらピュアな環境でインストールが失敗するようになったり、更新してもしなくても壊れることは多い。

今回はGitHub Actionsで複数のOS(Linux Distribution)へのインストールできるか確認をする。  
他にもLintや各種プラグインのインストールチェックを設定しても効果的かと思われる。

dotfiles/.github/workflows/check.yml

```yml
name: CI

on: [push]

jobs:
  ubuntu:
    runs-on: ubuntu-latest
    container: ubuntu:latest
    steps:
      - uses: actions/checkout@v1
      - name: Install required packages
        run: apt-get update && apt-get install -y git sudo
      - name: Install dotfiles
        run: .bin/install.sh install

  centos:
    runs-on: ubuntu-latest
    container: centos:latest
    steps:
      - uses: actions/checkout@v1
      - name: Install required packages
        run: yum install -y git sudo
      - name: Install dotfiles
        run: .bin/install.sh install

  alpine:
    runs-on: ubuntu-latest
    container: alpine:latest
    steps:
      - uses: actions/checkout@v1
      - name: Install required packages
        run: apk add git sudo bash
      - name: Install dotfiles
        run: .bin/install.sh install

  arch:
    runs-on: ubuntu-latest
    container: archlinux/base:latest
    steps:
      - uses: actions/checkout@v1
      - name: Update packages
        run: pacman -Syu --noconfirm
      - name: Install required packages
        run: pacman -S --noconfirm git sudo
      - name: Install dotfiles
        run: .bin/install.sh install

  lint:
    runs-on: ubuntu-latest
    container: ubuntu:latest
    steps:
      - uses: actions/checkout@v1
      - name: Update packages
        run: apt-get update
      - name: Install packages for install repository
        run: apt-get install -y git sudo software-properties-common
      - name: Install required repository
        run: apt-add-repository "deb http://archive.ubuntu.com/ubuntu trusty-backports main restricted universe"
      - name: Install required packages
        run: apt-get update; apt-get install -y shellcheck
      - name: Execute shellcheck
        run: test $(shellcheck  -f gcc ~/.zshrc ~/.zsh/rc/* | grep -v "SC1036\|SC1056\|SC1070\|SC1072\|SC1073\|SC1083\|SC2034\|SC2139\|SC2148\|SC1090\|SC1117\|SC2206\|SC1009\|SC2016\|SC2046\|SC2154" | tee -a /dev/stderr | wc -l) -le 1
```

## dotfilesを成長させていくためには？

dotfilesは作って終わりではない。しっかり育てて(メンテナンスし続けて)こそ価値がある。  
他の人の作業の様子や設定を見たり、新しいツールを試したりすることであなたのdotfilesはどんどん進化していくだろう。

一般的なdotfiles関連の情報収集ができるサイトは

- GitHub topicのdotfiles
- Redditの [r/doftiles](https://www.reddit.com/r/dotfiles/), [r/commandline](https://www.reddit.com/r/commandline/), 自分の使っているシェル (例: [r/zsh](https://www.reddit.com/r/zsh/))
- vim-jpのSlack dotfilesチャンネル [https://vim-jp.org/docs/chat.html](https://vim-jp.org/docs/chat.html) に招待リンクがあります (今のところ専用のコミュニティスペースがない+人がどれくらい集まるかもわからないため、またvimmerにdotfilesが多かったため間借りしています。。。)

あたりだろう。

ただもっとも大事なのは、日々の作業の中でもう少し改善できないかな？、未来で楽をするために今できることはないかなと考え続ける心だと思う。

## さいごに

dotfilesは今すぐ始められます！  
日々育てていくものなので、はやく始めたほうがそれだけ生産性が上がりますよ。

***dotfilesと一緒に日々成長していきましょう！！！***

[13](https://qiita.com/yutkat/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fyutkat%2Fitems%2Fc6c7584d9795799ee164&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fyutkat%2Fitems%2Fc6c7584d9795799ee164&realm=qiita)

## Qiita Advent Calendar 開催！

[1285](https://qiita.com/yutkat/items/c6c7584d9795799ee164/likers)

いいねしたユーザー一覧へ移動

977