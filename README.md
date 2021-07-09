
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
            - [オプション](#オプション)
                - [バックグラウンド実行（ -d ）・ポート転送（ -p ）・ボリューム（ -v ）](#バックグラウンド実行--d-・ポート転送--p-・ボリューム--v-)
        - [ローカルにあるイメージを削除](#ローカルにあるイメージを削除)
            - [使用していないイメージを削除](#使用していないイメージを削除)
        - [ローカルにあるコンテナを削除](#ローカルにあるコンテナを削除)
            - [使用していないコンテナを削除](#使用していないコンテナを削除)
    - [Dockerfile をもとにコンテナを実行](#dockerfile-をもとにコンテナを実行)
        - [Dockerfile からビルド](#dockerfile-からビルド)
    - [実行中のコンテナに変更を加えてプッシュ](#実行中のコンテナに変更を加えてプッシュ)
    - [バックアップ・リストア](#バックアップ・リストア)
        - [save・load](#save・load)
        - [export・import](#export・import)
    - [状態を確認](#状態を確認)
        - [ホストの状態を確認](#ホストの状態を確認)
        - [イベントを確認](#イベントを確認)
        - [コンテナの状態を確認](#コンテナの状態を確認)
            - [コンテナの情報を確認](#コンテナの情報を確認)
                - [条件でフィルタリング](#条件でフィルタリング-2)
                - [出力をフォーマット](#出力をフォーマット-1)
            - [詳細情報を確認](#詳細情報を確認)
            - [コンテナのリソース使用状況を確認](#コンテナのリソース使用状況を確認)
            - [コンテナのプロセス一覧を確認](#コンテナのプロセス一覧を確認)
            - [コンテナのログを確認](#コンテナのログを確認)
            - [ポート情報を確認](#ポート情報を確認)
    - [参考文献](#参考文献)

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


<a id="markdown-オプション" name="オプション"></a>
#### オプション

<details>
    <summary>Options</summary>

| 項目                                                               | 説明                                                                                                                        |
| ------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `-a` , `--attach=[]`                                               | STDIN、STDOUT、STDERR にアタッチする                                                                                        |
| `--add-host=[]`                                                    | ホストから IP アドレスのマッピングをカスタマイズして追加 (host:ip)                                                          |
| `--blkio-weight=0`                                                 | ブロック IO ウエイト (相対ウエイト)                                                                                         |
| `--blkio-weight-device=[]`                                         | ブロック IO ウエイト (相対デバイス・ウエイト。書式： `デバイス名:ウエイト`)                                                 |
| `--cpu-shares=0`                                                   | CPU 共有 (相対ウエイト)                                                                                                     |
| `--cap-add=[]`                                                     | Linux ケーパビリティの追加                                                                                                  |
| `--cap-drop=[]`                                                    | Linux ケーパビリティの削除                                                                                                  |
| `--cgroup-parent=""`                                               | コンテナ用のオプション親 cgroup を指定                                                                                      |
| `--cidfile=""`                                                     | コンテナ ID をファイルに書き出し                                                                                            |
| `--cpu-percent=0`                                                  | コンテナが実行可能な CPU 使用率のパーセントを制限。Windows のみ                                                             |
| `--cpu-period=0`                                                   | CPU CFS (Completely Fair Scheduler) ペイロードの制限                                                                        |
| `--cpu-quota=0`                                                    | CPU CFS (Completely Fair Scheduler) クォータの制限                                                                          |
| `--cpuset-cpus=""`                                                 | 実行を許可する CPU (0-3, 0,1)                                                                                               |
| `--cpuset-mems=""`                                                 | 実行を許可するメモリ必要量 (0-3, 0,1)                                                                                       |
| `-d` , `--detach`                                                  | コンテナをバックグラウンドで実行し、コンテナ ID を表示                                                                      |
| `--detach-keys`                                                    | コンテナのデタッチに使うエスケープ・キー・シーケンスを設定                                                                  |
| `--device=[]`                                                      | ホスト・デバイスをコンテナに追加                                                                                            |
| `--device-read-bps=[]`                                             | デバイスからの読み込みレート (バイト/秒) を制限 (例: --device-read-bps=/dev/sda:1mb)                                        |
| `--device-read-iops=[]`                                            | デバイスからの読み込みレート (IO/秒) を制限 (例: --device-read-iops=/dev/sda:1000)                                          |
| `--device-write-bps=[]`                                            | デバイスへの書き込みレート (バイト/秒) を制限 (例: --device-write-bps=/dev/sda:1mb)                                         |
| `--device-write-iops=[]`                                           | デバイスへの書き込みレート (IO/秒) を制限 (例: --device-write-bps=/dev/sda:1000)                                            |
| `--disable-content-trust=true`                                     | イメージの認証をスキップ                                                                                                    |
| `--dns=[]`                                                         | カスタム DNS サーバの指定                                                                                                   |
| `--dns-opt=[]`                                                     | カスタム DNS オプションの指定                                                                                               |
| `--dns-search=[]`                                                  | カスタム DNS 検索ドメインの指定                                                                                             |
| `-e, --env=[]`                                                     | 環境変数を指定                                                                                                              |
| `--entrypoint=""`                                                  | イメージのデフォルト ENTRYPOINT を上書き                                                                                    |
| `--env-file=[]`                                                    | ファイルから環境変数を読み込み                                                                                              |
| `--expose=[]`                                                      | ポートまたはポート範囲を露出                                                                                                |
| `--group-add=[]`                                                   | 参加するグループを追加                                                                                                      |
| `-h` , `--hostname=""`                                             | コンテナのホスト名                                                                                                          |
| `--help`                                                           | 使い方の表示                                                                                                                |
| `-i` , `--interactive`                                             | コンテナの STDIN にアタッチ                                                                                                 |
| `--ip=""`                                                          | コンテナの IPv4 アドレス (例: 172.30.100.104)                                                                               |
| `--ip6=""`                                                         | コンテナの IPv6 アドレス (例: 2001:db8::33)                                                                                 |
| `--ipc=""`                                                         | 使用する IPC 名前空間                                                                                                       |
| `--isolation=""`                                                   | コンテナの分離（独立）技術                                                                                                  |
| `--kernel-memory=""`                                               | Kernel メモリ上限                                                                                                           |
| `-l` , `--label=[]`                                                | コンテナにメタデータを指定 (例: --label=com.example.key=value)                                                              |
| `--label-file=[]`                                                  | 行ごとにラベルを記述したファイルを読み込み                                                                                  |
| `--link=[]`                                                        | 他のコンテナへのリンクを追加                                                                                                |
| `--link-local-ip=[]`                                               | コンテナとリンクするローカルの IPv4/IPv6 アドレス (例: 169.254.0.77, fe80::77)                                              |
| `--log-driver=""`                                                  | コンテナ用のログ記録ドライバを追加                                                                                          |
| `--log-opt=[]`                                                     | ログドライバのオプションを指定                                                                                              |
| `-m, --memory=""`                                                  | メモリ上限                                                                                                                  |
| `--mac-address=""`                                                 | コンテナの MAC アドレス (例： 92:d0:c6:0a:29:33)                                                                            |
| `--io-maxbandwidth=""`                                             | システム・デバイスの IO 帯域に対する上限を指定（Windows のみ） \(\*\)                                                       |
| `--io-maxiops=0`                                                   | システム・ドライブの最大 IO/秒に対する上限を指定（Windows のみ） `--io-maxbandwidth` と `--io-maxiops` は相互排他オプション |
| `--memory-reservation=""`                                          | メモリのソフト上限                                                                                                          |
| `--memory-swap=""`                                                 | 整数値の指定はメモリにスワップ値を追加。-1 は無制限スワップを有効化                                                         |
| `--memory-swappiness=""`                                           | コンテナ用メモリのスワップ程度を調整。整数値の 0 から 100 で指定                                                            |
| `--name=""`                                                        | コンテナに名前を割り当て                                                                                                    |
| `--net="bridge"`                                                   | コンテナをネットワークに接続                                                                                                |
|                                                                    | `bridge`: docker ブリッジ上でコンテナ用に新しいネットワーク・スタックを作成                                                 |
|                                                                    | `none`: コンテナにネットワーク機能を付けない                                                                                |
|                                                                    | `container:<name or id>`: 他のコンテナ用ネットワーク・スタックを再利用                                                      |
|                                                                    | `host`: コンテナ内でホスト側ネットワーク・スタックを使用                                                                    |
|                                                                    | `NETWORK`: 「docker network create」コマンドでユーザ作成したネットワークを使用                                              |
| `--net-alias=[]`                                                   | コンテナにネットワーク内部用のエイリアスを追加                                                                              |
| `--oom-kill-disable`                                               | コンテナの OOM Killer を無効化するかどうか指定                                                                              |
| `--oom-score-adj=0`                                                | コンテナに対してホスト側の OOM 優先度を設定 ( -1000 ～ 1000 を指定)                                                         |
| `-P` , `--publish-all`                                             | 全ての露出ポートをランダムなポートに公開                                                                                    |
| `-p` , `--publish=[]`                                              | コンテナのポートをホスト側に公開                                                                                            |
| `--pid=""`                                                         | 使用する PID 名前空間                                                                                                       |
| `--pids-limit=-1`                                                  | コンテナの pids 制限を調整 (kernel 4.3 以上は -1 で無制限に設定)                                                            |
| `--privileged`                                                     | このコンテナに対して拡張権限を与える                                                                                        |
| `--read-only`                                                      | コンテナのルート・ファイルシステムを読み込み専用としてマウント                                                              |
| `--restart="no"`                                                   | 再起動ポリシー ( `no` , `on-failure[:max-retry]` , `always` , `unless-stopped` )                                            |
| `--rm`                                                             | コンテナ終了時、自動的に削除                                                                                                |
| `--runtime=""`                                                     | コンテナで使うランタイム名を指定                                                                                            |
| `--shm-size=[]`                                                    | `/dev/shm` のサイズ。 \(\*\*\)                                                                                              |
| `--security-opt=[]`                                                | セキュリティ・オプション                                                                                                    |
| `--sig-proxy=true`                                                 | 受信したシグナルをプロセスにプロキシ                                                                                        |
| `--stop-signal="SIGTERM"`                                          | コンテナの停止シグナル                                                                                                      |
| `--storage-opt=[]`                                                 | コンテナごとにストレージ・ドライバのオプションを指定                                                                        |
| `-sysctl[=_[]_]]`                                                  | 実行時に名前空間カーネル・パラメータを調整                                                                                  |
| `-t` , `--tty`                                                     | 疑似ターミナル (pseudo-TTY) を割り当て                                                                                      |
| `-u` , `--user=""`                                                 | ユーザ名または UID                                                                                                          |
| `--userns=""`                                                      | コンテナのユーザ名前空間                                                                                                    |
|                                                                    | `host`:Docker ホストで使うユーザ名前空間                                                                                    |
|                                                                    | `''`: Docker デーモンのユーザ名前空間を指定するには `--userns-remap` オプションを使う                                       |
| `--ulimit=[]`                                                      | Ulimit オプション                                                                                                           |
| `--uts=""`                                                         | 使用する UTS 名前空間                                                                                                       |
| `-v` , `--volume=[ホスト側ソース:]コンテナ側送信先[:<オプション>]` | ボリュームを拘束マウント。カンマ区切りで指定                                                                                |
|                                                                    | `オプション` は [rw \| ro], [z \| Z], [[r]shared \| [r]slave \| [r]private], [nocopy]                                       |
|                                                                    | `ホスト側ソース` は絶対パスまたは名前の値                                                                                   |
| `--volume-driver=""`                                               | コンテナのボリューム・ドライバ                                                                                              |
| `--volumes-from=[]`                                                | 指定したコンテナからボリュームをマウント                                                                                    |
| `-w` , `--workdir=""`                                              | コンテナ内の作業用ディレクトリを指定                                                                                        |

- \(\*\) ... 書式は `<数値><単位>`。単位はオプションで `b` (B/秒)、`k` (kB/秒)、 `m` (MB/秒)、 `g` (GB/秒)。単位を指定しなければ、 `バイト/秒` <br> `--io-maxbandwidth` と `--io-maxiops` は相互排他オプション
- \(\*\*\) ... 書式は `<数値><単位>`. `数値` は必ず `0` より大きい。単位はオプションで `b` (bytes)、 `k` (KB)、 `m` (MB)、 `g` (GB) を指定可能。単位を指定しなければ `bytes` 、数値を指定しなければ `64m`

</details>

<br><br>


<a id="markdown-バックグラウンド実行--d-・ポート転送--p-・ボリューム--v-" name="バックグラウンド実行--d-・ポート転送--p-・ボリューム--v-"></a>
##### バックグラウンド実行（ -d ）・ポート転送（ -p ）・ボリューム（ -v ）

<details>
    <summary>Commands</summary>

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


<a id="markdown-実行中のコンテナに変更を加えてプッシュ" name="実行中のコンテナに変更を加えてプッシュ"></a>
## 実行中のコンテナに変更を加えてプッシュ

```bash
# コンテナを起動
$ docker run -it --name mydebian debian:latest /bin/bash

# コンテナに変更を加える
root@2c2a9ef0c999:/# touch my.txt

# デタッチ
root@2c2a9ef0c999:/# (Ctrl + p , Ctrl + q)

# ホスト・コンテナ間でファイルコピー
$ docker cp mydebian:/my.txt ./
$ docker cp ./my.txt mydebian:/my2.txt
# 結果を確認
$ docker exec mydebian ls / | grep .txt
my.txt
my2.txt

# コミット前に差分チェック
$ docker diff mydebian
A /my.txt
A /my2.txt

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


<a id="markdown-バックアップ・リストア" name="バックアップ・リストア"></a>
## バックアップ・リストア

- save ... 1 つまたは複数の **イメージ** を tar アーカイブとして保存
- export ... **コンテナー** のファイルシステムを tar アーカイブとして出力

<br>


<a id="markdown-save・load" name="save・load"></a>
### save・load

<details>
    <summary>Commands</summary>

```bash
# tar.gzファイルにエクスポート
$ docker save mydebian | gzip -c > saved.tar.gz
$ file saved.tar.gz
saved.tar.gz: gzip compressed data, from Unix, original size modulo 2^32 119273984
# tarファイル（gzip、bzip2、xzで圧縮されていてもよい）からインポート
$ cat saved.tar.gz | docker load
Loaded image: mydebian:latest
```

</details>

<br><br>


<a id="markdown-export・import" name="export・import"></a>
### export・import

<details>
    <summary>Commands</summary>

```bash
# tar.gzファイルにエクスポート
$ docker export mydebian | gzip -c > exported.tar.gz
# tarファイル（.tar, .tar.gz, .tgz, .bzip, .tar.xz, .txz）からインポート
$ cat exported.tar.gz | gzip -d | docker import - mydebian:imported
$ docker run -it --rm --name my-imported-debian mydebian:imported /bin/bash
root@f21cebc04d0a:/# pwd
/
root@f21cebc04d0a:/# ls -la *.txt
-rw-r--r-- 1 root root 0 Jul  7 09:28 my.txt
-rw-r--r-- 1 1000 1000 0 Jul  7 09:28 my2.txt
```

</details>

<br><br>


<a id="markdown-状態を確認" name="状態を確認"></a>
## 状態を確認


<a id="markdown-ホストの状態を確認" name="ホストの状態を確認"></a>
### ホストの状態を確認

```bash
$ docker info
```

<details>
    <summary>Results</summary>

```
Client:
 Debug Mode: false

Server:
 Containers: 3
  Running: 2
  Paused: 0
  Stopped: 1
  ...
```

</details>

<br><br>


<a id="markdown-イベントを確認" name="イベントを確認"></a>
### イベントを確認

```bash
$ docker events
```

<br><br>


<a id="markdown-コンテナの状態を確認" name="コンテナの状態を確認"></a>
### コンテナの状態を確認


<a id="markdown-コンテナの情報を確認" name="コンテナの情報を確認"></a>
#### コンテナの情報を確認

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

# 最後のコンテナのみ出力
$ docker ps -l
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
26c3e16c970e        debian:latest       "bash"              19 minutes ago      Exited (0) 19 minutes ago                       mydebian2

# ID、コマンドなどを省略表示しない
$ docker ps --no-trunc

# コンテナIDのみ出力
$ docker ps -q
c44ebce078ea
```

<br><br>


<a id="markdown-条件でフィルタリング-2" name="条件でフィルタリング-2"></a>
##### 条件でフィルタリング

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
##### 出力をフォーマット

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


<a id="markdown-詳細情報を確認" name="詳細情報を確認"></a>
#### 詳細情報を確認

```bash
$ docker inspect mydebian
```

```bash
$ docker inspect -f '{{ .State.Status }}' mydebian
```

<details>
    <summary>Results</summary>

```json
[
    {
        "Id": "***",
        "Created": "***",
        "Path": "bash",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 4822,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2021-07-07T01:23:45.0000000Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:***",
        ...
    }
]
```

```
running
```

</details>

<br><br>


<a id="markdown-コンテナのリソース使用状況を確認" name="コンテナのリソース使用状況を確認"></a>
#### コンテナのリソース使用状況を確認

```bash
$ docker stats -a
```

<details>
    <summary>Results</summary>

```
CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
26c3e16c970e        mydebian2           0.00%               0B / 0B               0.00%               0B / 0B             0B / 0B             0
c44ebce078ea        mydebian            0.00%               1.195MiB / 12.39GiB   0.01%               1.26kB / 0B         0B / 0B             1
```

</details>

<br><br>


<a id="markdown-コンテナのプロセス一覧を確認" name="コンテナのプロセス一覧を確認"></a>
#### コンテナのプロセス一覧を確認

```bash
$ docker top mydebian
```

<details>
    <summary>Results</summary>

```
PID                 USER                TIME                COMMAND
4822                root                0:00                bash
```

</details>

<br><br>


<a id="markdown-コンテナのログを確認" name="コンテナのログを確認"></a>
#### コンテナのログを確認

```bash
# タイムスタンプを表示
$ docker logs -t mydebian

# ログの最後から指定した行数を表示
$ docker logs --tail="1" mydebian

# sinceに指定できるのは、RFC 3339 date、UNIX タイムスタンプ、Go 言語の期間文字（1m30s 、 3h ）
$ docker logs --details -f --since="2021-07-07T01:23:45.6789+09:00" mydebian
```

<br><br>


<a id="markdown-ポート情報を確認" name="ポート情報を確認"></a>
#### ポート情報を確認

```bash
$ docker run -it --rm -d -p 8080:80 --name mynginx nginx
$ docker port mynginx 80
```

<details>
    <summary>Results</summary>

```
0.0.0.0:8080
```

</details>

<br><br>

---


<a id="markdown-参考文献" name="参考文献"></a>
## 参考文献

- https://docs.docker.jp/index.html

---

Copyright (c) 2021 YA-androidapp(https://github.com/YA-androidapp) All rights reserved.
