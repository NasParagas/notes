vscodeでgit管理下の自動監視？を切る方法
```
{
  "git.autorefresh": false
}
```

- `PATH`環境変数
	- 実行するプログラムを検索する際に参照するディレクトリの一覧
	- カレントディレクトリは含まれない
		- `ls`とかしたときに想定していないコマンドが実行されるのを防ぐため
		- だから毎回`./build.sh`とかしてるのか

## 6\. Luna Paint - Image Editor

[![Luna Paint - Image Editor](https://raw.githubusercontent.com/Yamazaki-Kei7/qiita-sync/main/images/vscode%E3%81%A7%E5%BF%AB%E9%81%A9%E3%81%ABmarkdown%E3%83%89%E3%82%AD%E3%83%A5%E3%83%A1%E3%83%B3%E3%83%88%E3%82%92%E4%BD%9C%E6%88%90%E3%81%99%E3%82%8B/LunaPaint-ImageEditor.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fraw.githubusercontent.com%2FYamazaki-Kei7%2Fqiita-sync%2Fmain%2Fimages%2Fvscode%25E3%2581%25A7%25E5%25BF%25AB%25E9%2581%25A9%25E3%2581%25ABmarkdown%25E3%2583%2589%25E3%2582%25AD%25E3%2583%25A5%25E3%2583%25A1%25E3%2583%25B3%25E3%2583%2588%25E3%2582%2592%25E4%25BD%259C%25E6%2588%2590%25E3%2581%2599%25E3%2582%258B%2FLunaPaint-ImageEditor.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=37a1cdc40444acdd8e83ecf12f55d7b8)

Markdown ドキュメントに画像を挿入するときに、画像の編集が必要な場合があります。Luna Paint - Image Editor は、画像の編集ができる拡張機能です。

Visual Studio Code を開いて、左側のアイコンから拡張機能を検索し、Luna Paint - Image Editor をインストールしてください。

Luna Paint - Image Editor をインストールすると、以下の手順で画像の編集ができます。

1. 画像を右クリックして、 `ファイルを開くアプリケーションの選択...`を選択
2. `Luna Paint Image Editor` を選択
3. 画像を編集し、 `Ctrl + S` で保存

テキストを追加したり、図形を追加したり、画像のリサイズやトリミングなどができます。

[![Luna Paint - Edit Image](https://raw.githubusercontent.com/Yamazaki-Kei7/qiita-sync/main/images/vscode%E3%81%A7%E5%BF%AB%E9%81%A9%E3%81%ABmarkdown%E3%83%89%E3%82%AD%E3%83%A5%E3%83%A1%E3%83%B3%E3%83%88%E3%82%92%E4%BD%9C%E6%88%90%E3%81%99%E3%82%8B/LunaPaint.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fraw.githubusercontent.com%2FYamazaki-Kei7%2Fqiita-sync%2Fmain%2Fimages%2Fvscode%25E3%2581%25A7%25E5%25BF%25AB%25E9%2581%25A9%25E3%2581%25ABmarkdown%25E3%2583%2589%25E3%2582%25AD%25E3%2583%25A5%25E3%2583%25A1%25E3%2583%25B3%25E3%2583%2588%25E3%2582%2592%25E4%25BD%259C%25E6%2588%2590%25E3%2581%2599%25E3%2582%258B%2FLunaPaint.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=1561037b46c6bf7d7ecf95c15f5e7b40)

## 7\. Draw.io Integration

[![Draw.io Integration](https://raw.githubusercontent.com/Yamazaki-Kei7/qiita-sync/main/images/vscode%E3%81%A7%E5%BF%AB%E9%81%A9%E3%81%ABmarkdown%E3%83%89%E3%82%AD%E3%83%A5%E3%83%A1%E3%83%B3%E3%83%88%E3%82%92%E4%BD%9C%E6%88%90%E3%81%99%E3%82%8B/Draw.ioIntegration.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fraw.githubusercontent.com%2FYamazaki-Kei7%2Fqiita-sync%2Fmain%2Fimages%2Fvscode%25E3%2581%25A7%25E5%25BF%25AB%25E9%2581%25A9%25E3%2581%25ABmarkdown%25E3%2583%2589%25E3%2582%25AD%25E3%2583%25A5%25E3%2583%25A1%25E3%2583%25B3%25E3%2583%2588%25E3%2582%2592%25E4%25BD%259C%25E6%2588%2590%25E3%2581%2599%25E3%2582%258B%2FDraw.ioIntegration.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=723ec22c091feb7992937127ad623db7)

Draw.io は、図を作成するためのツールです。  
そして、Draw.io Integration は、Visual Studio Code で Draw.io を利用するための拡張機能です。

Visual Studio Code を開いて、左側のアイコンから拡張機能を検索し、Draw.io Integration をインストールしてください。

Draw.io Integration をインストールすると、以下の手順で図を作成することができます。

1. 画像を保存しているフォルダに、 `*.drawio.svg` ファイルを作成
2. 作成したファイルを開き、編集する。
3. 編集したファイルのパスを Markdown ドキュメントに記述

下図のように、Draw.io で作図します。  
[![Draw.io](https://raw.githubusercontent.com/Yamazaki-Kei7/qiita-sync/main/images/vscode%E3%81%A7%E5%BF%AB%E9%81%A9%E3%81%ABmarkdown%E3%83%89%E3%82%AD%E3%83%A5%E3%83%A1%E3%83%B3%E3%83%88%E3%82%92%E4%BD%9C%E6%88%90%E3%81%99%E3%82%8B/Drawio.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fraw.githubusercontent.com%2FYamazaki-Kei7%2Fqiita-sync%2Fmain%2Fimages%2Fvscode%25E3%2581%25A7%25E5%25BF%25AB%25E9%2581%25A9%25E3%2581%25ABmarkdown%25E3%2583%2589%25E3%2582%25AD%25E3%2583%25A5%25E3%2583%25A1%25E3%2583%25B3%25E3%2583%2588%25E3%2582%2592%25E4%25BD%259C%25E6%2588%2590%25E3%2581%2599%25E3%2582%258B%2FDrawio.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=f031ebf6a2a9d30faa94b7403e67819d)

  

編集したファイルのパスを Markdown ドキュメントに記述します。

  

```markdown
<img src="./images/vscodeで快適にmarkdownドキュメントを作成する/hogehoge.drawio.svg" alt="Draw.io" width="400"/>
```

  

そうすることで、以下のような図が Markdown ドキュメントに挿入されます。  
[![Draw.io](https://qiita-user-contents.imgix.net/https%3A%2F%2Fraw.githubusercontent.com%2FYamazaki-Kei7%2Fqiita-sync%2Fmain%2Fimages%2Fvscode%25E3%2581%25A7%25E5%25BF%25AB%25E9%2581%25A9%25E3%2581%25ABmarkdown%25E3%2583%2589%25E3%2582%25AD%25E3%2583%25A5%25E3%2583%25A1%25E3%2583%25B3%25E3%2583%2588%25E3%2582%2592%25E4%25BD%259C%25E6%2588%2590%25E3%2581%2599%25E3%2582%258B%2Fhogehoge.drawio.svg?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a48bb868c8186031f20781a9943d68b7)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fraw.githubusercontent.com%2FYamazaki-Kei7%2Fqiita-sync%2Fmain%2Fimages%2Fvscode%25E3%2581%25A7%25E5%25BF%25AB%25E9%2581%25A9%25E3%2581%25ABmarkdown%25E3%2583%2589%25E3%2582%25AD%25E3%2583%25A5%25E3%2583%25A1%25E3%2583%25B3%25E3%2583%2588%25E3%2582%2592%25E4%25BD%259C%25E6%2588%2590%25E3%2581%2599%25E3%2582%258B%2Fhogehoge.drawio.svg?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a48bb868c8186031f20781a9943d68b7)

  

図の修正が必要になれば、 `*.drawio.svg` ファイルを編集することで、自動で Markdown ドキュメントに反映されます。

また、 `*.drawio.png` というファイルを作成することで、PNG 形式の画像を作成することもできます。  
個人的には、Lua Paint よりも Draw.io を使用したほうが画像の加工がし易いです。

ちなみに、下の画像は以下の手順で作成しました。

1. `hogehoge.drawio.png` というファイルを新規作成
2. 作成したファイルを開き、コピーした画像を貼り付け
3. 吹き出し図を挿入し、テキストを追加
4. 画像を保存
5. 作成したファイルのパスを Markdown ドキュメントに記述

[![Draw.io](https://raw.githubusercontent.com/Yamazaki-Kei7/qiita-sync/main/images/vscode%E3%81%A7%E5%BF%AB%E9%81%A9%E3%81%ABmarkdown%E3%83%89%E3%82%AD%E3%83%A5%E3%83%A1%E3%83%B3%E3%83%88%E3%82%92%E4%BD%9C%E6%88%90%E3%81%99%E3%82%8B/hogehoge.drawio.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fraw.githubusercontent.com%2FYamazaki-Kei7%2Fqiita-sync%2Fmain%2Fimages%2Fvscode%25E3%2581%25A7%25E5%25BF%25AB%25E9%2581%25A9%25E3%2581%25ABmarkdown%25E3%2583%2589%25E3%2582%25AD%25E3%2583%25A5%25E3%2583%25A1%25E3%2583%25B3%25E3%2583%2588%25E3%2582%2592%25E4%25BD%259C%25E6%2588%2590%25E3%2581%2599%25E3%2582%258B%2Fhogehoge.drawio.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=dfa9cc346038503f9066318a3472a9f1)

