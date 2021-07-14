<a id="markdown-docker-cheatsheet" name="docker-cheatsheet"></a>

# Docker-Cheatsheet

Dockerfile

---

<br><br>

<!-- TOC -->

- [Docker-Cheatsheet](#docker-cheatsheet)
  - [ビルドコマンド](#ビルドコマンド)
  - [記述書式](#記述書式)
  - [.dockerignore](#dockerignore)
    - [ワイルドカード](#ワイルドカード)
    - [除外](#除外)
  - [ARG](#arg)
    - [マルチステージビルド](#マルチステージビルド)
  - [FROM](#from)
    - [FROM で ARG の設定値を使いたい場合](#from-で-arg-の設定値を使いたい場合)
    - [マルチステージビルド](#マルチステージビルド-1)
  - [LABEL](#label)
  - [ENV](#env)
  - [USER](#user)
  - [WORKDIR](#workdir)
  - [ADD と COPY](#add-と-copy)
  - [SHELL](#shell)
  - [RUN](#run)
    - [apt の例](#apt-の例)
    - [コマンドの形式](#コマンドの形式)
  - [CMD と ENTRYPOINT](#cmd-と-entrypoint)
    - [コマンドを実行時に置き換える](#コマンドを実行時に置き換える)
    - [コマンドのオプションを実行時に置き換える](#コマンドのオプションを実行時に置き換える)
  - [参考文献](#参考文献)

<!-- /TOC -->

<br><br>

<a id="markdown-ビルドコマンド" name="ビルドコマンド"></a>

## ビルドコマンド

```bash
# カレントディレクトリをコンテキストに指定
$ docker build .

# Dockerfileのパスを指定
$ docker build -f ./Dockerfile.dev .
```

<a id="markdown-記述書式" name="記述書式"></a>

## 記述書式

```dockerfile
# Comment
INSTRUCTION arguments
```

<br><br>

<a id="markdown-dockerignore" name="dockerignore"></a>

## .dockerignore

- `.dockerignore` はビルドコンテキストの直下に置く
- `.dockerignore` に記述するパスは相対パスで指定する
- `Dockerfile` や `.dockerignore` ファイルも除外できる（ `ADD` や `COPY` ではコピーされなくなるが、ビルドの際にデーモンへ送信される）

<br>

```dockerignore
# ビルドコンテキスト直下の .DS_Store のみ
.DS_Store

# ビルドコンテキスト直下の logs ファイルまたは logs/ ディレクトリ
logs
```

<br><br>

<a id="markdown-ワイルドカード" name="ワイルドカード"></a>

### ワイルドカード

<details>
    <summary>.dockerignore</summary>

```dockerignore
# 任意のディレクトリにある.DS_Store
**/.DS_Store

# databaseディレクトリにある任意のSQLiteファイル
database/*.sqlite3

# foo/temp.txt, foo/tempdir/
*/temp*

# foo/bar/temp.txt, foo/bar/tempdir/
*/*/temp*

# tempa/, tempb/
temp?
```

</details>

<br>

<a id="markdown-除外" name="除外"></a>

### 除外

<details>
    <summary>.dockerignore</summary>

```dockerignore
# README.md 以外を除外する
*.md
!README.md
```

```dockerignore
# README.md は含まれるが README-secret.md は除外する
*.md
!README*.md
README-secret.md
```

```
# README-secret.md も除外しない
*.md
README-secret.md
!README*.md
```

</details>

<br>

<br><br>

<a id="markdown-arg" name="arg"></a>

## ARG

FROM よりも前に記述できる唯一の命令

<details>
    <summary>Commands</summary>

```dockerfile
FROM ubuntu

RUN echo '${user:-default_user}: ' ${user:-default_user}
RUN echo '${user:+default_user}: ' ${user:+default_user}
ARG user=default_user
RUN echo '${user}: ' $user
```

```bash
$ docker build -t yaand/arg:latest -f dockerfiles/ARG/Dockerfile dockerfiles/ARG/
```

```
${user:-default_user}:  default_user
${user:+default_user}:
${user}:  default_user
```

<br>

```bash
$ docker build -t yaand/arg:latest --build-arg user=option_user -f dockerfiles/ARG/Dockerfile dockerfiles/ARG/
```

```
${user:-default_user}:  default_user
${user:+default_user}:
${user}:  option_user
```

</details>

<a id="markdown-マルチステージビルド" name="マルチステージビルド"></a>

### マルチステージビルド

マルチステージビルドの場合、ビルドステージごとに `ARG` 命令を記述する必要がある

<details>
    <summary>Dockerfile</summary>

```dockerfile
FROM ubuntu
ARG SETTINGS
RUN echo $SETTINGS

FROM ubuntu
ARG SETTINGS
RUN echo $SETTINGS
```

</details>

<br><br>

<a id="markdown-from" name="from"></a>

## FROM

```dockerfile
FROM ubuntu
```

```dockerfile
FROM ubuntu AS myubuntu
```

```dockerfile
FROM ubuntu:21.04 AS myubuntu21
```

<br><br>

<a id="markdown-from-で-arg-の設定値を使いたい場合" name="from-で-arg-の設定値を使いたい場合"></a>

### FROM で ARG の設定値を使いたい場合

<details>
    <summary>Dockerfile</summary>

```dockerfile
ARG IMAGE_VERSION=latest
FROM ubuntu:${IMAGE_VERSION}
# FROMの前で定義したARGの値をFROM命令の後で再度参照したい場合は再び記述する必要がある
ARG VERSION
RUN echo $VERSION
```

</details>

<br><br>

<a id="markdown-マルチステージビルド-1" name="マルチステージビルド-1"></a>

### マルチステージビルド

<details>
    <summary>Dockerfile</summary>

```dockerfile
FROM node:alpine as node
FROM php:fpm-alpine

WORKDIR /var/www/html

RUN apk add --no-cache --virtual g++
RUN apk add --no-cache libstdc++ && apk add --no-cache libgcc

COPY --from=node /usr/local/bin/node /usr/local/bin/
COPY --from=node /usr/local/lib/node_modules/ /usr/local/lib/node_modules/
RUN ln -s /usr/local/bin/node /usr/local/bin/nodejs \
    && ln -s /usr/local/lib/node_modules/npm/bin/npm-cli.js /usr/local/bin/npm \
    && ln -s /usr/local/lib/node_modules/npm/bin/npm-cli.js /usr/local/bin/npx
```

</details>

<br>

<details>
    <summary>Commands</summary>

```bash
$ docker build -t yaand/from-php-node:latest -f dockerfiles/FROM/php-node/Dockerfile dockerfiles/FROM/php-node/
$ docker run --rm --name from-php-node yaand/from-php-node:latest node version
```

</details>

<br><br>

<a id="markdown-label" name="label"></a>

## LABEL

<details>
    <summary>Dockerfile</summary>

```dockerfile
FROM ubuntu

LABEL maintainer="YA-androidapp(https://github.com/YA-androidapp)" \
      version="1.0" \
      description="This is hoge image."
```

</details>

<br>

<details>
    <summary>Commands</summary>

```bash
$ docker build -t yaand/label:latest -f dockerfiles/LABEL/Dockerfile dockerfiles/LABEL/
$ docker run -it --name label yaand/label:latest

# `docker inspect` で確認
$ docker inspect --format '{{ index .Config.Labels "maintainer"}}' label
```

> YA-androidapp(https://github.com/YA-androidapp)

</details>

<br><br>

<a id="markdown-env" name="env"></a>

## ENV

```dockerfile
FROM ubuntu

ENV Key Value
ENV Key=Value
ENV Key1="Value 001" Key2=Value\ 002 \
    Key3="Value 003"
```

| 参照           | key が定義済 | key が未定義 |
| -------------- | ------------ | ------------ |
| `$key`         | `Value`      | `""`         |
| `${key}`       | `Value`      | `""`         |
| `${key:-word}` | `Value`      | `word`       |
| `${key:+word}` | `word`       | `""`         |

```bash
$ docker build -t yaand/env:latest -f dockerfiles/ENV/Dockerfile dockerfiles/ENV/
$ docker run -it --rm --env Key=ChangedValue --name env yaand/env:latest bash
root@91ae9e23e667:/# echo $Key
ChangedValue
root@91ae9e23e667:/# echo $Key1
Value 001
```

<br><br>

<a id="markdown-user" name="user"></a>

## USER

後続の RUN、CMD、ENTRYPOINT で使われるユーザー（・グループ）を指定

```dockerfile
USER <user>[:<group>]
# または
USER <UID>[:<GID>]
```

<details>
    <summary>Commands</summary>

```dockerfile
FROM ubuntu

RUN useradd docker
USER docker

CMD ["/bin/bash"]
```

```bash
$ docker build -t yaand/user:latest -f dockerfiles/USER/Dockerfile dockerfiles/USER/
$ docker run -it --rm --name user yaand/user:latest
docker@af16ca6acefe:/$ whoami
docker
```

</details>

<br><br>

<a id="markdown-workdir" name="workdir"></a>

## WORKDIR

後続の RUN、CMD、ENTRYPOINT、COPY、ADD で使われるワークディレクトリを指定

- WORKDIR が存在しないときは生成される
- 複数回定義することができる
- 相対パスで指定された場合は、直前の WORKDIR 命令からの相対パスとなる

```dockerfile
WORKDIR /var/www
WORKDIR html
RUN pwd
```

> /var/www/html

<br><br>

<a id="markdown-add-と-copy" name="add-と-copy"></a>

## ADD と COPY

- `ADD`
  - ファイル
  - ディレクトリ（ディレクトリそのものではなく中身がコピーされる）
  - 圧縮ファイル（ tar アーカイブであって、認識できるフォーマット（gzip、bzip2、xz））
  - リモートファイル URL

```dockerfile
ADD <src>... <dest>
ADD ["<src>",... "<dest>"]
```

- `COPY`
  - ファイル
  - ディレクトリ
  - 実行済のビルドステージ `--from=<name|index>`

```dockerfile
COPY <src>... <dest>
COPY ["<src>",... "<dest>"]
```

<details>
    <summary>Commands</summary>

```bash
$ cd dockerfiles/ADD/
$ tar -zcvf lipsum.tar.gz lipsum.txt
```

```dockerfile
FROM alpine

# 既存ディレクトリ
ADD lipsum.txt /tmp
ADD lipsum.txt /tmp/lipsum2.txt

ADD lipsum.tar.gz /tmp

ADD https://raw.githubusercontent.com/YA-androidapp/Docker-Cheatsheet/main/LICENSE /tmp

# 新規ディレクトリ
ADD lipsum.txt /lipsum1
ADD lipsum.tar.gz /lipsum2
ADD https://raw.githubusercontent.com/YA-androidapp/Docker-Cheatsheet/main/LICENSE /lipsum3

ADD lipsum.txt /lipsum4/
ADD lipsum.tar.gz /lipsum5/
ADD https://raw.githubusercontent.com/YA-androidapp/Docker-Cheatsheet/main/LICENSE /lipsum6/
```

```bash
$ docker build -t yaand/add:latest -f dockerfiles/ADD/Dockerfile dockerfiles/ADD/
$ docker run -it --name add yaand/add:latest sh
/ # ls -la /tmp
-rw-------    1 root     root         11324 Jan  1  1970 LICENSE
-rw-r--r--    1 501      dialout       2774 Jul 13 10:28 lipsum.txt
-rw-r--r--    1 root     root          2774 Jul 13 10:28 lipsum2.txt

/ # ls -laR /lipsum*
-rw-r--r--    1 root     root          2774 Jul 13 10:28 /lipsum1
-rw-------    1 root     root         11324 Jan  1  1970 /lipsum3

/lipsum2:
-rw-r--r--    1 501      dialout       2774 Jul 13 10:28 lipsum.txt

/lipsum4:
-rw-r--r--    1 root     root          2774 Jul 13 10:28 lipsum.txt

/lipsum5:
-rw-r--r--    1 501      dialout       2774 Jul 13 10:28 lipsum.txt

/lipsum6:
-rw-------    1 root     root         11324 Jan  1  1970 LICENSE
```

</details>

<br><br>

<a id="markdown-shell" name="shell"></a>

## SHELL

RUN、CMD、ENTRYPOINT の各コマンドをシェル形式で記述した際に影響する

```dockerfile
# 既定
SHELL ["/bin/sh", "-c"]

SHELL ["/bin/bash", "-c"]

SHELL ["powershell", "-command"]

SHELL ["cmd", "/S", "/C"]
```

<br><br>

<a id="markdown-run" name="run"></a>

## RUN

<a id="markdown-apt-の例" name="apt-の例"></a>

### apt の例

```dockerfile
FROM ubuntu
RUN apt-get update \
 && apt-get install -y --no-install-recommends \
    iproute2 \
 && apt-get -y clean \
 && rm -rf /var/lib/apt/lists/*
```

<br>

<details>
    <summary>Commands</summary>

```bash
$ docker build -t yaand/run-iproute2:latest -f dockerfiles/RUN/iproute2/Dockerfile dockerfiles/RUN/iproute2/
$ docker run --rm --name run-iproute2 yaand/run-iproute2:latest ip a
```

</details>

<br>

<details>
    <summary>Results</summary>

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: tunl0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
3: ip6tnl0@NONE: <NOARP> mtu 1452 qdisc noop state DOWN group default qlen 1000
    link/tunnel6 :: brd ::
16: eth0@if17: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
```

</details>

<br>

<a id="markdown-コマンドの形式" name="コマンドの形式"></a>

### コマンドの形式

```dockerfile
FROM ubuntu

# シェル形式
#   既定では、 `/bin/sh -c`(Linux) または `cmd /S /C`(Windows)
RUN echo $0 > sh.txt
#   '/bin/sh' 以外の別のシェルを利用する場合
RUN /bin/bash -c 'echo $0 > bash.txt'

# exec 形式(コマンドシェルを起動せずに直接実行する)
RUN ["/bin/ls", "-l", "/"]
RUN ["/bin/bash", "-c", "echo $0 > exec.txt"]
```

<br>

<details>
    <summary>Commands</summary>

```bash
$ docker build -t yaand/run-shell:latest -f dockerfiles/RUN/shell/Dockerfile dockerfiles/RUN/shell/
$ docker run -it --rm --name run-shell yaand/run-shell:latest /bin/bash
root@ac8d2263d31c:/# cat sh.txt
/bin/sh
root@ac8d2263d31c:/# cat bash.txt
/bin/bash
root@ac8d2263d31c:/# cat exec.txt
/bin/bash
root@ac8d2263d31c:/# exit
```

</details>

<br><br>

<a id="markdown-cmd-と-entrypoint" name="cmd-と-entrypoint"></a>

## CMD と ENTRYPOINT

```dockerfile
FROM ubuntu

# CMD を複数記述した場合は最後の1つのみ実行される

# シェル形式(`/bin/sh -c`の中で実行)
CMD ls -la /
ENTRYPOINT ls -la /

# exec 形式
CMD ["ls","-la","/"]
ENTRYPOINT ["ls","-la","/"]

# 実行モジュールを省略する場合は ENTRYPOINT 命令を合わせて指定する
ENTRYPOINT ["ls"]
CMD ["-la", "/"]
```

<br><br>

<a id="markdown-コマンドを実行時に置き換える" name="コマンドを実行時に置き換える"></a>

### コマンドを実行時に置き換える

<details>
    <summary>Commands</summary>

```bash
# シェル形式
$ docker build -t yaand/entrypoint-shell:latest -f dockerfiles/ENTRYPOINT/shell/Dockerfile dockerfiles/ENTRYPOINT/shell/
$ docker build -t yaand/cmd-shell:latest -f dockerfiles/CMD/shell/Dockerfile dockerfiles/CMD/shell/

# ENTRYPOINT で指定された `ls -la /` が実行される
$ docker run --rm --name entrypoint-shell yaand/entrypoint-shell:latest

# ENTRYPOINT の内容を引数で置き換えて `ls` が実行される（ `--entrypoint` にはコマンドオプションは指定できない）
$ docker run --rm --name entrypoint-shell --entrypoint="ls" yaand/entrypoint-shell:latest
$ docker run --rm --name entrypoint-shell --entrypoint="ls" yaand/entrypoint-shell:latest -la /home

# CMD で指定された `ls -la /` が実行される
$ docker run --rm --name cmd-shell yaand/cmd-shell:latest

# CMD の内容を引数で置き換えて `ls -la /home` が実行される
$ docker run --rm --name cmd-shell yaand/cmd-shell:latest ls -la /home
```

</details>

<br>

<details>
    <summary>Commands</summary>

```bash
# exec 形式
$ docker build -t yaand/entrypoint-exec:latest -f dockerfiles/ENTRYPOINT/exec/Dockerfile dockerfiles/ENTRYPOINT/exec/
$ docker build -t yaand/cmd-exec:latest -f dockerfiles/CMD/exec/Dockerfile dockerfiles/CMD/exec/

# ENTRYPOINT で指定された `ls -la /` が実行される
$ docker run --rm --name entrypoint-exec yaand/entrypoint-exec:latest

# ENTRYPOINT の内容を引数で置き換えて `ls` が実行される（ `--entrypoint` にはコマンドオプションは指定できない）
$ docker run --rm --name entrypoint-exec --entrypoint="ls" yaand/entrypoint-exec:latest
$ docker run --rm --name entrypoint-exec --entrypoint="ls" yaand/entrypoint-exec:latest -la /home

# CMD で指定された `ls -la /` が実行される
$ docker run --rm --name cmd-exec yaand/cmd-exec:latest

# CMD の内容を引数で置き換えて `ls -la /home` が実行される
$ docker run --rm --name cmd-exec yaand/cmd-exec:latest ls -la /home
```

</details>

<br><br>

<a id="markdown-コマンドのオプションを実行時に置き換える" name="コマンドのオプションを実行時に置き換える"></a>

### コマンドのオプションを実行時に置き換える

<details>
    <summary>Commands</summary>

```bash
# ENTRYPOINT 命令を合わせて指定
$ docker build -t yaand/cmd-entrypoint:latest -f dockerfiles/CMD/entrypoint/Dockerfile dockerfiles/CMD/entrypoint/

# ENTRYPOINT で指定された `ls` に CMD で指定された `-la /` を追加して実行される
$ docker run --rm --name cmd-entrypoint yaand/cmd-entrypoint:latest

# ENTRYPOINT で指定された `ls` に、 CMD の内容を引数で置き換えた `-la /home` を追加して実行される
$ docker run --rm --name cmd-entrypoint yaand/cmd-entrypoint:latest -la /home

# ENTRYPOINT の内容を引数で置き換えた `dpkg` が実行される (["dpkg", "-la", "/"] ではなく、 ["dpkg"] のみ)
$ docker run --rm --name cmd-entrypoint --entrypoint=dpkg yaand/cmd-entrypoint:latest
  # dpkg: error: need an action option

    # `docker run --rm --name cmd-entrypoint --entrypoint=dpkg yaand/cmd-entrypoint:latest -la /` と実行すると
    # dpkg-query: no packages found matching /
    # という、異なる結果になるため、 CMD の内容は無視されていることがわかる
    #   「--entrypoint を指定すると、イメージ上のあらゆるデフォルト命令群が削除されます」
    #   https://docs.docker.jp/engine/reference/run.html#entrypoint

# ENTRYPOINT の内容を引数で置き換えた `dpkg` に、 CMD の内容を引数で置き換えた `-l` を追加して実行される
$ docker run --rm --name cmd-entrypoint --entrypoint=dpkg yaand/cmd-entrypoint:latest -l
  # Desired=Unknown/Install/Remove/Purge/Hold
  # | Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
  # |/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
  # ||/ Name                    Version                      Architecture Description
  # +++-=======================-============================-============-========================================================================
  # ii  adduser                 3.118ubuntu2                 all          add and remove users and groups
  # ii  apt                     2.0.5                        amd64        commandline package manager
```

</details>

<br><br>

---

<a id="markdown-参考文献" name="参考文献"></a>

## 参考文献

- https://docs.docker.jp/index.html

---

Copyright (c) 2021 YA-androidapp(https://github.com/YA-androidapp) All rights reserved.
