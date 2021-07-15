<a id="markdown-docker-cheatsheet" name="docker-cheatsheet"></a>

# Docker-Cheatsheet

Docker Compose

---

<br><br>

<!-- TOC -->

- [Docker-Cheatsheet](#docker-cheatsheet)
  - [基本的なコマンド](#基本的なコマンド)
  - [参考文献](#参考文献)

<!-- /TOC -->

<br><br>

<a id="markdown-基本的なコマンド" name="基本的なコマンド"></a>

## 基本的なコマンド

```bash
# バージョン確認
$ docker-compose version
```

<details>
    <summary>Results</summary>

```bash
docker-compose version 1.29.2, build 5becea4c
docker-py version: 5.0.0
CPython version: 3.9.0
OpenSSL version: OpenSSL 1.1.1h  22 Sep 2020
```

</details>

<br>

```bash
# 構築
$ docker compose build --build-arg key=val --force-rm --no-cache --pull

# 構築、作成、起動、アタッチ
$ docker compose up -d --build --force-recreate

# 停止
$ docker compose stop

# コンテナを停止し、 up で作成したコンテナ・ネットワーク・ボリューム・イメージを削除
$ docker compose down
    # Compose ファイル内で定義したサービス用のコンテナ
    # Compose ファイルの network セクションで定義したネットワーク
    # default ネットワーク（を使っている場合）
$ docker compose down --rmi all
    # あらゆるサービスで使う全イメージを削除
$ docker compose down --volumes
    # Compose ファイルの `volumes` セクションの名前付きボリュームと、コンテナがアタッチしたアノニマス・ボリュームを削除
$ docker compose down --remove-orphans
    # Compose ファイルで定義していないサービス用のコンテナも削除
$ docker compose down --rmi all --volumes --remove-orphans
    # すべて削除

```

<br><br>

---

<a id="markdown-参考文献" name="参考文献"></a>

## 参考文献

- https://docs.docker.jp/compose/toc.html

Copyright (c) 2021 YA-androidapp(https://github.com/YA-androidapp) All rights reserved.
