---
title: "colcon buildのオプション一覧"
source: "https://qiita.com/porizou1/items/d39b0d5829b746570fcd"
author:
  - "[[porizou1]]"
published: 2024-01-03
created: 2025-08-26
description: "colcon buildのオプションをcolcon build --help の出力をもとにChatGPTで翻訳して整理したもの。 基本的なオプション --build-base BUILD_BASE ビルドディレクトリのベースパスを指定します（デフォルトはbuild..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=)

この記事は最終更新日から1年以上が経過しています。

colcon buildのオプションをcolcon build --help  
の出力をもとにChatGPTで翻訳して整理したもの。

## 基本的なオプション

- **\--build-base BUILD\_BASE**
	- ビルドディレクトリのベースパスを指定します（デフォルトはbuild）。
	- **実行例:**
		```bash
		colcon build --build-base ./custom_build_dir
		```
- **\--install-base INSTALL\_BASE**
	- インストールプレフィックスのベースパスを指定します（デフォルトはinstall）。
	- **実行例:**
		```bash
		colcon build --install-base ./custom_install_dir
		```
- **\--merge-install**
	- すべてのインストールプレフィックスを単一の場所に統合します。
	- **実行例:**
		```bash
		colcon build --merge-install
		```
- **\--symlink-install**
	- 可能な場所でファイルのコピーの代わりにシンボリックリンクを使用します。
	- **実行例:**
		```bash
		colcon build --symlink-install
		```
- **\--test-result-base TEST\_RESULT\_BASE**
	- テスト結果のベースパスを指定します（デフォルトは--build-base）。
	- **実行例:**
		```bash
		colcon build --test-result-base ./test_results
		```
- **\--continue-on-error**
	- パッケージのビルドが失敗しても、他のパッケージのビルドを続行します（失敗したパッケージに依存するパッケージはスキップされます）。
	- **実行例:**
		```bash
		colcon build --continue-on-error
		```

## Executorの引数

- **\--executor {parallel,sequential}**
	- すべてのパッケージを処理するための実行方法を指定します（デフォルトはparallel）。
	- **実行例:**
		```bash
		colcon build --executor sequential
		```
- **\--parallel-workers NUMBER**
	- 並列処理するパッケージの最大数を指定します、または'0'で制限なし（デフォルトは20）。
	- **実行例:**
		```bash
		colcon build --parallel-workers 8
		```

## イベントハンドラ引数

- **\--event-handlers \[name1+ \[name2-...\]\]**
	- イベントハンドラを有効（+）または無効（-）にします。
	- **実行例:**
		```bash
		colcon build --event-handlers console_direct+
		```

## ディスカバリ引数

- **\--ignore-user-meta**
	- ユーザ設定ディレクトリ内の\*.metaファイルを無視します。
	- **実行例:**
		```bash
		colcon build --ignore-user-meta
		```
- **\--metas \[PATH...\]**
	- `colcon.meta` ファイルが含まれているディレクトリ、または同じメタ情報を含む任意のファイルのパスを指定します。
	- **実行例:**
		```bash
		colcon build --metas ./my_meta_folder
		```
- **\--base-paths \[PATH...\]**
	- パッケージを再帰的に探すためのベースパスを指定します。
	- **実行例:**
		```bash
		colcon build --base-paths ./src
		```

## パッケージ選択引数

- **\--packages-ignore \[PKG\_NAME...\]**
	- 特定のパッケージを無視します。
	- **実行例:**
		```bash
		colcon build --packages-ignore package_to_ignore
		```
- **\--packages-select \[PKG\_NAME...\]**
	- 特定のパッケージのみを処理します。
	- **実行例:**
		```bash
		colcon build --packages-select package_to_build
		```
- **\--packages-skip \[PKG\_NAME...\]**
	- 特定のパッケージをスキップします。
	- **実行例:**
		```bash
		colcon build --packages-skip package_to_skip
		```
- **\--packages-up-to \[PKG\_NAME...\]**
	- 特定のパッケージとその依存パッケージのみを処理します。
	- **実行例:**
		```bash
		colcon build --packages-up-to package_to_limit
		```

## 'cmake' パッケージ用の引数

- **\--cmake-args \[...\]**
	- CMakeプロジェクトに引数を渡します。
	- **実行例:**
		```bash
		colcon build --cmake-args -DCUSTOM_OPTION=ON
		```
- **\--cmake-clean-cache**
	- ビルド前にCMakeキャッシュを削除します。
	- **実行例:**
		```bash
		colcon build --cmake-clean-cache
		```

## 'ros.catkin' パッケージ用の引数

- **\--catkin-cmake-args \[...\]**
	- 'catkin'パッケージに引数を渡します。
	- **実行例:**
		```bash
		colcon build --catkin-cmake-args -DCATKIN_OPTION=ON
		```
- **\--catkin-skip-building-tests**
	- 'colcon test'を後で実行しない場合、'catkin'パッケージの'tests'ターゲットのビルドをスキップします。
	- **実行例:**
		```bash
		colcon build --catkin-skip-building-tests
		```

[0](https://qiita.com/porizou1/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)

[新規登録](https://qiita.com/signup?callback_action=login_or_signup&redirect_to=%2Fporizou1%2Fitems%2Fd39b0d5829b746570fcd&realm=qiita) [ログイン](https://qiita.com/login?callback_action=login_or_signup&redirect_to=%2Fporizou1%2Fitems%2Fd39b0d5829b746570fcd&realm=qiita)

[9](https://qiita.com/porizou1/items/d39b0d5829b746570fcd/likers)

いいねしたユーザー一覧へ移動

3