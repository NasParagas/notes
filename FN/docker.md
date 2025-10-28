
terminalで
---
```bash
# 停止しているコンテナを含め、すべてのコンテナを表示
docker ps -a

# コンテナ削除 
docker rm <container name or id>

# コンテナを起動 
docker start <container name or id> 

# コンテナに入る（bashを起動する例） 
docker exec -it <container name or id> /bin/bash

```


docker run 内で
---
```bash
-v /host/path:/container/path

# ネットワークをホストと共有する
--network host
# GPU使いたい時は入れるといいらしい
--device /dev/dri


```

## dockerfile
### docker build [OPTIONS] PATH | URL | -
Dockerfile とcontextから Dockerイメージを構築するコマンド
指定したパス(URL)に含まれるすべてのファイルを参照できる。
    buildの過程で使用したいファイルはcontextに含める必要がある
```
.
.
├── dockerfiles
│   └── sample
│       ├── copy.txt
│       └── Dockerfile
└── memo
    └── dockerfile_memo.md
```
というディレクトリ構成で、`dockerfiles/sample`内のDockerfileを用いてイメージを作成したい場合、`docker build ./dockerfiles/sample`とする。 
また、Dockerfile内でホストにあるファイルを使用する場合、このcontextに含まれていなくてはならない（ここではcopy.txtがそれにあたる）

FROM ubuntu:24.04
    ベースとなるイメージ
WORKDIR /home/ws
    Dockerfile内で以降に続くRUN CMD ENTRYPOINT　COPY ADD命令の処理時に（コマンドを実行する場所として）使うworking directory。何度も使用可能(相対パスを指定するとその時のwdからの相対パス)
    コンテナを立てた後にターミナルに入ったときのcdにもなる
RUN echo 'sample dockerfile' > sample.txt
    コマンド実行
COPY ./copy.txt ./copy.txt
   　 ホスト側　　コンテナ内
CMD ["/bin/bash"]
    https://docs.docker.jp/engine/reference/builder.html#cmd
