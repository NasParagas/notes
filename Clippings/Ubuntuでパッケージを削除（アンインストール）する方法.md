---
title: "Ubuntuでパッケージを削除（アンインストール）する方法"
source: "https://vpslife.server-memo.net/package_uninstall/"
author:
  - "[[tamohiko]]"
published: 2023-08-08
created: 2025-08-26
description: "Ubuntuにインストールされているパッケージを削除（アンインストール）する方法です。パッケージの削除方法Ubuntuでパッケージを削除するには、下記どちらかのコマンドを使用します。 apt remove パッケージ ※設定ファイルは削除さ..."
tags:
  - "clippings"
---
PR

Ubuntuにインストールされているパッケージを削除（アンインストール）する方法です。

## パッケージの削除方法

Ubuntuでパッケージを削除するには、下記どちらかのコマンドを使用します。

- apt remove パッケージ ※設定ファイルは削除されない
- apt purge パッケージ ※設定ファイルも削除

どちらもパッケージを削除しますが、それぞれ動作に違いがあるのでそれぞのコマンドについて説明をしていきます。

## apt remove

apt removeは指定したパッケージと依存関係にあるパッケージを削除（アンインストール）します。

なお、パッケージの設定ファイルについては削除されません。

```
sudo apt remove パッケージ
```

コマンドを実行すると途中で「Do you want to continue? \[Y/n\] 」と削除処理を続けて良いかの確認メッセージが表示されます。

削除を行う場合は「y」を入力して「Enter」キーを押下するか、初期状態で「Y」が設定されているのでそのまま「Enter」キーを押下します。

削除をやめる場合は「n」を入力し「Enter」キーを押下します。

### パッケージ削除例

apt removeを使用して、メール転送エージェント（MTA）であるpostfixの削除を行ってみます。

```
$ sudo apt remove postfix
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following package was automatically installed and is no longer required:
  ssl-cert
Use 'sudo apt autoremove' to remove it.
The following packages will be REMOVED:
  postfix
0 upgraded, 0 newly installed, 1 to remove and 17 not upgraded.
After this operation, 4,180 kB disk space will be freed.
Do you want to continue? [Y/n] y
(Reading database ... 111170 files and directories currently installed.)
Removing postfix (3.6.4-1ubuntu1.1) ...
Processing triggers for man-db (2.10.2-1) ...
```

posfixの設定ファイルがある/etc/postfixディレクトリ内容を表示させると、パッケージを削除後も設定ファイルが残っていることが確認できます。

```
$ ls -l /etc/postfix/
total 104
-rw-r--r-- 1 root root    60 Aug  8 13:49 dynamicmaps.cf
-rw-r--r-- 1 root root 27120 Aug  8 13:49 main.cf.proto
lrwxrwxrwx 1 root root    31 Aug  8 13:49 makedefs.out -> /usr/share/postfix/makedefs.out
-rw-r--r-- 1 root root  6524 Aug  8 13:49 master.cf
-rw-r--r-- 1 root root  6524 Aug  8 13:49 master.cf.proto
-rw-r--r-- 1 root root 10268 Apr 10 20:35 postfix-files
-rwxr-xr-x 1 root root 11031 Apr 10 20:35 postfix-script
-rwxr-xr-x 1 root root 29872 Apr 10 20:35 post-install
```

## apt purge

apt purgeコマンドは、指定したパッケージと依存関係にあるパッケージを削除（アンインストール）し、パッケージの設定ファイルも一緒に削除します。

```
sudo apt purge パッケージ
```

コマンドを実行すると、途中で「Do you want to continue? \[Y/n\] 」と削除処理を続けて良いかの確認メッセージが表示されます。

パッケージの削除を行う場合は「y」を入力して「Enter」キーを押下するか、初期状態で「Y」が設定されているのでそのまま「Enter」キーを押下します。

削除をやめる場合は「n」を入力し「Enter」キーを押下します。

### パッケージ削除例

apt purgeを使用して、メール転送エージェント（MTA）であるpostfixの削除を行ってみます。

```
$ sudo apt purge postfix
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following package was automatically installed and is no longer required:
  ssl-cert
Use 'sudo apt autoremove' to remove it.
The following packages will be REMOVED:
  postfix*
0 upgraded, 0 newly installed, 1 to remove and 17 not upgraded.
After this operation, 4,180 kB disk space will be freed.
Do you want to continue? [Y/n] y
(Reading database ... 111170 files and directories currently installed.)
Removing postfix (3.6.4-1ubuntu1.1) ...
Processing triggers for man-db (2.10.2-1) ...
(Reading database ... 110991 files and directories currently installed.)
Purging configuration files for postfix (3.6.4-1ubuntu1.1) ...
Processing triggers for ufw (0.36.1-4build1) ...
Processing triggers for rsyslog (8.2112.0-2ubuntu2.2) ...
```

postfixの設定ファイルがある/etc/postfix/の内容を表示させようとすると、ディレクトリごと削除されていることが確認できます。

```
$ ls -l /etc/postfix/
ls: cannot access '/etc/postfix/': No such file or directory
```

タイトルとURLをコピーしました