---
title: "WSL2環境で動作するDockerを使用してX Window SystemのGoogle Chromeブラウザを動作させる"
source: "https://uepon.hatenadiary.com/entry/2020/12/30/005941"
author:
  - "[[ueponx]]"
published: 2020-12-30
created: 2025-09-02
description: "前にmicro:bitの最新版をRaspberry Piで動作させましたが、Raspberry Piでは結構動作が遅く、起動までにかなりの時間がかかっていました。 本当はWindows10でも試してみようかなと思ったのですが、割とインストールした開発アプリの状況が汚いWindows10しかなかったこともあり純粋に手順としてメモをしておく意味はないかなと考え、今回はWSL2環境でのDockerをインストールしていることを思い出しました。 参考 uepon.hatenadiary.com ただ、問題があってmicro:bitのGitHub版最新開発環境導入にはGUI環境（ブラウザが立ち上がる必要が…"
tags:
  - "clippings"
---
前に `micro:bit` の最新版を `Raspberry Pi` で動作させましたが、 `Raspberry Pi` では結構動作が遅く、起動までにかなりの時間がかかっていました。

本当はWindows10でも試してみようかなと思ったのですが、割とインストールした開発アプリの状況が汚いWindows10しかなかったこともあり純粋に手順としてメモをしておく意味はないかなと考え、今回は `WSL2` 環境での `Docker` をインストールしていることを思い出しました。

**参考**

[uepon.hatenadiary.com](https://uepon.hatenadiary.com/entry/2020/06/14/200918)

ただ、問題があって `micro:bit` の [GitHub](http://d.hatena.ne.jp/keyword/GitHub) 版最新開発環境導入には [GUI](http://d.hatena.ne.jp/keyword/GUI) 環境（ブラウザが立ち上がる必要があります）そのため、 `Docker` のコンテナ環境だけではなく、 `X Window System` のアプリWindows10側でも見えるようにする必要があります。そこで、 `X Window Systemサーバー` の設定も行っていこうと思います。情報では、今後 `WSL2（Windows Subsystem for Linux 2）` が [Windows](http://d.hatena.ne.jp/keyword/Windows) 上の `X Window System` のサーバと連携するようになるという話もありったりしたので、来年以降で [Windows](http://d.hatena.ne.jp/keyword/Windows) だけで別のアプリを入れることなく動作させることもできるかなと思っています。

[news.mynavi.jp](https://news.mynavi.jp/article/20200927-windows10report/)

そんな中、今回はWindows10の `WSL2` 上にインストールを行った `Docker` の環境（以後 `WSL2-Docker` 環境ということにします）を使用して、まずは環境整備を行ってみます。

次回はこの環境を使用して `micro:bit` の最新版を動かしていくことにします。 [micro:bit](http://d.hatena.ne.jp/keyword/micro%3Abit) の最新版の導入に関しては以前のエントリーとほとんど同じなのでそちらを参考にしてください。こちらではコマンドのみを記載するだけにとどめます。

**参考**

[uepon.hatenadiary.com](https://uepon.hatenadiary.com/entry/2020/12/22/235656)

## Windows10にX Window Systemサーバアプリをインストールする

今回の `X Window System` のサーバになるアプリは `VcXsrv Windows X Server` というものを使用します。 他にも同じようなアプリはあると思いますが、有料ではないのでこちらを使うことにしました。 以下からアクセスしてダウンロードを行ってください。

[sourceforge.net](https://sourceforge.net/projects/vcxsrv/)

![f:id:ueponx:20201229162601p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229162601.png)

f:id:ueponx:20201229162601p:plain

ダウンロードしたファイルをクリックしてインストールしていきます。 [インストーラ](http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%C8%A1%BC%A5%E9) がついているものなので導入は割と簡単だと思います。ファイルをダウンロードすると以下のファイルが入手できます。これをダブルクリックすればOKです。

![f:id:ueponx:20201229163155p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229163155.png)

f:id:ueponx:20201229163155p:plain

[インストーラ](http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%C8%A1%BC%A5%E9) が立ち上がると、オプション選択のダイアログが表示されますが、特別なことがなければそのままで【Next ＞】ボタンをクリックします。

![f:id:ueponx:20201229163340p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229163340.png)

f:id:ueponx:20201229163340p:plain

続いては、インストール先のフォルダ指定ですが、こちらもデフォルトで大丈夫です。【Install】ボタンをクリックします。

![f:id:ueponx:20201229163343p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229163343.png)

f:id:ueponx:20201229163343p:plain

するとインストール作業が行われます。細かなアプリケーションの設定はインストール後に行うことができる形となります。

![f:id:ueponx:20201229163346p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229163346.png)

f:id:ueponx:20201229163346p:plain

Completeと表示されたら、【Close】ボタンをクリックして [インストーラ](http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%C8%A1%BC%A5%E9) を終了させます。

![f:id:ueponx:20201229163349p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229163349.png)

f:id:ueponx:20201229163349p:plain

インストールが完了するとデスクトップに `XLaunch` というアイコンが表示されていると思います。これで `VcXsrv Windows X Server` の設定を行っていきます。ここからアプリの常駐化などの設定ができるようになりますが、今回は使用するときのみに使用するスタイルで行きたいと思います。

![f:id:ueponx:20201229163352p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229163352.png)

f:id:ueponx:20201229163352p:plain

アプリを起動すると、Windowの表示モードを尋ねられます。1つのデスクトップウインドウを開いて、その中でアプリを起動するパターンも可能ですし、それぞれのアプリをその他の [Windows](http://d.hatena.ne.jp/keyword/Windows) アプリと同様に開くパターンなどいろいろ設定が可能です。今回はMultiple [Windows](http://d.hatena.ne.jp/keyword/Windows) というモードを選択しています。あとは【次へ】ボタンをクリックします。

![f:id:ueponx:20201229163606p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229163606.png)

f:id:ueponx:20201229163606p:plain

続いては起動のさせ方（常駐）モード選択になります。今回は都度起動とするため、デフォルトのままで大丈夫です。このまま【次へ】ボタンをクリックします。

![f:id:ueponx:20201229163609p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229163609.png)

f:id:ueponx:20201229163609p:plain

最後は追加設定になりますが、ここでは最後の [チェックボックス](http://d.hatena.ne.jp/keyword/%A5%C1%A5%A7%A5%C3%A5%AF%A5%DC%A5%C3%A5%AF%A5%B9) にチェックを入れてください。アクセスコン [トロール](http://d.hatena.ne.jp/keyword/%A5%C8%A5%ED%A1%BC%A5%EB) のものなので、忘れていると使用できないかもしれません。必ず忘れずにチェックしましょう。 【次へ】ボタンをクリックします。

![f:id:ueponx:20201229163613p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229163613.png)

f:id:ueponx:20201229163613p:plain

これで、設定は完了となりますので、【完了】ボタンをクリックしてアプリを起動します。

![f:id:ueponx:20201229163616p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229163616.png)

f:id:ueponx:20201229163616p:plain

【完了】ボタンをクリックすると、 [Windows](http://d.hatena.ne.jp/keyword/Windows) の [ファイアウォール](http://d.hatena.ne.jp/keyword/%A5%D5%A5%A1%A5%A4%A5%A2%A5%A6%A5%A9%A1%BC%A5%EB) のダイアログが表示されるので、設定を確認して【アクセスを許可する】ボタンをクリックしましょう。これで起動されます。

![f:id:ueponx:20201229163619p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229163619.png)

f:id:ueponx:20201229163619p:plain

念の為、起動しているかはタスクバーにアイコンが表示されますので、確認してみてください。

![f:id:ueponx:20201229163622p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229163622.png)

f:id:ueponx:20201229163622p:plain

これで、 `VcXsrv Windows X Server` が動作するようになりました。残るは `Docker` で動作するコンテナを準備していくことになります。

## Dockerで動作するLinuxコンテナの準備をする

基本的には通常使う [Linux](http://d.hatena.ne.jp/keyword/Linux) コンテナを準備します。今回は [Ubuntu](http://d.hatena.ne.jp/keyword/Ubuntu) を使っていきます。 作業としては以下のようになります。

1. [Linux](http://d.hatena.ne.jp/keyword/Linux) コンテナを準備
2. `X Window System` に必要なパッケージのインストール
3. [環境変数](http://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4) を設定

今回は以前インストールしていた環境があるので `Docker for Windows` を使用して作業をしますが、もちろん [Windows](http://d.hatena.ne.jp/keyword/Windows) のCMDや [PowerShell](http://d.hatena.ne.jp/keyword/PowerShell) からでも同様のことはできます。

まずは [Ubuntu](http://d.hatena.ne.jp/keyword/Ubuntu) のイメージからコンテナを起動してみます。

`Docker for Windows` のウインドウを開いて、左側にあるimagesをクリックして

![f:id:ueponx:20201229223059p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229223059.png)

f:id:ueponx:20201229223059p:plain

インストール済みのImageを確認します。 [Ubuntu](http://d.hatena.ne.jp/keyword/Ubuntu) があったのでそれをクリックして、

![f:id:ueponx:20201229223103p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229223103.png)

f:id:ueponx:20201229223103p:plain

右側に【RUN】ボタンが表示されます。これをクリックして新しいコンテナを作成していきます。

![f:id:ueponx:20201229223107p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229223107.png)

f:id:ueponx:20201229223107p:plain

あとはコンテナ名とコンテナとホストのファイルを共有するパスを設定しておきます。これがあるとUSB接続ができないコンテナ側のOSとファイルの共有がスムーズになります。可能な限りやっておくといいと思います。設定が終わったら【Run】ボタンをクリックします。

![f:id:ueponx:20201229222832p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229222832.png)

f:id:ueponx:20201229222832p:plain

コンテナを起動したらコンソールを開いて（ [bash](http://d.hatena.ne.jp/keyword/bash) プロセスにアタッチでもOK）以下を行います。ここでインストールを行っているのは [X11](http://d.hatena.ne.jp/keyword/X11) の環境と [X11](http://d.hatena.ne.jp/keyword/X11) のアプリになります。

※今回は素の [Ubuntu](http://d.hatena.ne.jp/keyword/Ubuntu) のイメージを利用しているのでルートユーザで作業をしています。そのため、インストールなどの作業には `sudo` を行っていませんのでご注意ください。

```sh
# apt update
# apt install xserver-xorg x11-apps
```

![f:id:ueponx:20201229164550p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229164550.png)

f:id:ueponx:20201229164550p:plain

Xのインストール時に [タイムゾーン](http://d.hatena.ne.jp/keyword/%A5%BF%A5%A4%A5%E0%A5%BE%A1%BC%A5%F3) とキーマップを尋ねられるので、

[タイムゾーン](http://d.hatena.ne.jp/keyword/%A5%BF%A5%A4%A5%E0%A5%BE%A1%BC%A5%F3) は【6. Asia】を選択し、

![f:id:ueponx:20201229222653p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229222653.png)

f:id:ueponx:20201229222653p:plain

リージョンは【79. Tokyo】にしておきます。

![f:id:ueponx:20201229222700p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229222700.png)

f:id:ueponx:20201229222700p:plain

キーマップは接続に合わせて設定することになりますが、

![f:id:ueponx:20201229222707p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229222707.png)

f:id:ueponx:20201229222707p:plain

![f:id:ueponx:20201229222715p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229222715.png)

f:id:ueponx:20201229222715p:plain

私は【55. Japanese】と選択して、

![f:id:ueponx:20201229222722p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229222722.png)

f:id:ueponx:20201229222722p:plain

レイアウトは【6. Japanese - Japanese（OADG109A）】を選択しておきました。

![f:id:ueponx:20201229222729p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229222729.png)

f:id:ueponx:20201229222729p:plain

インストールが完了すると `X Window System` が使用可能になります。テスト用のアプリもインストールされているので早速テストを行います。以下のようにX環境の [GUI](http://d.hatena.ne.jp/keyword/GUI) を持つアプリを起動してみます。

```sh
$ xeyes
```

すると以下のようなエラーが発生します。これはどこの環境でウインドウを開くか設定を行っていないためのエラーメッセージとなります。本来であれば、ローカル環境にウインドウを開くことになるのですが、ディスプレイがない設定のため、別途設定が必要になります。

![f:id:ueponx:20201229164547p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229164547.png)

f:id:ueponx:20201229164547p:plain

そのために必要になるのは `DISPLAY` という環境設定になります。この [環境変数](http://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4) はネットワーク越しの設定も可能になるため、今回は表示させるのを `Docker` のホスト側にしたいと思うので、ホスト側の [IPアドレス](http://d.hatena.ne.jp/keyword/IP%A5%A2%A5%C9%A5%EC%A5%B9) を指定し、設定を行います。

注意となりますが、ホスト側の [IPアドレス](http://d.hatena.ne.jp/keyword/IP%A5%A2%A5%C9%A5%EC%A5%B9) は `WSL` で使用される仮想アダプタで設定されている [IPアドレス](http://d.hatena.ne.jp/keyword/IP%A5%A2%A5%C9%A5%EC%A5%B9) になります。 `ipconfigコマンド` で確かめるときには、 イーサーネットアダプター vEthernet（WSL） のものを確認して設定を行う点が注意となります。

今回の例では `172.19.32.1` となっていました。

![f:id:ueponx:20201229182113p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229182113.png)

f:id:ueponx:20201229182113p:plain

この場合の [環境変数](http://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4) の設定方法は以下のようになります。合わせて `xeye` の実行も行ってみます。

```sh
# export DISPLAY=172.19.32.1:0.0
# xeyes &
```

無事に実行が行われました。

![f:id:ueponx:20201229164529p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229164529.png)

f:id:ueponx:20201229164529p:plain

## chromiumブラウザの導入を試す

`Docker` コンテナ上にある [Ubuntu](http://d.hatena.ne.jp/keyword/Ubuntu) には [Webブラウザ](http://d.hatena.ne.jp/keyword/Web%A5%D6%A5%E9%A5%A6%A5%B6) はインストールされていないので、新規にインストールを行います。 `micro:bit` の [GitHub](http://d.hatena.ne.jp/keyword/GitHub) にある最新版はサービス起動すると `Chromiumブラウザ` を起動しようとするのでそちらをインストールしていきます。

ブラウザのインストールは以下のように行います。

```sh
$ apt -y install chromium-browser-l10n
$ apt -y install chromium-browser
```

インストール後、起動すると以下のようなメッセージが表示されます。

```sh
$ chromium-browser
Command '/usr/bin/chromium-browser' requires the chromium snap to be installed.
Please install it with:
snap install chromium
```

`snap` というものでインストールせよという内容です。実は、最近は `Chromiumブラウザ` は `aptパッケージ` ではなく `snap` で配布がされているようです。

![f:id:ueponx:20201229184911p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229184911.png)

f:id:ueponx:20201229184911p:plain

そのため、 `chromium-browser` は `aptコマンド` でインストールを行っても、それはダミーインストール状態になっていて `snap` を使用してインストールを行う必要があります。メッセージどおりにインストールを行うことになるのですが…

```sh
$ snap install chromium-browser
error: cannot communicate with server: Post http://localhost/v2/snaps/chromium: dial unix /run/snapd.socket: connect: no such file or directory
```

![f:id:ueponx:20201229185536p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229185536.png)

f:id:ueponx:20201229185536p:plain

こちらでもエラーが発生します。原因はDocker経由のコンテナでは `systemctrl` が使用できないためです。 `snap` は `snapd` というサービスを経由して実行することになりますが、 `snapd` サービスそのものが起動できないため、 `snap` は動作させることができません。

現状は `chromium-browser` が `snap` 経由でしかインストールできない状態のため、別の手段を考える必要があります。

そこで、純正の [Chrome](http://d.hatena.ne.jp/keyword/Chrome) ブラウザをインストールして試してみたいと思います。（ [micro:bit](http://d.hatena.ne.jp/keyword/micro%3Abit) の最新版は [Firefox](http://d.hatena.ne.jp/keyword/Firefox) ではうまく動作しなかったのでこちらの方法をとっています）

## Chromeブラウザのインストール

以下のコマンドで `Chromeブラウザ` をインストールを行っていきます。デフォルトで `apt` を使ったインストールができないので、以下のようにインストールしていきます。

**参考**

[qiita.com](https://qiita.com/pyon_kiti_jp/items/e6032eb6061a4774aece)

```sh
# apt install -y wget gpg
# wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add -
# sh -c 'echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google.list'
# apt update
# apt install google-chrome-stable -y
```

ここまでくれば、インストール完了です。あとはブラウザを起動するのですが、実行コマンドは `google-chrome` となっているので注意です。ただ、そのまま実行を行うとエラーが表示されます。

```sh
# google-chrome
[8725:8725:1229/191718.909795:ERROR:zygote_host_impl_linux.cc(90)] Running as root without --no-sandbox is not supported. See https://crbug.com/638180.
```

起動時のオプションで `--no-sandbox` をつけないと起動できないようになっているみたいです。以下の様に実行を行うと

```sh
# google-chrome --no-sandbox
```

デフォルトブラウザにするか、クラッシュレポートを [Google](http://d.hatena.ne.jp/keyword/Google) に送るかなどのダイアログが表示され、【OK】ボタンをクリックすれば

![f:id:ueponx:20201229192327p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229192327.png)

f:id:ueponx:20201229192327p:plain

無事に `WSL2-Docker環境` で [GUI](http://d.hatena.ne.jp/keyword/GUI) 環境の `GoogleChrome` ブラウザが起動するようになりました。

![f:id:ueponx:20201229192330p:plain](https://cdn-ak.f.st-hatena.com/images/fotolife/u/ueponx/20201229/20201229192330.png)

f:id:ueponx:20201229192330p:plain

## おわりに

今回はWindows10上で動作する `WSL2-Docker環境` で `X Window system` の [GUI](http://d.hatena.ne.jp/keyword/GUI) アプリを動作させてみました。わりと、簡単に実行できるようになるので、テストという位置づけであればかなり使えるかなと思います。

次回は更に、この環境に `micro:bit` の [GitHub](http://d.hatena.ne.jp/keyword/GitHub) 版最新開発環境をいれて動作できるかを試してみたいと思います。

[« micro:bitのGitHub版最新開発環境をWSL2-D…](https://uepon.hatenadiary.com/entry/2020/12/31/122704) [Chromebook（ASUS Chromebook Flip C101PA… »](https://uepon.hatenadiary.com/entry/2020/12/29/010700)