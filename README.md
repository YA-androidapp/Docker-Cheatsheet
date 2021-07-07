<a id="markdown-docker-cheatsheet" name="docker-cheatsheet"></a>

# Docker-Cheatsheet

Docker コマンド・Docker Compose コマンド

---

<br><br>

<!-- TOC -->

- [Docker-Cheatsheet](#docker-cheatsheet)
  - [バージョン確認](#バージョン確認)
  - [レジストリ（Docker Hub、ACR、ECR）に公開されているイメージをもとにコンテナを実行](#レジストリdocker-hubacrecrに公開されているイメージをもとにコンテナを実行)
    - [イメージを検索](#イメージを検索)
      - [件数を制限（既定値は 25 件）](#件数を制限既定値は-25-件)
      - [条件でフィルタリング](#条件でフィルタリング)
    - [イメージを取得](#イメージを取得)
    - [ローカルにあるイメージを一覧表示](#ローカルにあるイメージを一覧表示)
      - [出力をフォーマット](#出力をフォーマット)
      - [条件でフィルタリング](#条件でフィルタリング-1)
    - [イメージにタグ付け（名前を変える）](#イメージにタグ付け名前を変える)
    - [イメージからコンテナを実行（ run = （ pull ） + create + start ）](#イメージからコンテナを実行-run---pull---create--start-)
      - [アタッチ・デタッチ](#アタッチ・デタッチ)
      - [バックグラウンド実行（ -d ）・ポート転送（ -p ）・ボリューム（ -v ）](#バックグラウンド実行--d-・ポート転送--p-・ボリューム--v-)
    - [ローカルにあるイメージを削除](#ローカルにあるイメージを削除)
      - [使用していないイメージを削除](#使用していないイメージを削除)
    - [ローカルにあるコンテナを削除](#ローカルにあるコンテナを削除)
      - [使用していないコンテナを削除](#使用していないコンテナを削除)
  - [Dockerfile をもとにコンテナを実行](#dockerfile-をもとにコンテナを実行)
    - [Dockerfile からビルド](#dockerfile-からビルド)
  - [実行中のコンテナに変更を加えてプッシュする](#実行中のコンテナに変更を加えてプッシュする)
  - [コンテナの情報を確認](#コンテナの情報を確認)
    - [条件でフィルタリング](#条件でフィルタリング-2)
    - [出力をフォーマット](#出力をフォーマット-1)

<!-- /TOC -->

<br><br>

<a id="markdown-バージョン確認" name="バージョン確認"></a>

## バージョン確認

```bash
$ docker version
$ docker-compose version
```

<br><br>

<a id="markdown-レジストリdocker-hubacrecrに公開されているイメージをもとにコンテナを実行" name="レジストリdocker-hubacrecrに公開されているイメージをもとにコンテナを実行"></a>

## レジストリ（Docker Hub、ACR、ECR）に公開されているイメージをもとにコンテナを実行

<a id="markdown-イメージを検索" name="イメージを検索"></a>

### イメージを検索

<a id="markdown-件数を制限既定値は-25-件" name="件数を制限既定値は-25-件"></a>

#### 件数を制限（既定値は 25 件）

```bash
$ docker search --limit=10 ubuntu
```

<details>
    <summary>Results</summary>

```
NAME                                                      DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
ubuntu                                                    Ubuntu is a Debian-based Linux operating sys…   12466               [OK]
dorowu/ubuntu-desktop-lxde-vnc                            Docker image to provide HTML5 VNC interface …   546                                     [OK]
websphere-liberty                                         WebSphere Liberty multi-architecture images …   274                 [OK]
rastasheep/ubuntu-sshd                                    Dockerized SSH service, built on top of offi…   254                                     [OK]
consol/ubuntu-xfce-vnc                                    Ubuntu container with "headless" VNC session…   241                                     [OK]
ubuntu-upstart                                            Upstart is an event-based replacement for th…   111                 [OK]
1and1internet/ubuntu-16-nginx-php-phpmyadmin-mysql-5      ubuntu-16-nginx-php-phpmyadmin-mysql-5          50                                      [OK]
ubuntu-debootstrap                                        debootstrap --variant=minbase --components=m…   44                  [OK]
1and1internet/ubuntu-16-apache-php-7.0                    ubuntu-16-apache-php-7.0                        13                                      [OK]
1and1internet/ubuntu-16-nginx-php-phpmyadmin-mariadb-10   ubuntu-16-nginx-php-phpmyadmin-mariadb-10       11                                      [OK]
```

</details>

<br><br>

<a id="markdown-条件でフィルタリング" name="条件でフィルタリング"></a>

#### 条件でフィルタリング

```bash
$ docker search --filter is-official=true --filter stars=10000 ubuntu
```

<details>
    <summary>Results</summary>

```
NAME                DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
ubuntu              Ubuntu is a Debian-based Linux operating sys…   12466               [OK]
```

</details>

<br><br>

<a id="markdown-イメージを取得" name="イメージを取得"></a>

### イメージを取得

```bash
$ docker pull ubuntu:latest
```

<details>
    <summary>Results</summary>

```
latest: Pulling from library/ubuntu
c549ccf8d472: Pull complete
Digest: sha256:aba80b77e27148d99c034a987e7da3a287ed455390352663418c0f2ed40417fe
Status: Downloaded newer image for ubuntu:latest
docker.io/library/ubuntu:latest
```

</details>

<br><br>

<a id="markdown-ローカルにあるイメージを一覧表示" name="ローカルにあるイメージを一覧表示"></a>

### ローカルにあるイメージを一覧表示

```bash
$ docker image ls ubuntu:latest
```

<details>
    <summary>Results</summary>

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              9873176a8ff5        2 weeks ago         72.7MB
```

</details>

<br><br>

<a id="markdown-出力をフォーマット" name="出力をフォーマット"></a>

#### 出力をフォーマット

| プレースホルダ | 説明                             |
| -------------- | -------------------------------- |
| .ID            | イメージ ID                      |
| .Repository    | リポジトリ                       |
| .Tag           | イメージのタグ                   |
| .Digest        | イメージのダイジェスト版         |
| .CreatedSince  | イメージを作成してからの経過時間 |
| .CreatedAt     | イメージの作成時間               |
| .Size          | イメージ・ディスクの容量         |

```bash
$ docker image ls --format "{{.ID}}: {{.Repository}}" ubuntu:latest

$ docker image ls --format "table {{.ID}}\t{{.Repository}}" ubuntu:latest
```

<details>
    <summary>Results</summary>

```
9873176a8ff5: ubuntu

IMAGE ID            REPOSITORY
9873176a8ff5        ubuntu
```

</details>

<br><br>

<a id="markdown-条件でフィルタリング-1" name="条件でフィルタリング-1"></a>

#### 条件でフィルタリング

```bash
$ docker images --filter "dangling=true"
```

```bash
$ docker images --filter "label=maintainer=yaand" --filter "since=f666831163b8" --filter "before=e80cc859df7d"
```

<details>
    <summary>Results</summary>

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
<none>              <none>              dc605c5ef9b2        2 months ago        406MB
<none>              <none>              1431ddd51cb2        2 months ago        407MB
<none>              <none>              8ca706e417d7        2 months ago        406MB
```

```
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
docker-laravel-ui_app   latest              1501b7cc243e        2 months ago        574MB
docker-laravel-ui_db    latest              f445a8e4907b        2 months ago        406MB
```

</details>

<br><br>

<a id="markdown-イメージにタグ付け名前を変える" name="イメージにタグ付け名前を変える"></a>

### イメージにタグ付け（名前を変える）

```bash
$ docker tag ubuntu myubuntu:1.0
$ docker image ls myubuntu
```

<details>
    <summary>Results</summary>

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
myubuntu            1.0                 9873176a8ff5        2 weeks ago         72.7MB
```

</details>

<br><br>

<a id="markdown-イメージからコンテナを実行-run---pull---create--start-" name="イメージからコンテナを実行-run---pull---create--start-"></a>

### イメージからコンテナを実行（ run = （ pull ） + create + start ）

実行中のコンテナから抜ける場合

- exit ...コンテナを終了
- Ctrl + p , Ctrl + q ...デタッチ

```bash
$ docker run -it --name mydebian --rm debian:latest /bin/bash
```

<details>
    <summary>Results</summary>

```
Unable to find image 'debian:latest' locally
latest: Pulling from library/debian
0bc3020d05f1: Pull complete
Digest: sha256:33a8231b1ec668c044b583971eea94fff37151de3a1d5a3737b08665300c8a0b
Status: Downloaded newer image for debian:latest
root@b86e36d48765:/# cat /etc/os-release | grep VERSION=
VERSION="10 (buster)"
root@b86e36d48765:/# exit
exit
y@HOSTMACHINE:~$ cat /etc/os-release | grep VERSION=
VERSION="20.04.2 LTS (Focal Fossa)"
```

</details>

<br><br>

<a id="markdown-アタッチ・デタッチ" name="アタッチ・デタッチ"></a>

#### アタッチ・デタッチ

<details>
    <summary>Results</summary>

```bash
# コンテナを起動
$ docker run -it --name mydebian debian:latest /bin/bash

# デタッチ
root@2c2a9ef0c999:/# (Ctrl + p , Ctrl + q)

# コンテナの外からコマンドを実行
$ docker exec mydebian whoami
root

# アタッチ
$ docker attach mydebian

# コンテナを停止し削除
$ docker stop mydebian
$ docker rm mydebian
```

</details>

<br><br>

<a id="markdown-バックグラウンド実行--d-・ポート転送--p-・ボリューム--v-" name="バックグラウンド実行--d-・ポート転送--p-・ボリューム--v-"></a>

#### バックグラウンド実行（ -d ）・ポート転送（ -p ）・ボリューム（ -v ）

<details>
    <summary>Results</summary>

```bash
$ echo "Hi!" > docker/nginx/index.html
$ docker run -it --rm -d -p 8080:80 --name web -v $PWD/docker/nginx/index.html:/usr/share/nginx/html/index.html nginx

$ docker stop web
```

</details>

<br><br>

<a id="markdown-ローカルにあるイメージを削除" name="ローカルにあるイメージを削除"></a>

### ローカルにあるイメージを削除

```bash
$ docker rmi myubuntu:1.0
```

<details>
    <summary>Results</summary>

```
Untagged: myubuntu:1.0
```

</details>

<br><br>

<a id="markdown-使用していないイメージを削除" name="使用していないイメージを削除"></a>

#### 使用していないイメージを削除

```bash
$ docker rmi $(docker images -aqf "dangling=true")
```

<details>
    <summary>Results</summary>

```
Deleted: sha256:dc605c5ef9b2abfad7fa05fb7578e70596c74f8cfffdff6b6fc14cf5865cd177
Deleted: sha256:ab3bc7fe115f84196d7bb7fcdd56be56766cbe52ac7b673196d5a708dda501af
Deleted: sha256:3478a94ee1601c26d7f5c8ff32ed1210dcdec6bd131e19ce6d548c53f89a73a2
```

</details>

<br><br>

<a id="markdown-ローカルにあるコンテナを削除" name="ローカルにあるコンテナを削除"></a>

### ローカルにあるコンテナを削除

```bash
$ docker ps -a

$ docker stop mydebian

$ docker rm mydebian
```

<br><br>

<a id="markdown-使用していないコンテナを削除" name="使用していないコンテナを削除"></a>

#### 使用していないコンテナを削除

```bash
$ docker rm $(docker ps -aqf "status=exited")
```

<br><br>

<a id="markdown-dockerfile-をもとにコンテナを実行" name="dockerfile-をもとにコンテナを実行"></a>

## Dockerfile をもとにコンテナを実行

<a id="markdown-dockerfile-からビルド" name="dockerfile-からビルド"></a>

### Dockerfile からビルド

<details>
    <summary>Preparations</summary>

```bash
$ echo "Hi!" > docker/nginx/index.html
$ cat - << EOF > docker/nginx/Dockerfile
FROM ubuntu
LABEL maintainer="yaand <ya.androidapp@gmail.com>" \
      org.opencontainers.image.authors="yaand <ya.androidapp@gmail.com>" \
      org.opencontainers.image.url="https://github.com/YA-androidapp/Docker-Cheatsheet" \
      org.opencontainers.image.documentation="https://github.com/YA-androidapp/Docker-Cheatsheet" \
      org.opencontainers.image.source="https://github.com/YA-androidapp/Docker-Cheatsheet/blob/main/Dockerfile" \
      org.opencontainers.image.version="1.0.0"

ENV DB_HOST="db" \
    DB_PORT="3306"

RUN echo $DB_HOST $DB_PORT

ARG DEBUG=True
RUN echo $DEBUG

RUN apt-get update && apt-get install -y nginx
ADD index.html /var/www/html/
EXPOSE 80
ENTRYPOINT ["nginx", "-g", "daemon off;"]
EOF
```

</details>

<br>

```bash
$ docker build -t myweb:latest --build-arg DEBUG=False docker/nginx/
$ docker run -d -p 8080:80 --name myweb myweb:latest
$ docker ps
```

<br>

<details>
    <summary>Results</summary>

```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
3d4df3994a87        myweb:latest        "nginx -g 'daemon of…"   2 minutes ago       Up 2 minutes        0.0.0.0:8080->80/tcp   myweb
```

</details>

<br><br>

<a id="markdown-実行中のコンテナに変更を加えてプッシュする" name="実行中のコンテナに変更を加えてプッシュする"></a>

## 実行中のコンテナに変更を加えてプッシュする

```bash
# コンテナを起動
$ docker run -it --name mydebian debian:latest /bin/bash

# コンテナに変更を加える
root@2c2a9ef0c999:/# touch my.txt

# デタッチ
root@2c2a9ef0c999:/# (Ctrl + p , Ctrl + q)

# コミット
$ docker commit mydebian yaand/mydeb:latest
sha256:36d96b596a9c7489052e8a0da43bd82cab12af587f8ef068813ce54916e1c1f6
$ docker image ls yaand/mydeb
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
yaand/mydeb         latest              36d96b596a9c        7 minutes ago       114MB

# タグ付け
$ docker tag 36d96b596a9c yaand/mydeb2:1.0
$ docker image ls yaand/mydeb
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
yaand/mydeb         latest              36d96b596a9c        8 minutes ago       114MB
$ docker image ls yaand/mydeb2
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
yaand/mydeb2        1.0                 36d96b596a9c        8 minutes ago       114MB

# Docker Hub にプッシュする
$ docker login
$ docker push yaand/mydeb:latest
```

<br><br>

<a id="markdown-コンテナの情報を確認" name="コンテナの情報を確認"></a>

## コンテナの情報を確認

```bash
$ docker run -it -d --rm --name mydebian debian:latest
$ docker run -it --name mydebian2 debian:latest
root@26c3e16c970e:/# exit
exit

$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
c44ebce078ea        debian:latest       "bash"              52 seconds ago      Up 50 seconds                           mydebian

# すべてのコンテナを出力
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
26c3e16c970e        debian:latest       "bash"              19 seconds ago      Exited (0) 16 seconds ago                       mydebian2
c44ebce078ea        debian:latest       "bash"              55 seconds ago      Up 53 seconds                                   mydebian

# コンテナIDのみ出力
$ docker ps -q
c44ebce078ea
```

<br><br>

<a id="markdown-条件でフィルタリング-2" name="条件でフィルタリング-2"></a>

### 条件でフィルタリング

| 項目                                                  | 説明                                           |
| ----------------------------------------------------- | ---------------------------------------------- |
| id=\<ID\>                                             | コンテナ ID                                    |
| label=\<key\> or label=\<key\>=\<value\>              | ラベル                                         |
| name=\<文字列\>                                       | コンテナ名                                     |
| exited=\<整数\>                                       | 終了コードを <整数> で指定                     |
| status                                                | created, restarting, running, paused or exited |
| ancestor                                              | 特定のイメージや子孫から作成されたコンテナ     |
| before                                                | 指定したコンテナよりも前に作成したコンテナ     |
| since                                                 | 指定したコンテナよりも後に作成したコンテナ     |
| volume=\(\<ボリューム名\> or \<マウント・ポイント\>\) | コンテナがマウントしているボリューム           |
| network=\(\<ネットワーク ID\> or \<ネットワーク名\>\) | コンテナが接続しているネットワーク             |

```bash
$ docker ps -a --filter 'exited=0' --filter 'name=mydebian2'
```

<details>
    <summary>Results</summary>

```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
26c3e16c970e        debian:latest       "bash"              9 minutes ago       Exited (0) 9 minutes ago                       mydebian2
```

</details>

<br><br>

<a id="markdown-出力をフォーマット-1" name="出力をフォーマット-1"></a>

### 出力をフォーマット

| プレースホルダ | 説明                                     |
| -------------- | ---------------------------------------- |
| .ID            | コンテナ ID                              |
| .Image         | イメージ ID                              |
| .Command       | クォートされたコマンド                   |
| .CreatedAt     | コンテナが作成された時間                 |
| .RunningFor    | コンテナが起動してからの時間             |
| .Ports         | 公開しているポート                       |
| .Status        | コンテナのステータス                     |
| .Size          | コンテナのディスク容量                   |
| .Names         | コンテナ名                               |
| .Labels        | コンテナに割り当てられている全てのラベル |
| .Label         | コンテナに割り当てられた特定のラベル     |

```bash
$ docker ps -a --format "table {{.Names}} {{.Status}}"
```

<details>
    <summary>Results</summary>

```
NAMES STATUS
mydebian2 Exited (0) 17 minutes ago
mydebian Up 17 minutes
```

</details>

<br><br>

---

Copyright (c) 2021 YA-androidapp(https://github.com/YA-androidapp) All rights reserved.
