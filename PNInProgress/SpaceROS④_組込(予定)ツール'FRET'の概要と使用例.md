
概要
--
https://github.com/NASA-SW-VnV/fret
上記リンク先のIntroductionより
>FRET is a framework for the elicitation, specification, formalization, and analysis of system requirements. It enables users to specify unambiguous requirements using restricted English sentences while providing multiple representations to enhance clarity and validation of semantics. These representations include natural language descriptions, formal mathematical logics, and visual diagrams, allowing users with different expertise to review and understand the precise meaning of each requirement.

ということで、単に要件管理のツールというよりは**要件を定義する際の言語を制限することで、曖昧さを持たせないようにする**ことを目的とされていそう
実際の中身はモノを見ながらの方がわかりやすいので下の方で
ちなみに開発はNASAです


環境
--
### 必要環境
- Ubuntu 
- NodeJS v16.16.x - v20.19.x
- Python v3.10.x - v3.13.x

### 自環境
- Ubuntu 24.04

setup
--
NodeJSのインストールは以下のリンクに従います
https://github.com/NASA-SW-VnV/fret/blob/master/fret-electron/docs/_media/installingFRET/installationInstructions.md#how-to-install-nodejs

その後は以下のように進めます
```bash
git clone https://github.com/NASA-SW-VnV/fret.git
cd fret/fret-electron
npm run fret-install
```

※(docker環境なら)
`fret/fret-electron/package.json`を以下のように書き換えないとうまく動作しない
```json
"start": "cross-env NODE_ENV=production electron ./app/ "
```

```json
"start": "cross-env NODE_ENV=production electron ./app/ --no-sandbox"
```
`--no-sandbox`ってなに？
dockerコンテナ内ではlinuxカーネルの機能が制限されている
chromiumのsandboxはその制限されている機能を使わないと正常に機能しない
それを作らなくていいよ～とするのが `--no-sandbox`
ちなみにsandbixはプロセスをシステムから隔離してセキュリティを担保するものなので、オフにするのはセキュリティ上良くはない
みたいな感じだと思っています。間違ってたら教えてください
参考:
geminiちゃん
https://docs.docker.com/engine/security/
https://github.com/pedroslopez/whatsapp-web.js/issues/149
https://qiita.com/muddydixon/items/d2982ab0846002bf3ea8

(フォルダがなかったら)
spaceros-user/Documents を作成
ここに`.db`を格納するフォルダが作られます

その後
```bash
xhost +local:root
npm start
```
これでも警告は出るかもしれませんが、一応起動するはずです。（警告も致命的ではないはず）
![[_ATTACHMENT/Pasted image 20250717185833.png]]


いろいろやると
![[_ATTACHMENT/Pasted image 20250717190023.png]]