# Docker駆け出し

# コマンド

## `docker run`

### `--rm`オプション
```bash
docker run --name rmtest --rm hello-world
```
`--rm`オプションを付けると、コンテナ落とした際にコンテナ消してくれるので、コンテナ消し忘れを防げる
 
### `-it`オプション
```bash
docker run -it --name mycentos centos:8 /bin/bash
```
centos8を起動し、起動したコンテナに入った状態で且つシェルはbashを使う状態になる

## `docker exec`
### コンテナに入らずコマンド実行
```bash
docker exec mycentos cat /etc/redhat-release
```

## `docker rm`

- 停止中のコンテナを削除
- `-f`オプションで起動中コンテナも強制削除
- コンテナのデータも消えるので注意

## `docker rmi`

- 起動中のコンテナのイメージは削除できない
- Dockerイメージは依存関係があるので、ベースイメージは削除できない

## `docker build`

- Dockerfileからイメージを作成

## `docker cp`

- コンテナとホストマシンでファイルのやりとりを行うコピーコマンド
- ログファイルや設定ファイルの取り出し or 入力で使う

### ホスト→コンテナへ
```bash
docker cp {host_file_path} {container_name}:{container_path}
```


### コンテナ→ホストへ
```bash
docker cp {container_name}:{container_file_path} {host_path}
```

## `docker logs`

- Dockerコンテナのログ出力
  - `-f`オプションでリアルタイムログ
- 原因不明のコンテナ停止
- アクセスログなど
- `tail -f`と似たようなもの

## `docker inspect`

- Dockerの詳細情報出力
- 普段は見ない詳細情報を見れる
  - トラブル時などに使うことがある
- `Mounts`、`Config`、`Env`、`Cmd`あたり見ること多い
- `NetworkSettings`の`IPAddress`(コンテナのIPアドレス)も見ることある

## `docker pull`

- Dockerイメージのダウンロード
- pullの後ろに*プライベートイメージレジストリを付けると、DockerHub以外からダウンロードできる
```bash
docker pull {image_name}:{tag} [registry_url]
```
ex)
```bash
docker pull php:8.0
```

## `docker commit`

- コンテナをイメージ化
```bash
docker commit {container_name} DocherHubID/{image_name}:{tag}
```

# Dockerコンテナのストレージ

## Dockerボリュームの永続化問題

### 【演習】nginxにオリジナルのHTMLを表示させてみる

- とりあえずnginxのデフォルトページ表示
```bash
docker run --name mynginx -p 8080:80 nginx:1.27
```

- オリジナルHTMLを表示してみる
```bash
docker run -v ./src/:/usr/share/nginx/html --name mynginx -p 8080:80 nginx:1.27
```
# Dockerfile

## Dockerfileとは

- Dockerイメージをコード化したもの
- Dockerfileを読むと構成がわかる
- オリジナルイメージを作成できる
- 設定ファイルなども変更できる

## Dockerfileのベストプラクティス

[Docker-docs-ja](https://docs.docker.jp/develop/develop-images/dockerfile_best-practices.html)

### 一時的なコンテナを作成
- Dockerfileで定義したイメージによって意生成するコンテナは、可能な限り一時的（ephemeral）であるべき
- 一時的が意味するものは、コンテナとは停止および破棄可能であり、その後も極めて最小限のセットアップと設定により、再構築や置き換えが可能

### 不要なパッケージのインストールを避ける
- 余計なものは入れずに極力シンプルにする
- コンテナごとに１つのプロセスだけ実行するのが基本の考え方
  - Webアプリケーションを作ろうとしたら、下記のように複数のプロセスが必要になる
    - Webアプリケーションサーバー
    - DBサーバー
    - キャッシュサーバー
    - ・・・
  - 一つのコンテナに上記等をまとめて入れるのではなく、複数のコンテナで若手立ち上げておいて、コンテナを連携

### レイヤー
レイヤーが増えるほどファイルが大きくなり、読みづらくなり、ビルドにも時間がかかるので、極力レイヤーの数は最小限にする

### 複数量の引数

- 自分以外が読む時のことも考えて、複数行で記述する
- 一行が長くなる場合、`\`で改行入れて見やすくできる

```Dockerfile
RUN apt-get update && apt-get install -y \
  bzr \
  cvs \
  git \
  mercurial \
  subversion \
  && rm -rf /var/lib/apt/lists/*
```

## RUNとCMDでコマンドを実行する

- RUNもCMDもコマンドを実行する命令
  - この２つの違いは実行タイミングが異なる
- RUN：DockerfileからDockerイメージにビルドするときに１回だけ実行するコマンド
- CMD：Dockerfileができたイメージをコンテナ化する時に実行されるコマンド

### CMD
- CMDコマンドわかりにくいのでもうちょっと説明
  - [公式ドキュメント](https://docs.docker.jp/engine/reference/builder.html#cmd)
- CMDには３つの形式がある
  - 今回のレクチャーではexec形式だけ覚えておく
  - exec形式は、必ずコマンドをダブルクォーテーションで括ることに注意
    - シングルクォーテーションではコマンドとして認識されない
    - ex) `CMD ["executable", "param1", "param2"]`