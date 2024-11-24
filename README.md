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

