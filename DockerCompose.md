<a id="markdown-docker-cheatsheet" name="docker-cheatsheet"></a>

# Docker-Cheatsheet

Docker Compose

---

<br><br>

<!-- TOC -->

- [Docker-Cheatsheet](#docker-cheatsheet)
  - [基本的なコマンド](#基本的なコマンド)
    - [バージョン確認（version）](#バージョン確認version)
    - [docker-compose.yml の内容を表示（config）](#docker-composeyml-の内容を表示config)
    - [サービスを構築（build）](#サービスを構築build)
    - [構築、作成、起動、アタッチ（up）](#構築作成起動アタッチup)
      - [Docker Compose でスケール＋ロードバランシング（up --scale）](#docker-compose-でスケール＋ロードバランシングup---scale)
        - [Nginx のみ利用](#nginx-のみ利用)
        - [haproxy イメージを利用](#haproxy-イメージを利用)
    - [コンテナ一覧（ps）・プロセス一覧（top）](#コンテナ一覧ps・プロセス一覧top)
      - [ID のみ](#id-のみ)
    - [コンテナでコマンドを実行](#コンテナでコマンドを実行)
      - [docker compose up で起動しているコンテナでコマンドを実行（exec）](#docker-compose-up-で起動しているコンテナでコマンドを実行exec)
        - [データベースアクセスなど](#データベースアクセスなど)
      - [コンテナを新たに起動してコマンドを実行する（run）](#コンテナを新たに起動してコマンドを実行するrun)
    - [コンテナを停止し、 docker compose up で作成したコンテナ・ネットワーク・ボリューム・イメージを削除（down）](#コンテナを停止し-docker-compose-up-で作成したコンテナ・ネットワーク・ボリューム・イメージを削除down)
    - [ログを表示（logs）](#ログを表示logs)
      - [ポートを確認する（port）](#ポートを確認するport)
  - [参考文献](#参考文献)

<!-- /TOC -->

<br><br>

<a id="markdown-基本的なコマンド" name="基本的なコマンド"></a>

## 基本的なコマンド

<a id="markdown-バージョン確認version" name="バージョン確認version"></a>

### バージョン確認（version）

```bash
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

<br><br>

<a id="markdown-docker-composeyml-の内容を表示config" name="docker-composeyml-の内容を表示config"></a>

### docker-compose.yml の内容を表示（config）

```bash
$ docker compose --file ./compose/nginx/docker-compose.yml config
```

<details>
    <summary>Results</summary>

```yaml
services:
  nginx:
    container_name: nginx
    image: nginx:latest
    networks:
      default: null
    ports:
      - mode: ingress
        target: 80
        published: 80
        protocol: tcp
    volumes:
      - type: bind
        source: /Users/yu/Documents/GitHub/Docker-Cheatsheet/compose/config/index.html
        target: /usr/share/nginx/html/index.html
        bind:
          create_host_path: true
networks:
  default:
    name: config_default
```

</details>

<br><br>

<a id="markdown-サービスを構築build" name="サービスを構築build"></a>

### サービスを構築（build）

```bash
# docker compose build --build-arg key=val --force-rm --no-cache --pull
# docker compose build --build-arg key=val --force-rm --no-cache --pull <service>
$ docker compose -f ./compose/nginx/docker-compose.yml build
```

<br><br>

<a id="markdown-構築作成起動アタッチup" name="構築作成起動アタッチup"></a>

### 構築、作成、起動、アタッチ（up）

```bash
# docker compose up -d --build --force-recreate
# docker compose up -d --build --force-recreate <service>
$ docker compose -f ./compose/nginx/docker-compose.yml up -d
```

<details>
    <summary>Results</summary>

```
[+] Running 7/7
 ⠿ nginx Pulled                                                                                    10.6s
   ⠿ b4d181a07f80 Pull complete                                                                     4.1s
   ⠿ 66b1c490df3f Pull complete                                                                     5.3s
   ⠿ d0f91ae9b44c Pull complete                                                                     5.3s
   ⠿ baf987068537 Pull complete                                                                     5.4s
   ⠿ 6bbc76cbebeb Pull complete                                                                     5.5s
   ⠿ 32b766478bc2 Pull complete                                                                     5.5s
[+] Running 2/2
 ⠿ Network nginx_default  Created                                                                   4.0s
 ⠿ Container nginx        Started                                                                   4.0s
```

</details>

<br><br>

<a id="markdown-docker-compose-でスケール＋ロードバランシングup---scale" name="docker-compose-でスケール＋ロードバランシングup---scale"></a>

#### Docker Compose でスケール＋ロードバランシング（up --scale）

<a id="markdown-nginx-のみ利用" name="nginx-のみ利用"></a>

##### Nginx のみ利用

<details>
    <summary>Commands</summary>

```yaml
# docker-compose.yml

version: "3"
services:
  web:
    image: nginx

  loadbalancer:
    image: nginx
    container_name: loadbalancer
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/nginx.conf
    ports:
      - 80:80
```

<br>

```
# nginx.conf

upstream loadbalancer {
    server nginx_web_1;
    server nginx_web_2;
}

server {
    listen      80;
    location / {
        proxy_pass http://loadbalancer;
    }
}
```

<br>

```bash
$ docker compose -f ./compose/scale/nginx/docker-compose.yml up --scale web=3

$ docker-compose -f ./compose/scale/nginx/docker-compose.yml logs -f web
```

</details>

<br><br>

<a id="markdown-haproxy-イメージを利用" name="haproxy-イメージを利用"></a>

##### haproxy イメージを利用

<details>
    <summary>Commands</summary>

```yaml
# docker-compose.yml

version: "3"

services:
  web:
    image: nginx:alpine

  loadbalancer:
    image: haproxy:alpine
    ports:
      - 80:80
    volumes:
      - ./haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg
    depends_on:
      - web
```

<br>

```
# haproxy.cfg

defaults
    timeout connect 5s
    timeout client 5s
    timeout server 30s

frontend web_proxy
    bind *:80
    use_backend web-server

backend web-server
    balance roundrobin

    server web1 loadbalancer_web_1:80 check inter 2s
    server web2 loadbalancer_web_2:80 check inter 2s
    server web3 loadbalancer_web_3:80 check inter 2s

    mode http
    option forwardfor
```

<br>

```bash
$ COMPOSE_PROJECT_NAME=loadbalancer docker compose -f ./compose/scale/haproxy/docker-compose.yml up --scale web=3
```

</details>

<br><br>

<a id="markdown-コンテナ一覧ps・プロセス一覧top" name="コンテナ一覧ps・プロセス一覧top"></a>

### コンテナ一覧（ps）・プロセス一覧（top）

```bash
# 予めコンテナを起動しておく
# docker compose -f ./compose/nginx/docker-compose.yml up -d

# docker compose ps
$ docker compose -f ./compose/nginx/docker-compose.yml ps

# docker compose top
# $ docker compose -f ./compose/nginx/docker-compose.yml top # ← --file オプションを指定すると結果を取得できない
$ cd ./compose/nginx # 一旦 docker-compose.yml と同じ階層に cd
$ docker compose top
$ cd ../../
```

<details>
    <summary>Results</summary>

```
# docker compose ps

NAME                SERVICE             STATUS              PORTS
nginx               nginx               running             0.0.0.0:80->80/tcp, :::80->80/tcp
```

<br>

```
# docker compose top

nginx
UID     PID     PPID    C    STIME   TTY   TIME       CMD
root    20256   20230   0    03:37   ?     00:00:00   nginx: master process nginx -g daemon off;
uuidd   20469   20256   0    03:38   ?     00:00:00   nginx: worker process
uuidd   20470   20256   0    03:38   ?     00:00:00   nginx: worker process
uuidd   20471   20256   0    03:38   ?     00:00:00   nginx: worker process
uuidd   20472   20256   0    03:38   ?     00:00:00   nginx: worker process
```

</details>

<br>

<a id="markdown-id-のみ" name="id-のみ"></a>

#### ID のみ

```bash
# docker compose ps -q
$ docker compose -f ./compose/nginx/docker-compose.yml ps -q
```

<details>
    <summary>Results</summary>

```
70070728c13efbdc6318b45d51aa2b7f145ccbee9711bd5530bfeb3e40c3243c
```

</details>

<br><br>

<a id="markdown-コンテナでコマンドを実行" name="コンテナでコマンドを実行"></a>

### コンテナでコマンドを実行

<a id="markdown-docker-compose-up-で起動しているコンテナでコマンドを実行exec" name="docker-compose-up-で起動しているコンテナでコマンドを実行exec"></a>

#### docker compose up で起動しているコンテナでコマンドを実行（exec）

```bash
# docker compose exec <service> bash
$ docker compose -f ./compose/nginx/docker-compose.yml exec nginx bash
root@69ee177813c0:/# exit
exit
$ docker compose --file ./compose/nginx/docker-compose.yml ps
NAME                SERVICE             STATUS              PORTS
nginx               nginx               running             0.0.0.0:80->80/tcp, :::80->80/tcp

# 残ったコンテナを停止
$ docker compose -f ./compose/nginx/docker-compose.yml stop
[+] Running 2/2
 ⠿ Container nginx_nginx_run_dfe0eb2265c9  Stopped                                                                                        0.0s
 ⠿ Container nginx                         Stopped                                                                                        1.2s
```

<br>

<a id="markdown-データベースアクセスなど" name="データベースアクセスなど"></a>

##### データベースアクセスなど

```bash
$ docker compose exec db bash -c 'mysql -h $DB_HOST -u $DB_USER -p$DB_PASSWORD $DB_DATABASE'
$ docker compose exec db bash -c 'PGPASSWORD=$DB_PASSWORD psql -h $DB_HOST -p 5432 -U $DB_USER'
```

<br><br>

<a id="markdown-コンテナを新たに起動してコマンドを実行するrun" name="コンテナを新たに起動してコマンドを実行するrun"></a>

#### コンテナを新たに起動してコマンドを実行する（run）

```bash
# docker compose run <service> bash
$ docker compose -f ./compose/nginx/docker-compose.yml run nginx bash
root@c77ae496cb52:/# exit
exit
yu@A-MBP Docker-Cheatsheet % docker compose --file ./compose/nginx/docker-compose.yml ps
NAME                SERVICE             STATUS              PORTS
nginx               nginx               exited (0)
```

<br><br>

<a id="markdown-コンテナを停止し-docker-compose-up-で作成したコンテナ・ネットワーク・ボリューム・イメージを削除down" name="コンテナを停止し-docker-compose-up-で作成したコンテナ・ネットワーク・ボリューム・イメージを削除down"></a>

### コンテナを停止し、 docker compose up で作成したコンテナ・ネットワーク・ボリューム・イメージを削除（down）

```bash
$ docker compose down
    # Composeファイル内で定義したサービス用のコンテナ
    # Composeファイルの networkセクションで定義したネットワーク
    # defaultネットワーク（を使っている場合）
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

<a id="markdown-ログを表示logs" name="ログを表示logs"></a>

### ログを表示（logs）

```bash
$ docker compose logs
# ログの出力を表示しつづける
$ docker compose logs -f
$ docker compose logs --follow
# タイムスタンプ
$ docker compose logs -t
$ docker compose logs --timestamps
# 最終行付近
$ docker compose logs --tail
# サービス指定
$ docker compose logs -f --no-color <service>
```

<br><br>

<a id="markdown-ポートを確認するport" name="ポートを確認するport"></a>

#### ポートを確認する（port）

```bash
# docker compose port <service> <port number>
$ docker compose -f ./compose/nginx/docker-compose.yml up -d
$ docker compose -f ./compose/nginx/docker-compose.yml port nginx 80
```

```
0.0.0.0:80
```

<br><br>

---

<a id="markdown-参考文献" name="参考文献"></a>

## 参考文献

- https://docs.docker.jp/compose/toc.html

Copyright (c) 2021 YA-androidapp(https://github.com/YA-androidapp) All rights reserved.
