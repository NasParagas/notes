## chmod
```bash
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```
#### `chmod` コマンドの引数

|引数|内容|解説|
|---|---|---|
|**`a+r`**|**a**ll users (すべてのユーザー) に **r**ead (読み取り) 権限を **+** (追加) する。|これにより、一般ユーザーを含むシステムの全ユーザーがこのキーファイルを読み取れるようになります。APTがパッケージの署名を検証する際に、このファイルにアクセスする必要があるためです|
## gpg
```bash
sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

|オプション|内容|解説|
|---|---|---|
|**`--dearmor`**|ASCII armor形式のデータをバイナリ形式に変換します。|ダウンロードしたGPGキーはテキスト形式（ASCII armor）なので、APTが直接利用できるバイナリ形式に変換する必要があります。|
|**`-o`**|--output|出力先のファイルを指定します。ここでは `/etc/apt/keyrings/docker.gpg` というファイルに保存しています。|

# mkdir
ディレクトリを作成するコマンド
- `-p`
	- 親ディレクトリの有無にかかわらず作成する