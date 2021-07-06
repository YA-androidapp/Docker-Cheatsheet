# Docker-Cheatsheet

Docker コマンド・Docker Compose コマンド

---

<br><br>

## バージョン確認

```bash
$ docker version
$ docker-compose version
```

<br><br>

## レジストリ（Docker Hub、ACR、ECR）に公開されているイメージをもとにコンテナを実行

### イメージを検索

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

### イメージからコンテナを実行（ run = （ pull ） + create + start ）

```bash
$ docker run -it --name mydebian debian:latest /bin/bash
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

---

Copyright (c) 2021 YA-androidapp(https://github.com/YA-androidapp) All rights reserved.
