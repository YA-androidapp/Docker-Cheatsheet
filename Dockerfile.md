<a id="markdown-docker-cheatsheet" name="docker-cheatsheet"></a>

# Docker-Cheatsheet

Dockerfile

---

<br><br>

<!-- TOC -->

- [Docker-Cheatsheet](#docker-cheatsheet)
  - [ビルドコマンド](#ビルドコマンド)
  - [FROM](#from)
    - [FROM で ARG の設定値を使いたい場合](#from-で-arg-の設定値を使いたい場合)
    - [マルチステージビルド](#マルチステージビルド)
  - [RUN](#run)
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

<br><br>

<a id="markdown-from" name="from"></a>

## FROM

```dockerfile
FROM ubuntu AS myubuntu
FROM ubuntu:21.04 AS myubuntu21
```

<a id="markdown-from-で-arg-の設定値を使いたい場合" name="from-で-arg-の設定値を使いたい場合"></a>

### FROM で ARG の設定値を使いたい場合

```dockerfile
ARG IMAGE_VERSION=latest
FROM ubuntu:${IMAGE_VERSION}
# FROMの前で定義したARGの値をFROM命令の後で再度参照したい場合は再び記述する必要がある
ARG VERSION
RUN echo $VERSION
```

<a id="markdown-マルチステージビルド" name="マルチステージビルド"></a>

### マルチステージビルド

```dockerfile

```

<br><br>

<a id="markdown-run" name="run"></a>

## RUN

```dockerfile
FROM ubuntu
RUN apt-get update \
 && apt-get install -y --no-install-recommends \
    iproute2 \
 && apt-get -y clean \
 && rm -rf /var/lib/apt/lists/*
```

```bash
$ docker build -t yaand/mycontainer:latest docker/dockerfiles/RUN/
$ docker run --name mycontainer yaand/mycontainer:latest ip a
```

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

---

<a id="markdown-参考文献" name="参考文献"></a>

## 参考文献

- https://docs.docker.jp/index.html

---

Copyright (c) 2021 YA-androidapp(https://github.com/YA-androidapp) All rights reserved.
