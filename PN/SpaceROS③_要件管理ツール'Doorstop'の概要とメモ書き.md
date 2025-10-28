ひとまず文書として残しておきたいので殴り書き

概要 
---
- SpaceROSで組み込み予定の要件管理(requirement management)ツール
	- まだ組み込まれてもいないしSpaceROSにおけるサンプルみたいなやつもない
- gitを用いたソースコードのバージョン管理と並行して、テキスト形式の要件を管理
 - 各要件は`.yaml`で管理し、それぞれをリンクさせたり、要件のカテゴリごとにツリー構造を作ったりできる
- python上で動作
- 個人で使うというよりはプロジェクト全体でこれを参照しながら進めていく感じで使うモノっぽい

環境
---
### 必要環境
- Python 3.9 以上

### 自環境
- Ubuntu 24.04
- Python 3.12.3
多分Pythonが動く環境なら動きます

setup
---
- Doorstopのインストール
```
pip install doorstop
```

- 動作確認
```
doorstop -V  // Doorstop V3.0.2
```

- ちなみにPythonのモジュールとしても使用可能
```
python
>>> import doorstop
>>> doorstop.__version__  #3.0.2
```

作成されるもの
---
- （公式サンプルですが）作成物はこんな感じ↓（ここのSample: Generated HTMLの部分）
https://github.com/doorstop-dev/doorstop/tree/develop#overview
 - 今作れるものよりちょっと見た目が古いっぽいです
- このREQ019やTUT002などが一つ一つ`.yaml`になっていて、各`yaml`とリンクさせたりテキストで説明を加えたりすることが出来る
	- この例だと、REQで要件を定義してTUT(tutorial)で実装、HLTでそのテストみたいな感じ
- ファイル例↓
  - (REQは`doorstop/reqs`, HLTは`doorstop/cli/tests/docs`, LLTは`doorstop/core/tests/docs`, TUTは`reqs/tutorial`に`.yaml`が格納されている)
 - ファイルのテンプレート自体は後述するコマンドで作ってくれます


REQ003.yml
```yaml
active: true
derived: false
header: |
  Identifiers
level: 2.1
links: []
normative: true
ref: ''
reviewed: 9TcFUzsQWUHhoh5wsqnhL7VRtSqMaIhrCXg7mfIkxKM=
text: |
  Doorstop **shall** provide unique and permanent identifiers to linkable
  sections of text.
```

LLT001.yml
```yaml
active: true
derived: false
header: ''
level: 1.1
links:
- REQ003: 9TcFUzsQWUHhoh5wsqnhL7VRtSqMaIhrCXg7mfIkxKM=
normative: true
ref: Verify an item can be added to a document.
reviewed: 3cWsswJTpxB9WHng7lXLeM4jQW6zbPTZtt1vL1mZFMI=
text: |
  Test adding items:
```

HLT003.yml
```yml
active: true
derived: false
header: ''
level: 1.3
links:
- TUT012: VkQ9YnIdcmX4-9b9WpMwi2Ghqd4Tg5qMn8pqrGGmXas=
- TUT013: 7lqiX5VtcvclAHuCzl7mqVr17Xu1PEQNuquw80DNDSQ=
- TUT016: ZA0D9hs_RcGQvBR3EjlVdAYRp1lonVCBBq29AVU-vPE=
normative: true
ref: test_tutorial_section_3
reviewed: aME0_SIctRosiaSzFJLZSugR1lZfDkgnSXyPiMUQd-A=
text: |
  Tutorial Section 3.0:
```



 使用例
---
git管理されているフォルダに移動しておく
`tree`はディレクトリ構成を表示してくれるやつです

```bash
tree -a -I .git
```

```
.
└── README.md
```

- カテゴリ作成

```bash
doorstop create REQ ./reqs
tree -a -I .git
```

```
.
├── .gitignore
├── README.md
└── reqs
    └── .doorstop.yml
```

(ちなみに.gitignoreは勝手に作成してくれて、このあとhtmlファイルを出力するときに出てくる`public`フォルダをトラックしないようにしてくれます)

- ドキュメント作成

```bash
doorstop add REQ
doorstop add REQ
doorstop add REQ

tree -a -I .git
```

```
.
├── .gitignore
├── README.md
└── reqs
    ├── .doorstop.yml
    ├── REQ001.yml
    ├── REQ002.yml
    └── REQ003.yml
```

- 子カテゴリの作成

```bash
doorstop create TST ./reqs/tests --parent REQ
doorstop add TST
doorstop add TST
tree -a -I .git
```

```
.
├── .gitignore
├── README.md
└── reqs
    ├── .doorstop.yml
    ├── REQ001.yml
    ├── REQ002.yml
    ├── REQ003.yml
    └── tests
        ├── .doorstop.yml
        ├── TST001.yml
        └── TST002.yml
```

- ドキュメント同士をリンクする

```bash
doorstop link TST1 REQ1
```

- HTMLとして出力
```
doorstop publish all ./public/
```
![[_ATTACHMENT/スクリーンショット 2025-07-17 170645.png]]
![[_ATTACHMENT/スクリーンショット 2025-07-17 170812.png]]

なんかサンプルより見た目が新しいし、issueの数とかも表示できそうになってていいね
もちろん実際はここに各要件の概要を書いたりします


- その他コマンド例が記されている公式リンク
https://doorstop.readthedocs.io/en/latest/index.html
これのcommand-line interfaceにまとまってる